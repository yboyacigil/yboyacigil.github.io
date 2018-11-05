---
title:  "How to Implement a Wallet Server using SpringBoot with Akka"
date:   2018-11-03 15:15:15 +0300
categories: english springboot akka
header:
  overlay_image: /assets/images/springboot+akka.png
  overlay_filter: 0.5
---

In this post, I will share how to use SpringBoot together with Akka framework to build a reactive rest service (a wallet server) and also demonstrate how the polyglot approach can be applied to your project for writing tests using a very nice testing framework Spock in Groovy programming language.

SpringBoot and Akka are two powerful frameworks both have their own strengths and can be used alone to develop microservices. You can argue both has some pros and cons. Although it mostly depends on the taste of the developer, I love SpringBoot a lot when it comes to building microservices quickly with the help of huge support for integrations with almost any service you may need. Akka on the otherhand has a very solid actor model for developing concurrent and distributed applications but, imho compared to spring, it lacks easy dependency injection; designing REST endpoints with AkkaHttp is not straight forward and integrations with services are limited.

Let's jump into solution how I made use of SpringBoot and Akka together. To give some background, let me explain my case a little: In a typical online casino setup, a casino operator hosts games of a provider on its portal. There is a two way integration between online casino and game provider services to be able to launch game and to be able to process money transactions on player's account for bets and winnings. A wallet server is a service on casino side that provides a set of REST endpoints which handles money transaction on a player's wallet during game play.

SpringBoot fits nicely on setting up endpoints, injecting dependencies, processing database transactions, etc. and Akka is a great fit to model each players interaction with his wallet as an actor per game play and to supervise all players' game play sessions.

Below you can find specific implementation sections with example code snippets.

## SpringBoot JAX-RS

JAX-RS is a well-defined standart for developing REST endpoints and you can use JAX-RS with SpringBoot. All you need to do is to bring right dependencies into the scope.

```
dependencies {

    testCompile 'junit:junit'

    // Spring
    compile 'org.springframework.boot:spring-boot-starter-jersey'
    testCompile 'org.springframework.boot:spring-boot-starter-test'
    
}
```

I chose JAX-RS over Spring MVC, I believe, JAX-RS has better model than Spring MVC. But using it, brings a small overhead that is you need to configure it manually which is fairly easy as a Spring configuration class:

```
@Configuration
public class JerseyConfig  extends ResourceConfig {

    public JerseyConfig() {
        packages("com.yboyacigil.experiments.springboot.akka.walletserver.endpoints");

        register(LoggingFeature.class);
        property(LoggingFeature.LOGGING_FEATURE_LOGGER_LEVEL_SERVER, "INFO");
    }
}
```

## Akka

As mentioned above, I created one actor to handle all requests of a player during game play. Since actors have one mailbox for messages and messages are processed in order, that makes it more suitable in processing bets & winnings in the order it has received. Simply named it `GamePlayActor` as follows:

```
@Component
@Scope("prototype")
public class GamePlayActor extends AbstractLoggingActor {

    private AccountingService accountingService;

    public GamePlayActor(AccountingService accountingService) {
        this.accountingService = accountingService;
    }

    @Override
    public Receive createReceive() {
        return receiveBuilder()
            .match(GetCurrency.class, this::receiveGetCurrency)
            .match(GetBalance.class, this::receiveGetBalance)
            .matchAny(o -> log().info("Received unknown message: {}", o))
            .build();
    }

  // rest is omitted
}
```

As you notice, the actor is defined as a Spring component with `prototype` scope that is a necessity for actor system andSpring can inject all dependencies that it needs.

We need one more thing which is an extension for actor system that can produce actor instances with their dependencies.

```
import akka.actor.Extension;
import akka.actor.Props;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;

@Component
public class SpringExtension implements Extension {

    private ApplicationContext applicationContext;

    public void initialize(ApplicationContext applicationContext) {
        this.applicationContext = applicationContext;
    }

    public Props props(String actorBeanName) {
        return Props.create(SpringActorProducer.class, applicationContext, actorBeanName);
    }
}
```

where `SpringActorProducer` is a factory class for creating actor instances as Spring beans:

```
import akka.actor.Actor;
import akka.actor.IndirectActorProducer;
import org.springframework.context.ApplicationContext;

public class SpringActorProducer implements IndirectActorProducer {

    private final ApplicationContext applicationContext;
    private final String actorBeanName;

    public SpringActorProducer(ApplicationContext applicationContext,
        String actorBeanName) {
        this.applicationContext = applicationContext;
        this.actorBeanName = actorBeanName;
    }

    @Override
    public Actor produce() {
        return (Actor) applicationContext.getBean(actorBeanName);
    }

    @SuppressWarnings("unchecked")
    @Override
    public Class<? extends Actor> actorClass() {
        return (Class<? extends Actor>) applicationContext.getType(actorBeanName);
    }
}
```

## Reactivity

Akka actor are by design reactive. To complete the picture, you can use JAX-RS `AsyncResponse` in your endpoint:

```
@GET
@Path("/players/{pid}/account/balance")
@Produces(MediaType.APPLICATION_JSON)
public void getBalance(@Suspended final AsyncResponse asyncResponse, @PathParam("pid") Long pid) {

  GetBalance getBalance = GetBalance.builder()
    .pid(pid)
    .gameCode("not-important-atm")
    .build();

  askForResult(asyncResponse, getBalance);
}

@SuppressWarnings("unchecked")
private void askForResult(final AsyncResponse asyncResponse, GameMessage gameMessage) {
    Future<Object> result = Patterns.ask(gamePlaySupervisor, gameMessage, 1000L);

    FutureConverters.toJava(result).toCompletableFuture()
        .thenApply(o -> {
            if (o instanceof Optional<?>) {
                Optional<Currency> optionalCurrency = (Optional<Currency>) o;
                if (optionalCurrency.isPresent()) {
                    log.debug("Returning currency result: {}", o);
                    return asyncResponse.resume(Response.ok().entity(((Optional<Currency>) o).get()).build());
                } else {
                    throw new NotFoundException("No player/currency found");
                }
            } else {
                log.warn("Unexpected result: {}", o);
                throw new InternalServerErrorException("Unexpected result");
            }
        })
        .exceptionally(asyncResponse::resume);
}
```

where you ask for response from an actor, the result is a scala `Future` which is needed to be converted to a java `CompleteableFuture` to create responses back to callers asynchronously.

## Polyglot Approach

Finally, I want to show how you can use multiple languages in your project. You can take advantage of different programming languages on areas on your project where it makes a good fit. For my case, I used Spock that has nice syntax to better describe your tests. Spock is written in Groovy and uses language features to make it possible.

To use Groovy in your project just enable plugin:

```
plugins {
    id 'java'
    id 'groovy'

}
```

and for Spock bring these in to compile and test scopes:

```
dependencies {
    
    // Spock
    compile 'org.codehaus.groovy:groovy-all:2.4.10'
    testCompile 'org.spockframework:spock-core:1.1-groovy-2.4'
    testCompile 'org.spockframework:spock-spring:1.1-groovy-2.4'
    
}
```

Then you can write integration tests like: 

```
def ".../currency should return ok for an existing player info"() {
    given:
        def pid = 1L
        def url = "/walletserver/players/${pid}/account/currency"
        def isoCurrencyCode = "SEK"
        def playerInfo = AccountingService.PlayerInfo.builder()
        .pid(pid)
        .isoCurrencyCode(isoCurrencyCode)
        .build()
        1 * accountingService.getPlayerInfo(pid) >> Optional.of(playerInfo)

    when:
        def entity =
                restTemplate.getForEntity(url, Currency.class)

    then:
        entity.statusCode == HttpStatus.OK
        entity.body.pid == pid
        entity.body.isoCurrencyCode == isoCurrencyCode
}
```

In Spock, you can use interpolated strings as the test fixture names and the `given`, `when`, `then`
labels nicely express the intent how you want to test.

If you are interested more on the complete solution, source code can be found on [Github](https://github.com/yboyacigil/springboot-akka-walletserver)
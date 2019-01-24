---
title:  "Building microservices in NodeJS at full extent"
date:   2019-01-24 00:00:00 +0300
categories: english nodejs microservices
header:
  overlay_image: /assets/images/nodejs+microservices.jpg
  overlay_filter: 0.5
  caption: "Photo credit: Galina Pankratova"
---

In this post, I will share my experiences in building a microservice in NodeJS using a lovely backend framework [NestJs](https://docs.nestjs.com/) also including how to do configuration, logging, testing of the service, finally showing how it can be build as being installed as a system service.

I am a core Java/Scala backend developer and I would like to experiment new programming languages and frameworks where I see a fit to overcome issues that make the life easier compared to solutions that I am used to apply. Plus it gives you  better understanding of alternative ways to get things done.

One of the issues I dealt with recently was one of Spring/Java + Tomcat project taking a few minutes to startup that really needed to be fastened up. I took the chance to implement it in Node to see how fast it would be. (Happened to be a few secs!) It came with its own perils though where I had to implement some complementary Java services again. At the end, that was a nice experiment that worth trying.

Let's dive into the details. Below you fill find sections each explains how I implemented the solution.

## NestJS

If you want to develop a microservice in Node, the obvious path is to use plain vanilla Javascript with the great [ExpressJS](https://expressjs.com). I started with that. There are bunch of boilerplate templates to start a microservice project. And adding transpilers such as Babel you can use the new EcmaScript features in your project. After some experimeting, I came across with a nice framework that handles bunch of things, the most important: "Dependency Injection". That would be a good fit for people like me who worked with frameworks like Spring, Play for some long time. So I decided to make a switch to Nest. 

Here is an excerpt from NestJS introduction:

> Nest is a framework for building efficient, scalable Node.js server-side applications. It uses progressive JavaScript, is built with TypeScript (preserves compatibility with pure JavaScript) and combines elements of OOP (Object Oriented  Programming), FP (Functional Programming), and FRP (Functional Reactive Programming).

That is a good premise. The more I worked with it, the more I liked it. Unlike express, nest is an opionated one. It is written in TypeScript which brings type checking. It has dependency injection and gives you the option to clearly define components of your service. 

A typical Nest application has modules which basically provides and exposes classes, interfaces, services, controllers, etc. to itself or other modules. There's a nice `nest-cli` which eliminates boilerplate to create a project and components as you go.

I started to develop complementary Java services as Nest services. Those are mostly clients of other microservices such as accessing external configuration, geo ip location, session, etc. To be able to use other microservices in a non-blocking, reactive mode, nest comes with an HttpService which depends on Axios/RxJs.

Below is an example of how to implement a service (e.g. session service client):

```js
import { Injectable, HttpService } from '@nestjs/common';
import { PropertiesService } from '../properties/properties.service';
import { LoggerService } from '../logger/logger.service';
import { Observable } from 'rxjs';
import { AxiosResponse } from 'axios';

type Nullable<T> = T | null;

export interface Session {
  readonly sessionId: string;
  readonly playerId: number;
  readonly inactivityTimeout: number;
}

@Injectable()
export class SessionService {
  constructor(private readonly http: HttpService, private readonly properties: PropertiesService, private readonly logger: LoggerService) {}

  async get(sessionId: string): Promise<Nullable<Session>> {
    this.logger.debug(`Will get session by: ${sessionId}`);
    if (!sessionId) {
      Promise.reject(new Error('Invalid session id'));
    }

    const baseUri: string = await this.properties.get('SYSTEM/SESSION_SERVICE/ENDPOINT', () => '');
    if (!baseUri) {
      Promise.reject(new Error('No baseUri for session service'));
    }

    const response: Observable<AxiosResponse<Session>> = this.http.get(`${baseUri}/sessions/${sessionId}`);
    return response.toPromise()
      .then(axiosResponse => {
        if (axiosResponse.status === 200 && axiosResponse.data) {
          const session: Session = axiosResponse.data;
          this.logger.debug(`Session => ${JSON.stringify(session)}`);
          return session;
        }
        this.logger.debug(`Response status is not ok. Session => null`);
        return null;
      })
      .catch(reason => {
        this.logger.error(`Error in getting session. Session => null. (${reason})`);
        return null;
      });
  }
}
```

## Configuration

As for the configuration of the service, the npm package [dotenv](https://www.npmjs.com/package/dotenv) was what I chose. First it would allow you to have different sets of configurations for different environments (i.e. development, test, production) that can be controlled using env variable `NODE_ENV`. Upon that using another npm package [joi](https://www.npmjs.com/package/joi) you can validate configuration once you load it. Finally to go with Nest way, I created the service: `ConfigurationService` which I can inject it wherever needed.

Here is a configuration file for development:

```bash
NODE_ENV=development
PORT=3000
ETCD_HOST=localhost
LOG_PATH=./logs
LOG_FILE=application.log
LOGSTASH_ENABLED=false
GEOIP_HOST="geoip:8080"
```

and the code snippet from configuration service:

```js
export interface EnvConfig {
  [key: string]: string;
}

@Injectable()
export class ConfigService {
  private readonly envConfig: EnvConfig;

  constructor(filePath: string) {
    const config = dotenv.parse(fs.readFileSync(filePath));
    this.envConfig = this.validateInput(config);
  }

  // ...
}
```

and here is how you can validate your config file:

```js
  private validateInput(envConfig: EnvConfig): EnvConfig {
    const envVarsSchema: Joi.ObjectSchema = Joi.object({
      NODE_ENV: Joi.string()
        .valid(['development', 'production', 'test', 'provision'])
        .default('development'),
      PORT: Joi.number().default(3000),
      ETCD_HOST: Joi.string().required(),
      ETCD_PORT: Joi.number().default(4001),
      LOG_PATH: Joi.string().required(),
      LOG_FILE: Joi.string().required(),
      LOGSTASH_ENABLED: Joi.boolean().default(false),
      LOGSTASH_HOST: Joi.string(),
      LOGSTASH_PORT: Joi.number(),
      GEOIP_HOST: Joi.string(),
    });

    const { error, value: validatedEnvConfig } = Joi.validate(
      envConfig,
      envVarsSchema,
    );
    if (error) {
      throw new Error(`Config validation error: ${error.message}`);
    }
    return validatedEnvConfig;
  }
```

## Logging

For logging, the number one npm package is [winston](https://www.npmjs.com/package/winston), I started to use it as well. I had a couple of requirements for logging:
* I should be able to log incoming requests and outgoing responses with the payload,
* I should be able to send logs to our ELK,
* I should be able inject logging service into others where I need to log something.

I was able to handle all with winston. But on sending logs to ELK, using an npm package [winston-logstash](https://www.npmjs.com/package/winston-logstash) which provides a new `Transporter` that you can configure to which logstash instance logs should be shipped to.

Then, I came across [log4js](https://www.npmjs.com/package/log4js). As a loyal user of `log4j`, I jumped in to that and learning that with the addition of another package [logjs-logstash-tcp](https://www.npmjs.com/package/log4js-logstash-tcp) I implemented my LoggerService as:

```js
@Injectable()
export class LoggerService implements NestLoggerService {
  private readonly log4jsLogger: Logger;

  constructor(private readonly config: ConfigService) {
    this.log4jsLogger = getLogger();

    const log4jsConfig: Configuration = {
      appenders: {
        access: {
          type: 'dateFile',
          filename: `${this.config.getLogPath()}/access.log`,
          pattern: '-yyyy-MM-dd',
          category: 'http',
        },
        app: {
          type: 'file',
          filename: `${this.config.getLogPath()}/${this.config.getLogFile()}`,
          maxLogSize: 5242880,
          numBackups: 5,
        },
        errorFile: {
          type: 'file',
          filename: `${this.config.getLogPath()}/error.log`,
        },
        errors: {
          type: 'logLevelFilter',
          level: 'ERROR',
          appender: 'errorFile',
        },
      },
      categories: {
        default: { appenders: ['app', 'errors'], level: 'INFO'},
        http: {appenders: ['access'], level: 'INFO'},
      },
    };

    if (this.config.isLogstashEnabled()) {
      log4jsConfig.appenders.logstash = {
        type: 'log4js-logstash-tcp',
        category: 'default',
        host: this.config.getLogstashHost(),
        port: this.config.getLogstashPort(),
        fields: {
          source: 'co-game-loader',
          environment: os.hostname(),
          group: process.env.NODE_ENV,
        },
      };
      log4jsConfig.categories.default.appenders.push('logstash');
    }

    if (process.env.NODE_ENV !== 'production') {
      log4jsConfig.appenders.console = {
        type: 'console',
      };

      log4jsConfig.categories.default.appenders.push('console');
      log4jsConfig.categories.default.level = 'DEBUG';
    }

    configure(log4jsConfig);
  }
```

Here I need to mention a two things:

One, you can extend `NestLoggerService` to be able to write the logging service. By doing that Nest was able to be use it for its logging needs. You can add your logger on bootstrapping the Nest application like:

```js
async function bootstrap() {
  const app = await NestFactory.create(AppModule, { logger: false });
  app.useLogger(app.get(LoggerService));
  // ...
}
bootstrap();
```

Then, you can configure `log4js` in a .js file over which a typical `log4j` user prefers to do in a config file. Doing so gives you the flexibility to customize logging depending on envrionment variables or your service configuration. See for instance how to send logs to logstash as well conditionally:

```js
if (this.config.isLogstashEnabled()) {
  log4jsConfig.appenders.logstash = {
    type: 'log4js-logstash-tcp',
    category: 'default',
    host: this.config.getLogstashHost(),
    port: this.config.getLogstashPort(),
    fields: {
      source: 'co-game-loader',
      environment: os.hostname(),
      group: process.env.NODE_ENV,
    },
  };
  log4jsConfig.categories.default.appenders.push('logstash');
}
```

or how to enable console logging for environments other than `production`:

```js
if (process.env.NODE_ENV !== 'production') {
  log4jsConfig.appenders.console = {
    type: 'console',
  };

  log4jsConfig.categories.default.appenders.push('console');
  log4jsConfig.categories.default.level = 'DEBUG';
}
```

## Testing

As Nest comes with a very good testing framework [Jest](https://jestjs.io/), I did not search for more and just tried to stick with it. When you create a Nest component via `nest-cli`, there you have the initial code to write the tests for that component. With a little read and try-and-fail, I was able to write tests for all the services, classes, I created. It helped a lot especially finding errors I made during implementation. I will not go into the details of how to write tests using Jest. I just want to share an example here to give you the feel of a simple test:

```js
describe('SessionService', () => {
  let service: SessionService;
  let logger: LoggerService;
  let properties: PropertiesService;
  let http: HttpService;

  beforeAll(async () => {
    const module: TestingModule = await Test.createTestingModule({
      imports: [HttpModule, PropertiesModule, LoggerModule],
      providers: [SessionService],
    }).compile();
    http = module.get<HttpService>(HttpService);
    properties = module.get<PropertiesService>(PropertiesService);
    logger = module.get<LoggerService>(LoggerService);
    service = new SessionService(http, properties, logger);

    jest.spyOn(logger, 'debug').mockImplementation((message: any, context?: string) => console.log(message));
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });

  it('should return EMPTY when session service request responds with status code NOT OK', async (done) => {
    const mockGet = jest.spyOn(properties, 'get').mockImplementation((key: string, defaultSupplier: any) => Promise.resolve('http://session-service-host'));
    jest.spyOn(http, 'get').mockImplementation(() => of({status: 500}));

    await expect(service.get('session-id')).resolves.toEqual(null);

    expect(mockGet).toBeCalledWith('SYSTEM/SESSION_SERVICE/ENDPOINT', expect.any(Function));

    done();
  });

  it('should return SESSION when session service request responds with status OK and session', async (done) => {
    const mockGet = jest.spyOn(properties, 'get').mockImplementation((key: string, defaultSupplier: any) => Promise.resolve('http://session-service-host'));
    const expectedSession = {sessionId: 'sessionId', playerId: 1, inactivityTimeout: 10}
    jest.spyOn(http, 'get').mockImplementation(() => of({status: 200, data: expectedSession}));

    await expect(service.get('session-id')).resolves.toEqual(expectedSession);

    expect(mockGet).toBeCalledWith('SYSTEM/SESSION_SERVICE/ENDPOINT', expect.any(Function));

    done();
  });
});
```

Jest requires you write tests in BDD style. Before all, I had to create a test module and bring dependant modules into my test scope. Then I created mocks for defining test behaviours of dependant services.

## Debian Packaging

As I see it, typical deployment of a Node service is either docker or checking out the source from a repo and running it node (e.g. `node server.js`). In my case, those were not the options and since the original source code (in TypeScript) needs to be compiled into plain vanilla Javascript that Node can understand and I needed to create a deb(ian) package. Here another npm package [node-deb](https://www.npmjs.com/package/node-deb) helped. With `node-deb` you can create packages that can be installed as a system service on a target machine.

To be able use `node-deb` and control how the package will be created. First I needed to add instructions to `package.json`:

```json
"devDependencies": {
    "node-deb": "^0.10.7"
  },

 "node_deb": {
    "init": "none",
    "dependencies": "base-passwd, adduser (>= 3.11), nodejs",
    "entrypoints": {
      "cli": "dist/main.js"
    }
  }
```

where I basically defined the entry point and dependent debian packages and added `npm run` scripts like:

```json
  {
    "predeb:test": "rimraf dist && tsc",
    "deb:test": "./node_modules/node-deb/node-deb --no-default-package-dependencies --template-default-variables deb/env-test -- test.env dist/",
    "predeb:prod": "rimraf dist && tsc",
    "deb:prod": "./node_modules/node-deb/node-deb --no-default-package-dependencies --template-default-variables deb/env-production -- production.env dist/"
  },
```

to build the deb package for test and production targets. And to introduce environment variables (`NODE_ENV` in my case) to the service, I needed to tell node-deb the template. Here is the `production.env` template:

```bash
# This file was autogenerated by node-deb {{ node_deb_version }}
#
# Configuration for the {{ node_deb_package_name }} daemon
#
NODE_ENV="production"
```

With this setup, I was able to build the deb package even in Jenkins and to push it to an internal repo, where target server machines can pull and (re)deploy the service automatically with the help of puppet.

## Final words

I hope that I will help to the adventurers of Node by at least giving useful insights how to write a microservices in Node.
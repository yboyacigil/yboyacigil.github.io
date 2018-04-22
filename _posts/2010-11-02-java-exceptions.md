---
layout: post
title:  "Java Exception"
date:   2010-11-02 16:16:01 +0200
categories: turkish java
---
On küsür yıldır Java'da program yazan biri olarak exception'lar baş belası mı yoksa nimet mi anlayabilmiş değilim. Sanırım bunun basit, tek bir cevabı yok. Her an ikisinden birine dönüşebilir. :)

Exception'ı daha iyi algılamak/anlamlandırmak için İstisna ya da İstisnai Durum diye mi adlandırmalıyım? Bu ve bunun gibi ithal ettiğimiz (herşeyi ediyoruz ya!) kavramları bir kelime ile ifade etmek sanıldığı kadar kolay değil! Şundan eminim ki ortalama bir bilgisayar programcısının, exception dendiğinde kafasında üç aşağı beş yukarı şöyle bir algı oluşuyor: Bir metodu kodlarken, metodun normal akışını bozan bir durumdur exception. Mesela: bir dosyayı okumak istiyoruz ama öyle bir dosya yok. Bu tür durumları ifade etmek, oluştuğunda çaresine bakmak veya birilerinin bakmasını sağlamak için programlama dilleri bir takım araç veya yapılar tanımlamışlardır.

Yukarıda yaptığım tanımı yeterli kabul ederek, bundan sonra exception demek koşuluyla, Java'da exception'lar ile ilgili anlatmak istediklerime geçebilirim.

İlk olarak anlatacaklarıma temel oluşturması için Java penceresinden bakıldığında exception nasıl birşeydir, exception'larla nasıl çalışırız basit olarak açıklayayım:

Java'da `Throwable` ana sınıfı ile exception tanımlanır ve üç alt sınıf ile exception'lar farklı anlamlar kazanır: `Error`, `Exception`, `RuntimeException`.

## Error

Java Virtual Machine (JVM)'e havale edilmesi gereken ve her ne kadar mümkün olsa da program içerisinde müdahele etmenin gereksiz olduğu durumu ifade eder. Mesela: OutOfMemoryError program için ayrılan hafızanın tükendiğini gösterir. Hafıza olmadan ne yapılabilir ki? Error durumunda uygulamanın kapatılması gerekir.

## Exception

Çaresine program içerisinde bakılabilecek durumları ifade eder. Mesela bir işi yapmak için veritabanına bağlanmanız gerekiyor, ağ sorunu yüzünden bağlanamadığınız veya bağlandınız ama diyelim işlemi yaparken bir veritabanı tablosuna ait kısıtı bozdunuz. Sizin veya sizin yazdığınız metodu çağıran kişinin alabileceği bir takım aksiyonlar olabilir: bağlantı sorunu varsa tekrar denemek, bunu kullanıcıya raporlamak ya da kısıtı bozan durumu anlayıp, bozmayacak şekilde yeniden istekte bulunmak gibi.

## RuntimeException

Program çalışırken, erişilen bir değişkenin null olması, veya bir dizinin var olamayan bir indeks'teki elemanına erişmek gibi telafisi olmayan durumları ifade eder. Bunlar için çoğu zaman bir aksiyon almanız gerekmez. Programın böyle bir durumda işine devam etmemesi gerekebilir.

Programı kodlarken exception'ları fırlatırız (throw), bizim çağırdığımız başka bir metod fırlatır (throws), onları yakalarız ve çaresine bakarız (try-catch-finally). Bunları Java anlatan kitaplara bırakarak asıl anlatmak istediğim meseleye geçiyorum.

Error'ları JVM'e havale ettiğimizde, geriye: Exception ve RuntimeException kalır. Ve bu iki exception (ve tabiki bunların alt sınıfları) sırasıyla kontrollü (checked) ve kontrolsüz (unchecked) exception diye bilinirler. 

Kontrollü olması programı daha yazarken bunların yakalanması ve icabına bakılması veya tekrar fırlatılmasını gerektirir. 

Kontrolsüz exception'ları yakalamak zorunluluğu yoktur, ama oluştuğunda çaresine de bakılması gerekebilir. 

Yani birinde herşeyi kontrol etme ve bunun getirdiği yük, diğerinde hiçbir şey kontrol etmeme lüksü öte yandan oluşmaması için bir çaba harcanması gerekliliği var. Şöyle de bir durum var ki eğer kimse yakalamazsa JVM bunu yakalayıp, programı sonlandırabilir.

## Kontrollü mü kontrolsüz mü olsun?

Kodlarken ne zaman kontrollü ne zaman kontrolsüz exception kullanmamız gerekir? Kontrollü ve kontrolsüz kullanmanın bir ayarı var mı ve bu ayar kaçarsa ne olur? Kodlarken bu ikilemden nasıl kaçarız? 

Bu konuya kafa yoranlar veya bundan dolayı ağzı yananlar, bazı kurallar koyarak bunun üstesinden gelebiliriz demişler. Yani:

- Kontrollü exception'ları, metodu kodlarken, çağıran kişi oluştuğunda çaresine bakabileceğine inanıyorsanız kullanabilirsiniz.
- Gereksiz yere kontrollü exception kullanmaktan kaçınınız.
- Kontrolsüz exception'ları metodun olmazsa olmaz ön koşullarını kontrol etmek için kullanırsanız iyi olur.

gibi.

Son zamanlarda kontrollü exception'ları kullanmama, yerine kontrolsüz exception'larla gitme yönünde bir eğilim var. Örnegin Spring, Hibernate geliştiricileri kontrollü exception kullanmaktan vazgeçmiş durumdalar. C#'da kontrollü exception diye birşey yok. Bunun sebepleri var elbette:

- Kontrollü exception'lar yakalanmak veya fırlatılmak zorunda olduğu için ya kodun okunmasını güçleştiriyor (bir yada daha çok try-catch blokları ile) ya da metod tanımında bu exception'ların tek tek yazılması gerekiyor. Metodu çağıran başka metodlar ya bunları yakalamak ya da tekrar fırlatmak durumunda kalıyorlar.
- Özellikle son programlarda kullanılmak üzere hazırlanan ara ürünler (Framework, API gibi) kontrollü exception'lar yüzünden sonraki versiyonlarda sıkıntı yaşayabiliyorlar. Nasıl mı? Mesela ilk sürümde foo diye A, B, C exception'larını atan bir metod tanımlı, ikinci sürümde foo metoduna yeni bir özellik eklediniz ve bu da D diye bir exception daha eklemenizi gerektirdi metodun tanımına. Bu durumda ilk sürümü kullanan kişi ikinci sürüme direkt olarak geçemiyor. Çünkü D exception'ını da yakalaması gerekir.

Şunu da not etmemiz gerekir ki sadece biri veya ötekiyle gitmek zorunluluğu yok. İhtiyacınıza göre her ikisini kullanmanız gerekebilir. Sadece kullanırken burada bahsi geçen konuları göz önüne alıp bir karar verebilirsiniz.

Ben, exception'larla çalışırken, bu tür ikilemlerle çok karşılaşıyorum ve genellikle şu şekilde üstesinden geliyorum:

- Zorunda kalmadıkça kontrollü exception kullanmamak, kontrolsüz exception'larla gidebildiğim kadar gitmek.
- Var olan kontrolsüz exception'ları (IllegalArgumentException, IllegalStateException, NullPointerException, vs) kullanmak ve gerekmedikçe kendi exception sınıfımı uydurmaktan kaçınmak.
- Eğer bir exception sınıfı tanımladıysam, onu yakalayana çaresine bakması için gerekli tüm bilgileri aktarmak ve ekstra yardımcı olabilecek metodlarla exception sınıfını zenginleştirmek.
- Exception'ların mümkün mertebe en üst katmanda yakalanması için uğraşmak, daha alt katmanlarda gerekmedikçe bir exception'ı yakalamamak.
- Programın veya program thread'lerinin yakalanmamış bir exception yüzünden kapanmaması için gerekli aksiyonları almak.
- Kontrolsüz exception'ları javadoc ile yanlış anlaşılmaya izin vermeyecek şekilde açıklamak.

Bu konuyla alakalı başka (ingilizce) kaynaklar: 
1. [The Trouble with Checked Exceptions](http://www.artima.com/intv/handcuffs.html)
2. [Checked or Unchecked Exceptions?](http://tutorials.jenkov.com/java-exception-handling/checked-or-unchecked-exceptions.html) 
3. [Checked versus unchecked exceptions](http://www.javapractices.com/topic/TopicAction.do?Id=129)
4. Effective Java Jashua Bloch - Chapter 8. Item: 40/41
---
layout: post
title:  "Code Review"
date:   2010-12-22 16:16:01 +0200
categories: turkish
---
Code review'ı üç başlık altında yapabiliriz:

1. Design
2. Maintenance
3. Performance

O zaman herbir başlık altında code review yaparken nelere dikkat etmeliyiz, bunlara tek tek değinelim.

## Design: Kodu tasarımı açısından incelemek.

İyi tasarım kriteri nedir biraz tartışılır. İyi tasarım nedir diye sorulduğunda herkes bir şey söyleyebilir. Mesela pure object-oriented kod yazınca iyi tasarım yapmış olmayız. Birçok yerde encapsulation yok diye tasarımın başarısız olduğuna hükmedilemez. Tasarım başka şey. Eğer ürettiğiniz şey kendisinden bekleneni hakkıyla yerine getiriyorsa tasarım iyidir diyebiliriz. Tek bir şartla: Kendisinden beklenen ve onu karşıladığı nesnel olarak belirlenmiş olmalı. Bu kesinlikle şu anlama da gelmemeli: Benim yazdığım bölüm çalışıyor işte o yüzden tasarım tamamdır denizi bulandırmayayım kerizi uyandırmayayım! Tasarım açısından sorun olmayabilir ama maintenace'ı kötüdür. Mesela metoda öyle bir isim verilmiştir ki anlayana aşk olsundur.

Asgari müştereklerimiz olmalıdır ki bunlar:

### Granularity/Low Coupling/High Cohesion

Bir sınıf sadece bir işi yapmalı ve onu iyi yapmalı. Bunu ancak problemi iyi anlarsak yapabiliriz.

- Bir sınıf yazıp içini statik metotlarla doldurup her yerde onu çağırmak. İlgili unitlerin içerisinde tanımla.
- Zero-abstraction: Ortak özellik ve davranışı her sınıfta tekrarlamak. Bir üst sınıf yarat ortak özellikleri orada tanımla.
- Magic class: Herşeyi tek bir sınıfta yapmak. Bu sınıfı öyle böl ki granül olsun, kendi içerisinde tutarlı, dışarısı ile bağları mümkün mertebe az olsun.
- API Business Logic (BL) Interference Hell: API ve business logic'in net sınırlarla birbirinden ayrıştırılamaması. Biz sadece API kullanmıyoruz, yazıyoruz da. Her işin bir API bir de BL tarafı vardır. İkisini birbirinden ayırıp ona göre kodlamalı.
- Irrelevant Packaging: Gereksiz, saçma paket açmak. Tek sınıflık paketler açmak. Paketlerin ortak iş yapan sınıflarca doldurulması gerekir. İsmi ile müsemma olması gerekir paketlerin. Alakasız bir sınıfın alakasız paketler içerisinde olmaması gerekir.

### Flow Control

Metod'u implement ederken kod akışının akllıca kontrol edilmesi gerekir

- Zero-exception: Hiç exception fırlatmamak tüm exceptionları catch etme log basmak daha kötüsü hiçbir şey yapmamak. Exception'lar istisnai durumları ifade eder ve bu durumlarıda normal akışın kesilip bir fault tolerans yapılması gerekir. Exception catch edildiğinde log basmak sadece durumun oluştuğunu ifade eder ve onu çözmek için hiçbir şey yapmamışsınız demektir. Yerine API seviyesinde exception fırlatmak ve BL seviyesinde handle etmek gerekir.
- Interruption'lara kayıtsız kalmak. Thread içerisinde beklerken InterruptedException'ı susturmak yerine Thread'in stop etmesini sağlayın.
- Infinite Loops Loop kontrol değişkenlerini kontrol edin.
- If Without Else Tüm If statmentlarının else case'lerine dikkat etmeli. Else'siz if olmaz!
- Ambigious If statements If statement condition'ları içerisinden metod call olmamalı ve çok karışık and ve or içermemeli. Metod call yapıldığında exception yersek ne if ne else koluna düşeriz!

## Maintenance: Bakım açısından kodu incelemek.

Maintenance ve Performans arasındaki denge çok önemlidir. Ne performance için maintenance'dan vazgeçilir ne de tam tersi. Hatta maintenance'ı göz ardı etmek çok pahalıya mal olabilir. Performance problemini mesela daha iyi donanım kullanarak bile çözebilecekken maintenance ile alakalı bir bug'ı çözmek bir kaç aylık iterasyonlara mal olabilir.

Maintenance ile alakalı incelemeyi Code Maintenance ve Operations Maintenance olarak ikiye bölebiliriz.

### Code Maintenance

- Bad Naming: İsmi ile müsemma olmayan değişken/metot isimleri. Mesela: justDoIt() iyi de neyi just do edeyim! İsimleri düzeltin. Eğer isim bulamıyorsanız: 1.Kelime haznenizi genişletin. 2.Metod çok iş yapıyordur parçalara bölün. 3.Neden böyle bir isim koydunuz açıklayın.
- Name pollution: Aynı şeyi farklı isimlerle tanımlama. Bir şeyin tüm projede tek ismi olsun. Result, response, answer gibi aynı şeyi ifade eden 3 tane isim kullanmak yerine bir tanesini seçin. Tüm projede derken DB tablo, kolon isimleri ve konfigurasyon parametreleri, proje dokümanını kast ediyorum.
- Comment'ler Sınıf, değişken ve metod tanımlamalarında yazan asgari javadoc commentleri girilmelidir. getName(), setName() gibi accessor metodları için veya isminden ne yaptığı herkesin anlayabileceği sınıf, değişken ve metotlar için yazılmasa da olur. Ek olarak kod içerisinde kodun niçin öyle yazıldığına dair comment'ler olmalıdır. Burada ne yapıldığından çok neden öyle yapıldığının anlatılması gerektiğini tekrar vurgulamak isterim.
- Method Extraction Çok uzun olan ve ayrılabilecek kodun tek metot yerine bir çok metoda ayrıştırılması gerekebilir.
- Exception Handling Exception'ı catch edip log basıp exception'ı rethrow etmemek lazım. Ya log bas ya throw et.
Rename, method extraction, signature change vs. operasyonları için Eclipse/Refactor menusune müracaat ediniz. Kısa yolları ezberlemekte de fayda var. `Alt+Shift+R: Rename` gibi. Refactor ederken bir acımasız olmak iki korkmamak gerektiğini de akıldan çıkarmamalı.

### Operations Maintenance

- Yeterli ve tam seviyesinde log basmamak. Yeterli derken nerelerde ve neyi loga basmak gerektiği konusunda bir konsensus sağlamayı kastediyorum. Bu şirket/grup/proje bazında şekillendirilebilir. Tam seviyesinden kastım ne zaman debug ne zaman info ne zaman warning ne zaman error basılacağının belirlenmesidir tosun. Bu biraz his biraz da tecrübe ile tamamlanabilir. Destek grubu bu konuda öneride bulunabilir.
- CLI eksikliklerinin giderilmesi. CLI'den administration/operation yapılabilmesi tüm komutları sağlamamız gerekir. Bunları neler olduğu project spesifik olmakla beraber bir common set çıkarılması gerekir. CLI eksikliklerinin review ederken çıkarılması iyi olur.
- Configuration eksikliklerinin giderilmesi On the fly konfig değişiklikleri yapılabiliyor mu ...? Hangi konfigurasyon params online hangileri offline olmalıdır belirlenmeli ve bunlar review ederken valide edilmelidir.

## Performance: Performability açısından kodu incelemek.

Performance deyince nedense aklıma optimizasyon ve şu kurallar gelir:

1. The First Rule of Program Optimization: Don't do it.
2. The Second Rule of Program Optimization (for experts only): Don't do it yet.

- Unnecessary Object Creation Object creation'ın olması gerektiğinden fazla olmadığı doğrulanmalıdır. Döngü içerisinde create edilen objeleri dışarda bir kere create etmek mümkün mü? Pooling yapılsa daha iyi mi olur? vs. sorgulanmalıdır.
- Bad Threading Thread'lerin CPU kullanımını tavan yaptırmasına neden olacak etmenlerin araştırılması gerekir. Sleep yapılıyor mu? Kısır döngüye neden olacak birşey var mı? vs. araştırılmalıdır.
- Too many String operation Java String operasyonları pahalı olduğu için StringBuffer, StringBuilder class'larını kullanmak gerekebilir.

Eminim bu listeye eklenecek maddeler olabilir. Bunları yorum olarak ekleyip katılmanız dileğiyle bitiriyorum.
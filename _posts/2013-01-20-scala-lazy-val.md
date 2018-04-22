---
layout: post
title:  "Scala 'lazy val'"
date:   2013-01-20 16:16:01 +0200
categories: turkish scala
---
Scala'da değişken/argüman tanımlarken iki tane anahtar kelime var: `var` ve `val`. İlki için söylecek çok bir şey yok. Değişken tanımlamak için var'ı kullanmak durumundasın. Ama ikincisi aynı Java'daki `final` gibi sadece ilk seferinde bir değer atanmasını istediklerini tanımlamaya yarıyor. İkinci defa bir atama yapamıyorsun; aslında program içerisinde böyle sadece bir kere atama yapmaktan ziyade `val` ile tanımladıkların daha çok sonradan değerinin değiştirilmesini istemediğin değişkenler içindir.

{% gist 5989762 %}

`var` ve `val`'ı sadece değişkenleri tanımlarken değil aynı zamanda fonkisyon ve sınıf yapılandırıcıları (constructor) parametrelerini tanımlarken de kullanabilirsin. Bu durumda `var` ile tanımladığın parametreler fonksiyon ve yapılandırıcı içerisinde değiştirilebilirken, `val` ile tanımladıklarının sadece değerini okuyabilirsin.

Bunların yanında bir `lazy val` diye bir anahtar kelime daha var. Lazy isminden anlaşılabileceği veya programlamaya aşina olanlar için belli şimdi değil sonrayı çağrıştırıyor. Değişkenin değerini şimdi değil de sonra ata demek. Sonra ne zaman? İlk defa o değişkene kod çalışırken erişildiği zaman.

{% gist 5989774 %}

Yukarıda `y` değişkeni `lazy val` olarak tanımladık ve erişene kadar değer atamadığını görebiliyoruz. Yukarıdaki örneklerde atama yaparken `{ println("..."); ... }` dikkatinizi çekmiştir. Scala'da kod blokları `{...}` arasında tanımlanır ve atama yaparken de bir kod bloğu kullanabilirsiniz.

`val` diye tanımladığımız değişkenler kod çalışırken en az bir kez yazılacak ve belki bir kere, on kere, yüz kere, bin kere okunacak belki de hiç okunmayacak(!). Madem okunmaya da bilir o zaman okunmayacak birşey okunana kadar atamazsak hafızayı daha az kullanmış oluruz. Onun için değişkeni `lazy val` ile tanımlayabiliriz. Bu yöntem hafıza kullanımı açısından faydalı olsa da cpu'yu normal bir atama gibi değil de daha fazla meşgul ediyor. Nedeni her erişimde en azından bir kez atama yapıldı mı yapılmadı mı diye kontrol etmek gerekiyor bir de ufak bir flag tutmak gerekir atandığını işaretlemek için. Bir de birden fazla thread bu değişkene erişebileceği için bu erişim kontrollü yapılması yani herhangi bir t anında sadece ve sadece bir thread'in erişmesi için gerekli senkronizasyonun işletilmesi gerekir.

Lazy'nin kullanılması gereken ilginç bir örnek vereyim:

{% gist 5989772 %}

`B`'yi ilk kullanmaya çalıştığımızda NPE'yi yeriz. ;) Ama `B`'yi şu şekilde tanımlarsak sorun olmaz:

{% gist 5989774 %}

Bunun nedeni `A` sınıfındaki `x`'i `B` objesinde `lazy` olarak değiştirdik. Artık `x`'e ilk erişildiğinde değeri atanacak. İlk erişildiği nokta ise `A` sınıfının başlangıcı (initialization'ı). İlk örnekte,`x` daha tanımlanmadığı için yani `B` objesi hafızada daha oluşturulmadan `x`'e atama yapılmayacağı için null olarak kalacağından `NPE` oluşur.

Son olarak lazy'nin bir de döngüsel (cyclic) bağımlılıklarda nasıl işe yaradığını görelim ve macerayı tamamlayalım. Aşağıda birbirine bağımlı sınıf tanımları var:

{% gist 5989780 %}

`B` ve `C` sınıfları birbirine bağımlı. Bunların başlangıc kod bloklarında ki `val x = ...` kısmını `lazy val x = ...` şekline dönüştürmezsek `StackOverflowError` alırız. Lazy'yi bu örnekte olduğu gibi döngüsel bağımlılıklardan kaynaklanan durumları önlemek için de kullanabiliriz.








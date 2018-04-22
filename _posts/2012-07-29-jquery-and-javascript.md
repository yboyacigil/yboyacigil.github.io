---
title:  "JQuery ve Javascript"
date:   2012-07-29 16:16:01 +0200
categories: turkish javascript
---
Web 2.0 web uygulamaları için yeni bir dönem başlattı. Web sayfalarının daha interaktif olmasını ve web tarayıcısı tarafında javascript'in daha çok kullanılmasını sağladı. Böylece web uygulamaları daha çok masaüstü uygulamalarına benzemeye başladı. İşte bu değişim neredeyse web'in başlangıcından beri var olan javascript'i daha önemli kıldı ve harika javascript kütüphanelerinin oluşturulmasını sağladı. JQuery bu kütüphanelerden biri ve sanırım en çok kullanılanı.

Günümüzde hemen hemen tüm web sayfalarında kullanılan [JQuery](http://jquery.com/) javascript kütüphanesi HTML dokümanındaki ögelere erişmek, bu ögelerle olan etkileşimi programlamak (event handling), animasyon, ajax ile interaktif veri alış-verişi sağlamayı hızlı ve kolay bir şekilde yapmanızı mümkün kılıyor.

JQuery'nin yetenekleri, ne yaptığı ayrı bir yazı konusudur ve hem [ingilizce](http://docs.jquery.com/Main_Page) hem de [türkçe](http://docs.jquery.com/Tutorials#T.C3.BCrk.C3.A7e_e.C4.9Fitseller) güzel dokümante edilmiştir. Ben bu yazıda JQuery'nin kodunu ve JQuery'de yazılmış plugin'leri incelerken karşılaştığım bana "Nasıl ya?!!" dedirten sonradan araştırıp bulduğum birkaç konuyu başkaları da benim gibi kasmasın diye anlatmak istiyorum.

Öncelikle şu meşhur `$` dan bahsedelim. `$` veya `jQuery` JQuery script'i yüklendikten sonra kendi yazacağınız script kodundan erişilebilir; global değişkendir.

{% gist 5990044 %}

Yukarıdaki örnekte `$(a)` yerine `jQuery(a)` da yazabiliriz. Bu ifadelerden herhangi birisini yazdığımızda bir JQuery objesi yaratmış oluruz ve JQuery'nin sağladığı tüm fonksiyonlara bu obje üzerinden erişebiliriz.

JQuery aynı zamanda genişletilebilir bir yapıya sahip. Yani siz yeni eklentiler (plugin) yazabilirsiniz. JQuery'nin eklenti geliştirme ile ilgili dokümanlarında şöyle ilginç bir ifade görmüştüm:

{% gist 5990053 %}

Buradaki `fn` in ne demek olduğu kolay bir şekilde anlaşılmıyor. Biraz araştırdıktan sonra javascript'in tüm nesneleri anında değiştirmek için var olan prototype özelliğine bir kısayol olarak jQuery objesinde tanımlanmış bir değişken olduğu gördüm.

Bir tane daha ilginç örnek verip bitirmek istiyorum. Aşağıdaki javascript koduna bir bakalım:

{% gist 5990064 %}

Burada bir javascript fonksiyonu tanımı var onu anlayabiliyoruz ama hemen arkasındaki parantez içersindeki jQuery ifadesi de ne demek oluyor? Bunun da detayda javascript'te var olan bir özellikle alakalı olduğu hatta bunun isminin "immediate/interactive code execution" olduğunu gördüm. Yani fonksiyon tanımını yapıp hemen çalıştırma işi. Yerine:

{% gist 5990070 %}

de yazabilirsiniz. Fakat burada myFunction isimli özel bir fonksiyon yaratıyorsunuz. Öncesindeki örnekte yaratılan fonksiyonun ismini umurunuzda değil hatta ilk çağırmadan sonra ona bir daha erişmek gibi bir niyetiniz de yok.

Ayrıca JQuery kullanan script'lerin sayfa hazır olduğunda uygulamanızın kodunu çalıştırmak için kullanılan klasik başlangıç scripti yerine kısayol olarak kullanılabilir. Yani bunun yerine:

{% gist 5990072 %}

JQuery kodunu ve yazılan eklentileri inceledikçe javascript ile ilgili daha derinlemesine bilgi sahibi olabilirsiniz. Hatta bunu açık kaynak kodlu tüm kütüphanelere uygulamanızı tavsiye ederim.






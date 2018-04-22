---
title:  "Scala Programlama Dili"
date:   2013-01-05 16:16:01 +0200
categories: turkish scala
---
Zamanında bir kitap okumuştum. İsmi: [Pragmatic Programmer](http://pragprog.com/the-pragmatic-programmer). Kapağı da ilginçti. Marangozluktan biraz anlayan bilir kapağında bir rende resmi var. Benim babam marangozdu ben de onun yanında zamanında çıraklık yaptım. Bu resim kanımca programlama bir zanaattır mantığıyla konmuştur ve hakikaten de öyledir. Sanattır diyeni de vardır ama bana göre sanattan çok zanaattır o başka. Herkes babasının oğludur. Ben de bir nevi marangoz gibi zanaatkar insanım, öyle görüyorum kendimi. O bakımdan kitabın kapağıyla da o taraflara yapığı gönderme ayrıyeten anlamlıdır benim için.

Bu kitapta şöyle bir ögüt vardı: "Her sene yeni bir programlama dili öğren!". Nitekim o öğütü tuttum ve o günden sonra yeni dilleri öğrenmeye çalıştım. Python, erlang, javascript, ruby, smalltalk dillerini çalıştım. Öğrendiğim kadar da unutmuşluğum vardır. Ama yeni bir dile çalışmaya başlayınca programlamaya bakışın, problemlere çözümde yaklaşımın gelişiyor. Aradaki farkları, benzerlikleri görüyorsun; daha yukarıdan bakabiliyorsun. Bu dillerin bir de kendine ait felsefeleri var; anlamaya çalışıyorsun oradan yeni şeyler kapıyorsun. Kesinlikle tavsiye ediyorum herkese. Şöyle bir geçmişe dönüp bakınca bak .net'e (döt net) neden ilgi duymamışım diye de kendime soruyorum şimdi?!! Microsoft'a zamanında gıcıklığımızdan (bugün bunun çok da anlamlı olmadığını anlıyorum) belki uzak kaldım micro$oft'un sunduklarına.

Yeni bir programlama dili öğrenmek kolaydır aslında. İki haftada alışır birşeyler yapamaya muktedir olursun. Ama o dile tam anlamıyla hakim olmak bir seneni alır. O yüzden yavaş yavaş bıkmadan usanmadan; acele etmeden ağır ağır gideceksin. Scala çalışıyorum. Bu seneki dilim bu.

Scala hibrit bir dil. Hibrit? Yani hem fonksiyonel hem de nesneye dönelik yani yönelik :) Scala'nın  daha ilginç yanı kendi derleyicisi var ama Java byte-code üretiyor. Yani JVM üzerinde çalışıyor. Java standart kütüphanesindeki sınıfları ve bilinen tüm Java kütüphanelerini direkt olarak kullanabiliyorsun. Yanında kendi kütüphanesi de var. Kendi kütüphanesi olması gerektiği gibi Java kütüphanesini tamamlıyor onu tekrarlamıyor.


Bugün linkedin, twitter ve foursquare gibi startup'ların kendi geliştirmeleri için kullanıyor olmasıyla  popüler de oldu. Bilinen programlama dilleri listesinde de çok kullanılmasına göre 34. sıraya kadar da yükselmiş ve yine JVM üzerinde geliştirilmiş kendinden eski başka dilleri (mesela: Clojure, Groovy) de geçmiş durumda. Bu açıdan bakıldığında da kötü bir seçim değil yani.

Son olarak Scala ile ilgili olarak fonksiyonel programalama dili olması ve Java'nın sevilmeyen açıklarını kapaması gibi iki konuya değinmek istiyorum.

## Fonksiyonel Programlama

Fonksiyonel programlama aslında uzun zamandır var olan bir programlama stili ve son yıllarda daha çok konuşulmaya başladı. Matematikteki fonksiyon mantığının programlamaya uyarlanmasıdır aslında. Bu programlamanın doğasına da tam oturur. Program dediğin netice itibariyle bir girdiyi alıp onu işleyip bir çıktı üreten şeydir yani bir fonksiyondur. Bir programın tüm parçalarını bu şekilde ifade edebilirsin. Program dediğin bir fonksiyonlar bütünüdür. Scala ile pür fonksiyonel programlama yapmak mümkündür.

Fonksiyonel programlama deyince tabi literatürde geçen "high order functions", "no state preservation", "clojure functions" gibi konulara da ileride değiniriz.

Scala aynı zamanda nesneye yönelik programlama için de gerekli olan yapıları barındırır. Bu onu hibrit yapan özelliğidir. Java gibi nesnel programlamaya aşina birine de Scala bu özelliğiyle kapıyı açık tutsa da kanımca, Scala'nın mucitleri o kafadakileri de fonksiyonel programlamaya devşirmek istemiş olabilirler.

## Java'daki Eksiklikler
Java'nın eksiklerini tamamlaması ile ilgili olarak da Java'da çokça şikayet edilen primitive tipleri ortadan kaldırmasıyla dikkate şayandır. Bu şunun için önemlidir: Scala'da herşey nesnedir yani primitive tip yoktur. Bu da primitive'i nesneye, nesneyi primitive dönüştürmek gibi ameli işleri ortadan kaldırdığı gibi aynı zamanda temizlik anlamında da hoştur.

Bir başka konu "operator overloading" diye bilinen C++'çıların Java'da en çok istediği ve belki bu yüzden de çokça eleştirdiği Java'da olmayan bu özelliği eklemiş olmalarıdır. Yani siz herhangi yarattığınız bir tip (sınıf/class) için mesela "+" operatörünü tanımlayabilirsiniz. Yalnız single-inheritance hala var ama o da aslında olmasının bir programlama diline hayır getirdiği oldukça tartışmalı bir konu zaten.

Java'da sıklıkla kullanılan anonymous class'ları da fonksiyonlara havale ederek daha az kod yazarak aynı işi yapabilmesi de bir anlamda sentaks olarak bir temizlik, kolaylık getiriyor.



Scala ile ilgili maceraları daha sonraki yazılara havale ederek burada bitiriyorum. Yeni yılın ilk yazısı olması itibariyle de okuyan herkese iyi seneler dilerim. :)
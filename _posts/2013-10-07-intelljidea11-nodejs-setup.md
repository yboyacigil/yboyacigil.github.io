---
layout: post
title:  "IntellijIdea 11 ile NodeJS Kullanımı"
date:   2013-10-13 16:16:01 +0200
categories: turkish nodejs
---
Javascript ile geliştirme yapanlar için faydalı olacağını düşündüğüm bir çalışma ortamını sizlerle paylaşmak istiyorum.

Hazırladığım ortam IntelliJ IDEA 11 + [NodeJS Plugin](http://plugins.jetbrains.com/plugin/?id=6098) ve [NodeJS](http://nodejs.org) kurulularından oluşuyor.

IDEA javascript geliştirmek için piyasada olan en başarılı editor'lerden birini sağlıyor. Sadece kodu renklendirme, tamamlama değil bunun yanında JSHint'i de devreye alıp, javascript hatalarını ve doğru yazım kuralları konusunda da sizi uyarıyor.

NodeJS ile konunun ilintisi ise şuradan kaynaklanıyor: Javascript ile kodu yazarken herhangi bir kod parçasını   anında çalıştırıp sonucunu görebilmeniz için önemli bir araç. Bunu IDE içerisinden çıkmadan yapabilmek size çok hız kazandıracağı için NodeJS'i IDEA içerisinden çalıştırabilmeniz gerekir. Onun için de Node JS Plugin'ini kurmalısınız.

`Path To Node` sizin makinenizdeki Node JS'in yerini ve Working Directory üzerinde çalıştığınız javascript dosyalarının olduğu yeri göstermeli. `Path to Node App JS File`'ı girmediğiniz için size kızabilir ama dikkate almayın bunu. :)

Bundan sonra IDEA'da hazırladığımız Run/Debug Confguration'u olan  NodeJS Interactive Shell'i (yukarıda bu ismi vermiştik) çalıştırıp, Run penceresinde denemelerinizi yapabilirsiniz.

Burada bahsetmem gereken bir konu daha var. Eğer hazırladığınız kodu bir NodeJS modülü haline getirirseniz tüm kodu Node'a yükleyebilir üzerinde oynayabilirsiniz. Node modülü haline getirmek için yapmanız gereken export'ları tanımlamak olacaktır. Buna ilaveten aşağıdaki gibi reload fonksiyonunu da eklerseniz kodu değiştirdikten sonra yeniden yükleyebilecek duruma da geleceksiniz.

{% gist 5965893 %}

Umarım yukarıda hızlıca bahsettiğim konfigurasyon başkalarının da işine yarar.


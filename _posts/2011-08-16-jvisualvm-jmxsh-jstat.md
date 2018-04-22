---
layout: post
title:  "jvisualvm, jmxsh, jstat (potpuri*)"
date:   2011-08-16 16:16:01 +0200
categories: turkish java
---
[potpuri](http://www.eksisozluk.com/show.asp?t=potpuri), potbori veya potburi: bileşim, harman, karışım. İngilizcesi [potpourri](http://tureng.com/search/potpourri)

Bu yazıda bi potpuri patlatacağım. Her ne kadar kendisinin anlamlarından biri: Birbirinden epey farklı şeylerden oluşan karışım da olsa, başlıkta geçen şeylerin hepsi “Java Monitoring” ile alakası olması bakımından aynı şeyle ilgili. Konu aslında DEVEloper’lardan ziyade SUPport’çuları ilgilendiriyor olsa da bir javacının bunları bilmesi iyi bir şeydir netekim.

## jvisualvm

`jvisualvm` ile “local” veya “remote” jvm’e bağlanmak için illaki `-Dcom.sun.management.jmxremote*` system property’lerini uygulamayı başlatan java komutuna geçmek gerekir mi gerekirse hangi durumlarda gerekir buna dair: Visual VM Java 6 (jdk 1.6.x) üzerinde çalışan bir uygulamanın JMX Agent’ını otomatik olarak bulur. (Visual VM çalışan java uygulamalarını jps tool’u ile bulur. jps $JAVA_HOME/bin altında) Daha eski Java versiyonları için yukarıda bahsi geçen system property’lerini girmek gerekir. Ama otomatik bulmanın da istisnaları var:

* Eğer uygulamayı çalıştıran kullanıcı ile jvisualvm’i çalıştıran kullanıcılar farklı ise,
* Eğer uygulama uzaktaki makinede çalışıyorsa, jstatd çalışmıyorsa veya çalışsa bile uygulama ile farklı bir kullanıcı ile başlatılmışsa,

yine bahsi geçen system property’leri gereklidir.

## jmxsh

`jmxsh` Apache 2.0 lisansı ile dağıtılan, code google’da open source, JMX agent’a komut satırından bağlanmak için bir arabirim. JConsole, JVisualVM gibi grafik ortam gerektirmediği için uzaktaki makineye bağlanıp hemen kullanılabilecek bir araç. Ayrıca script desteği de var. Bu ne demek diyenler için: Bir (tcl) script yazarak, mesela herhangi managed bean’in attribute’ları okunabilir/değiştirilebilir veya operasyonları çalıştırılabilir. Operasyonel açıdan mesela belli attribute’lar kontrol edilerek çalışan java uygulamasını sanity kontrolünü yapmak için bir script belirli aralıkla çalıştırılabilir, vs.

## jstat



Java SDK ile gelen tool’lardan biridir kendisi prstat gibi, çalışan jvm ile ilgili bir kısım istatistiki bilgileri belli aralıklarla almak için kullanılır. Kullanımı çok zariftir. Hatta basitçe şöyledir:


```
jstat -<option> <vmid> [<interval>][<count>] 
```

`option` şunlardan biridir:


```
-class
-compiler
-gc
-gccapacity
-gccause
-gcnew
-gcnewcapacity
-gcold
-gcoldcapacity
-gcpermcapacity
-gcutil
-printcompilation
```

`vmid` process id yani pid’dir.

`interval` N sn veya mili sn mesela 1s ve 100ms gibi.

`count` da haliyle 0′dan büyük bir sayıdır.

Nasıl mı? Şöyle:

```
> jstat -gccause 16642 1s

S0 S1 E O P YGC YGCT FGC FGCT GCT LGCC GCC

42.78 0.00 50.07 15.08 30.53 44 4.605 0 0.000 4.605 unknown GCCause No GC
42.78 0.00 50.07 15.08 30.53 44 4.605 0 0.000 4.605 unknown GCCause No GC
42.78 0.00 50.07 15.08 30.53 44 4.605 0 0.000 4.605 unknown GCCause No GC
````

adı üstünde GC varsa nedeni ile birlikte basan bir komut. Survivor, Eden, Old space’lerin doluluk oranlarını, Young ve Old generation GC sayı ve zamanını basıyor. (pid: 16642 interval: 1s)

```
> jstat -gcnew 16642 1s

S0C S1C S0U S1U TT MTT DSS EC EU YGC YGCT

3840.0 3968.0 0.0 2048.0 1 15 3840.0 118912.0 3643.6 61 46.500
3840.0 3968.0 0.0 2048.0 1 15 3840.0 118912.0 3643.6 61 46.500
3840.0 3968.0 0.0 2048.0 1 15 3840.0 118912.0 3643.6 61 46.500
```

Young generation Survivor space’lerinin ve Eden space’in kapasitesi ve ne kadarının kullanıldığı, Young generation GC sayı ve zamanını ve tenuring threshold, max tenuring threshold değerlerini basıyor. (pid: 16642 interval: 1s)

Opsiyonların cümlesini burada yazmanın bir alemi yok. Zaten çoğu opsiyonun isminden anlaşılıyor. Bunları evde kendi başınıza deneyebilirsiniz.

Potborimi burada bitirirken, illaki bir sonuç olması gerekmemeli diye bitirmek istiyorum ama gördüğünüz gibi olmuyor. Neden küçülükten beyni türkçe/edebiyat hocaları yıkadığı için. Efenim neymiş muhakkak bir giriş, gelişme ve sonuç olmalıymış. O bakımdan alın size sonuç bu işte!
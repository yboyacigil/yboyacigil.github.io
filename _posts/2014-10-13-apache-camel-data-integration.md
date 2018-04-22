---
layout: post
title:  "Apache Camel ile Veri Entegrasyonu"
date:   2014-10-13 16:16:01 +0200
categories: turkish
---
Finans/Bankacılık alanı başta olmak üzere, BT dünyasında veri işleme üzerine geliştirilen çözümlerin adreslemesi gereken temel sorun verinin nasıl kullanılabilir hale getirileceği, nasıl doğrulanıp dönüştürüleceği yani veri entegrasyonudur.

Veri entegrasyonu konusunda çözüm sağlayıcılar daha çok kendilerine has yöntemler sunuyorlar ki genellikle bu ilişkisel veri tabanları üzerinden verinin paylaşılması veya içeri alınması şeklinde gerçekleniyor.

Bu yaklaşım hızlı bir şekilde projelerin ilerlemesini sağlayabilir. Ancak potansiyel olarak bir takım dezavantajlar barındırıyor:

1. Alternatif kaynaklardan veri aktarmak için ek çözümler gerekir. Bu durum rekabet anlamında çözüm sağlayıcıya zorluk yaşatacaktır. Diğer kaynakları kullanmak için ek geliştirmeye ihtiyaç duyulacaktır.
2. Hızlı (ve ucuz) çözümler tek bir müşteriye/duruma göre geliştirildiği için, entegrasyon alanı ile ilgili tüm durumları hesaba katarak geniş bir bakış açısıyla ele alamazlar. Bu yüzden beklenmedik sorunlarla her an karşılaşılabilir.
3. Çözüm sağlayıcı kendi geliştirdiği yöntem ile ilgili tüm sorunları tek başına çözmek durumundadır.
Entegrasyon her yönüyle kompleks bir iştir. Kompleks problemlerle baş etmek için insan varoluşundan bu yana şablonları kullanagelmiştir. Tabi ki entegrasyon problemi için de bir takım [entegrasyon şablonları](http://www.eaipatterns.com/) geliştirilmiştir.

[Apache Camel](http://camel.apache.org) işte bu entegrasyon şablonlarını kullanarak, Java'da açık kaynaklı, neredeyse tüm entegrasyon ihtiyaçlarına cevap veren bir çözüm olarak kullanıma hazır olarak beklemektedir. Camel yukarıda bahsedilen sorunları basit bir şekilde adreslemektedir.

Camel veriyi hem tüketirken hem de üretirken farklı veri kaynakları ile birlikte bir arada kullanılabilir. Mesela HTTP client üzerinden toplanan bir veri dönüştürülüp doğrulandıktan sonra veritabanına yazılacak hale dönüştürülebilir. Camel ile böyle bir veri entegrasyonunu hiçbir geliştirme yapmadan gerçeklemek mümkündür. Aklınıza gelebilecek hemen hemen tüm veri entegrasyon noktaları uygun Camel bileşenleri (Camel Components) kullanılarak bağlanabilir.

Bunlardan bir kısmı:

* File (Dosya)
* HTTP
* FTP
* JMS
* JDBC

şeklinde sıralanabilir.

Bileşenler hem veri okurken (from) veya veriyi yazarken (to) kullanılabilir.

Camel bir Java kütüphanesi şeklinde mevcut uygulamalara eklenebildiği gibi, çok kullanılan Spring gibi framework'lerle de çalışabilecek şekilde tasarlanmıştır ve Spring'in XML konfigurasyon dosyasında veri entegrasyonu için gerekli konfigurasyon standart bileşenlerle hiç kod yazmadan yapılandırılabilir.

Camel veri çözümleri konusunda uygulama geliştiren çözüm üretecilerinin muhakkak değerlendirmeleri gereken önemli bir kaynaktır.
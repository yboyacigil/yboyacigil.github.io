---
layout: post
title:  "Oracle 12c Pattern Matching"
date:   2014-10-20 16:16:01 +0200
categories: turkish
---

Oracle Türkiye'nin geçen hafta düzenlediği Oracle 12c etkinliğinde, yeni sürüm ile ilgili birçok yenilik tanıtıldı. Bunlar arasında: Database As Service, Pluggable DB, In-Memory Option ve Pattern Matching daha çok dikkatimi çekti.

Database As Service 12c ile gelen bulut hizmetini verebilmek için olmazsa olmaz bir özellik olarak karşımıza çıkıyor. Veritabanını donanım, kurulum, yedekleme derdini düşünmeden basit bir hizmet satın almak da artık mümkün olabilir. Mesela bir Amazon Elastic Compute Cloud (EC2) servisi gibi internet üzerinde bir Oracle veri tabanı kiralayabiliriz. Etkinlikte daha çok private cloud'dan bahsedildi. İşin public tarafı ile ilgili ne tür opsiyonlar olduğundan çok bahsedilmedi.

Pluggable DB bir Oracle kurulumunda birbirinden tamamen bağımsız ve farklı şekilde yapılandırılabilen birden çok veritabanını çalıştırmayı mümkün kılacak bir teknoloji. Her bir veritabanı için ayrı kurulum olması zorunluluğu çok doğal değil. Yani ilişkisel veri tabanları konseptinde veritabanını da bir obje olarak düşünürsek, neden tek bir veritabanı engine'i aynı anda birden fazla veritabanına hizmet veremesin ki? Pluggable DB ile bu sorunun artık bir cevabı var.

In-Memory Option tablo, partition bazında tüm veya seçilen kolonları hafızaya alıp, veri tutarlılığını da koruyarak sorguları çok hızlı çalışmayı sağlayacak bir yenilik. Onu diğer cache çözümlerinden farklı kılan veriye her zaman en güncel hali ile erişimi sağlaması. Oracle'ın hafızasında (SGA) ayrı bir alan (Column Store) ayrılyor ve verinin en güncel halini "transaction commit" esnasında bu hafıza alanında da yazılıyor. Bu yeni hafıza alanında tablo satır bazlı değil kolon bazlı tutuluyor; böylelikle veriye erişim ve verinin hafızada kapladığı alanı optimum seviyeye çıkartılıyor. Netice itibariyle verinin analizine yönelik sorgularda doğru konfigurasyon yapılırsa sorgu hızında in-memory option'la 1000 kat ve belki daha fazla bir iyileşme yaşandığına dair örnek çalışmaların olduğu söyleniyor. Yalnız verinin devamlı güncellendiği (OLTP) durumda bu kadar hızlı sorgu performansı olmayacağı tabi ki eskisine oranla daha hızlı olacağını bekleyebiliriz.

Pattern Matching SQL dünyasında bir süredir beklenen veri üzerinde belli bir paterne uygun veriyi bulmayı kolaylaştıracak önemli bir özellik. Veriyi analiz ederken trend'leri belirlemek, anormal durumları tespit etmek, sahtekarlık/iç usulsüzlük tespiti tipik kullanım alanları arasında.

Aşağıda bunun nasıl kullanılabileceği ile ilgili bir sorgu örneğini görebiliriz.

{% gist d5397b96c4600815b863 %}

Örnekte bir hesaptan para çekip (WTD) arada başka bir işlem olmaksızın parayı aynı hesaba geri yatıran (DEP) banka çalışanlarını tespit edecek bir sorgu verilmiştir. Banka çalışanının bir hesaptaki parayı zimmetine geçirip bir süre sonra aynı hesaba geri yatırması tespit edilmesi gereken şüpheli bir işlemdir.

Sql'in yapısına geçersek, SELECT ifadesinin sonuna MATCH_RECOGNIZE cümleciği koyarak başlıyoruz. Burada:

* PARTITION BY EMP ve ORDER BY TSTAMP kayıtlara çalışan bazında ve tarih sırasına göre erişmek istediğimizi,
* MEASURES alt cümleciği sorgu sonucunda pattern'e uyan hangi kolonları listeleyeceğimizi,
* ONE ROW PER MATCH pattern'e uyan tüm eşleşmeler için sadece bir kayıt dönmesini,
* PATTERN bu sorguda kullanmak istediğimiz pattern'i yani önce para çekme (WITHDRAW) sonra en azından bir para yatırmayı (DEPOSIT),
* DEFINE ise para çekme ve yatırmanın ne demek olduğunu

tanımlar. 

Sorgunun kalan kısmında pattern'e uyan kayıtların aynı çalışan ve aynı hesap üzerinden yapılmış olması koşulunu içermektedir. Eşleşen kayıt sorgunun alt kısmında gösterilmiştir.

Örnekte de görüldüğü gibi MATCH_RECOGNIZE cümleciği ile okuması ve anlaşılması kolay sorgular yazabiliriz.

Oracle 12c veritabanı sistemi en azından bu yeni özelliği ile veri analizinde rakiplerinden bir adım öne çıkıyor.

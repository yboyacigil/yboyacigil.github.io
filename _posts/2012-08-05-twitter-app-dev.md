---
title:  "Twitter Uygulaması Geliştirmek"
date:   2012-08-05 16:16:01 +0200
categories: turkish
---
Twitter'ı bilmeyen yoktur farkındayım ama "Twitter Uygulaması" isim tamlamasının ne olduğunu merak edecek birçok kişi çıkacaktır, eminim. O zaman ne demek istediğimi yavaş yavaş açmama izin verin.

Twitter sadece kişinin arkadaşları ve hatta tüm internet alemi ile her seferinde maksimum 140 harften oluşan mesajları paylaşabildiği sosyal bir ortam olması ötesinde; sağladığı alt yapı, ki kendisine "Application Programming Interface (API)" Türkçe olarak "Uygulama Programlama/Geliştirme Arayüzü" de denir, sayesinde kendi ekosistemini zenginleştirecek ek hizmetler geliştirilmesine izin vermektedir. Zaten akıllıca olanı da budur. Neden? Twitter ekosisteminde verilebilecek tüm hizmetleri kendisi yapamaz. Dolayısıyla başkalarının enerji ve yaratıcılığını kullanarak bir kazan-kazan durumu oluşturmak akıllıca bir davranış olur. Mesela, Microsoft'tan Windows işletim sistemi için yazılabilecek tüm programları sağlamasını bekler misiniz? Beklemeye beklersiniz de; Microsoft bunu yapmak ister mi? Konu anlaşıldı sanırım.

İyi de ne gibi uygulamalar geliştirilebilir? Twitter'ın web önyüzü dışında Windows, Mac için geliştirilmiş masaüstü uygulamalarını ilk çırpıda sayabiliriz. (bkz: [22 best twitter app](http://www.techradar.com/news/internet/22-best-twitter-apps-for-2012-930383)) Veya web sitenize herhangi bir twitter kullanıcısını kabul edip, sitenizden twitter kullanıcısı adına mesela twit attırabilirsiniz. Onun da ötesinde Twitter'da var olan alt yapıyı zenginleştirecek uygulamalar geliştirilebilir. Örneğin bir süreliğine bazı takip ettiğiniz kişilerin twit'lerini  görmek istemeyebilirsiniz (bkz: [Twitter-DogHouse](http://twitter-doghouse.herokuapp.com/)).

Örnekler uzatılabilir ama benim burada asıl anlatmak istediğim şey bir Twitter uygulamasını nasıl geliştireceğiniz konusunda bilgi vermek. Hadi başlayalım:

1. **Yeni bir twitter hesabı açın**: Geliştireceğiniz uygulama için bir twitter hesabı açmanız gerekir. Bu hesap, uygulamanızı twitter'a tanıtmak için gerekli olduğu gibi, aynı zamanda uygulamanızı kullanacak kişilerle yine twitter üzerinden haberleşmek imkanı tanıyacaktır. Bu kişiler uygulamanızı kullandıkları gibi twitter üzerinden sizin uygulamanızla ilgili bir takım güncel bilgileri paylaşmanızı sağlayabilir.

2. **Uygulamanızı twitter'a kaydedin**: Yeni açtığınız hesap ile giriş yaptıktan sonra uygulamanızı twitter ekosistemine ekleyin (bkz: [yeni bir uygulama yaratmak](https://dev.twitter.com/apps/new)). Burada uygulama ile ilgili resim, açıklama, web adresi gibi bilgiler yanında uygulamanın twitter kullanıcısı adına twit atmak, twit'lerine ulaşmak, direkt mesaj atmak gibi neler yapmak isteyeceğini belirtiyorsunuz. Uygulamanızı kaydettikten sonra API'yi kullanmaya başlayabilmeniz için gerekli olan tüketici anahtarı ve şifresi (consumer key & secret) veriliyor sistem tarafından.

3. **Twitter API'sini öğrenin**: Twitter API'si ile neler yapabileceğinizi  öğrenin (bkz: [Exploring the twitter API](https://dev.twitter.com/console)). [Dokümanları](https://dev.twitter.com/docs) karıştırın. Sizin öğrenme metodunuzu bilmem ama baştan sonra tüm dokümanları okumanızı tavsiye etmem. Herşeyi bir anda öğrenmeye kalkışmayın. Çabuk tükenirsiniz. Büyük resmi anlamaya çalışın, detaylara gerektiği zaman girersiniz. Üstelik herhangi bir konu kafanıza mı takıldı; hz. google'a sorun o sizi doğru yere yönlendirecektir. Sorunuzun cevabını bulamadığınız zaman [stackoverflow](http://www.stackoverflow.com/) ve twitter dev gruplarına (bkz: [Twitter Dev Talk](https://groups.google.com/forum/?fromgroups#!forum/twitter-development-talk)) sorun.

4. **Twitter API'sini nasıl kullanacağınızı öğrenin**: Uygulamanızı kullanacak olan twitter kullanıcıları adına API çağrıları yapmadan önce kullanıcıların size yetki vermesi gerekir. Twitter bunun için [OAuth](https://dev.twitter.com/docs/auth/oauth) yetkilendirme mekanizmasını kullanmanızı şart koşuyor.

OAuth, Twitter gibi kullanıcıları adına işlem yaptıran platformlarda kullanılan güvenli ve standart bir yetkilendirme mekanizmasıdır. OAuth hiçbir şekilde kullanıcıların şifresini üçüncü partilerle paylaşmadığı için güvenlidir. OAuth standart olduğu için şimdiye kadar geliştirilmiş olan istemci  kütüphanelerini (client libraries) uygulamanızda kullanabilir ve destek alabilirsiniz.

Twitter'da OAuth ile kullanıcı adına işlem yapabilmeniz için:

1. Erişim için bir anahtar almanız,
2. Tüm API çağrılarını aldığınız bu anahtar ile işaretlemeniz

gerekecektir. 

Bunlar ile ilgili teknik detaylar için bkz: [OAuth'u kullanmak](https://dev.twitter.com/docs/auth/using-oauth).

Tabi hepsinden önce Twitter ekosistemine nasıl bir servis hazırlayarak katkıda bulunacağınızı bilmeniz gerekir. Bu sizin hayal gücünüze ve Twitter API'si ile neler yapabileceğinizi bilmenize kalıyor.
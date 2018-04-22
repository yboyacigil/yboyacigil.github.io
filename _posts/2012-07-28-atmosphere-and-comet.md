---
title:  "Atmosfer ve Comet"
date:   2012-07-28 16:16:01 +0200
categories: turkish
---
Atmosfer ([Atmosphere](https://github.com/Atmosphere/atmosphere)) isimli bir framework geçen hafta başında geldi beni buldu. Normalde oturup şöyle birşey var inceleyeyim şeklinde değil de bu sefer kendisi bana geldi.

Burada bir paragraf açıp şöyle acayip bir teorim/inanışım var ondan bahsedeyim: Internet gezginleri şu şekilde seviyelendirilebilir: 

1. İlk seviye öylesine gezinenlerin seviyesidir. Onlar için önemli olan gezmektir. Amaç odur. 
2. Sonraki "bilinçli tüketici" seviyesidir. Onlar özel birşeye bakar ve onu bir şekilde arar, bulurlar. 
3. Daha sonraki seviye internete herhangi bir içeriği koyanlarındır. Bu seviyedekiler diğer iki seviyeden daha yukarıdadır. 
4. Son ve en üst seviye ise aranılan şeyin mucizevi bir şekilde bulunduğu seviyedir. O seviyedekinin birşeyi aramasına bile gerek yoktur. Yeri ve zamanı geldiğinde aradığı şey onu bulur. Ben kendimi bu seviyede olan şanslı veya seçilmiş kişi olarak görürüm. İçeriğin beni bulduğu çok olmuştur.

Atmosfer, HTTP sunucudan web tarayıcısına, kullanıcının herhangi özel bir isteği olmaksızın bilgi aktarmasına yarayan bir çözüm. Biliyorum bir önceki cümle pek bir anlam ifade etmiyor. Bir örnekle açıklayayım: Twitter hesabınıza girdiğinizde takip ettiklerinizin twit'lerini sayfayı ilk görüntülediğinizde görebiliyorsunuz. Ama o esnada aynı kişiler yeni twitler yayınlayabilirler değil mi? Bunları nasıl görebilirsiniz? Sayfayı tekrar yükletirsiniz olur biter! Evet olur biter ama ya siz yükletmezseniz ya siz yüklettiğinizde çok geç olmuşsa. Devamlı tekrar yüklemek zorunda mısınız? "Otomatik yükletirim abi, tarayıcılar bunu destekliyor zati" diyecek süper kullanıcılar veya "Abi, script'le sayfayı tekrar yükletmek mümkün, sen ne anlatıyosun!"diyecek programcı ruhlu kardeşler çıkabilir. Ama olayımız o değil! İşte atmosfer bu twit'leri siz sayfayı tekrar yüklemeden/yükletmeden görüntületmeyi sağlayabilecek bir çözüm.

Aslında bu tip sunucudan tarayıcıya doğru ve tarayıcının istemesine gerek kalmadan bilgi aktarımı teknolojisine [Comet](https://en.wikipedia.org/wiki/Comet_(programming)) deniyor. Atmofer comet tekniğini kullanarak bu işi hem tarayıcı hem de sunucu tarafında çözen yapıları sağlıyor. Atmosferin detayına bu yazıda girmek niyetinde değilim.

Biraz comet'ten bahsedeyim: Comet yukarıdaki örnekte anlattığım devamlı yükleme yapmayı/yaptırmayı elemine etmek için sunucu tarafından kapatılmayan özel bir tarayıcı isteğine, içerik oluştukça tarayıcıya veri göndererek cevap veren bir yöntem sağlıyor.

Sunucu tarafında kapatılmayan özel bir istek ne ola ki derseniz: HTTP sunucu veriyi istemcisine ya tek seferde ya da parçalar halinde (chunked) iletir. Parçalar halinde iletme durumunda en son parça gelene kadar tarayıcı isteği son bulmaz. Eğer sunucu en son parçayı sana gönderdim dedirtilmezse, tarayıcı ondan veri geldikçe işlemeye devam eder.

İşte comet tekniği ile sunucu devamlı veri gönderek tarayıcının sayfanın bazı bölümlerini gerektiğinden değiştirmesi sağlanabilir. Bu gönderilen veri ya script olarak iletilir ve tarayıcı tarafından geldikçe çalıştırılır. Ya da JSON veya XML gibi verilerdir yine daha önceden yüklenmiş script'ler tarafından işlenir.

Comet tekniği ile biraz zorlama gibi gözüken bu tip veri aktarımı işine daha kolay ve entegre çözüm HTML5 ile gelen websocket teknolojisidir. Websocket HTTP sunucusu tarafından da desteklenen ve veri aktarımı için açılan çift yönlü bir kanaldır. Bu kanal üzerinde sunucu ve tarayıcı birbirleri ile veri paylaşımı yapabilirler. Neredeyse yeni versiyon tüm web tarayıcıları HTML5'i destekliyor.

Atmosfer aynı zamanda websocket'i de destekleyen sunucu ve tarayıcılarla da çalışabiliyor. O bakımdan, internete çok çeşitli tarayıcılarla bağlanan kullanıcılar olduğu için web uygulama geliştiricileri için bu özelliği onu öne çıkarıyor.

Son olarak; web uygulamalarında comet tekniği kullanmak isteyenlerin atmosferi kesinlikle değerlendirmeleri gerekir diye düşünüyorum.
---
layout: post
title:  "Scala Nil, Nothing, Null"
date:   2013-01-09 16:16:01 +0200
categories: turkish scala
---
Herşey aşağıdaki gibi bir fonksiyon/metodu anlamaya çalışırken gelişti:

{% gist 5990000 %}

Burada herhangi tipteki nesneyi n kere çoğatıp sonunda o tipte bir liste üreten bir fonkisyon tanımı var.

{% gist 5990010 %}

Yukarıda bu fonksiyonu String tipinde bir nesne ile çağırdığımız zaman elde ettiğimiz sonucu görüyoruz. ;)

Fonksiyon ne yapıyor? `n` 0 olana dek `x`'i bir listenin başına ekliyor. `::` listeler için tanımlı ve `elm :: list` şeklinde kullanılan ve elemanı listenin başına eklemek için tanımlı bir operatördür. Yani:

{% gist 5990013 %}

En sondaki `Nil`'e dikkat! `Nil` Java'cı biri için `null`'sa haliyle bu NPE'ye neden olur diye düşündüm ilk olarak. Ama fonksiyon çalıştı. Nasıl? Demek ki `Nil` eşittir `null` doğru değil. Hadi diyelim Nil null değil. İyi de `::` operatörü nasıl çalışıyor? O zaman `type inference` diye geçen, derleyicinin burada akıllı bir şekilde Nil'in boş bir liste olacağını anlayıp ona göre bir düzenleme yaptığı geldi aklıma. Ama o da değilmiş! 

Scala API dokümanına bakınca, Nil'in boş bir List'e olduğunu gördüm:

{% gist 5990016 %}

`Nothing`?!! Nothing scala'da tanımlı tüm tiplerin alt tipiymiş. İyi de bu ne demek? Scala'da tüm tipler `scala.Any` tipinden geliyor. Java'daki `java.lang.Object` gibi. Any'nin iki direkt alt tipi var biri `scala.AnyVal` diğeri `scala.AnyRef`. İlki Java'daki int, double, float gibi sabit bir değer içeren tipleri, ikincisi de geri kalan diğer tüm tipleri ifade ediyor. `Nothing`'in Any'den gelen diğer tüm tiplerin bir alt tipi olması demek Nothing herhangi tipe referans olarak kullanılabilir demek. Ne var ki Nothing tipinde bir nesne yaratmazsınız. Hiç/hiçlik bilinir ama gösterilemez yani ;)

`List[Nothing]` de bu durumda herhangi bir tipteki boş liste demektir. Çünkü hem Nothing tüm tiplerin alt tipidir hem de Nothing tipinde bir nesne olamayacağında göre liste boş olmak zorundadır. Bu yüzden de `Nil`'in `List[Nothing]` olarak ifade edilmesi ve `List[T]` yani `T` tipindeki herhangi bir boş liste olması mantıklı hale gelmiş olur.

{% gist 5990019 %}

Yukarıdaki örnekteki `f` fonksiyonuna dikkat! Bu metodu hiçbir zaman çağıramazsınız. Zira çağıracak hiçi hiçbir zaman yaratamayacaksınız!

Nothing Scala'da hiç bitmesini istemediğimiz fonksiyonların dönüş tipi olarak kullanılır. Bir fonksiyonun dönüş tipi Nothing ise ve Nothing diye bir nesne üretemeyeceğimize göre bu fonksiyon bu tipte hiçbir değer üretemeyeceği için bitemeyecektir!

Nothing ile ilgili bir başka şey `Null` tipinin Nothing'den türemesidir. Null scala'da tanımlı bir başka tiptir ve sadece ama sadece bir objesi (instance'ı) vardır o da null'dur. `null` Java'daki null'un aynısıdır hiçbir nesneye referans olmayan bir değeri ifade etmek için kullanılır.

"Nil, Nothing, Null... Bunlara ne gerek vardı?", "Java'daki gibi null neyinize yetmedi de bunları uydurdunuz?" diyebilirsiniz. Bunlar programlama mantığı açısından bakıldığında hem olması gereken yapılar hem de programlamada düşülen bazı tuzakları önlemek, programcının işini kolaylaştırmak adına iyi bir programlama dilinde olması gereken özellikler.
---
title: "AdGuard ile Reklam Engelleme"
date: 2023-04-06
tags: ["windows", "ad", "adblock", "adguard", "youtubeads", "youtube", "dns", "doh", "dns-over-https", "dns-over-tls", "dns-over-Quic", "DNSSEC", "DNS-crypt","ESNI", "Encryted SNI", "ECH", "Encrypted Client Hello"]
author: "Wise"
draft: false
---
# 1. AdGuard: Reklamsız Gezinti İçin Nihai Çözümünüz

İnternette gezinirken sürekli karşınıza çıkan reklam ve açılır pencerelerinden sıkıldıysanız, [AdGuard Windows uygulamasını](https://adguard.com/tr/adguard-windows/overview.html) kullanmayı düşünebilirsiniz. AdGuard, yalnızca reklamları kaldırmakla kalmayıp aynı zamanda bilgisayarınızı kötü amaçlı yazılımlardan ve kimlik avı saldırılarından koruyan güçlü bir reklam engelleyicidir. Tanıdığınız ve bildiğiniz tüm reklamlara son kez sarılın artık onları bir daha göremeyeceksiniz. Çünkü bu blog gönderisinde, AdGuard'ın özelliklerini ve göz atma deneyiminizi nasıl geliştirebileceğini keşfedeceğiz.

## 1.1 AdGuard Uygulamasının Özellikleri

### 1.1.1 Reklam engelleyici

* AdGuard'ın birincil özelliği, banner reklamlar, açılır pencereler ve video reklamlar dahil olmak üzere her türlü reklamı[^1] web sayfalarından kaldırabilen reklam engelleyicisidir. Bu özellik yalnızca gezinmeyi hızlandırmakla kalmaz, aynı zamanda verilerden tasarruf etmenizi sağlar ve göz yorgunluğunu azaltır.

[^1]: `Contextual Advertising`, `Analytics Tools`, `Banner Advertising`, `Error Monitoring`, `Cookie Consent Notification`, `Youtube Ads` ve `Message Box`

### 1.1.2 Gizlilik koruması

* AdGuard, web sitelerinin verilerinizi toplamak için kullandığı izleme çerezlerini ve diğer izleme mekanizmalarını engelleyerek gizliliğinizi korur. Ayrıca, IP adresinizi ve diğer hassas bilgileri web sitelerinden gizleyen Gizli mod adı verilen bir özelliği de vardır.

### 1.1.3 Ebeveyn Kontrolü

* AdGuard, yetişkinlere uygun içerik veya diğer uygunsuz materyaller barındıran web sitelerine erişimi engellemenizi sağlayan bir ebeveyn denetimi özelliğine sahiptir. Belirli web sitelerini veya kategorileri engellemek için özel kurallar da oluşturabilirsiniz.

### 1.1.4 Malware koruması

* AdGuard, kötü amaçlı kod veya bağlantılar içeren web sitelerini engelleyen yerleşik bir kötü amaçlı yazılımdan koruma özelliğine sahiptir. Ayrıca tehlikeli olduğu bildirilen bir web sitesini ziyaret ettiğinizde sizi uyarabilir.

### 1.1.5 Özelleştirilebilir Filtreler

* AdGuard, belirli içerik türlerini veya web sitelerini engellemek için kendi filtrelerinizi oluşturmanıza veya diğer kullanıcılardan filtreler eklemenize olanak tanır. Bu özellik, gördükleriniz ve görmedikleriniz üzerinde tam kontrol sahibi olmanızı sağlar.

### 1.1.6 Kullanıcı dostu arayüz

* AdGuard, en acemi kullanıcılar için bile kullanımı kolaylaştıran basit ve sezgisel bir arayüze sahiptir. Tüm özelliklere ve ayarlara ana panodan kolayca erişebilirsiniz.

### > 1.1.7 Kısa Özet

> (TL;DR)[^2] AdGuard karşınıza çıkan reklamları engellerken aynı zamanda da kullanıcı dostu arayüzü ve özelleştirilebilir filtreleri sayesinde gizliliğinizi de korur. Ebeveyn kontrolü ve Malware koruması sayesinde ise istenmeyen sonuçlara karşı hazırlıklı olursunuz.

[^2]: TL;DR: `Too long; Didn't read` veya `Çok uzundu; okumadım` özet geç anlamında

## 1.2 Neden AdGuard'ı Seçmelisiniz?

AdGuard, gelişmiş özellikleri ve kullanımı kolay arayüzü nedeniyle Windows için mevcut olan en iyi reklam engelleyicilerden biridir. İşte AdGuard'ı seçmeniz için bazı nedenler:

### 1.2.1 Kapsamlı Reklam Engelleme

* AdGuard, pop-up'lar, video reklamlar ve banner reklamlar dahil her türlü reklamı[^1] engelleyerek tarama deneyiminizi daha akıcı ve hızlı hale getirebilir.

### 1.2.2 Gelişmiş Gizlilik Koruması

* AdGuard yalnızca reklamları engellemekle kalmaz, izleme çerezlerini engelleyerek ve IP adresinizi gizleyerek gizliliğinizi de korur.

### 1.2.3 Malware koruması

* AdGuard'ın kötü amaçlı yazılımdan koruma özelliği, sizi kötü amaçlı web sitelerinden korur ve tehlikeli bir siteyi ziyaret ettiğinizde sizi uyarır.

### 1.2.4 Özelleştirilebilir Filtreler

* AdGuard'ın özelleştirilebilir filtreleri, size hangi içeriği görüp neyi görmediğiniz konusunda tam kontrol sağlar.

### 1.2.5 Kullanımı Kolay Arayüz

* AdGuard'ın kullanıcı dostu arayüzü, teknik uzmanlıkları ne olursa olsun tüm kullanıcılar için kullanımı kolaylaştırır.

### > 1.2.6 Kısa Özet

> (TL;DR) [^2] AdGuard diğer reklam engelleme yazılımları ve çözümlerinden daha gelişmiş ve hızlı bir çözüme sahiptir. Ayrıca diğer yazılımlarda olmayan Malware koruması ve Gelişmiş Gizlilik özelliklerine de sahiptir.

----

# 2. AdGuard Windows Uygulaması Nasıl Kurulur

AdGuard, bilgisayarınızı kötü amaçlı yazılımlardan ve kimlik avı saldırılarından koruyabilen ve aynı zamanda web sayfalarından her türlü reklamı[^1] kaldırabilen güçlü bir reklam engelleyicidir. Reklamsız bir tarama deneyimi yaşamak istiyorsanız, AdGuard'ı Windows PC'nize yüklemek kolay ve kolaydır. Bu blog gönderisinde, AdGuard'ı Windows PC'nize yükleme adımlarında size rehberlik edeceğiz.

## 2.1 Adım Adım Kılavuz

### 2.1.1 AdGuard Yükleyiciyi İndirin

AdGuard'ı Windows PC'nize kurmanın ilk adımı, AdGuard yükleyicisini resmi web sitesinden indirmektir. AdGuard web sitesindeki "İndir" düğmesine tıklayarak yükleyiciyi indirebilirsiniz.

![https://adguard.com/tr/adguard-windows/overview.html (Erişim Tarihi 07.04.2023)](/images/adguard/1.jpg)

### 2.1.2 Yükleyiciyi Çalıştırın

AdGuard yükleyicisini indirdikten sonra, PC'nizde dosyayı bulun ve yükleyiciyi çalıştırmak için üzerine çift tıklayın. Kullanıcı Hesabı Denetimi (UAC) tarafından istenirse, yükleyiciye PC'nizde değişiklik yapması için izin vermeniz gerekebilir.

![Adguard Windows uygulaması yükleyici görüntüleri](/images/adguard/2.png)

### 2.1.3 Kurulum Seçeneklerini Seçin

Bir sonraki adım, AdGuard için kurulum seçeneklerini seçmektir. AdGuard'ı tüm kullanıcılar için veya yalnızca mevcut kullanıcı için yüklemeyi seçebilirsiniz. Ayrıca kurulum klasörünü seçebilir ve bir masaüstü kısayolu oluşturabilirsiniz.

![Adguard Windows uygulaması kurulum seçenekleri](/images/adguard/3.png)

### 2.1.4 AdGuard'ı yükleyin

Kurulum seçeneklerini seçtikten sonra, kurulum sürecini başlatmak için "Yükle" düğmesine tıklayın. Yükleme işlemi bilgisayarınızın hızına bağlı olarak birkaç dakika sürebilir.

![Adguard Windows uygulaması kurulum görüntüleri](/images/adguard/4.png)

### 2.1.5 AdGuard'ı başlatın

Yükleme tamamlandıktan sonra, AdGuard'ı masaüstü kısayolundan veya Başlat menüsünden başlatabilirsiniz. AdGuard'ı ilk kez başlattığınızda, ayarları tercihlerinize göre yapılandırmanızı isteyecektir.

![Adguard Windows uygulaması genel ayarları](/images/adguard/5.jpg)

### 2.1.6 Reklamsız Taramanın Keyfini Çıkarın

AdGuard'ın ayarlarını yapılandırdıktan sonra, reklamsız bir göz atma deneyiminin keyfini çıkarabilirsiniz. AdGuard, web sayfalarından her türlü reklamı[^1] otomatik olarak kaldırarak gezinmenizi daha hızlı ve sorunsuz hale getirir.

![https://www.youtube.com/watch?v=YW9Ojcm1Gkg (Erişim Tarihi 07.04.2023)](/images/adguard/6.jpg)

### > 2.1.7 Kısa Özet

> Sistem admini iseniz veya herhangi bir ayar ile uğraşmaadan direk kurmak istiyorsanız `Msiexec /q /i AdGuard.msi` komutunu kullanabilirsiniz.
>
> Alternatif olarak da halk arasında "Next > Next > Next" tabir edilen yöntemi kullanabilirsiniz. Yani `kapattım gözlerimi karanlığa, açtım ellerimi sonsuzluğa, rabbim sonum hayrola :D` diyerek her gelen sayfada hiçbir şeyi okumadan İleri/Next butonuna tıklayarak çok hızlı bir şekilde kurabilirsiniz.
----

# 3. AdGuard Nasıl Çalışıyor?

AdGuard, dünya çapında milyonlarca kullanıcının güvendiği güçlü bir reklam engelleyicidir. Reklamları engelleme yollarından biri, DNS tabanlı engelleme teknikleri kullanmaktır. Bu blog gönderisinde, AdGuard'ın DNS, DNS-over-HTTPS (DoH) ve diğer teknikleri kullanarak reklamları nasıl engellediğini keşfedeceğiz.

## 3.1 DNS Tabanlı Engelleme

DNS tabanlı engelleme, AdGuard tarafından reklamları DNS isteklerini kara listeye alınmış bir IP adresine yönlendirerek engellemek için kullanılan bir tekniktir. Bir web sitesini ziyaret ettiğinizde, tarayıcınız, web sitesinin alan adını bir IP adresine çevirmek için bir DNS sunucusuna bir DNS isteği gönderir. AdGuard bu isteği yakalar ve kara listesine göre kontrol eder. Alan kara listedeyse AdGuard, isteği kara listedeki bir IP adresine yönlendirerek reklamın ekranınızda görüntülenmesini etkili bir şekilde engeller.

DNS filtresi, reklamları daha bilgisayarınıza yüklenmelerine fırsat vermeden engelleyerek zamandan ve bant genişliğinden tasarruf etmenizi sağladığı için oldukça etkilidir. AdGuard'ın DNS filtresi de son derece özelleştirilebilir olup, kullanıcıların gerektiğinde belirli alanları beyaz listeye veya kara listeye almasına olanak tanır. DNS tabanlı engelleme, reklamları engellemenin basit ve etkili bir yoludur, ancak bazı sınırlamaları vardır. Örneğin, bazı reklamlar, standart olmayan bağlantı noktaları veya şifreli bağlantılar kullanarak DNS engellemesini atlayabilir.

## 3.2 HTTPS filtresi

HTTPS filtresi, AdGuard'ın bir başka önemli özelliğidir ve HTTPS şifrelemesi kullanan web sitelerindeki istenmeyen içeriği filtreleyerek çalışır. Birçok reklam engelleme aracı, şifreleme web sitesinin içeriğini görmelerini engellediği için HTTPS web sitelerindeki reklamları engellemekte zorlanır. Ancak, AdGuard'ın HTTPS filtresi, web sitesinin içeriğinin şifresini çözebilir ve istenmeyen reklamları veya diğer içeriği filtreleyebilir.

HTTPS filtresi, daha fazla web sitesi gelişmiş güvenlik için HTTPS'ye geçtikçe giderek yaygınlaşan HTTPS şifrelemesi kullanan web sitelerindeki reklamları engellemede oldukça etkilidir. AdGuard'ın HTTPS filtresi de son derece özelleştirilebilir ve kullanıcıların belirli web sitelerini veya etki alanlarını beyaz veya kara listeye almasına olanak tanır.

## 3.3 İçerik filtresi

İçerik filtresi, AdGuard'ın bir diğer önemli özelliğidir ve web sitelerindeki istenmeyen içeriği filtreleyerek çalışır. Buna, kullanıcıların dikkatini dağıtabilecek veya rahatsız edebilecek reklamlar, açılır pencereler, Cookie (Çerez) uyarı bildirimleri, afişler ve diğer istenmeyen içerik türleri dahildir.

İçerik filtresi, tüm etki alanlarını veya IP adreslerini engellemek yerine bir web sitesindeki belirli öğeleri hedef aldığı için oldukça etkilidir. Bu, kullanıcıların istenmeyen içerikle bombardımana tutulmadan daha akıcı bir tarama deneyiminin keyfini çıkarmasına olanak tanır. AdGuard'ın İçerik filtresi de son derece özelleştirilebilir ve kullanıcıların belirli web sitelerini veya etki alanlarını beyaz veya kara listeye almasına olanak tanır.

## > 3.4 Kısa Özet

> (TL;DR) [^2] ![https://adguard.com/tr/adguard-windows/overview.html (Erişim Tarihi: 07.04.2023)](/images/adguard/adguard-nasil-calisir.jpg)

----

# 4. DNS ile ilgili Gelişmiş Ayalar

DNS (Alan Adı Sistemi), insanlar tarafından okunabilen alan adlarını bilgisayarların anlayabileceği IP adreslerine çevirmek için kullanılan bir protokoldür. Ancak, DNS sorguları normal olarak 53 numaralı port üzerinden gönderilir ve herhangi bir şifreleme yapılmaz. Dolayısıyla varsayılan olarak güvenli değildir ve DNS sorguları saldırganlar tarafından ele geçirilebilir veya manipüle edilebilir.

![https://blog.chromium.org/2020/05/a-safer-and-more-private-browsing-DoH.html (Erişim Tarihi: 07.04.2023)](/images/adguard/Unsecured-DNS.png)

Bu sorunu çözmek için HTTPS üzerinden DNS (DoH), TLS üzerinden DNS (DoT), QUIC üzerinden DNS (DoQ) ve DNSCrypt dahil olmak üzere birkaç yeni protokol geliştirilmiştir. Blog gönderisinin bu kısmında, bu protokoller arasındaki farkları ve DNS sorgularınızı korumaya nasıl yardımcı olabileceklerini tartışacağız. DNS-over-HTTPS (DoH), DNS-over-TLS (DoT), DNS-over-QUIC (DoQ) ve DNSCrypt birçok avantaj sağlarken, dikkate alınması gereken bazı potansiyel dezavantajlar da vardır. [^3]

[^3]: Büyük güç büyük sorumluluk getirir. -Örümcek Adam

## 4.1 HTTPS üzerinden DNS (DoH)

DNS-over-HTTPS (DoH), HTTPS protokolünü kullanarak DNS sorgularını şifreleyen bir protokoldür. DoH, üçüncü tarafların DNS sorgularına müdahale etmesini ve kurcalamasını önleyerek gizliliği ve güvenliği artırmak için tasarlanmıştır. DoH ile, DNS sorguları şifrelenmiş bir kanal üzerinden gönderilerek saldırganların bunları yakalamasını ve manipüle etmesini zorlaştırır.

DoH, Firefox, Chrome ve Edge dahil olmak üzere birçok büyük web tarayıcısı tarafından desteklenir. Bir DoH uygulaması yükleyerek veya cihazınızı bir DoH sunucusu kullanacak şekilde yapılandırarak mobil cihazlarda da kullanılabilir.

![https://blog.chromium.org/2020/05/a-safer-and-more-private-browsing-DoH.html (Erişim Tarihi: 07.04.2023)](/images/adguard/DNS-over-HTTPS.png)

* ### 4.1.1 Dezavantajları

  * DoH'nin bir dezavantajı, belirli ortamlarda uygulanmasının zor olabilmesidir. Örneğin DoH, bazı güvenlik duvarları veya internet servis sağlayıcıları tarafından engellenebilir ve bu da kullanımını zorlaştırabilir. Ek olarak, bazı ağ yöneticileri, ağ trafiğini izlemeyi ve denetlemeyi zorlaştırabileceğinden, şifrelenmiş DNS sorgularına izin vermek istemeyebilir.

  * DoH ile ilgili bir başka olası sorun, gecikmeyi artırabilmesi ve DNS sorgularını yavaşlatabilmesidir. Bunun nedeni, DoH sorgularının, geleneksel bir DNS sorgusundan daha fazla ek yük gerektiren bir HTTPS bağlantısı üzerinden gönderilmesidir. Bu, çoğu kullanıcı için fark edilmese de, çevrimiçi oyunlar veya gerçek zamanlı video konferans gibi düşük gecikmeli DNS sorguları gerektiren uygulamalar için bir sorun olabilir.

## 4.2 TLS üzerinden DNS (DoT)

DNS-over-TLS (DoT), DNS sorgularını şifreleyen başka bir protokoldür, ancak HTTPS yerine Aktarım Katmanı Güvenliği (TLS) protokolünü kullanır. DoT, DoH ile benzer güvenlik avantajları sağlar, ancak yaygın olarak desteklenmez.

DoT'yi kullanmak için cihazınızı bir DoT sunucusu kullanacak şekilde yapılandırmanız gerekir. DoT, Cloudflare, Quad9 ve Google dahil olmak üzere birkaç DNS sunucusu tarafından desteklenir.

![https://www.myrasecurity.com/de/knowledge-hub/dns-over-tls (Erişim Tarihi: 07.04.2023)](/images/adguard/DNS-over-TLS.jpg)

* ### 4.2.1 Dezavantajları

  * DoH gibi, DoT da güvenlik duvarları veya internet servis sağlayıcıları tarafından engellenebilir. Bu, özellikle ağ yöneticilerinin şifreli DNS sorgularına izin vermek istemediği ortamlarda kullanımı zorlaştırabilir.

  * DoT ile ilgili bir başka olası sorun, güvenlik duvarında açık olmak için özel bir bağlantı noktası (853 numaralı bağlantı noktası) gerektirmesidir. Güvenlik duvarı özellikle izin verilenler dışındaki tüm bağlantı noktalarını engelleyecek şekilde yapılandırılmışsa bu sorun olabilir.

## 4.3 QUIC üzerinden DNS (DoQ)

DNS-over-QUIC (DoQ), QUIC protokolünü kullanarak DNS sorgularını şifreleyen yeni bir protokoldür. DoQ hala geliştirme aşamasındadır, ancak DoH veya DoT'den daha hızlı ve daha güvenli DNS sorguları sağlama potansiyeline sahiptir.

DoQ, gecikmeyi azaltan ve performansı artıran TCP yerine UDP kullanır. Ayrıca, bir DNS sunucusuyla bağlantı kurmak için gereken süreyi azaltabilen sıfır RTT anlaşmalarını da destekler.

* ### 4.3.1 Dezavantajları

  * DoQ nispeten yeni bir protokoldür ve şu anda onu destekleyen yalnızca birkaç DNS sunucusu vardır. Bu, özellikle DoQ'yu desteklemeyen belirli bir DNS sunucusu kullanmanız gerekiyorsa, kullanımı zorlaştırabilir.

  * DoQ ile ilgili bir başka olası sorun, hala geliştirme aşamasında olması ve çözülmesi gereken hatalar veya uyumluluk sorunları olabilmesidir. Bu, daha yavaş performansa veya başka sorunlara neden olabilir.

## 4.4 DNSCrypt

DNSCrypt, DNS sorgularını ve yanıtlarını açık anahtarlı şifreleme kullanarak şifreleyen bir protokoldür. DNSCrypt, DNS sahtekarlığını ve ortadaki adam saldırılarını önlemek için tasarlanmıştır.

DNSCrypt ile, DNS sorguları bir ortak anahtar kullanılarak şifrelenir ve sunucu, ortak anahtar kullanılarak doğrulanabilen imzalı bir yanıtla yanıt verir. Bu, saldırganların DNS sorgularını ve yanıtlarını yakalamasını ve manipüle etmesini önler.

DNSCrypt, OpenDNS ve Cloudflare dahil olmak üzere birkaç DNS sunucusu tarafından desteklenir. Ayrıca, dnscrypt-proxy ve Simple DNSCrypt dahil olmak üzere birkaç DNS istemcisiyle birlikte kullanılabilir.

![https://dev.to/cipherops/using-dnscrypt-with-adguard-home-pi-hole-7j6 (Erişim Tarihi: 07.04.2023)](/images/adguard/DNS-crypt.png)

* ### 4.4.1 Dezavantajları

  * DNSCrypt'in olası bir dezavantajı, hem istemcinin hem de sunucunun protokolü desteklemesini gerektirmesidir. DNSCrypt birkaç DNS sunucusu ve istemcisi tarafından desteklenirken, DoH veya DoT kadar yaygın olarak desteklenmez. Bu, özellikle DNSCrypt'i desteklemeyen belirli bir DNS sunucusu kullanmanız gerekiyorsa, kullanımı zorlaştırabilir.

  * DNSCrypt ile ilgili bir başka olası sorun, gecikmeyi artırabilmesi ve DNS sorgularını yavaşlatabilmesidir. Bunun nedeni, DNSCrypt sorgularının, geleneksel bir DNS sorgusundan daha fazla ek yük gerektiren açık anahtarlı şifreleme kullanılarak şifrelenmesidir

## > 4.5 Kısa Özet

> (TL;DR) [^2] DNS-over-HTTPS, DNS-over-TLS, DNS-over-QUIC ve DNSCrypt birçok avantaj sağlarken, dikkate alınması gereken potansiyel dezavantajları da vardır. Bu protokoller, güvenlik duvarları veya internet servis sağlayıcıları tarafından engellenebilir ve gecikmeyi artırabilir veya DNS sorgularını yavaşlatabilir. Bu protokollerden herhangi birini kullanmadan önce, olası dezavantajları göz önünde bulundurmak ve ek güvenlik ve gizlilik avantajlarına değip değmeyeceğini belirlemek önemlidir. Ek olarak, tüm sunucular tüm protokolleri desteklemediğinden, kullandığınız DNS sunucusunun kullanmak istediğiniz protokolü desteklediğinden emin olmanız önemlidir. Seçtiğiniz protokol ne olursa olsun, güvenli bir DNS sunucusu kullanmak çevrimiçi gizliliğinizi ve güvenliğinizi korumada önemli bir adımdır. DNS sorgularınızın şifreli ve güvenli olduğundan emin olmak için bu protokollerden birini kullanmayı düşünün. ![https://www.researchgate.net/profile/Minzhao-Lyu/publication/357587121 (Erişim Tarihi: 07.04.2023)](/images/adguard/DNS-over-TLDR.png)

----

# 5. Daha İleri Gizlilik İçin

DNSSEC, EDNS İstemci Alt Ağı (ECS), Şifreli SNI (Encrypted SNI) ve Şifreli İstemci Merhaba (Encrypted Client Hello), internette güvenliği ve gizliliği geliştirmeye yardımcı olan önemli teknolojilerdir. Blog gönderimizin bu kısmında, söz konusu teknolojilerin her birine daha yakından bakacağız [^4] ve faydalarını keşfedeceğiz.

[^4]: Dikkatli bakıyor musunuz? -Prestige [Youtube](https://www.youtube.com/watch?v=C6MlffsLfkM)

## 5.1 DNSSEC

DNSSEC, DNS sızdırma saldırılarını önlemek için tasarlanmış bir güvenlik protokolüdür. DNS sahtekarlığı, bir bilgisayar korsanı bir DNS sorgusunu yakaladığında ve istenen etki alanının IP adresini kötü amaçlı bir IP adresiyle değiştirdiğinde gerçekleşir. DNSSEC, DNS yanıtlarının gerçekliğini doğrulamak ve kullanıcıların doğru web sitesine yönlendirilmesini sağlamak için kriptografik imzalar kullanır.

DNSSEC, her DNS kaydına, yanıta güvenmeden önce istekte bulunan cihaz tarafından doğrulanan bir dijital imza ekleyerek çalışır. DNSSEC ayrıca, orijinalliklerini doğrulamak için kullanılabilecek DNS kayıtları için ortak anahtarları dağıtmak için bir mekanizma sağlar.

![https://www.dk-hostmaster.dk/en/dnssec (Erişim Tarihi: 07.04.2023)](/images/adguard/DNSSEC.png)

## 5.2 EDNS İstemci Alt Ağı (ECS)

EDNS İstemci Alt Ağı, DNS sunucularının istemcinin IP adresi hakkında bilgi almasına izin veren DNS protokolünün bir uzantısıdır. Bu bilgiler, DNS sorgularına daha doğru yanıtlar sağlamak için kullanılır ve DNS çözümleme sürecinin hızını ve güvenilirliğini artırmaya yardımcı olabilir.

EDNS İstemci Alt Ağı ile DNS sunucuları, istekte bulunan cihazın coğrafi konumunu belirleyebilir ve bu konum için optimize edilmiş bir yanıt sağlayabilir. Bu, gecikmeyi azaltmaya ve internetin genel performansını iyileştirmeye yardımcı olabilir.

## 5.3 Şifreli SNI (Encrypted SNI)

Şifreli SNI (Server Name Indicator), TLS el sıkışma işleminde SNI alanını şifreleyen bir teknolojidir. SNI alanı, istemcinin bağlanmak istediği sunucunun ana bilgisayar adını belirlemek için kullanılır. Şifreli SNI, bu alanı şifreleyerek gizli dinleme ve ortadaki adam saldırılarını önlemeye yardımcı olur.

Şifreli SNI, önemli bir gizlilik özelliğidir çünkü SNI alanı bir kullanıcının çevrimiçi etkinliğini izlemek için kullanılabilir. Şifreleme olmadan, ISP'ler, hükümetler ve diğer üçüncü tarafların SNI bilgilerine müdahale etmesi ve kullanıcı gizliliğini tehlikeye atarak izlemesi mümkündür.

![https://blog.cloudflare.com/encrypted-client-hello (Erişim Tarihi: 07.04.2023)](/images/adguard/ESNI.png)

## 5.4 Şifreli İstemci Merhaba (Encrypted Client Hello)

Encrypted Client Hello, TLS anlaşma sürecinin gizliliğini ve güvenliğini artırmaya yardımcı olan başka bir teknolojidir. İstemci Merhaba mesajı, TLS bağlantısını başlatmak için istemci tarafından gönderilir ve istemcinin desteklediği TLS sürümleri ve şifre paketleri hakkında bilgi içerir.

Encrypted Client Hello, İstemci Merhaba mesajını şifreleyerek, gizli dinleme ve ortadaki adam saldırılarını önlemeye yardımcı olur. Ayrıca, üçüncü tarafların Müşteri Merhaba mesajını ele geçirmesini ve analiz etmesini önleyerek kullanıcı gizliliğinin korunmasına yardımcı olur.

![https://blog.cloudflare.com/encrypted-client-hello (Erişim Tarihi: 07.04.2023)](/images/adguard/ECH.png)

Çözüm
DNSSEC, EDNS İstemci Alt Ağı, Şifreli SNI ve Şifreli İstemci Hello, internette güvenliği ve gizliliği geliştirmeye yardımcı olan önemli teknolojilerdir. Bu teknolojiler, DNS sahtekarlığı saldırılarını önlemek, daha doğru DNS yanıtları sağlamak ve TLS el sıkışma işlemi sırasında kullanıcı gizliliğini korumak için tasarlanmıştır. Bu teknolojileri uygulayarak herkes için daha güvenli ve güvenli bir internet oluşturmaya yardımcı olabiliriz.

----

# 6. Sonuç

(TL;DR) İnternette gezinirken karşınıza çıkan reklamlardan sıkıldıysanız, AdGuard reklamları engellemek için mükemmel bir seçimdir. Gelişmiş özellikleri ve özelleştirilebilir filtreleri, size hangi içeriği görüp neyi görmediğiniz üzerinde tam kontrol sağlar. AdGuard'ı bugün indirin ve daha hızlı, daha güvenli ve daha keyifli bir tarama deneyiminin keyfini çıkarın.

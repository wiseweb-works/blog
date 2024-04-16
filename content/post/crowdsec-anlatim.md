---
title: "Crowdsec IPS/IDS Yazılımı"
date: 2023-04-11
tags: ["linux", "bot", "botnet", "dos", "ddos", "crowdsec", "ıps", "ids", "parser", "scenario", "data source", "ban", "captcha","ip", "asn", "subnet", "ip block"]
author: "Wise"
draft: false
---
# 1. Crowdsec IPS/IDS Yazılımı Tanıtımı

CrowdSec, siber tehditlere karşı gerçek zamanlı koruma sağlamak için kolektif bir zeka yaklaşımı kullanan Linux sunucuları için açık kaynaklı bir güvenlik çözümüdür. Bu blog gönderisinde, CrowdSec'in (v.1.4.6)[^1] özelliklerini, Linux sunucularınızı korumak için neden harika bir seçim olduğunun nedenlerini ve CrowdSec ile diğer IPS/IDS yazılımları arasındaki farkları ve benzerlikleri keşfedeceğiz.

[^1]: https://github.com/crowdsecurity/crowdsec/releases/tag/v1.4.6

## 1.1 CrowdSec'in Özellikleri

CrowdSec, onu Linux sunucuları için güçlü ve etkili bir güvenlik çözümü yapan çeşitli özellikler sunar. Temel özelliklerinden bazıları şunlardır:

### 1.1.1 Gerçek zamanlı tehdit tespiti

* CrowdSec, tehditleri gerçek zamanlı olarak tespit etmek için makine öğrenimi algoritmalarını kullanır ve saldırıların gerçekleşmesini önler.

### 1.1.2 Otomatik engelleme

* Bir tehdit algılandığında CrowdSec, saldırıyla ilişkili IP adresini otomatik olarak engelleyerek anında koruma sağlar.

### 1.1.3 Kolektif zeka

* CrowdSec, yeni ve gelişmekte olan tehditleri algılamak için diğer CrowdSec kullanıcıları da dahil olmak üzere birden çok kaynaktan gelen verilerden yararlanır.

### 1.1.4 Kolay entegrasyon

* CrowdSec, kapsamlı bir güvenlik çözümü sağlamak için güvenlik duvarları ve SIEM'ler gibi diğer güvenlik çözümleriyle kolayca entegre edilebilir.

### 1.1.5 Özelleştirilebilir kurallar

* CrowdSec, kullanıcıların güvenlik politikalarının kendi özel gereksinimleriyle uyumlu olmasını sağlayarak kendi kurallarını oluşturmasına ve özelleştirmesine olanak tanır.

## 1.2 Neden CrowdSec'i Seçmelisiniz?

CrowdSec'in Linux sunucularınızın güvenliğini sağlamak için mükemmel bir seçim olmasının birkaç nedeni vardır. Bu nedenlerden bazıları şunlardır:

### 1.2.1 Açık kaynak

* CrowdSec açık kaynaklı bir çözümdür, yani kullanımı ücretsizdir ve özel ihtiyaçlarınızı karşılayacak şekilde kolayca özelleştirilebilir.

### 1.2.2 Toplu zeka

* CrowdSec, yeni ve gelişmekte olan tehditleri tespit etmek için toplu zekanın gücünden yararlanarak geleneksel güvenlik çözümlerinden daha yüksek düzeyde koruma sağlar.

### 1.2.3 Kolay entegrasyon

* CrowdSec, diğer güvenlik çözümleriyle kolayca entegre edilebilir ve bu da onu her kuruluş için esnek ve uyarlanabilir bir çözüm haline getirir.

### 1.2.4 Özelleştirilebilir kurallar

* CrowdSec, kullanıcıların güvenlik politikalarının kendi özel gereksinimleriyle uyumlu olmasını sağlayarak kendi kurallarını oluşturmasına ve özelleştirmesine olanak tanır.

## 1.3 CrowdSec ve Cloudflare Arasındaki Fark ve Benzerlikler

CrowdSec ve Cloudflare güvenlik çözümleri olmakla birlikte, ikisi arasında bazı temel farklılıklar vardır. Örneğin:

### 1.3.1 Farkları

* CrowdSec açık kaynaklı bir çözümken Cloudflare ticari bir çözümdür.

* CrowdSec, yeni ve gelişmekte olan tehditleri algılamak için toplu zekayı kullanırken Cloudflare, makine öğrenimi ve imza tabanlı algılamanın bir kombinasyonunu kullanır.

* CrowdSec, Linux sunucularını korumaya odaklanırken, Cloudflare çok çeşitli platformlar için güvenlik çözümleri sunar.

### 1.3.2 Benzerlikleri

Bu farklılıklara rağmen, hem CrowdSec hem de Cloudflare bazı benzerlikleri paylaşır. Örneğin:

* Her iki çözüm de tehditleri gerçek zamanlı olarak algılamak için makine öğrenimi algoritmalarını kullanır.

* Her iki çözüm de kötü amaçlı IP adreslerinin otomatik olarak engellenmesini sağlar.

* Her iki çözüm de diğer güvenlik çözümleriyle kolayca entegre edilebilir ve kapsamlı bir güvenlik çözümü sunar.

## 1.4 Kısa Özet

(TL;DR) [^2] CrowdSec, Linux sunucuları için gerçek zamanlı tehdit algılama, otomatik engelleme ve toplu zeka sağlayan güçlü ve etkili bir güvenlik çözümüdür. Açık kaynak yapısı, kolay entegrasyonu ve özelleştirilebilir kuralları, onu Linux sunucularının güvenliğini sağlamak isteyen tüm kuruluşlar için mükemmel bir seçim haline getirir. CrowdSec ve Cloudflare arasında bazı farklılıklar olsa da, her iki çözüm de bazı benzerliklere sahiptir ve dijital varlıklarını güvence altına almak isteyen kuruluşlar için mükemmel seçeneklerdir.

[^2]: TL;DR: `Too long; Didn't read` veya `Çok uzundu; okumadım` özet geç anlamında

# 2. Crowdsec Linux Nasıl Kurulur

CrowdSec, siber tehditlere karşı gerçek zamanlı koruma sağlamak için toplu zeka kullanan Linux sunucuları için açık kaynaklı bir güvenlik çözümüdür. Blog yazısının bu kısmında CrowdSec'i Linux sunucunuza kurma adımlarının yanı sıra sunucunuzun güvenliğini daha da artırmak için bir Linux bouncer'ı nasıl kuracağınız konusunda size yol göstereceğiz.

## 2.1 Adım Adım Kılavuz

Başlamadan önce, kurulum işleminin Linux dağıtımınıza bağlı olarak değişebileceğini lütfen unutmayın. Aşağıdaki adımlar Ubuntu 20.04'ü temel alır, ancak diğer dağıtımlar için talimatları CrowdSec dokümantasyon sayfasında bulabilirsiniz.

### 2.1.1 CrowdSec'i yükleyin

Öncelikle, aşağıdaki komutu çalıştırarak CrowdSec deposunu sisteminize eklemeniz gerekir:

#### Repo ve GPG anahtarını manul ekleme yöntemi

```bash
echo 'deb https://packages.crowdsec.net/deb stable main' | sudo tee /etc/apt/sources.list.d/crowdsec.list
```

Ardından, aşağıdaki komutu çalıştırarak CrowdSec imzalama anahtarını sisteminize eklemeniz gerekir:

```bash
wget -qO - https://packages.crowdsec.net/crowdsec.gpg.key | sudo apt-key add -
```

#### Otomatikleştirilmiş script ile Repo ve GPG anahtarının eklenmesi

```bash
curl -s https://packagecloud.io/install/repositories/crowdsec/crowdsec/script.deb.sh | sudo bash
```

|Debian[^3]|Ubuntu[^4]|
|:---:|:---:|
| ![](/images/crowdsec/1.jpg) | ![](/images/crowdsec/1-1.jpg) |

[^3]: https://www.youtube.com/watch?v=7jwVkWt_4TI (Erişim Tarihi: 11.04.2023)
[^4]: https://www.youtube.com/watch?v=2kW_200N60k (Erişim Tarihi: 11.04.2023)

Depoyu ve anahtarı ekledikten sonra CrowdSec'i aşağıdaki komutu çalıştırarak kurulumu yapabilirsiniz:

```bash
sudo apt update && sudo apt install crowdsec
```

|Debian[^3]|Ubuntu[^4]|
|:---:|:---:|
| ![](/images/crowdsec/2.jpg) | ![](/images/crowdsec/2-1.jpg) |

### 2.1.2 CrowdSec'i yapılandırın

CrowdSec yüklendikten sonra ihtiyaçlarınıza göre yapılandırmanız gerekir. CrowdSec için ana yapılandırma dosyası `/etc/crowdsec/config.yaml` konumunda bulunur. Bu dosyayı nano veya vim gibi bir metin düzenleyici kullanarak düzenleyebilirsiniz.

Yapılandırma dosyası iyi yorumlanmıştır, bu nedenle her seçeneğin ne yaptığını anlamak kolaydır. CrowdSec tarafından kullanılan Bouncer ve Parserların yanı sıra log kaydı alma, engelleme ve bildirim seçeneklerini özelleştirebilirsiniz.

### 2.1.3 CrowdSec'i Başlatın

CrowdSec'i yapılandırdıktan sonra, aşağıdaki komutu çalıştırarak hizmeti başlatabilirsiniz:

```bash
sudo systemctl start crowdsec
```

Aşağıdaki komutu çalıştırarak CrowdSec'in durumunu kontrol edebilirsiniz:

```bash
sudo systemctl status crowdsec
```

CrowdSec düzgün çalışıyorsa hizmetin etkin olduğunu belirten bir mesaj görmelisiniz.

![Debian(3)](/images/crowdsec/3.jpg)

### 2.1.4 Bir Linux Bouncer Kurun

Linux Bouncer, sunucunuzu siber tehditlere karşı korumaya yardımcı olabilecek ek bir güvenlik katmanıdır. Bouncerlar, ağ trafiğini izleyerek ve bilinen kötü amaçlı IP adreslerinden gelen istekleri engelleyerek çalışır.

CrowdSec, iptables bouncer ve nftables bouncer dahil olmak üzere kurabileceğiniz birkaç bouncer ile birlikte gelir.

#### Iptables Bouncer yüklemek için aşağıdaki komutu çalıştırın

```bash
sudo apt install crowdsec-firewall-bouncer
```

![Ubuntu(4)](/images/crowdsec/4.jpg)

#### Nftables Bouncer yüklemek için aşağıdaki komutu çalıştırın

```bash
sudo apt install crowdsec-nftables-bouncer
```

Bir bouncer kurduktan sonra onu CrowdSec ile çalışacak şekilde yapılandırmanız gerekir. bouncer için yapılandırma dosyası `/etc/crowdsec/bouncers.yaml` konumunda bulunur. Bu dosyayı nano veya vim gibi bir metin düzenleyici kullanarak düzenleyebilirsiniz.

Yapılandırma dosyası iyi yorumlanmıştır, bu nedenle her seçeneğin ne yaptığını anlamak kolaydır. CrowdSec tarafından kullanılan Bouncer ve Parserların yanı sıra günlüğe kaydetme, engelleme ve bildirim seçeneklerini özelleştirebilirsiniz.

### 2.1.5 Bouncer'ı Başlatın

bouncer yapılandırdıktan sonra, aşağıdaki komutu çalıştırarak hizmeti başlatabilirsiniz:

#### Iptables için

```bash
sudo systemctl start crowdsec-firewall-bouncer
```

Aşağıdaki komutu çalıştırarak bouncerların durumunu kontrol edebilirsiniz:

```bash
sudo systemctl status crowdsec-firewall-bouncer
```

#### Nftables için

```bash
sudo systemctl start crowdsec-nftables-bouncer
```

Aşağıdaki komutu çalıştırarak bouncerların durumunu kontrol edebilirsiniz:

```bash
sudo systemctl status crowdsec-nftables-b
```

Aşağıdaki komutu çalıştırarak CrowdSec'in durumunu kontrol edebilirsiniz:

```bash
sudo systemctl status crowdsec
```

CrowdSec düzgün çalışıyorsa hizmetin etkin olduğunu belirten bir mesaj görmelisiniz.

![Debian(3)](/images/crowdsec/3.jpg)

### 2.1.6 CrowdSec'i Test Edin

CrowdSec'in düzgün çalıştığını test etmek için sunucunuza farklı bir IP adresinden birkaç kez giriş yapmayı deneyebilirsiniz. Birkaç başarısız oturum açma denemesinden sonra CrowdSec, başarısız denemelerle ilişkili IP adresini bloke etmelidir.

Engellenen IP adreslerini aşağıdaki komutu çalıştırarak kontrol edebilirsiniz:

```bash
sudo cscli alerts list
```

Bu komut size CrowdSec tarafından engellenen IP adreslerinin bir listesini gösterecektir.

### 2.1.7 Kısa Özet

(TL;DR) [^2] CrowdSec'i Linux sunucunuza yüklemek, sunucunuzu gerçek zamanlı olarak siber tehditlere karşı korumaya yardımcı olabilecek basit bir işlemdir. CrowdSec, yeni ve gelişmekte olan tehditleri algılamak için toplu zekayı kullanarak geleneksel güvenlik çözümlerinden daha yüksek düzeyde koruma sağlar. Bu gönderide belirtilen adımları izleyerek CrowdSec'i Linux sunucunuza yükleyip yapılandırabilir ve dijital varlıklarınızı korumaya başlayabilirsiniz.

# 3. CrowdSec Nasıl Çalışır?

CrowdSec, toplu zeka kullanarak siber tehditlere karşı gerçek zamanlı koruma sağlayan, Linux sunucuları için açık kaynaklı bir güvenlik çözümüdür. Bu blog gönderisinde, CrowdSec'in nasıl çalıştığını ve işleyişinde yer alan farklı bileşenleri tartışacağız.

## 3.1 Zararlı Trafiği Tespit ve İşleme

CrowdSec, çeşitli kaynaklardan veri toplayarak, ilgili bilgileri çıkarmak için verileri ayrıştırarak ve olası tehditleri belirlemek için senaryolar uygulayarak çalışır. Bir tehdit belirlenirse, IP adresini bloke etmek veya bir bildirim göndermek gibi uygun eylem gerçekleştirilir.

CrowdSec, potansiyel tehditleri belirleme yeteneğini geliştirmek için toplu zekayı da kullanır. Bir tehdit belirlendiğinde, benzer tehditleri belirleme becerilerini geliştirmek için bilgiler diğer CrowdSec kullanıcılarıyla paylaşılır.

### 3.1.1 Data Sources

CrowdSec, olası tehditleri belirlemek için çeşitli kaynaklardan veri toplar. Bu veri kaynakları, sistem günlüklerini, ağ trafiğini ve üçüncü taraf API'leri içerir. Toplanan veriler daha sonra ilgili bilgileri çıkarmak için Parserlar tarafından işlenir.

### 3.1.2 Parsers

Parserlar, toplanan verilerden ilgili bilgileri çıkarmak için kullanılır. CrowdSec, sistem günlükleri, ağ trafiği ve üçüncü taraf API'ler için yerleşik Parserlarla birlikte gelir. Ayrıca, diğer kaynaklardan bilgi ayıklamak için özel Parserlar oluşturabilirsiniz.

### 3.1.3 Scenarios

Senaryolar, olası tehditleri belirlemeye yönelik kuralları tanımlamak için kullanılır. CrowdSec, kaba kuvvet saldırıları ve bağlantı noktası taraması gibi yaygın tehditler için yerleşik senaryolarla birlikte gelir. Ayrıca, ortamınızla ilgili belirli tehditleri belirlemek için özel senaryolar da oluşturabilirsiniz.

### 3.1.4 Collections

Koleksiyonlar, benzer senaryoları birlikte gruplandırmak için kullanılır. Bu, senaryoların yönetimini ve dağıtımını basitleştirmeye yardımcı olur. CrowdSec, SSH ve HTTP gibi yaygın tehdit türleri için yerleşik koleksiyonlarla birlikte gelir. Senaryoları gerektiği gibi gruplandırmak için özel koleksiyonlar da oluşturabilirsiniz.

### 3.1.5 Kısa Özet

(TL;DR) [^2] CrowdSec, siber tehditlere karşı gerçek zamanlı koruma sağlayan, Linux sunucuları için güçlü bir açık kaynaklı güvenlik çözümüdür. CrowdSec, çeşitli kaynaklardan veri toplayarak, verileri ayrıştırarak ve senaryolar uygulayarak potansiyel tehditleri belirleyebilir ve uygun önlemleri alabilir. Ek olarak, toplu zekanın kullanılması, CrowdSec'in potansiyel tehditleri belirleme yeteneğini sürekli olarak geliştirmesine olanak tanıyarak onu herhangi bir Linux sunucusu için etkili bir güvenlik çözümü haline getirir.

## 3.2 Zararlı Trafiği Engelleme Yöntemleri

CrowdSec, siber tehditlere karşı gerçek zamanlı koruma sağlayan, Linux sunucuları için açık kaynaklı bir güvenlik çözümüdür. CrowdSec'in temel özelliklerinden biri, yasaklama, captcha'lar ve özel kararlar dahil olmak üzere çeşitli yöntemler kullanarak yasal olmayan trafiği önleme yeteneğidir. Bu blog gönderisinde, bu yöntemleri ve sunucunuzun güvenliğini artırmak için nasıl kullanılabileceğini ayrıntılı olarak tartışacağız.

### 3.2.1 Ban

![https://tr.wordpress.org/plugins/crowdsec/ (Erişim Tarihi: 11.04.2023)](/images/crowdsec/5-ban.jpg)

Yasaklama, meşru olmayan trafiği önlemenin en basit yöntemidir. CrowdSec potansiyel bir tehdit belirlediğinde, daha fazla erişimi engellemek için rahatsız edici IP adresini engelleyebilir. Bu, IP adresini her yeni bağlantı kurulduğunda kontrol edilen bir kara listeye ekleyerek yapılır. Yasaklama, tekrarlayan suçluların sunucunuza erişmesini önlemenin etkili bir yoludur, ancak aynı zamanda yanlış pozitiflere yol açabilir ve meşru trafiği engelleyebilir.

### 3.2.2 Captcha

![https://tr.wordpress.org/plugins/crowdsec/ (Erişim Tarihi: 11.04.2023)](/images/crowdsec/6-captcha.jpg)

Captcha, otomatik botların sunucunuza erişmesini engellemek için yaygın olarak kullanılan bir yöntemdir. CrowdSec potansiyel bir tehdit belirlediğinde, kullanıcıya bir captcha sorgulaması sunabilir. Bu zorluk tipik olarak belirli görüntüleri tanımlamayı veya bir dizi karakter girmeyi içerir. Kullanıcı captcha sınamasını geçerse, sunucunuza erişebilir. Captcha, otomatik botların sunucunuza erişmesini önlemenin etkili bir yoludur, ancak gelişmiş botlar tarafından da atlanabilir.

### 3.2.3 Custom Decision

CrowdSec, belirli senaryolara göre özel kararlar almanıza da olanak tanır. Örneğin, sürekli olarak sunucunuza erişmeye çalışan bir IP adresi tespit ederseniz, bu IP adresinden gelecek tüm istekleri engellemeye karar verebilirsiniz. Alternatif olarak, kullanıcıyı farklı bir sayfaya yönlendirmeyi veya özel bir mesaj görüntülemeyi seçebilirsiniz. Özel kararlar, CrowdSec'in yanıtını belirli senaryolara uyarlamanıza olanak tanıyarak sunucunuzun güvenliği üzerinde daha fazla kontrol sahibi olmanızı sağlar.

### 3.2.3 Kısa Özet

(TL;DR) [^2] CrowdSec, siber tehditlere karşı gerçek zamanlı koruma sağlayan Linux sunucuları için güçlü bir güvenlik çözümüdür. CrowdSec, yasaklama, captcha'lar ve özel kararlar gibi yöntemleri kullanarak meşru olmayan trafiği önleyebilir ve sunucunuzun güvenliğini artırabilir. İster küçük bir web sitesi ister büyük bir kurumsal uygulama çalıştırıyor olun, CrowdSec sunucunuzu siber tehditlere karşı korumanıza ve kullanıcılarınızın sitenize güvenli bir şekilde erişmesini sağlamanıza yardımcı olabilir.

# 4. Crowdsec İle Tech Demo

{{< youtube kG68PgwOOeU >}}
> <https://www.youtube.com/watch?v=kG68PgwOOeU>
>
> Crowdsec Youtube Kanalı (Erişim Tarihi: 11.04.2023)

Günümüzün dijital çağında, Dağıtılmış Hizmet Reddi (DDoS) saldırıları, işletmeler ve kuruluşlar için ortak bir tehdit haline geldi. Bu saldırılar, bir web sitesini veya uygulamayı trafikle doldurmak, trafiği bunaltmak ve meşru kullanıcılar için kullanılamaz hale getirmek için tasarlanmıştır. Önde gelen bir güvenlik çözümleri sağlayıcısı olan CrowdSec, yakın tarihli bir [blog gönderisinde (Erişim Tarihi: 11.04.2023)](https://www.crowdsec.net/blog/how-to-beat-application-ddos), uygulama DDoS saldırılarını yenmek için bazı etkili stratejiler paylaştı.

Blog gönderisi, kuruluşların karşılaşabileceği farklı uygulama DDoS saldırı türlerini açıklayarak başlıyor. Bunlar, HTTP/S Flood, Slow R/W Atağı (Slowloris) ve Application Layer Atak saldırılarını içerir. Her saldırı türü farklı bir yürütme yöntemine sahip olsa da, hepsi bir uygulamayı aşırı yükleyerek kullanıcılar tarafından kullanılamaz hale getirmeyi amaçlar.

Gönderi daha sonra uygulama DDoS saldırılarını yenmek için bazı etkili teknikleri açıklamaya devam ediyor. En önemli stratejilerden biri, kapsamlı bir DDoS koruma yazılımına sahip olmaktır. Bu, diğer önlemlerin yanı sıra güvenlik duvarlarının, izinsiz giriş tespit sistemlerinin (IDS), izinsiz giriş engelleme sisstemlerinin (IPS) ve yük dengeleyicilerin (Load Balancing) kurulmasını içerir.

Diğer bir etkili yaklaşım, İçerik Dağıtım Ağlarını (CDN'ler) kullanmaktır. CDN'ler, trafiği birden çok sunucu arasında dağıtmaya yardımcı olur ve bu da bir saldırının etkisini azaltmaya yardımcı olabilir. Ek olarak, CDN'ler, meşru kullanıcılar için performansını iyileştirerek bir uygulamanın gecikmesini azaltmaya yardımcı olabilir.

Gönderi ayrıca hız sınırlayıcı tekniklerin uygulanmasını önerir. Bu, belirli bir zaman çerçevesi içinde bir uygulamaya gönderilebilecek trafik miktarına ilişkin sınırlar belirlemeyi içerir. Rate Limiting (Hız sınırlama), bir uygulamaya gönderilebilecek trafik miktarını sınırlayarak uygulama DDoS saldırılarını önlemeye yardımcı olabilir.

Son olarak gönderi, tüm yazılımları güncel tutmanızı önerir. Buna işletim sistemleri, web sunucuları ve uygulamalar dahildir. Yazılımı güncel tutmak, bilinen tüm güvenlik açıklarının yamalanmasını sağlamaya yardımcı olarak saldırı riskini azaltır.

Sonuç olarak, uygulama DDoS saldırıları, işletmeler ve kuruluşlar için önemli bir tehdit olabilir. Ancak doğru stratejiler uygulandığında bu saldırıları yenmek ve uygulamaları ve web sitelerini çevrimiçi tutmak mümkündür. Kuruluşlar, kapsamlı bir DDoS koruma planı uygulayarak, CDN'leri kullanarak, hız sınırlayıcı teknikler uygulayarak ve yazılımları güncel tutarak, bir uygulama DDoS saldırısı riskini önemli ölçüde azaltabilir.

## 4.1 Beta Sürümü ve Geleceği Hakkında

![Crowdsec Beta Duyurusu (Erişim Tarihi: 11.04.2023)](/images/crowdsec/crowdsec-beta.png)

Crowdsec yazılımı yakın zaman önce v1.5 Beta sürümünü[^5] kısıtlı kullanıma açtı. Kısıtlı test için yapmış olduğum başvuruyu kabul etmeleri sebebiyle kendilerine öncelikle teşekkür ediyorum. Mevcut trendlere ve siber güvenlik çözümlerine yönelik artan talebe dayanarak, Crowdsec yazılımının önümüzdeki yıllarda büyümeye ve popülerlik kazanmaya devam edeceğini tahmin ediyorum. Yazılımın, topluluk zekası ve makine öğrenimi algoritmalarını kullanarak tehdit algılama ve hafifletmeye yönelik benzersiz yaklaşımı, sektörde potansiyel olarak devrim yaratabilecek umut verici bir yeniliktir.

[^5]: https://www.crowdsec.net/blog/crowdsec-v1-5-beta

Ayrıca, açık kaynak topluluğundan gelen güçlü destek, Crowdsec'in başarısının arkasındaki itici güç olmuştur. Yazılımın açık kaynak olması ve herkesin kullanması ve katkıda bulunması için ücretsiz olarak erişilebilir olması, yazılımı iyileştirmek ve geliştirmek için sürekli çalışan (ve Beta sürümünde olacağı gibi feedback veren) büyük ve özel bir kullanıcı ve geliştirici topluluğunun gelişmesine yardımcı olmuştur.

Daha fazla insan Crowdsec'in yeteneklerinin farkına vardıkça ve bir açık kaynak projesine katkıda bulunmanın faydalarını gördükçe, bu topluluk desteğinin büyümeye ve güçlenmeye devam etmesini bekliyorum. Topluluğun geri bildirimleri ve katkıları, Crowdsec'in siber güvenlik sektörünün ön saflarında kalmasını sağlayarak yeni özelliklerin ve iyileştirmelerin geliştirilmesine yardımcı olacaktır. Bu nedenle bir sonraki blog yazımda Crowdsec'in Beta sürümünü inceleyip deneyimlerimi paylaşacağım.

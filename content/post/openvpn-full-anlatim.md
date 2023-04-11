---
title: "OpenVPN Derinlemesine Anlatım"
date: 2022-03-27
tags: ["linux", "ssl", "security", "ecc", "elliptic curve", "openvpn", "tls", "aes"]
author: "Wise"
draft: false
---
# Giriş ve Özet

Bugün sizlerle kendi bence son zamanların en önemli yazılımlarından olan OpenVPN ile ilgili derinlemesine bir inceleme yapacağız. Bu incelememizde öncelikle OpenVPN'in ne için kullanıldığından bahsedeceğiz. Ardından programı çalıştırmak için nelere ihtiyaç duyduğumuzu ve ilk çalıştırma öncesi yapılması gerekenleri inceleyeceğiz. Son olarak da bağlantının başlatıldığı ilk andan verinin çözüldüğü son adıma kadar nelerin arka planda döndüğünü anlatmaya çalışacağım. Dolayısıyla yazımız tahminimce 3 bölüm ve gerekirse bir de soru-cevap bölümünden oluşacak. Şimdi kemerlerimizi bağlayalım ve internetin derin ve kasvetli dünyasında bir geziye çıkalım.

## OpenVPN nedir ve ne için kullanılıyor ?

Günümüzde artık internet üzerinden yapılmayan bir iş neredeyse kalmadı. Hatta normalde internet üzerinden olmayan çalışma eylemimiz bile pandemi ve yeni normal nedeniyle evden çalışmaya doğru evrildi. Fakat hem alışık olmadığımız bir çalışma yöntemi olması nedeniyle hem de insanlarımızın teknoloji ile arasının pek iyi olmaması nedeniyle büyük sorunlar yaşandı. İnsanların evlerindeki bilgisayarlardan ofisteki bilgisayarlarına bağlanması gerektiği anlaşılmadan önce bazı firmalar çalışanların evlerine ofis bilgisayarlarını gönderme gibi uçuk fikirler buldu. Bunun ne kadar hatalı bir gidiş yolu olduğunu kısa süre içinde aldıkları geri dönüşlerden çok iyi anladılar. Kısaca elektronik cihazların ofiste kalması ve bir şekilde uzaktan güvenli ve sürdürülebilir bir bağlantı yapılması gerektiği sonunda kabullenildi. Kurumlar daha önceleri de kendilerini böyle ihtiyaçlar içinde buluyordu elbette ama bu derece büyük ölçekli bir durum söz konusu değildi o zamanlar. Pandemi öncesinde PPTP, L2TP, IPSec, IKev2, SSTP ve nihayet OpenVPN gibi çeşitli protokoller kullanıyordu. Bunlar genelde belirli uzun ve havalı kelimelerin kısaltması olup temel mantıkları iki veya daha fazla cihazı birbirine bağlamak ve aynı ağdaymış gibi hareket etmelerini sağlamak üzerinedir. OpenVPN'den önceki protokoller belirli zayıflıkları, yavaşlıkları ve uygulanmasıyla ilgili teknik zorlukları da beraberlerinde getirdikleri için çok bahsetmeyeceğim. OpenVPN sunucu ve istemci rolündeki en az 2 cihazın birbirlerine bağlanması ve  bunu endüstiri standartlarını karşılayacak şekilde yapmasına yarayan protokol ve programın adıdır. Ben uzak masaüstü programı kullanıyorum buna ne gerek var dediğinizi duyar gibiyim. Maalesef o ve onun gibi diğer tüm programlar temelde bu protokolü kullanmak durumunda kalıyorlar. En meşhurlarından olan TeamViewer programında kalkan simgesine veya bağlantı ayrıntılarına bastığınız takdirde OpenVPN protokolünü görebilirsiniz.

## OpenVPN bağlantısı kurmak için nelere ihtiyaç duyarız ?

Öncelikle hem sunucu hem de istemci (bağlanacak cihaz) tarafında OpenVPN'in kurulu olması gerekiyor. Ardından cihazların hangi şartlar altında iletişim kuracaklarını gösterir bir ayar (config) dosyasının düzenlenmesi gerekmektedir. Asıl olay zaten bu config dosyasının üretilmesi ve istemci tarafından kullanılmasıdır. Bu config dosyası sunucu tarafından kullanılan server_config ve istemci tarafından kullanılan client_config olarak ikiye ayrılır.

### Server tarafında tutulan ayar dosyası şu girdileri içermektedir

- `port 1194` OpenVPN bağlantısını yapmak için kendisine hangi port üzerinden bağlantı talebi geleceğini belirtir.
- `proto tcp` Bağlantının TCP veya UDP üzerinden yapılması mümkün. Seçim için girilen ayar girdisi.
- `dev tun` TAP veya TUN arabirimi kullanılabilir. Bunlar sanal arabirimlerdir. TAP layer 2 bir bağlantı kurarken TUN layer 3 bir bağlantı kurar.
- `user nobody` Bağlanan kullanıcıların sunucu üzerinde yetkisiz bir kullanıcıya linklenmesini sağlıyor.
- `group $NOGROUP` Bağlanan kullanıcıların sunucu üzerinde grup olarak da yetkisiz bir gruba linklenmesini sağlıyor.
- `persist-key` Sanal arabirimin oluşturulması ve yeniden başlatılması ile ilgili bir yetkilendirme ayarı
- `persist-tun` Yine aynı şekilde sanal arabirimin oluşturulması ve yeniden başlatılması ile ilgili bir yetkilendirme ayarı
- `keepalive 10 120` Kaç adet bağlantının aktif tutulacağını ve ne kadar süre iletişim kurulmaz ise aktif bağlantının sonlandırılacağı ile ilgili bir ayar
- `ifconfig-pool-persist ipp.txt` OpenVPN tarafından istemcilere sanal ağda verilen IP adreslerinin tutulması ve tekrar bağlandıkları takdirde aynı adreslerin verilmesi için bir ayar
- `push "dhcp-option DNS 1.1.1.1"` Sunucunun ağa çıkarken kullanması için bir DNS ayarı
- `compress` Sıkıştırma seçeneklerinin ayarlandığı kısım
- `dh none` Diffie-Hellman'ın açılıp kapatılması ile ilgili bir ayar
- `ecdh-curve` Eğer Elliptik Eğri Diffie-Hellman kullanıyor iseniz yanında seçmeniz gereken eğrinin ayarlandığı ayar
- `dh dh.pem` Diffie-Hellman kullanıyor iseniz önceden oluşturmanız gereken PEM dosyasının konumunu belirten ayar
- `tls-crypt tls-crypt.key` TLS katmanının pre-shared master öncesinde dahi şifrelenmesi için gerekli ayar
- `tls-auth tls-auth.key 0` TLS katmanının pre-handshake aşamasında şifrelenmesinin de ötesinde tarafların da doğrulanmasını sağlayan ayar
- `crl-verify crl.pem` Üretilen sertifikaların revoke edilip edilmediğinin CRL listesi üzerinden kontrol edilmesine yarayan ayar
- `ca ca.crt` Üretilen sertifikaya ait sertifika otoritesinin sertifikasının konumunu bildiren bir ayar
- `cert $SERVER_NAME.crt` Sunucunun sertifikasının konumunu bildiren bir ayar
- `key $SERVER_NAME.key` Sunucunun sertifikasının yanında yine gerekli olan asimetrik secret keyinin konumunu bildiren bir ayar
- `auth $HMAC_ALG` Veri kanalı ve gerekirse `tls-auth` için hangi özet algoritmasının kullanılacağını bildiren bir ayar
- `cipher $CIPHER` Veri kanalı için hangi şifreleme algoritmasının kullanılacağını bildiren bir ayar
- `ncp-ciphers $CIPHER` Sunucunun kullanabileceği şifreleme algoritmalarını bildiren bir ayar
- `tls-server` Sunucunun TLS kanalını kullanmasını söyleyen bir ayar
- `tls-version-min 1.2` TLS kanalında kullanılması için en düşük versiyonu bildiren bir ayar
- `tls-cipher $CC_CIPHER` Veri kanalından hariç TLS katmanında da şifreleme kullanılıyor bu da kontrol kanalı şifrelemesini bildiren ayar
- `client-config-dir /etc/openvpn/ccd` İstemci ayar dosyalarının tutulduğu konumu bildiren ayar
- `status /var/log/openvpn/status.log` Durum raporlarının yazılacağı konumu ve log dosyalarının tutulduğu konumu bildiren ayar
- `verb 3` Verbose kelimesinin kısaltılmışı olan bu ayar ne kadar detaylı durum raporu verileceğinin ayarıdır.

### İstemci tarafında tutulan ayar dosyası şu girdileri içermektedir

- `client` İlgili cihazın istemci rolünde olduğunu belirtiyor
- `proto tcp-client` Protokol olarak TCP'nin kullanılacağını bildiriyor
- `remote $IP $PORT` Bağlanılacak sunucu(ların) IP adresinin ve Port numarasının ayarladığı kısım
- `dev tun` TUN/TAP arabirimlerinden hangisinin kullanılacağını ayarlıyor
- `resolv-retry infinite` Eğer IP veya DNS nedeniyle adres çözümlemesi gecikir ise ne kadar süre ile bekleyeceğini söylüyoruz
- `nobind` Lokaldeki herhangi bir adrese bağlanılmamasını bildiren ayar
- `persist-key` Yeniden başlatma durumunda anahtar dosyalarının ek bir yetkiye gerek kalmadan okunabilmesini yarar
- `persist-tun` Aynı şekilde yeniden başlatma durumunda TUN/TAP arabiriminin yetkiye gerek kalmadan uyandırılabilmesine yarar
- `remote-cert-tls server` Bağlanılan sunucunun sertifikasını TLS katmanında doğrulanmasını sağlar
- `verify-x509-name $SERVER_NAME name` Sunucunun geri döneceği sertifikasındaki ismi ve sunucunun isminin ne olması gerektiğini bildiren komut
- `auth $HMAC_ALG` Doğrulama için hangi algoritmanın kullanılacağını bildiren komut
- `auth-nocache` Oturum açmak için gerekli parolayı önbelleğe almaz
- `cipher $CIPHER` Şifreleme için kullanılacak algoritmayı seçmeye yarayan komut
- `tls-client` TLS iletişimi sırasında TLS'yi etkinleştirir ve istemci rolünü üstlenir
- `tls-version-min 1.2` En düşük TLS versiyonunu ayarlar
- `tls-cipher $CC_CIPHER` TLS kontrol kanalında kullanılacak şifreleme algoritmasını seçer
- `ignore-unknown-option block-outside-dns` Bilinmeyen DNS adreslerinin kullanılmasını engeller
- `setenv opt block-outside-dns` Windows 10 için DNS sızıntılarını engeller
- `verb 3` Rapor verme derecesini belirler
- `compress` Sıkıştırma algoritması ayarları burada bildirilir
- `"<ca>/etc/openvpn/easy-rsa/pki/ca.crt</ca>"` Beklenilen sunucu sertifika otoritesi dosyasının Hard-Coded gömülmesi
- `"<cert>/etc/openvpn/easy-rsa/pki/issued/$CLIENT.crt</cert>"` İstemci sertifika dosyasının Hard-Coded gömülmesi
- `"<key>/etc/openvpn/easy-rsa/pki/private/$CLIENT.key</key>"` İstemci asimetrik secret keyinin Hard-Coded gömülmesi
- `"<tls-crypt>/etc/openvpn/tls-crypt.key</tls-crypt>"` TLS crypt için key dosyasının belirtilmesi
- `"<tls-auth>/etc/openvpn/tls-auth.key</tls-auth>"` TLS auth için key dosyasının belirtilmesi
- `key-direction 1` TLS katmanı şifrelenmesi için istemci ve sunucuya rol atıyor (0 ve 1 şeklinde)

Bu ayarları ve daha bir çoğunun ayrıntılı dökümantasyonunu [OpenVPN](https://openvpn.net/community-resources/reference-manual-for-openvpn-2-4/) web sayfasında bulabilirsiniz.

## OpenVPN bağlantısı kurulurken neler oluyor ?

OpenVPN ile bağlantı kurduğum her zaman kendimi Yıldız Filosu planlarını kaçıran R2-D2 gibi hissediyorum. İnsanlar kendilerini her zaman için derinlemesine bir inceleme içerisinde bulmak istemiyorlar ve birilerinin onlara neyin nasıl döndüğünü açıklamalarını isteyebiliyorlar. Benim bu yazıyı kaleme alma amacımda aslında bu soruyu kendime sormuş olmam ve cevabını almak için çok çaba sarfetmiş olmam. Sizin de bu kadar uğraşmanızı istemem fakat size hemencecik bunu yükle gerisini düşünme o iş bende de diyemem. Başta söz verdiğim gibi derinlemesine bir şekilde bu süreci sizlere anlatacağım ve kararı size bırakacağım. Bir OpenVPN bağlantısında artısıyla eksisiyle (benim şu ana kadar çözebildiğim şekliyle) süreç şöyle işliyor. Önce bir TCP/UDP bağlantısı kuruyorsunuz. TCP kullanan her uygulama gibi bir süreç yürütüyorsunuz ve ardından TLS katmanına geçiyorsunuz. TLS katmanında el sıkışma (handshake) ve bazı kimlik doğrulama işlemleri yapıyorsunuz. Bu katmana aynı zamanda kontrol kanalı da deniliyor. Ardından belirli bir iletişim tutturulmuş oluyor ve veri kanalına geçiliyor. Veri veya data kanalında bu sefer gönderilecek veri paketlerinin şifrelenmesi ve çözülmesi süreci başlıyor. Bunun için yine cihazlar birbirleri ile konuşuyor ve belirli ortak şartlar altında veriler gönderilmeye başlanıyor. Kısaca bu şekilde anlattığım sürecin sonunda 0'dan başlattığımız iletişim bize güvenli ve istediğimiz şekilde verilerin ulaşması ile son buluyor veya açık tutulan bağlantı üzerinden bu sefer tersine bir yolla yeniden istekler iletiliyor. Böylece iç içe borular gibi bir sistem ortaya çıkıyor. Yazı için gerekli olan tek önemli şeyi buraya yazmak gerekirse eğer:

- `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P512` şeklinde olacaktır.
  - Burada `TLS` girdisi kontrol kanalının TLS katmanı üzerinden yürütüleceğini belirtir. Diğer alternatifler `SSL` veya `NULL`'dur.
  - `ECDHE` girdisi Elliptik Diffie-Hellman algoritması kullanılarak ilk ön-anahtarın üretileceğini belirtir. Diğer alternatifler `DHE`, `DH` veya kullanmamaktır.
  - `ECDSA` verisi karşılıklı kimlik doğrulama ve asimetrik anahtar için Elliptik Dijital İmza Sertifikası Algoritmasının kullanılacağını belirtir. Diğer kullanılabilir alternatif `RSA`'dır. Diğerlerini saymaya gerek bile yok.
  - `AES_256_GCM` veri kanalında kullanılacak şifreleme algoritmasının belirtir. Diğer alternatifler `AES-128-CBC`, `AES-128-GCM` ve `AES-256-CBC`'dir
  - `SHA384` kullanılacak özet algoritmasını belirtir. Diğer alternatif `SHA256`'dır.
  - `P512` ise kullanılacak elliptik eğrinin Prime-512 adlı eğri olarak seçilmesini sağlar. Diğer alternatifler `P-256` ve `P-384`'dür.

### TCP bağlantısının kurulması süreci

Şimdi kafanızda sürecin yaklaşık bir resmi oluştu ise başlangıcı TCP sürecinin anlatımıyla yapıyorum. Olayımızda bir istemci ve bir sunucunun olduğunu ve bağlantının sadece bu ikisinden ibaret olduğunu düşünelim. İstemci bağlanmak istediği sunucuya bir SYN (m) paketi gönderir. Sunucu ise buna cevap olarak aynı port üzerinden bir SYN (n) paketi ve ACK (m+1) paket gönderir. Bunu alan istemci de cevap olarak ACK (n+1) şeklinde dönüş yapar ve 3'lü TCP el sıkışması veya 3 Way TCP handshake gerçekleşmiş olur. Böylece belirtilen port üzerinden istemci ve sunucu arasında açık bir kanalımız oluştu.

![https://blog.shiftasia.com/what-happen-when-access-website (Erişim Tarihi: 08.04.2023)](/images/openvpn-full/TCP-Handshake.jpg)

---
![https://www.netscout.com/blog/asert/ddos-attacks-ssl-something-old-something-new (Erişim Tarihi: 08.04.2023)](/images/openvpn-full/TCP-Handshake-2.png)

Fotoğraflarda da görüleceği üzere eğer süreç sorunsuz işler ise 3 adımda iletişim kurulabiliyor. Fakat neden 3 adımda bu işi yapıyoruz daha kısa şekilde olmaz mı derseniz size (şimdilik) hayır olmaz full-duplex bir iletişim için her iki tarafın da SYN ve ACK paketlerini göndermesi gerekiyor derim. İleride belki farklı yollarını da anlatırım ama şimdilik böyle. Zaten işin TCP/UDP kısmı her zaman için kısa ve basittir.

### TLS katmanındaki işletilen süreç

TCP üzerinden bir iletişim kurulmasının ardından yine muhabbeti başka bir aşamaya taşıyan kişi istemci oluyor. Her zaman için istemciler sunucudan bir şeyler talep eder veya bir cevap ister. Sunucular genel olarak kendilerine gelmeyen bir isteği cevapladığı çok görülmemiştir. Önce talep sonra arz ilkesine göre süreç ilerler. Evet, taraflar TLS katmanındalar şimdi. İstemci sunucuya önce bir merhaba diyor. Şaka değil gerçek. İstemci tarafından gönderilen ilk pakete `Client-Hello` paketi denir. Bu paketin yanında (süreci hızlandırmak adına) desteklediği şifreleme algoritmalarını belirten `Supported-Chipers` paketi, istemci tarafından rastgele üretilmiş bir sayı, aynı IP adresinde birden fazla hizmet çalıştırılıyor ise bir `SNI` sunucu adı indikatörü ve yine gerekiyor ise oturum ID'si gönderilir. Sunucunun buna cevabı ise öncelikle kibar bir merhaba demek oluyor. Çünkü sunucunun cevaben gönderdiği ilk pakete de `Server-Hello` paketi denir. Bu paketin yanında sunucu sertifikasını, kendi desteklediği şifreleme algoritmalarını ve seçtiği algoritmayı belirten `Selected-Chiper` paketi, kendisinin ürettiği rastgele bir sayıyı, gerekirse Oturum ID'sini ve aynı IP üzerinden birden fazla istemci bağlanıyor ise buna ilişkin SNI benzeri bir ID'yi gönderir. İstemci öncelikle iletişime başladığı tarafından gerçekten beklediği kişi olup olmadığını sunucu sertifikası ile doğrular. Ayrıca bazı durumlarda da sunucu istemcinin beklediği istemcilerden biri olup olmadığını yine sertifika ile doğrular. Eğer bu karşılıklı doğrulama (mutual-authentication) süreci olumlu sonuçlanır ise bir sonraki aşamaya geçilir. Anahtar üretim ve değişim süreci tetiklenmiş olur. Bu aşamda yine istemci devreye girer ve güvensiz önkabul edilen bu iletişim sırasında belirledikleri algoritma ile anahtar değiştirmek istediğini söyler. Taraflar Diffie-Hellman veya ECDHE ile bir önanahtar oluşturmaya başlarlar. Bunun için istemci ve sunucu tarafından ön-sırlar paylaşılır. Bir takım matematiksel işlemler yapılarak bulunan cevaplar karşıya gönderilir ve tekrar matematiksel işlemler yapılarak aynı sonuca ulaşılır. İşte ulaşılan sonuç aralarında güvenli bir şekilde oluşturdukları ilk ön-anahtar oluyor. Bundan sonra belirledikleri şifreleme algoritması ile
iletişime geçmek için kontrol kanalından hariç bir veri kanalı oluşturulur ve süreç oradan devam eder.

![https://www.researchgate.net/publication/298065605_A_multi-level_framework_to_identify_HTTPS_services (Erişim Tarihi: 08.04.2023)](/images/openvpn-full/TLS-Handshake.png)

---
![](/images/openvpn-full/TLS-Handshake-2.png)

Fotoğraflarda da görülebileceği üzere süreç bir web sayfasına bağlanılırken yaşanan süreçle neredeyse aynı. Sadece ihtiyaçlara göre belirli aşamalar ekleniyor, çıkarılıyor veya değiştiriliyor. Örneğin İleri Seviye Gizlilik anlamına gelen PFS gereğince taraflar ön-anahtarı sunucunun asimetrik anahtarı ile iletmiyor. Çünkü bu durumda her oturum için aynı anahtar kullanılacağı için verilerin depolanıp daha sonra anahtar açığa çıktığı bir gün beklenerek veriler geçmişe dönük okunabilir bir hale gelecektir. Bu yüzden bu değişiklik yapıldı. Yine sıfır güven tehdit modeli gereğince her bir katmanın ve sürecin bir diğerinin işini doğru yapacağına güvenmeden süreci ilerletmesini istiyorum. Bu yüzden TLS katmanındaki o ilk iletişim anında dahi paketlerin `tls-auth` özelliği gereğince şifrelenmesini ve gelen-giden verilerin bütünlüğünün doğrulanmasını istiyoruz. Daha ilk merhaba dediğiniz andan itibaren üçüncü kişiler sizin ne konuştuğunuzu hangi aşamada olduğunuzu anlayamayacaklardır. Bunun için önceden belirlenmiş bir anahtar/anahtarlar ile ilk iletişim başlatılır ve gerekirse belirli aralıklarla bu anahtarlar yenilenir. Böylece TLS katmanında ilk ön-anahtar oluşturulana kadar dahi gizlilikten ödün verilmemiş ve yetkisiz kişilerce boşuna tarafik yaratılmamış olur.

### Veri katmanında işleyen süreç

Eğer tüm bu süreç başarılı bir şekilde tamamlanmış ve veri kanalına geçilebildiyse eğer artık işin en güzel kısmına gelmiş bulunuyorsunuz. Veriler AES şifreleme methodu ile şifrelenecek. Şifreleme sırasında seçiminize göre CBC-GCM counter moduna göre tablolar karıştırılacak ve bu süreçte seçiminize göre 128 veya 256 bit uzunluğunda şifreleme anahtarı kullanılacak. Tabi ne hangisini seçerseniz seçin şifreleme blok uzunluğu 128 bit olucak. Değişen sadece şifreleme anahtarı uzunluğu. Benim bu anlatımım için seçmiş olduğum AES-256-GCM bir AEAD şifreleme türüdür. Diğer kanallardan ve süreçlerden bağımsız olarak gönderdiği verileri belirli bir aşamada özetini çıkartır ve özeti ile birlikte gönderir. Böylece 'Authentication Encryption with associated data' anlamına gelen AEAD'de doğrulama ve şifreleme işlevleri yerine getirilmiş oluyor. Burada bir ayrıma gidilmesini gerektirecek şöyle bir sorun mevcuttur. Şifreleme ve Özet alma algoritmalarını hangi aşamada ve sırayla kullanacağız.

||||
|:---:|:---:|:---:|
| ![Encrypt-then-MAC (EtM)](/images/openvpn-full/EtM.png) | ![Encrypt-and-MAC (E-and-M)](/images/openvpn-full/EaM.png) | ![MAC-then-Encrypt (MtE)](/images/openvpn-full/MtE.png) |

> https://en.wikipedia.org/wiki/Authenticated_encryption (Erişim Tarihi: 08.04.2023)

- Birinci yaklaşım olan EtM'ye göre veri önce şifrelenir ardından başka bir anahtar ile özeti sonucu şifrelenir ve ortaya çıkan sonuç bloklar halinde birlikte gönderilir. Bunu kullanan gerçek dünya çözümlerine bakacak olursak IPSec protokolü ilk akla gelen olacaktır. Bu, AE'de en yüksek güvenlik tanımına ulaşabilen tek yöntemdir, ancak bu ancak kullanılan MAC algoritmasının bozulma içermediği veya henüz kırılmadığı takdirde elde edilebilir. SSHv2 için de çeşitli EtM şifre takımları mevcuttur. Ancak veri ve özet için anahtar ayrımının zorunlu olduğunu unutmayın (şifreleme ve anahtarlı karma için farklı anahtarlar kullanılmalıdır), aksi takdirde kullanılan belirli şifreleme yöntemine ve karma işlevine bağlı olarak potansiyel olarak güvensiz bir sonuç elde edebilirsiniz.

- İkinci yaklaşım olan E&M'ye göre düz metin olan veri şifrelenir ve yanına düz metin verinin şifrelenmemiz halinin özeti eklenir. Burada sadece bir anahtar kullanılmış olmasına rağmen aynı veriye ait iki farklı sonucun (şifreleme sonucu ve özet sonucu) olması güvenliğin yeterince iyi olmadığını açıkca gözler önüne sermektedir. Bu sistemi kullanan gerçek dünya çözümü olarak SSH'ın ilk versiyonlarını örnek gösterebiliriz. Bunu geliştirmek için ayrıca gönderilen özet dosyasını da aynı anahtar ile şifreleme gibi yöntemler denenmiştir.

- Üçüncü ve bildiğim son yaklaşım olan MtE'ye göre düz metine dayalı olarak bir özet dosyası üretilir. Ardından düz metin ve özet dosyası birlikteyken anahtar ile şifrelenir.  Şifreli metin ve şifreli özet dosyası birlikte gönderilir. Bunu kullanan gerçek dünya çözümlerine bakacak olursak ilk ve en önemlisi SSL/TLS uygulamalarıdır. SSL/TLS uygulamalarının kendi içlerinde ne kadar güvenilir ve sürdürülebilir olduklarını hepimiz biliyoruz. Bunun ötesinde de güvenliği artırmak adına yıllar içersinde `MAC-then-pad-then-encrypt` gibi geliştirmeler yapıldı. Bu geliştirmeye göre önce düz metinin özeti alınır ardından blok boyutuna kadar doldurulur ve ardından şifreleme işlemi yapılır. Böylece daha da güvenilir bir şifreleme sonucu oluşur. Ama doldurma mekanizmasının belirli hatalar yapması durumunda Padding Oracle gibi saldırılara neden olduğu durumlar mevcuttur.

![https://community.openvpn.net/openvpn/wiki/Gigabit_Networks_Linux (Erişim Tarihi: 08.04.2023)](/images/openvpn-full/TAP-TUN.png)

Kullanılacak AEAD yaklaşımı da seçildikten sonra TAP veya TUN kullanımına göre yukarıdaki grafikte görülen yol izlenir. Bu yola göre kullanıcı alanında yapılan/yapılmak istenen eylem çekirdek (kernel) seviyesinde TAP/TUN adaptörlerine gider. Bu adaptörler çekirdek seviyesinde bulunmaları nedeniyle çok hızlı bir şekilde işlem yaparlar. Ardından sanal adaptörler ilgili kütüphane ile gerekli şifrelemeyi yapar, gerekirse özeti ekler ve paket boyutu ayarı yapar. Ardından sunucu Ethernet arayüzü üzerinden istemcinin Ethernet arayüzüne paketleri sırayla gönderir. Bunu alan istemci ise paketleri yeniden ayarlar, düzenler gerekirse birleştirir ve gerekli kütüphaneler ile şifresini çözer. Şifresini çözdükten sonra bunu sanal adaptör aracılığı ile istemcini son kullanıcısına iletir. Böylece tüm bu matematiksel işlemler, uğraşlar sonucunda birkaç çevrim neticesinde kullanıcı istediği içeriğe ulaşmış oldu. Anlatması oldukça uzun ama kullanması çok kolay sevgili okuyucular. Sadece GitHub sayfama girik ilgili [script sayfasını](https://github.com/wiseweb-works/openvpn-most-secure-install/) ziyaret etmeniz yeterlidir. İlgili script tüm bu ayarlamaları interaktif olarak sizin yerinize yapmaktadır. Size de arkanıza yaslanıp keyfini çıkarmak kalıyor.

# SSS ve Son

Bana [mail](mailto:wisewebworks@outlook.com) yoluyla, [Fosstodon](https://fosstodon.org/@wise) üzerinden veya [GitHub](https://github.com/wiseweb-works) üzerinden gelen soruları zaman zaman buraya eklemeye çalışacağım. Böylece tarihsel olarak da hangi tarihte ne gibi sorular olmuş veya çözümü mevcut mu gibi düşüncelere kapılmadan direk sonuca ulaşabileceksiniz. Bunun haricinde de teknik dökümanı değiştirmeden ekstra açıklama gerektiren sorular gelirse onları da bu kısma almayı düşünüyorum.

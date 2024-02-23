---
title: "Eski Maclere OpenCore ile Desteklenmeyen Sürümlerin Kurulumu "
date: 2023-10-08
tags: ["Macbook", "MacOS", "OpenCore", "Legacy", "Sonoma"]
author: "Wise"
draft: false
---
# OpenCore Legacy Mac Yama Aracı

![OpenCore Logosu](/images/opencore-legacy/1.jpeg)
**Rehber son güncellenme: 08.10.2023 (Sonoma desteği dahil)**

Bu rehber, OpenCore Legacy Patcher (bundan sonra OCLP olarak anılacaktır) programını kullanma konusunda size yol gösterecektir. Geliştirici katkıları ve kullanıcı raporlarının yanı sıra kişisel deneyimlerdeki araştırmalara dayanmaktadır. OCLP kullanılarak macOS 14 Sonoma ilginç bir şekilde 12 yıllık bir Mac'e başarıyla yüklendi. Söz konusu Mac, desteği 2017'de macOS 10.13 High Sierra ile sonlanan [2011 ortası iMac](https://support.apple.com/kb/SP689?locale=en_US&viewlocale=en_US) 27 inçtir.

![Kaynak: macsofa.net](/images/opencore-legacy/2.jpeg)

## OCLP ne yapabilir?

OCLP 2021 yılında ilk defa geliştirilmeye başlanmış olup bu programın yardımıyla, 2006'dan itibaren artık Apple tarafından desteklenmeyen eski Mac'lere (an itibariyle 83 tane cihaz) Apple’ın sunduğu daha yeni işletim sistemi versiyonlarını yükleyebilirsiniz. Bunlar [macOS 11 Big Sur](https://support.apple.com/tr-tr/HT211238), [macOS 12 Monterey](https://support.apple.com/tr-tr/HT212551), [macOS 13 Ventura](https://support.apple.com/tr-tr/HT213264), [macOS 14 Sonoma](https://www.apple.com/tr/macos/sonoma/) ve [semantik sürüm numaralandırması sistemine göre](https://semver.org/lang/tr/) alt sürüm güncellemeleridir. Kural olarak, başarıyla OCLP ile yamalanmış bir sistem ile yerel olarak çalışan (yani Apple’ın resmi olarak desteklediği) bir sistem arasında herhangi bir fark görmezsiniz.

OCLP web sitesine [buradan](https://dortania.github.io/OpenCore-Legacy-Patcher) ulaşabilirsiniz ve rehber süresince ihtiyacımız olan programı [bu web sitesinden](https://github.com/dortania/OpenCore-Legacy-Patcher) ücretsiz olarak indirebilirsiniz. Yeni sistemi Mac'inizin dahili sabit sürücüsüne veya harici bir sabit sürücüye kurabilirsiniz.

## OCLP nasıl çalışır?

Basitçe açıklamak gerekirse, OCLP değiştirilmiş bir önyükleme bölümü oluşturur ve Mac'i sisteme karşı simule ederek (desteklenen bir Mac'miş gibi davranarak) sistemin başlatılmasını sağlar. OCLP kendi işletim sistemini değil, orijinal macOS'u başlatır. Bu amaçla OCLP, macOS önyükleyicisine açık kaynaklı bir alternatif olan OpenCore önyükleyicisini kullanır. Eğer Hackintosh/Ryzentosh terimlerini daha önce duyduysanız OpenCore önyükleyicisine aşinasınızdır.

Bunu yapmak için OCLP, OpenCore yapılandırmasındaki belirli bileşenleri değiştirir ve karşılık gelen bir yapılandırma dosyası (OpenCore config) oluşturur. Değiştirilen bileşenler RAM'deki bir referans dosyasında saklanır. İlgili bir config dosyası kullanılarak bu referans dosyası, Mac'in her başlatılışında oluşturulur.

İlgili bileşenlere erişmek için bu referanslardan bazıları kök ayrıcalıklarına (root yetkisine) ihtiyaç duyar. Bu nedenle yazının ilerleyen safalarında belirtilen kök yaması (root patch), bileşenler veya donanım değiştiğinde referans dosyasını yeniler. Bu, gerekli bileşenlerin doğru şekilde adreslemesini ve sistemin hatasız çalışmasını sağlar.

Tüm bu işlemler yama yapıldıktan sonra arka planda otomatik olarak çalışır.

## OCLP'yi kim geliştirir?

OCLP, Hackintosh topluluğundan bir grup özel programcı olan [Dortania](https://github.com/dortania) tarafından geliştirilmekte ve [Mykola Grymalyuk](https://khronokernel.github.io/about-me) yani Khronokernel tarafından yönetilmektedir.

Amaçları erken eskime veya [planlı eskitme](https://tr.wikipedia.org/wiki/Planl%C4%B1_eskime) yerine eski Mac'lerin mümkün olduğu kadar uzun süre kullanılabilmesini sağlamaktır. Yamalı yeni sistem sayesinde veri güvenliği ve programlar güncel tutulmaya devam edilebilmektedir.

Bu sürdürülebilirliğin yanı sıra OCLP'nin güvenilirliği de geliştiriciler için önemlidir, bu nedenle kaynak kodu Github üzerinden kullanıcılara açıktır. Buna göre programda tespit edilecek güvenlik açıkları topluluk kullanıcıları tarafından hemen fark edilebilecek ve gerekli düzeltmeler yine kullanıcılar/proje yöneticileri tarafından yayımlanacaktır. Fakat şimdiye kadar o ölçekte büyük bir açık söz konusu olmamıştır.

OCLP'nin teknolojik kökleri [Collin Mistr'a](https://www.youtube.com/@dosdude1/videos) dayanmaktadır. Yama aracı macOS 10.12 Sierra'nın yüklenmesine olanak tanıyan [dosdude1](https://dosdude1.com/software.html) adlı projesi temel alınarak geliştirildi.

## OCLP karmaşık mı?

OCLP'yi kullanmak bu talimatlarda göründüğünden çok daha az karmaşıktır. Olası sorunları önlemek için rehberin uzun versiyonunu okumanızı öneririm. Burada açıklanan adımları takip ettiğiniz takdirde, derinlemesine teknik bilgiye sahip olmayan bir kullanıcı bile başarılı olacaktır. Daha kolay yönlendirme için kısa ve uzun versiyonların yapısı aynıdır.

### KISA VERSİYON

Aşağıdaki süreç macOS 11 Big Sur, macOS 12 Monterey ve macOS 13 Ventura sistemlerine yama uygulanması için geçerlidir. MacOS 14 Sonoma'ya yama uygulanması uzun versiyonun IV. Bölümünde anlatılmaktadır.

#### I. ARAŞTIRMA

Mac'inizin OCLP ile uyumluluğunu [buradan](https://dortania.github.io/OpenCore-Legacy-Patcher/MODELS.html) kontrol edin. Ayrıca oradaki cihaz listesinin yorum sütununda yer alan kısıtlamalara ve önerilere de dikkat edin.

![Destek](/images/opencore-legacy/3.png)

#### II. VERİ GÜVENLİĞİ

Daha sonra oluşabilecek sistem veya program sorunlarına karşı önerim en az iki güncel yedek bulunmalıdır. Biri Time Machine yöntemi kullanılarak alınacak olan yedek olup diğeri ise dosyalarınızın harici bir hard diske sürükle bırak yöntemi ile yedeklenmesi şeklindedir. 

#### III. GEREKLİLİKLER

İstediğiniz sistemin yükleyicisine, yani [güncel OCLP sürümüne](https://github.com/dortania/OpenCore-Legacy-Patcher/releases/latest) ihtiyacınız var. OCLP destekli bir Mac, fare, klavye, en az 16 GB belleğe sahip bir yardımcı sürücü (usb disk yeterlidir) veya harici sabit disk, gerekirse yeni sisteminizin hedef sürücüsü olmalıdır. Tüm cihazları USB üzerinden bağlamanız gerekir. Güvenli tarafta olmak için, güç kaynağını kullanarak dizüstü bilgisayar çalıştırmalısınız.

#### IV. KURULUM

Aşağıdaki bölümlerde yeni sistemin kurulumu adım adım anlatılmaktadır.

1\. Yardımcı cihazın hazırlanması
![Disk İzlencesi](/images/opencore-legacy/4.jpeg)

➝ Yardımcı cihazı (örn. USB çubuğu) Mac'e bağlayın.

➝ Disk İzlencesi Programını açın.

➝ “Görüntüle”ye ve ardından “Tüm cihazları göster”e tıklayın.

➝ Yardımcı cihaz simgesini seçin; simgesi sola hizalanmıştır.
![Usb Formatlama](/images/opencore-legacy/5.jpeg)

➝ “Sil”e tıklayın.

➝ İsim aynı kalabilir.

➝ Format olarak “Mac OS Genişletilmiş (Günlüklü)” seçeneğini seçin.

➝ Şema olarak “GUID Bölümleme Tablosu”nu seçin.

➝ “Sil”e tıklayın; komut tüm verileri siler.

➝ “Bitti”ye tıklayın.

2\. Sistem kurucunun yardımcı cihaza kurulması

![OCLP 1.0.1](/images/opencore-legacy/6.png)

➝ “MacOS Yükleyici Oluştur”a tıklayın.

➝ “MacOS Installer'ı İndir” seçeneğine tıklayın.

➝ macOS'u seçin.

|||
|:---:|:---:|
![1](/images/opencore-legacy/7.png) | ![2](/images/opencore-legacy/8.png)

➝ İndirdikten sonra otomatik doğrulamayı bekleyin.

➝ Yönetici şifresini girin.

➝ “MacOS Yükleyici Oluşturulsun mu?” penceresinde “Evet”e tıklayın.

➝ “Yerel macOS Yükleyiciyi Seç” penceresinde yükleyiciyi seçin.

![3](/images/opencore-legacy/9.png)

➝ Yardımcı sürücüyü seçin (Mac'inizin dahili HD'si olarak disk0'ı değil).

➝ “Onay” penceresinde “Evet”e tıklayın; komut tüm verileri siler.

➝ Yönetici şifresini girin.

➝ “MacOS yükleyici başarıyla oluşturuldu!” penceresinde “Evet”e tıklayın.

➝ “OpenCore yapılandırmanızın oluşturulması tamamlandı!” penceresinde “Diske yükle” seçeneğine tıklayın.

➝ Yardımcı cihazı seçin (disk0'ı değil).

➝ Yönetici şifresini girin.

➝ “Uygulamak için yeniden başlatılsın mı?” penceresinde alt tuşunu basılı tutarak “Yeniden Başlat”a tıklayın.

➝ “Yeniden Başlat”a tıklayın ve alt tuşunu basılı tutmaya devam edin.

➝ Başlangıç yöneticisinde “EFI boot”u seçin ve alt tuşunu basılı tutmaya devam edin.

➝ Bir sonraki başlangıç ekranında alt tuşunu basılı tutarken “MacOS xyz'yi yükle” seçeneğini seçin ve alt tuşunu bırakın.

➝ Kurtarma bölümü (macOS Recovery) görüntülenir.

3\. Sistemin kurulumu

“MacOS Recovery” kurtarma bölümünde yükseltme veya temiz kurulum gerçekleştirebilirsiniz. Ancak başka bir program zaten bir sistem yaması oluşturduysa temiz kurulum yapmanız gerekir. Harici bir HD'ye kurulum yaparken lütfen uzun versiyondaki açıklama bölümlerine de dikkat edin.

a) Yükseltme

➝ Aşağıdaki düğmelere tıklayın:

➝ “MacOS xyz'yi (hangi sürümü indirdiyseniz) yükle”

➝ “Devam Et”

➝ “Devam Et”

➝ “Kabul et”

➝ “Kabul et”

➝ Hedef sürücüyü seçin.

➝ “Devam Et”e tıklayın.

➝ Kurulum birkaç yeniden başlatmayla başlar (30 dk kadar ve daha uzun).
Bu süre zarfında herhangi bir şekilde Usb'yi çıkarmayın ve yeniden başlatıldığı zaman çıkan önyükleme seçeneklerine karışmayın. Her şey otomatik olarak ilerleyecektir. Siz sadece sizden bir bilgi girmeniz gerektiği takdirde kuruluma müdahale etmelisiniz.

b) Temiz Kurulum

➝ “Disk Yardımcı Programı”na tıklayın.

➝ “Devam Et”e tıklayın.

➝ Hedef sürücüyü seçin.

➝ “Sil”e tıklayın.

➝ Bir isim verin.

➝ Format olarak APFS'yi seçin.

➝ Şema olarak GUID bölümleme tablosunu seçin.

➝ “Sil”e tıklayın; komut tüm verileri siler.

➝ “Bitti”ye tıklayın.

➝ Aşağıdaki düğmelere tıklayın:

➝ “MacOS xyz'yi yükle”

➝ “Devam Et”

➝ “Devam Et”

➝ “Kabul et”

➝ “Kabul et”

➝ Hedef sürücüyü seçin.

➝ “Devam Et”e tıklayın.

➝ Kurulum birkaç yeniden başlatmayla başlar (30 dk kadar ve daha uzun).

➝ Bu süre zarfında herhangi bir şekilde Usb'yi çıkarmayın ve yeniden başlatıldığı zaman çıkan önyükleme seçeneklerine karışmayın. Her şey otomatik olarak ilerleyecektir. 

#### V. KURULUMDAN SONRA

![Post-Install](/images/opencore-legacy/10.jpeg)

➝ Sürücülerin güncel olup olmadığını kontrol edin ve OCLP'yi açın.

➝ “Yükleme Sonrası Kök Yaması”na tıklayın.

![Root Patch](/images/opencore-legacy/11.jpeg)

➝ “Tüm geçerli yamalar zaten yüklü” mesajını görürseniz başka bir şey yapmanıza gerek yoktur.

➝ “Sistem için mevcut yamalar” mesajında “Kök Yamalamayı Başlat” seçeneğine tıklayın.

➝ “Evet”e tıklayın.

➝ Yönetici şifresini girin.

➝ “Yeniden Başlat”a tıklayın.

➝ “Yeniden Başlat”a tıklayın.

➝ Yeniden başlattıktan sonra tekrar “Post-Install Root Patch” seçeneğine tıklayın.

➝ Şimdi “Tüm geçerli yamalar zaten yüklü” mesajı görüntülenmelidir.

#### VI. SORUN ÇÖZÜMLERİ

Önerilen çözümler için [Github](https://dortania.github.io/OpenCore-Legacy-Patcher/TROUBLESHOOTING.html) sayfasını ziyaret edebilirsiniz.

### UZUN VERSİYON

Aşağıdaki süreç macOS 11 Big Sur, macOS 12 Monterey, macOS 13 Ventura ve MacOS 14 Sonoma sistemlerine yama uygulanması için geçerlidir. Kısa bölümde anlatılan şeylerin daha ayrıntılı ve geniş bir şekilde anlatıldığı bir bölüm olacaktır.

#### I. ARAŞTIRMA

1\. Mac'inizin OCLP ile uyumluluğunu [buradan](https://dortania.github.io/OpenCore-Legacy-Patcher/MODELS.html) kontrol edin. Ayrıca oradaki cihaz listesinin yorum sütununda yer alan kısıtlamalara ve önerilere de dikkat edin. Model adını (örn. iMac 12.2) eski sistemlerde şu şekilde bulabilirsiniz: Sol üstte Apple simgesi ➝ “Bu Mac Hakkında” ➝ “Sistem raporu” ➝ “Model tanımlama”.

![Sistem Raporu](/images/opencore-legacy/12.png)

2\. Ayrıca OCLP cihaz listesinin yorum sütunundaki kısıtlamalara veya önerilere de dikkat edin.

#### II. VERİ GÜVENLİĞİ

1\. OCLP'yi kullanmadan önce en az iki güncel yedeğin mevcut olması gerekir çünkü geliştiricilerin gösterdiği tüm özene rağmen sistem veya program sorunları ortaya çıkabilir. Asıl yedekleme Time Machine yöntemi kullanılarak alınacak olan yedek olup diğeri ise dosyalarınızın harici bir hard diske sürükle bırak yöntemi ile yedeklenmesi şeklindedir.

2\. Mac'inizin dahili sabit sürücüsüyle herhangi bir risk almak istemiyorsanız yamayı boş bir harici sabit sürücüde de gerçekleştirebilirsiniz.

3\. OCLP, Mac'inizin dahili HD'sinin ilk bölümünü "disk0" olarak ve diğer tüm bölüm ve sürücüleri "disk1", "disk2" vb. olarak ifade eder. Bu nedenle, bunları OCLP'leriyle tanımlayarak karışıklığı önleyin.

#### III. GEREKLİLİKLER

1\. USB fare ve USB klavye

2\. Talimatları okumak için çıktı veya cihaz

3\. OCLP destekli bir Mac (dizüstü bilgisayar Güvenli tarafta olmak için güç kaynağını kullanmalısınız.)

4\. En az 16 GB kapasiteli USB sürücüsü (çubuk yeterlidir)

Aşağıda ve yazının devamında _yardımcı cihaz_ olarak anılacaktır.

5\. USB bağlantı noktasına sahip harici sürücü (hedef sürücü ise)

6\. [OCLP'nin son sürümü](https://github.com/dortania/OpenCore-Legacy-Patcher/releases/latest)

7\. Yeni işletim sistemi kurulum sihirbazı (InstallAssistant.pkg)

İndirme işlemi OCLP aracılığıyla, App Store'dan veya

İndirme bağlantıları Apple sunucusuna yönlendirilen aşağıdaki web siteleri:
[MacOS 11 Big Sur](https://support.apple.com/tr-tr/HT211238), [macOS 12 Monterey](https://support.apple.com/tr-tr/HT212551), [macOS 13 Ventura](https://support.apple.com/tr-tr/HT213264), [macOS 14 Sonoma](https://www.apple.com/tr/macos/sonoma/) aracılığı ile yapılabilir.

#### IV. KURULUM

Aşağıdaki süreç macOS 11 Big Sur, macOS 12 Monterey ve macOS 13 Ventura sistemlerine yama uygulanması için geçerlidir. MacOS 14 Sonoma'ya yama uygulanması **4. maddede** açıklanacaktır.

1\. Yardımcı cihazın hazırlanması

Yeni sisteminize yama yapmadan önce, sistem yükleyiciyi yardımcı bir cihaza (örn. USB bellek) yüklemelisiniz. Bunu şu şekilde hazırlayın:

➝ Yardımcı cihazı Mac'e bağlayın.

➝ Disk İzlencesi Programını açın.

➝ “Görüntüle”ye ve ardından “Tüm cihazları göster”e tıklayın.

➝ Yardımcı cihazın sürücü sembolünü seçin (simge sola hizalanmıştır).

![Usb Formatlama](/images/opencore-legacy/13.jpeg)

➝ “Sil”e tıklayın.

➝ İsim kalabilir.

➝ Format olarak “Mac OS Genişletilmiş (Günlüklü)” seçeneğini seçin.

➝ Şema olarak “GUID Bölümleme Tablosu”nu seçin.

➝ “Sil”e tıklayın (komut tüm verileri siler).

➝ “Bitti”ye tıklayın.

➝ Önerilen Time Machine yedeklemesini reddedin.

2\. Sistem kurucunun yardımcı cihaza kurulması

![OCLP 1.0.1](/images/opencore-legacy/14.png)

Yardımcı cihazı hazırladıktan sonra artık sistem kurulumunu ona kurabilirsiniz. Bunu yapmak için OCLP'de “MacOS Yükleyici Oluştur” seçeneğine tıklayın. Yükleyiciye zaten sahipseniz “Mevcut macOS Yükleyiciyi Kullan” seçeneğini seçin. Aksi takdirde “MacOS Installer'ı İndir” seçeneğini ve istediğiniz macOS'u seçin. Daha sonra indirme işlemi başlayacaktır.

|||
|:---:|:---:|
![1](/images/opencore-legacy/7.png) | ![2](/images/opencore-legacy/8.png)

İndirme işleminden sonra otomatik doğrulama gerçekleşir. Daha sonra yönetici şifresini girin ve “InstallAssistant.pkg” kurulum sihirbazından çıkartılan “install MacOS xyz.app” isimli sistem yükleyicisinin dahili sürücünüzün Uygulamalar klasöründe olduğundan emin olun. OCLP yalnızca orada sistem kurulumunu tanır.

![3](/images/opencore-legacy/9.png)

Ardından “MacOS Yükleyici Oluşturulsun mu?” penceresinde “Evet”e tıklayın, “Yerel macOS Yükleyiciyi Seçin” penceresinde yükleyiciyi seçin, yardımcı sürücüyü seçin (Mac'inizin dahili HD'si olduğu için disk0 değil), “Evet”e tıklayın “Onay” penceresinde ” (komut tüm verileri siler), yönetici şifresini girin ve önerilen Time Machine yedeklemesini reddedin.

Kurulum tamamlanana kadar “Yükleyici Oluşturuluyor: MacOS xyz’yi Yükle” penceresini açık bırakın. Yardımcı cihazın hızına bağlı olarak yaklaşık 30 dakika veya daha uzun sürebilir. Yardımcı cihaz otomatik olarak yeni adı “MacOS xyz'yi yükle” alır.

Bundan sonra, “macOS yükleyicisi başarıyla oluşturuldu!” penceresinde “Evet”e tıklayın, “OpenCore yapılandırmanızı oluşturma tamamlandı!” penceresinde “Diske yükle”ye tıklayın, yardımcı cihazı seçin (disk0 değil), birimi seçin ve Yönetici-Şifresini girin.

Ardından, “Uygulamak için yeniden başlatılsın mı?” penceresinde alt tuşunu basılı tutarak “Yeniden Başlat” seçeneğine tıklayın, ardından “Yeniden Başlat” seçeneğine tıklayın (alt tuşuna basmaya devam edin), başlangıç yöneticisinde “EFI önyükleme” seçeneğini seçin (alt tuşuna basmaya devam edin) tuşuna basın), bir sonraki başlangıç ekranında “MacOS xyz'yi yükle”yi seçin (Alt tuşuna basmaya devam edin) ve ardından Alt tuşunu bırakın. Artık “macOS Recovery” kurtarma bölümü görüntülenecektir.

3\. Sistemin kurulması

“MacOS Recovery” kurtarma bölümüne ulaştığınızda, yeni sistemin asıl kurulumu burada başlar. Bu nedenle güncel yedeklemelerin önemini son kez hatırlatmak istiyoruz.

Kurulum süresi, ilgili sürücülerin hızına bağlı olarak değişebilir ve bu nedenle bazen sabır gerektirir. Örneğin, "Bir dakikadan az kaldı..." mesajı uzun süre görüntüleniyorsa, bu mutlaka bir sorun olduğu anlamına gelmez, çünkü kurulum bir saate kadar veya daha uzun sürebilir.

Mevcut sistemi güncelleyebilir veya yeni bir kurulum gerçekleştirebilirsiniz. Deneyim raporlarına göre temiz kurulumlarda başarı oranı daha yüksektir. Ancak bu, yama yapmadan önce yeni sistemin hedef birimini silmenizi gerektirir. Son olarak, kullanıcılarınızı, ayarlarınızı, programlarınızı vb. bir yedekten hedef birime kopyalamak için geçiş yardımcısını kullanabilirsiniz.

Ancak yükseltme yaparken yeni sistemin hedef birimini silmeniz gerekmez, çünkü bu yalnızca sistemdeki sistemi günceller. Ancak başka bir program zaten bir sistem yaması oluşturduysa temiz kurulum yapmanız gerekir. Yabancı yama silinir ve OCLP yaması ile değiştirilir.

Yeni sistemin kurulum konumu, Mac'inizin dahili sürücüsü veya harici bir sürücü olabilir. Kurulumun ilgili sürücünün ilk bölümünde yapılması tavsiye edilir.

a) Yükseltme

Adım IV.2. sonunda macOS Recovery bölümüne ulaştıktan sonra yükseltme yapmak için şu adımları izleyin:

➝ Aşağıdaki düğmelere tıklayın:

➝ “MacOS xyz'yi yükle”

➝ “Devam Et”

➝ “Devam Et”

➝ “Kabul et”

➝ “Kabul et”

➝ Yeni sistemin hedef hacmini seçin.

➝ “Devam Et”e tıklayın.

➝ Hedef sürücüye bağlı olarak c) veya d) noktasından okumaya devam edin.

Not: OCLP geliştiricileri, Big Sur veya Monterey'den Ventura'ya daha sonraki bir yükseltme için yazılım güncelleme sistemi ayarını değil, harici bir kurulum ortamı kullanılmasını önerir.

b) Temiz Kurulum

Temiz bir kurulum için “macOS Recovery” kurtarma bölümünde şu yolu izleyin:

➝ Disk Yardımcı Programını açın.

➝ “Devam Et”e tıklayın.

➝ Yeni sistemin hedef sürücüsünü seçin.

➝ “Sil”e tıklayın.

➝ Bir isim verin.

➝ APFS formatını seçin.

➝ Şema olarak GUID bölümleme tablosunu seçin.

➝ “Sil”e tıklayın; komut tüm verileri siler.

➝ “Bitti”ye tıklayın.

➝ Disk Yardımcı Programından çıkın.

➝ Aşağıdaki düğmelere tıklayın:

➝ “MacOS xyz'yi yükle”

➝ “Devam Et”

➝ “Devam Et”

➝ “Kabul et”

➝ “Kabul et”

➝ Yeni sistemin hedef hacmini seçin.

➝ “Devam Et”e tıklayın.

➝ Hedef sürücüye bağlı olarak c) veya d) noktasından okumaya devam edin.

Not: APFS biçimindeki bir birim, eski HFS+ biçimindeki bir birimi okuyabilir ve yazabilir. Ancak bir HFS+ birimi yalnızca HFS+ biriminde en az macOS 10.12.6 Sierra yüklüyse APFS birimini okuyabilir ve yazabilir. Maalesef hâlâ önemli kısıtlamalar mevcut.

c) Dahili sürücüye kurulum

a) veya b) noktasından sonra "Yaklaşık xy dakika kaldı" ilerleme çubuğu görünür ve bunu daha fazla yeniden başlatma izler (hiçbir şeye tıklamayın) ve "xy % tamamlandı" ilerleme çubuğunun bulunduğu siyah bir ekran gelir.

Kurulum sırasında kurtarma bölümü ikinci kez görünürse sol üstteki Apple simgesinin altındaki "Yeniden Başlat"a tıklayın, ardından Alt tuşunu basılı tutun, Başlangıç Yöneticisi'nde "EFI Boot"u seçin ve Alt tuşunu basılı tutmaya devam edin ve " Bir sonraki başlangıç ekranında macOS Installer”. Kurtarma bölümü yeniden görünürse bu işlemi tekrarlayın.

d) Harici sürücüye kurulum

Harici bir sürücüye kurulum yaparken aşağıdaki noktalar dikkate alınmalıdır: MacOS 10.15 Catalina'dan bu yana harici sürücüler artık Firewire aracılığıyla başlatılamadığından, sürücüyü Mac'teki USB bağlantı noktasına bağlamanız gerekir. Ancak yama yaptıktan sonra OCLP'de harici sürücünüzün Firewire aracılığıyla yeniden başlatılabileceğini ayarlayabilirsiniz. Harici bir HD'nin yalnızca Bilgi penceresinde "Bu birimdeki sahipliği yoksay" seçeneği işaretlenmemişse önyüklenebilir olacağını unutmayın.

MacOS 13 Ventura'nın harici bir sabit sürücüye temiz kurulumu 0.6.4 sürümünden bu yana yeni OCLP sürümlerinde daha kısadır:

➝ “Yükle: “Yaklaşık xy dakika kaldı” ilerleme çubuğu görünür. 

➝ Başlangıç yöneticisinde “macOS Installer”ı seçin.

➝ Apple simgesinin bulunduğu siyah bir ekran ve “Yaklaşık xx dakika kaldı” ilerleme çubuğunu görebilirsiniz.

➝ Başlangıç Yöneticisi görünür (hiçbir şeye tıklamayın).

➝ Bunu, Apple simgesinin bulunduğu siyah bir ekranla ve 15 dakika boyunca tam bir ilerleme göstergesiyle yeniden başlatma izler.

➝ Yeniden başlatma gerçekleşir.

➝ Başlangıç yöneticisinde yeni sistemin hedef birimini seçin.

➝ Başka bir yeniden başlatma olacak.

➝ Başlangıç yöneticisinde yeni sistemin hedef hacmini tekrar seçin.

➝ %98'de uzun bir bekleme süresiyle birlikte “%xy tamamlandı” ilerleme göstergesinin yer aldığı siyah bir ekran görüntülenir.

➝ Başka bir yeniden başlatma olacak.

➝ Startup Manager'da aşağıdaki hiçbir şeyi seçmeyin.

➝ Kurtarma bölümü (macOS Recovery) görünür.

➝ Sol üstteki Apple simgesine tıklayın.

➝ Alt tuşunu basılı tutarken “Yeniden Başlat”a tıklayın.

➝ Başlangıç yöneticisinde yeni sistemin yüklendiği diski seçin.

Kurulum sırasında kurtarma bölümünün ikinci kez görüntülenmesi gerekiyorsa: Sol üstteki Apple simgesinin altındaki "Yeniden Başlat" seçeneğine tıklayın ve ardından alt tuşunu basılı tutun, başlangıç yöneticisinde "EFI Boot" seçeneğini seçin ve alt tuşunu basılı tutmaya devam edin. tuşuna basın ve bir sonraki başlangıç ekranında “macOS Installer”ı seçin. Kurtarma bölümü yeniden görünürse bu işlemi tekrarlayın.

Temiz kurulumu tamamlamak için, normal sistem kurulumunda olduğu gibi kurulum sihirbazında ülkeyi, dili, ağı (şifrenizi hazır bulundurun) vb. seçersiniz. Kullanıcı adını atarken, lütfen kullanıcı adını kullanmamanın daha iyi olacağını unutmayın. Daha sonra taşınacak kullanıcının adını seçin.

#### V. KURULUMDAN SONRA

1\. Yazılım Güncelleme sistem ayarlarında otomatik güncellemeleri devre dışı bırakmalısınız. Bu, Apple yazılım güncellemesinin bir sonucu olarak olası sistem sorunlarını önlemek için tavsiye edilir. İlk lansmanından sonra yeni sistem, öncelikle Spotlight endeksini yeniden oluşturması gerektiğinden genellikle biraz daha yavaş olacaktır.

2\. İlk sistem önyüklemesinden hemen sonra OCLP, OpenCore'u dahili sabit sürücüye yüklemek isteyip istemediğinizi soracaktır. Bu, gelecekte yardımcı cihaz olmadan sistemi başlatmanıza olanak sağlayacaktır. Yeni sistem harici bir sürücüye kurulmuşsa, bu iletişim kutusundaki "dahili sabit sürücüye" ifadesi yanıltıcı olabilir. Bu durumda harici sürücüye OpenCore'un da kurulması gerekir.

OpenCore kurulumu şu şekilde çalışır: “Tamam”a tıklayın, ardından “Diske yükle”ye tıklayın, yeni sistemin sürücüsünü ve ardından birimi seçin. Daha sonra alt tuşunu basılı tutarak “EFI Boot”un başlangıç yöneticisini başlatın ve alt tuşunu basılı tutmaya devam edin. Bir sonraki önyükleme ekranında yeni sistemin biriminden önyükleme yapın.

Yukarıdaki "Tamam" seçeneğine tıkladıktan sonra, aşağıdaki günlük penceresi OpenCore kurulumunun ayrı ayrı adımlarını gösterir. Daha sonra OpenCore kurmaya karar verirseniz “Build and Install OpenCore” komutunu kullanarak yukarıda anlatılan işlemi başlatabilirsiniz.

3\. Yeni sisteminizi harici bir birime kurduysanız:

a) 0.6.7 sürümünden itibaren OCLP'yi artık harici birimin programlar klasörüne değil, Library/Application Support/Dortania klasörüne yerleştirmeniz gerekiyor. Ancak OCLP'den bir takma ad oluşturabilir ve bunu Uygulamalar klasörüne koyabilirsiniz.

b) Adım V.2.'yi tamamladıysanız, gelecekte yeni sisteminizi yardımcı cihaz olmadan başlatabileceksiniz. Şu şekilde çalışır: Mac'i açtığınızda Alt tuşunu basılı tutun, ilk başlangıç ekranında "EFI Boot"u seçin. “EFI Boot” tanınmazsa, harici HD'yi Mac'in USB bağlantı noktasından kısa süreliğine çıkarabilir (başlangıç ekranını olduğu gibi bırakarak) ve tekrar takabilirsiniz. Kural olarak “EFI önyüklemesi”nin tanınması gerekir.

c) Yama sonrasında harici sürücünüzü Firewire aracılığıyla yeniden başlatmak istiyorsanız bunu aşağıdaki şekilde yapabilirsiniz:

➝ OCLP'yi açın.

➝ “Ayarlar”a tıklayın.

➝ “Hedef Model” altında Mac türünü seçin.

➝ “Oluştur”a tıklayın.

➝ “FireWire Önyükleme” seçeneğini etkinleştirin.

➝ “Geri Dön”e tıklayın.

➝ “OpenCore Oluştur ve Kur” seçeneğine tıklayın.

➝ “Diske yükle”ye tıklayın.

➝ Yeni sistemin sürücüsünü seçin.

➝ Yönetici şifresini girin.

➝ “Yeniden Başlat”a tıklayın.

4\. “Ayarlar” altında OpenCore ayarlarını bulacaksınız. Ancak bunları yalnızca etkisini biliyorsanız değiştirin; aksi halde bilgisayarın bir sonraki başlatılması başarısız olabilir. Ayrıca, Hedef Model ayarlarını değiştirmeden önce her Mac modelinin farklı ayarları olduğundan öncelikle bilgisayar modelinizi seçmelisiniz.

Ayarları düzenlemek ve kaydetmek için şu adımları izleyin:

➝ OCLP'yi açın.

➝ “Ayarlar”a tıklayın.

➝ “Hedef Model” altında Mac'i seçin.

➝ Daha sonra OCLP'yi kapatmadan ayarlara girin. Aksi halde önceki ayarlar kalır.

➝ “Geri Dön”e tıklayın.

➝ “OpenCore Oluştur ve Kur” seçeneğine tıklayın.

➝ “Diske yükle”ye tıklayın.

➝ Yeni sistemin sürücüsünü seçin.

➝ Yönetici şifresini girin.

➝ “Yeniden Başlat”a tıklayın.

5\. Yeni sistemi dahili bir sabit sürücüye kurduysanız ve artık Başlangıç Yöneticisinin gelecekte görüntülenmesini istemiyorsanız, aşağıdaki şekilde ilerleyin:

➝ OCLP'yi açın.

➝ “Ayarlar”a tıklayın.

➝ “Açık Çekirdek Önyükleme Seçicisini Göster” seçeneğinin işaretini kaldırın.

➝ Aşağıdaki “Ana Menüye Dön” seçeneğine tıklayın.

➝ “OpenCore Oluştur ve Kur” seçeneğine tıklayın.

➝ “Diske yükle”ye tıklayın.

➝ disk0'ı seçin (= dahili sürücü).

➝ Yönetici şifresini girin.

➝ “Yeniden Başlat”a tıklayın.

➝ “Yeniden Başlat”a tıklayın.

6\. "Önyükleme Seçici Zaman Aşımı" ayarı, başlangıç yöneticisinin (önyükleme seçicinin) ne kadar süreyle görüntüleneceğini belirtmenize olanak tanır. Bu süre zarfında yamalı ya da başka bir başlangıç diskinizi seçebilirsiniz. Bu süre dolduktan sonra standart işletim sistemi başlatılır.

7\. Metal olmayan bir Mac'te menü çubuğunun yazı tipi rengini beyazdan siyaha değiştirmek istiyorsanız OCLP'yi açın, Ayarlar'a tıklayın, Metal Olmayan Ayarlar'a tıklayın, Koyu Menü Çubuğu'nu seçin ve “ Ayarlar'a dönün”. Son olarak kullanıcıyı yeniden kaydetmeniz yeterlidir; “OpenCore'u Oluştur ve Kur” komutu gerekli değildir

8\. Sistem ile donanım arasındaki iletişim için gereken tüm sürücülerin güncel olup olmadığını kontrol edin. Bu şu şekilde çalışır:

➝ OCLP'yi açın.

➝ “Yükleme Sonrası Kök Yaması”na tıklayın.

➝ “Tüm geçerli yamalar zaten yüklü” mesajını görürseniz başka bir şey yapmanıza gerek yoktur.

➝ “Sistem için mevcut yamalar” mesajında “Kök Yamalamayı Başlat” seçeneğine tıklayın.

➝ “Evet”e tıklayın.

➝ Yönetici şifresini girin.

➝ “Yeniden Başlat”a tıklayın.

➝ “Yeniden Başlat”a tıklayın.

➝ Başlangıç yöneticisinde hiçbir şey seçmeyin.

➝ Yeniden başlattıktan sonra tekrar “Post-Install Root Patch” seçeneğine tıklayın.

➝ Şimdi “Tüm geçerli yamalar zaten yüklü” mesajı görüntülenmelidir.

9\. Yamalı bir birimi başka bir Mac'te başlatmayı da deneyebilirsiniz. Ancak lütfen bunun uygun donanım yazılımına, ilgili macOS'a ve yamalı birim ile aynı OpenCore'a sahip olması gerektiğini unutmayın. Ayrıca donanımın aynı olması gerekmektedir. Farklı Bluetooth modülleri gibi farklılıklar varsa, donanım tanınmazsa başlatma sorunları ortaya çıkabilir.

#### VI. SORUN ÇÖZÜMLERİ

1\. Önerilen çözümler için [Github](https://dortania.github.io/OpenCore-Legacy-Patcher/TROUBLESHOOTING.html) sayfasını ziyaret edebilirsiniz. Özellikle Bluetooth sorunları için bakmanızda da fayda var. Gerekirse en son sürüm olan Sonoma yerine bir önceki sürümü yüklemeniz bazen daha uyumlu ve faydalı olabiliyor.

2\. 0.6.7 sürümünden bu yana, ~/Library/Logs/Dortania klasöründeki OCLP günlük dosyasına “Dosya” ve “Günlük Dosyasını Göster” menüleri aracılığıyla doğrudan erişebilirsiniz. Bu, hataların olası nedenlerinin belirlenmesine olanak sağlar.

3\. Bir ses arızası varsa, Ses sistemi ayarlarından dahili hoparlörlerin çıkış cihazı olarak seçilip seçilmediğini kontrol edebilirsiniz.

4\. Sistem bir sistem veya güvenlik güncellemesinden sonra artık başlamazsa, Shift tuşunu basılı tutarak Mac'i Güvenli Modda başlatmayı ve ardından "Yükleme Sonrası Kök Yaması" yolunu kullanmayı deneyebilirsiniz

Bu işe yaramazsa kök yamaları silmenizi öneririz. Bu şu şekilde çalışır:

➝ Başlatma sırasında alt tuşunu basılı tutun ve başlangıç yöneticisinde “EFI önyükleme” seçeneğini seçin.

➝ Bir sonraki ekranda önyükleme seçenekleri menüsünü açmak için boşluk çubuğuna basın.

➝ Kurtarma bölümünü seçin.

➝ Orada Terminal programını açın.

➝ Aşağıdaki komut satırını kopyala ve yapıştır kullanarak terminal penceresine yapıştırın:

`mount -uw /Volumes/Macintosh\ HD`

➝ Enter tuşuna basın.

➝ Bu ikinci komut satırını ekleyin:

`bless --mount /Volumes/Macintosh\ HD --bootefi --last-sealed-snapshot`

➝ Enter tuşuna tekrar basın.

➝ Sol üstteki Apple simgesine tıklayın ve alttaki “Yeniden Başlat”a tıklayın.

Her iki komut satırını da girerken, _Macintosh\\ HD_ adını yamalı biriminizin adı ile değiştirin ve addaki her boşluğun önüne \\ ters eğik çizgi karakterini ekleyin.

Bu adımlardan sonra, OCLP'yi güncelleyene kadar veya önyükleme sorunundan önce bunu zaten yaptıysanız, sorunu çözen yeni bir OCLP güncellemesi mevcut olana kadar kök yamaları yeniden yüklemeyi beklemelisiniz.

6\. Büyük sorunlar olması durumunda, yama yapılmamış önceki sistem sürümüne geçmek de mümkündür. Bunu yapmak için Time Machine'i, kurtarma bölümünü veya önyüklenebilir bir yükleyiciyi kullanabilirsiniz. Bununla ilgili talimatları [burada](https://www.drbuho.com/how-to/downgrade-macos) bulabilirsiniz. (Lütfen reklamları dikkate almayın). Time Machine için yamadan önce oluşturulmuş bir yedeği seçmeniz gerektiğini unutmayın. Ancak eski bir OCLP sistem yamasına geçmek mümkün değildir. Bunu yapmak için, Temiz Kurulum'u kullanarak OCLP'yi kullanarak istediğiniz eski sistem sürümünü yüklemelisiniz.

# SON

Neyse ki OCLP yalnızca ücretsiz değil aynı zamanda son derece sürdürülebilirdir. Mac'in daha uzun süre kullanılabilmesi nedeniyle program yalnızca donanım maliyetlerini ve elektronik atıkları azaltmakla kalmıyor, aynı zamanda kaynak tüketimini ve üretimden kaynaklanan emisyonları da azaltıyor.

OCLP geliştiricilerinin sürekli kararlılığını desteklemek isterseniz, örneğin çok sayıda test bilgisayarı satın alarak veya yalnızca teşekkür etmek amacıyla bunu küçük bir bağışla geliştiricilerin [web sitesinden](https://opencollective.com/opencore-legacy-patcher) yapabilirsiniz.

OCLP gelişmeye devam ettikçe gelecekte bu talimatlardan sapmalar ortaya çıkabilir. Daha sonra yeni özellikleri [OCLP web sitesinde](https://github.com/dortania/OpenCore-Legacy-Patcher) bulabilirsiniz. Ayrıca şu [video koleksiyonunu](https://www.youtube.com/@Mr.Macintosh/videos) ve ilgili [Wiki](https://de.wikipedia.org/wiki/OpenCore_Legacy_Patcher) sayfasına da göz atılmasını öneririm.

MacOS Sonoma kurulumu ile ilgili örnek videoya aşağıdan ulaşabilirsiniz:
{{< youtube eGR2qGw9lG8 >}}

## Kaynakça: 
Yazı sırasında kullandığım kaynaklar:
* [OpenCore Legacy Patcher Github](https://github.com/dortania/OpenCore-Legacy-Patcher)
* [OpenCore Legacy Patcher Web Sayfası](https://dortania.github.io/OpenCore-Legacy-Patcher)
* [Macsofa Forum](https://macsofa.net/)
* [Mr.Macintosh Youtube Kanalı](https://www.youtube.com/@Mr.Macintosh/videos)
* [Khronokernel Web Sayfası](https://khronokernel.github.io)
* İlk Yayım: [BTT](https://btt.community/t/eski-maclere-opencore-ile-desteklenmeyen-surumlerin-kurulumu/2140)
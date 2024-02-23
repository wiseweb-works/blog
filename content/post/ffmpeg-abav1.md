---
title: "FFmpeg ile video çevirmek için en uygun ayarı kolayca bulmak"
date: 2024-01-15
tags: ["FFmpeg", "AB-AV1", "H265", "H264", "AV1", "WMAF", "Encoder", "Decoder"]
author: "Wise"
draft: false
---
# Giriş

Günümüzde video ve ses (genel olarak medya diyebiliriz) dosyalarını işlemek (kesme, yeniden boyutlandırma ve filtre uygulama) ve dönüştürmek için birçok araç bulunmaktadır. Ancak, bu araçlardan biri diğerlerinden daha çok öne çıkmakta ve belki de birçoğunun arka planında ([Yusuf İpek Videosu](https://www.youtube.com/watch?v=clrVhDx_A-s)) kullanılmaktadır. FFMPEG adlı bu araç/yazılım ücretsiz ve [açık kaynaklı](https://github.com/FFmpeg/FFmpeg) olup LGPL, GPL2 ve GPL3 (farklı kısım ve tarihler açısından kullanılan diğer lisanlar da mevcut) lisanları altında geliştirilmektedir.

Lisansla ilgili orjinal dilinde açıklama:
> Most files in FFmpeg are under the GNU Lesser General Public License version 2.1 or later (LGPL v2.1+). Read the file COPYING.LGPLv2.1 for details. Some other files have MIT/X11/BSD-style licenses. In combination the LGPL v2.1+ applies to FFmpeg.

> Some optional parts of FFmpeg are licensed under the GNU General Public License version 2 or later GPL v2+). See the file COPYING.GPLv2 for details. None of these parts are used by default, you have to explicitly pass --enable-gpl to configure to activate them. In this case, FFmpeg's license changes to GPL v2+.

Bu rehberde sizlerle birlikte FFMPEG yazılımın kullanımından ve kendimiz için en uygun/optimize ayarı;

1) Hız, 
2) Dosya Boyutu,
3) Görüntü Kalitesi ve
4) (Opsiyonel) Uyumluluk

başlıklarını da dikkate alarak nasıl bulabileceğimizi anlatacağım. 

## Teknik açıklama kısmı

Sürecin tamamına hakim olmak veya arka planında neler olduğunu anlamak isteyenler için işin teknik kısmını açıklayarak başlayacağım. Bu kısmı isterseniz atlayıp direk yandan "AB-AV1 Yazılımı" ile ilgili başlığa tıklayabilirsiniz. 

### Video/Ses Codecleri Nelerdir?

[Video/Ses codecleri](https://ffmpeg.org/ffmpeg-codecs.html) video ve ses dosyalarını sıkıştırmak ve/veya yeniden kodlamak için kullanılan algoritmalardır. Bu işlem (encode/decode) daha küçük dosya boyutları elde etmek ve iletim ile depolama sırasında bant genişliğini azaltmak için önemlidir. Eğer bir video/ses dosyasını sıkıştırmadan saklamak isteseydik ihtiyacımız olacak alan gerçekten çok fazla olurdu. Örnek olarak 1 dk'lık 1080p 30fps bir video uncompress şekilde ([Kaynak](https://dvsgroup.com/tools/BitRateCalculator.php)) yaklaşık 10,5 GB yer kaplamaktadır. Bu miktar 4K 10 bit HDR dünyasına giriş yaptığımızda 1 dk için 55,6 GB gibi aşırı uçuk bir rakama çıkmaktadır. 

Bu boyutların ne kadar büyük olduğunu şu örnek ile farkedebiliriz. 4K UHD Blu-ray bir diskin içerisine bu 1dk'lık sıkıştırılmamış video yerine 3:30 Saat civarı süren [The Irishman Filmini](https://www.imdb.com/title/tt1302006/) farklı dillerde seslendirme, altyazı ve ekstra içeriklerle sığdırabiliyorlar.  

#### x264: H.264 Video Encoder

x264, H.264/MPEG-4 AVC (Advanced Video Codec) standardını uygulayan bir video encoder'dır. Bu codec, yüksek kaliteli (zamanına göre) video sağlamak için optimize edilmiştir ve geniş bir cihaz yelpazesinde desteklenir. x264 çıktığı dönem için yüksek sıkıştırma oranları sağlayarak düşük bant genişliği kullanımı yani kaliteli videolara erişmemizi mümkün kıldı. Halen daha kullandığımız birçok elektronik cihaz tarafından gerek yazılımsal gerek de donanımsal boyutta destekleniyor. Fakat günümüzün yüksek kalite ve düşük dosya boyutu isteklerine uyum sağlamakta güçlük çekmektedir. Zamanına göre yüksek sıkıştırma ve yüksek kalite verdiği düşünülen sonuçları artık bazı çevrelerce yeterli görülmemektedir. 

#### x265: H.265/HEVC Video Encoder

x265, H.265/HEVC (High-Efficiency Video Coding) standardını uygulayan ve H264'ün ardılı olan bir video encoder'dır. x265, x264'e kıyasla daha iyi sıkıştırma performansı sunar. Bu, daha düşük bant genişliği kullanımı ve aynı kalitede daha küçük dosya boyutları anlamına gelir. H265'de kendinden önce gelen video codec'i gibi birçok cihaz tarafından yazılımsal ve bazı cihazlar tarafından donanımsal hızlandırma ile kullanılabilmektedir. Her iki codec arasındaki sıkıştırma farkı aynı kalite için yaklaşık %25 ile %50 civarında farketmektedir. 

#### AV1 Video Encoder

Geleceğin standartlarından biri olması beklenen AV1 özellikle yüksek verimli video sağlamak için tasarlanmıştır. x264 ve x265'e alternatif olarak geliştirilen AV1, özellikle diğerlerinin aksine açık kaynaklı ve tescilli olmayan bir codec olarak dikkat çeker. AV1, çok daha yüksek kalitede videoyu (her zaman için ana hedef bu olmuştur) sağlaması ve çok daha düşük bant genişliği kullanımıyla bilinir. Diğer codeclerle asıl ayrıldığı nokta da zaten gerçekten çok düşük bant genişliklerinde verdiği yüksek kaliteden kaynaklıdır. Canlı yayın yapan oyuncular için çok büyük gelecek vaadetmekte ve 3,5 MBps gibi bir değerde dahi güzel bir deneyim sunabilmektedir.

#### Karşılaştırma: x264, x265 ve AV1

- **Sıkıştırma Oranları:** x265 ve AV1, genellikle daha iyi sıkıştırma oranları sunar.
- **Performans:** x264, genellikle düşük kaynak tüketimi (CPU/GPU) ve hızlı oynatma performansı ile bilinir; ancak x265 ve AV1, yüksek performans ve daha iyi sıkıştırma sağlayabilir.
- **Uyumluluk:** AV1 diğerlerinin aksine henüz çok geniş bir cihaz desteğine (sıkıştırma ve oynatma açısından) ulaşamadı. En yüksek cihaz/oynatıcı uyumluluğunu arayanlar için x264 en iyi sonucu verecektir.  

#### Kısa Sonuç

Her video encoder, belirli senaryolarda daha iyi performans gösterir ve seçim, kullanım amaçlarına ve gereksinimlere bağlıdır. x264, x265 ve AV1, farklı avantajlara sahip olabilir ve FFmpeg, bu encoder'ları kullanarak geniş bir video işleme yelpazesi sunar. Ben şahsen AV1'in henüz ev kullanıcıları özelinde kullanılabilir bir seviyede olmaması nedeniyle H265'i tercih ediyorum. Yazının geri kalanında da bunun üzerinden anlatımıma devam edeceğim.

### Preset Nedir?

FFmpeg'deki presetler, bir video veya ses dosyasını belirli bir kalite ve boyutta kodlamak için kullanılan [önceden tanımlanmış ayarlardır](https://x265.readthedocs.io/en/stable/presets.html) Bu ayarlar, codec tarafından kullanılan bir dizi parametre grubunu (komut) içerir ve genellikle kalite ile dosya boyutu arasındaki dengeyi sağlamak için tasarlanmıştır.

Presetlerde daha hızlı bir kodlama süreci genellikle daha düşük kalite ile sonuçlanır, bu nedenle kullanıcılar ihtiyacına bağlı olarak farklı presetleri seçebilirler. X265 için tanımlanmış olan presetler aşağıdaki gibidir:

1) `ultrafast`: En hızlı kodlama, ancak düşük kalite.
2) `superfast`: ultrafast'ten biraz daha yavaş, daha iyi kalite.
3) `veryfast`: Daha dengeli bir hız ve kalite.
4) `faster`: Daha yüksek kalite, biraz daha uzun süren kodlama.
5) `fast`: Standart ayar, genel kullanım için iyi bir denge.
6) `medium`: Kalite ve hız arasında bir denge.
7) `slow`: Daha iyi kalite, ancak daha uzun süren kodlama.
8) `slower`: slow'dan daha yüksek kalite, daha uzun süre.
9) `veryslow`: En yüksek kalite, ancak en uzun kodlama süresi.
10) `placebo`: Adından da anlaşılacağı üzere çok önerilen (veya dikkate alınan) bir preset değil

![X265 Encoding](/images/ffmpeg-abav1/1.png)

Şahsen genelde veryfast veya slow presetlerini kullanıyorum. Farklı ihtiyaçlarıma bu ikisi (genelde) en iyi cevap veriyor. Fakat girdi olarak hangi videonun dikkate alınacağı ve her video özelinde verecekleri sonuç her zaman için aynı olmayabiliyor. Bknz: [Anime videolarının sıkıştırılmasındaki problemler](https://www.youtube.com/watch?v=mntswiXuvfI)

### CRF/VBR ve CBR Nedir?

**CRF (Constant Rate Factor)**, bir video dosyasının kalitesini kontrol etmek için kullanılan bir parametredir. Bu yöntemde, belirli bir hedef kalite seviyesine ulaşılması için codec, bitrate'i otomatik olarak ayarlar. Yani, kullanıcı belirli bir bitrate'i elle belirtmez, sadece kalite seviyesini seçer. Daha düşük bir CRF değeri, daha yüksek bir kalite anlamına gelir, ancak dosya boyutu artar.

**CBR (Constant Bitrate)**, videonun belirli bir sabit bitrate ile kodlanmasını sağlar. Bu, dosya boyutunu önceden belirlemenizi sağlar, ancak kalite değişkenlik gösterebilir. CBR, özellikle belirli bir bant genişliği sınırlaması varsa veya bir iletim ortamı belirli bir bitrate'i destekliyorsa kullanışlıdır. Her saniye için harcanacak değer belirli olduğundan hareketli ve sabit sahneler arasında belirgin bir kalite farkı oluşacaktır. Bunu kendinize daha önce "Neden konfeti atıldığında videonun kalitesi bozuluyor?" diyerek sormuş olabilirsiniz. Eğer sormadıysanız da [Tom Scott'un](https://www.youtube.com/watch?v=r6Rp-uo6HmI) bununla ilgili eğitici bir videosu bulunuyor.

**VBR (Variable Bitrate)**, videonun her bir çerçevesi için farklı bit oranlarını kullanarak kodlanmasını sağlar. Bu, sahnede daha fazla ayrıntı olduğunda daha yüksek bir bitrate sağlayarak kaliteyi artırabilir. Ancak, dosya boyutu daha değişken olacaktır. Bu ayar aslında CRF ile aynı olarak düşünülebilir. Fakat farklı mecralarda ayrı ayrı ve bazen de birlikte anılmaları nedeniyle ekstradan açıklama gereği duydum.

![CRF-VMAF](/images/ffmpeg-abav1/2.png)

Şahsi olarak bazen 22 bazen de 24 olan CRF değerini kullanıyorum. Eğer biraz daha hızlı bir şekilde sonuçlandırmak istiyorsam 26 veya 28'i de kullanabiliyorum. CRF değerleri 1-1 artmamaktadır. İsterseniz 22,03 gibi bir ayar dahi kullanabilirsiniz. Ama bu derece ince ayarın pek mantığı olmayacaktır. Genel kullanımda 2'şer adım farklarla değişiklikler yapılmaktadır. 

### Profil Türü ve Seviyeleri Nelerdir?

Video kodlamada profil, bir codec'in belirli bir uygulama senaryosu (film, video, animasyon ve resim gösterisi :smiley: ) için optimize edilmiş ayarları içeren bir grup parametreyi tanımlar. Her profil, belirli bir kullanım durumu veya cihaz için uygun olan farklı sıkıştırma ve kalite ayarlarına sahiptir.

Profile seviyeleri ise bir codec tarafından desteklenen en yüksek çözünürlük, en yüksek bit hızı ve diğer teknik özellikleri belirleyen bir dizi sınırlamadır. Belirli bir seviye, belirli bir profil ile birleşerek bir codec'in kapasitesini tanımlar.

x265 için öntanımlı profiller olarak `auto, main, main10, main448, main448-intra, high` ve `mainstillpicture` gibi profilleri sayılabilir. Her profil, belirli bir uygulama senaryosuna yönelik optimize edilmiş ayarlar içerir. Bunlarla ilgili ayrıntılı teknik bilgi için [Wiki sayfasına](https://en.wikipedia.org/wiki/High_Efficiency_Video_Coding_tiers_and_levels) göz atabilirsiniz. Ben aksine bir gereklilik olmadıkça main profili ve 5.1 seviyesini kullanıyorum. 

### VMAF Nedir ve Neden Kullanıyoruz?

VMAF, Netflix tarafından [geliştirilen](https://github.com/Netflix/vmaf) ve 2014 yılında ortaya çıkan bir video kalite metriğidir. Bu metrik, izleyici deneyimini daha iyi anlamak ve video içeriğinin kalitesini değerlendirmek için tasarlanmıştır. Eğer Netflix'in güzel içeriklerinin yüksek görüntü kalitesine aşina iseniz ve bu sizi tatmin ediyorsa o zaman görüntü kalitesi ölçütü olarak VMAF'ya da güvenebilirsiniz.

FFmpeg ise bu metriği kullanarak video kodlama ayarlarını optimize etme konusunda bize yardımcı olur. Belirli bir kalite aşıldıktan sonra insan gözüyle daha iyi/kötü ayrımı yapmamız veya bunu saatlerce süren bir video özelinde aynı özenle devam ettirmemiz mümkün değil. Rehberin önceki kısımlarında paylaştığım sıkıştırma teknolojileri yanlış veya bilinçsiz kullanımda izleyici deneyimini önemli ölçüde etkilemektedir. Bu bağlamda, Video Multimodal Assessment Fusion (VMAF) gibi teknolojiler (SSIM veya PSNR), video kalitesini artırmak ve daha verimli bir sıkıştırmayı tercih etmemiz konusunda bize yardımcı olmaktadır. Kaldı ki her zaman için daha yüksek dosya boyutu daha çok kalite anlamına gelmeyecektir. Bu nedenle kalitenin matematiksel ölçümünde VMAF ölçütünü kullanıyoruz. 

VMAF değeri 0-100 aralığında bir değer olup orjinal video ile dönüştürülmüş (kendileri bozulmuş da der aslında) video arasındaki benzerliği göstermektedir. 90 ve üzeri bir değeri neredeyse aynı kalite (bizi rahatsız etmeyecek kadar) olarak kabul etmemiz mümkün iken 95 ve üstünü artık en dikaktli gözlerin dahi farkedemeyeceği şekilde aynı olarak düşünebiliriz.

Şahsi olarak min 90 değerini tutturabildiğim zaman video çevirimini başarılı olarak değerlendiriyorum.

## Otomatikleştirilmiş hali: AB-AV1 yazılımı

AB-AV1 gönüllüler tarafından MIT lisansı altında geliştirilen ve [açık kaynaklı](https://github.com/alexheretic/ab-av1) bir yazılımdır. Yazılımın odaklandığı ve çözmek istediği sorun video dönüştürürken ve ffmpeg yazılımını kullanırken teknik bilgilerle çok boğuşmadan istediğimiz sonuca bizi ulaştıracak değer veya değerleri hızlı bir şekilde elde etmektir. Yazılım rehberin paylaşıldığı tarih itibariyle şu yöntemi izlemektedir. Öncelik her 12 dk'lık video kısmı için bir örnek kısım alıyor. Bu örnek kısım/lar video çok kısa olmadığı sürece 20'şer sn'ye kadar çıkabilmektedir. Sonrasında bu örnek videoları farklı CRF ve Preset değerleri ile kombine edip ortaya çıkan sonuçların VMAF değerlerini de hesaplıyor. Bunu yaparken de kaba kuvvet saldırısı yapar gibi 1 den başlayıp 50 ye doğru gitmiyor. Her iki ayar içinde ortadan başlayıp (CRF 25, preset medium gibi) uçlara doğru giden bir yol izliyor. Eğer istenilen şartlara uyan 4-5 adet sonuç bulur ise aramayı durduruyor bu da çok hızlı bir şekilde sonuca ulaşmamızı sağlıyor.

Bu yazılımı bulmadan önce kendim tüm 20-30 arasındaki tüm CRF değerleri ve tüm presetler için topluca komut çalıştırıp sonra gene elle hepsinin VMAF değerlerine bakıyor, bir excel tablosu yapıyordum. Bu da gerçekten çok uzun süren ve teknik bilgi olmadan yapılması kolay olmayan bir yola götürüyordu beni. İlgili eski çalışmalarımdan birkaç ekran görüntüsünü sizlerle paylaşmak istiyorum ki ne büyük bir dertten kurtulduğum daha ne anlaşılsın. 

|||
|:---:|:---:|
![1](/images/ffmpeg-abav1/3.jpeg) | ![2](/images/ffmpeg-abav1/4.jpeg)


İki adet test videosu ile yapmış olduğum bu testler yaklaşık 1 gün sürmüştü. Ben cihazı kullanırken arka planda sistem de bu testleri yapıyordu. 

### Yazılımın indirilmesi ve/veya kurulması

Yazılım Linux ve Windows işletim sistemlerini desteklemektedir. Windows için exe uzantılı dosyayı indirip kullanabilirsiniz. Arch Linux kullanıyorsanız [Aur'da](https://aur.archlinux.org/packages/ab-av1) paketi mevcut. Diğer linux dağıtımı kullanıcıları ise binary dosyası olarak indirebilir veya kendisi derleyebilir. Yükleme ile ilgili [talimatlara](https://github.com/alexheretic/ab-av1?tab=readme-ov-file#install) Github sayfasından ulaşabilirsiniz.

### Yazılımın ayarları

Yazılımı kurduktan sonra `ab-av1 -h` komutu ile yardım kısmına veya ayrıntılı bilgiye ulaşabilirsiniz. Sizi yaklaşık olarak şöyle bir ekran karşılayacaktır.

```
AV1 encoding with fast VMAF sampling

Usage: ab-av1 <COMMAND>

Commands:
  sample-encode      Encode & analyse input samples to predict how a full encode would go.
                         This is much quicker than a full encode/vmaf run.
  vmaf               Full VMAF score calculation, distorted file vs reference file.
                         Works with videos and images.
  encode             Invoke ffmpeg to encode a video or image
  crf-search         Interpolated binary search using sample-encode to find the best crf
                         value delivering min-vmaf & max-encoded-percent.
  auto-encode        Automatically determine the best crf to deliver the min-vmaf and use it to encode a video
                         or image.
  print-completions  Print shell completions
  help               Print this message or the help of the given subcommand(s)

Options:
  -h, --help     Print help
  -V, --version  Print version
```

En basit kullanım senaryosu olarak aşağıdaki gibi yapabilirsiniz:

```
ab-av1 crf-search --encoder libx265 -i dosyanız.uzantısı
```

bu şekilde yazılımın öntanımlı olarak dikkate aldığı değer aralıklarında ve x265 için uygun olacak sonuçları size verecektir. Biraz ince ayar yapmak isterseniz eğer istediğiniz kalite miktarını VMAF değeri olarak aşağıdaki gibi belirtebilirsiniz:

```
ab-av1 crf-search --encoder libx265 --min-vmaf 95 -i dosyanız.uzantısı (veya 90 artık size kalmış)
```

Ben nasıl kullanıyorum diye soracak olursanız eğer:

```
ab-av1 crf-search --encoder libx265 --min-vmaf 90 --min-crf 20 --max-crf 30 --crf-increment 2 --preset veryfast --enc x265-params=level=5.1:high-tier=1 -i dosyanız.uzantısı
```

Örnek olarak çok bilinen bir video olan [Tears of Steel](https://mango.blender.org/download/) üzerinde bir deneme yapmak gerekirse eğer:

```
- crf 25 VMAF 94.40 (33%) (cache)
- crf 27 VMAF 92.30 (26%) (cache)
- crf 30 VMAF 87.55 (17%) (cache)
- crf 28 VMAF 90.94 (22%) (cache)
- crf 29 VMAF 89.36 (20%) (cache)
00:00:00 ################################################(sampling crf 29, eta 0s)
Encode with: ab-av1 encode -e libx265 -i tears_of_steel_1080p.mov --crf 28 --preset veryfast --enc x265-params=level=5.1:high-tier=1

crf 28 VMAF 90.94 predicted video stream size 124.37 MiB (22%) taking 18 minutes
```
çıktısını verecektir. Ben daha önce denemek için kullandığımdan bende cache'den verileri hemen getirdi.

Bize önerdiği ayar **CRF: 28 Preset: Veryfast** şeklinde olup tahmini olarak çevirinin 18 dk süreceğini ve sonuç dosya boyutunun %22'si kadar olacağını (neredeyse 5'te bir) öngörüyor. Özellikle öngörüyor dememim sebebi videonun tamamı özelinde bir kontrol değil de aldığı örnekler özelinde bir tahmin yürütttüğü için ufak da olsa bir hata payı mümkün. Bu bilgileri aldıktan sonra ister ffmpeg üzerinden siz ilgili komutları yazıp çeviriye başlayabilirsiniz ister de size verdiği ve `Encode with: ab-av1 ....` diye başlayan komutu direk yapıştırarak aynı araç içerisinden çeviriyi de yapabilirsiniz. Yani size komutu dahi veriyor :D

### Gerçek dünya testleri

Evdeki hesap her zaman pazara uymadığı için ve bunca yazının da gerçekten işe yarayıp yaramadığından emin olmak için test etmek iyi olacaktır. Videonun "Full Movie – First version (HD rendered) - HD 1080p (mov)" olan versiyonunu indirdim. İlgili video 12dk 14sn uzunluğunda ve 557M (583774083) boyutunda olup çeviri işlemi ffmpeg ile yapıldığında aşağıdaki:

```
x265 [info]: HEVC encoder version 3.5
x265 [info]: build info [Linux][GCC 11.2.0][64 bit] 8bit+10bit+12bit
x265 [info]: using cpu capabilities: MMX2 SSE2Fast LZCNT SSSE3 SSE4.2 AVX FMA3 BMI2 AVX2
x265 [warning]: Specifying a decoder level with constant rate factor rate-control requires
x265 [warning]: enabling VBV with vbv-bufsize=160000kb vbv-maxrate=160000kbps. VBV outputs are non-deterministic!
x265 [info]: Main profile, Level-5.1 (High tier)
x265 [info]: Thread pool created using 4 threads
x265 [info]: Slices                              : 1
x265 [info]: frame threads / pool features       : 2 / wpp(13 rows)
x265 [info]: Coding QT: max CU size, min CU size : 64 / 8
x265 [info]: Residual QT: max TU size, max depth : 32 / 1 inter / 1 intra
x265 [info]: ME / range / subpel / merge         : hex / 57 / 1 / 2
x265 [info]: Keyframe min / max / scenecut / bias  : 24 / 250 / 40 / 5.00 
x265 [info]: Lookahead / bframes / badapt        : 15 / 4 / 0
x265 [info]: b-pyramid / weightp / weightb       : 1 / 1 / 0
x265 [info]: References / ref-limit  cu / depth  : 2 / on / on
x265 [info]: AQ: mode / str / qg-size / cu-tree  : 2 / 1.0 / 32 / 1
x265 [info]: Rate Control / qCompress            : CRF-28.0 / 0.60
x265 [info]: VBV/HRD buffer / max-rate / init    : 160000 / 160000 / 0.900
x265 [info]: tools: rd=2 psy-rd=2.00 early-skip rskip mode=1 signhide tmvp
x265 [info]: tools: fast-intra strong-intra-smoothing lslices=5 deblock sao
frame=17620 fps= 22 q=32.8 Lsize=  124521kB time=00:12:14.07 bitrate=1389.6kbits/s speed=0.909x    
x265 [info]: frame I:    172, Avg QP:26.60  kb/s: 9754.63 
x265 [info]: frame P:   3528, Avg QP:28.30  kb/s: 3513.82 
x265 [info]: frame B:  13920, Avg QP:34.37  kb/s: 577.41  
x265 [info]: Weighted P-Frames: Y:4.1% UV:2.5%
x265 [info]: consecutive B-frames: 4.8% 0.6% 0.8% 1.0% 92.8% 

encoded 17620 frames in **807.69s (21.82 fps)**, 1254.95 kb/s, Avg QP:33.08
```

çıktıyı verdi ve neticeten benim cihazımda (CPU i5-7300U) yaklaşık (807sn) 13,5 dk sürdü. Sonuç dosya boyutu ise 122 M (127509197) oldu. Ardından FFMPEG ile bu sefer de VMAF değerini hesaplatmak ve tüm sonuçları bir araya getirip değerlendirmek istedim. VMAF değerini hesaplamak için aşağıdaki komutu kullandım.

```
ffmpeg -i output.mkv -i tears_of_steel_1080p.mov -lavfi libvmaf -f null –
```

ve bana sonuç olarak aşağıdaki çıktıyı verdi:

```
[out#0/null @ 0x558d1bc41180] video:8259kB audio:126464kB subtitle:0kB other streams:0kB global headers:0kB muxing overhead: unknown
frame=17620 fps= 12 q=-0.0 Lsize=N/A time=00:12:14.16 bitrate=N/A speed=0.492x    
[Parsed_libvmaf_0 @ 0x558d1a9b2880] **VMAF score: 91.112250**
```

| # | Orjinal Dosya | AB-AV1 in Vaadi | Gerçek Dünya Testi | Fark |
|---|---|---|---|---|
| VMAF | % 100 | % 90,94 | % 91.11 | %+ 0,17 |
| Dosya Boyutu | 557 MB | 124,37 MB (%22) | 122 MB (%21.84) | 2,4 MB %-1,94 |
| Uzunluk/Çevirirken geçen süre | 12dk 14sn | 18 dk | 13dk  27 sn | 4 dk 33 sn %-25,28 |

Sonuç olarak hepsini bir araya getirdiğimizde gerçekten iç açıcı ve vaat edilenden daha iyi sonuçlar elde etmiş olduk. Bütün bu süreçler devam ederken PC'yi kullanmaya ve bu yazıyı yazmaya devam ettiğim de düşünülürse çok güzel sonuçlar verdiğini söyleyebiliriz.

## Anlatım dışı bırakılan kısımlar

Bu rehberde ses dosyalarının yeniden sıkıştırılması veya dönüştürülmesi konusuna hiç girmedim. Eğer böyle bir istek gelir ise bu kısma ekleme yapabilirim. Fakat 192 kb/s gibi bir bitrate bile kullansanız sonuç dosyayı çok farkettirmeyeceği için bu nedenle dikkate almadım. Odyofil olarak da adlandırılan kişilerden biri iseniz ve 5.1 veya 7.1 bir ses sistemine sahipseniz eğer işte o zaman bu kısım bir anlam ifade edebilir. Onun haricinde ses dosyalarını direk olduğu gibi alabilirsiniz ki ek bir ayar yapmazsanız zaten böyle olmaktadır. 

# Son Söz ve Kaynakça

Artık siz de profesyoneller gibi neredeyse hiç kalite kaybı yaşamadan videolarınızı sıkıştırabilir ve alandan tasarruf edebilirsiniz. Elinizdeki Blu-ray ve UltraHD Blu-ray'leri daha az yer kaplayacak şekilde hard diskinize kaydetmek veya Plex gibi bir ev sinema sistemi kurmak istiyorsanız artık bunu gönül rahatlığı ile yapabilirsiniz. Bir sonraki rehbere kadar iyi seyirler ve esenlikler dileğiyle.

Kaynaklar:
* https://github.com/FFmpeg/FFmpeg
* https://ffmpeg.org/ffmpeg-codecs.html
* https://dvsgroup.com/tools/BitRateCalculator.php
* https://www.imdb.com/title/tt1302006/
* https://x265.readthedocs.io/en/stable/presets.html
* https://streaminglearningcenter.com (Grafik)
* https://goughlui.com (Grafik)
* https://en.wikipedia.org/wiki/High_Efficiency_Video_Coding_tiers_and_levels
* https://github.com/Netflix/vmaf 
* https://github.com/alexheretic/ab-av1 
* https://aur.archlinux.org/packages/ab-av1
* https://github.com/alexheretic/ab-av1
* https://mango.blender.org/
* https://ottverse.com/analysis-of-svt-av1-presets-and-crf-values/
* https://superuser.com/questions/1556953/why-does-preset-veryfast-in-ffmpeg-generate-the-most-compressed-file-compared
* https://codecalamity.com/encoding-uhd-4k-hdr10-videos-with-ffmpeg/
* İlk Yayım: [BTT](https://btt.community/t/ffmpeg-ile-video-cevirmek-icin-en-uygun-ayari-kolayca-bulmak/4443)
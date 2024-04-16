---
title: "Fotoğraf sıkıştırma yöntemleri rehberi"
date: 2024-06-16
tags: ["imagemagick", "webp", "cwebp", "avif", "heic", "heif", "magick", "avifenc"]
author: "Wise"
draft: false
---    

# Giriş

|||
|:---:|:---:|
![1](/images/fotograf-compress-magick/giris1.png) | ![2](/images/fotograf-compress-magick/giris2.png)

Günümüzde yüksek kaliteli görüntüler satın aldığımız fotoğraf makineleri veya en basitinden cebimizdeki telefonlar aracılığı ile çok kolay ulaşılabilir hale geldi. Mesela son 4-5 sene içerisinde aldığımız bir telefon ile yüksek çözünürlüklü (50-100MP) fotoğraf çekebiliyor veya yüksek çözünürlüklü olmasa dahi RAW formatında daha fazla veri içeren fotoğraflar elde edebiliyoruz. Yani eskiden dert yakındığımız birçok konu (çözünürlük, kalite vb.) günümüzdeki teknolojiler tarafından sorun olmaktan çıkarıldı. Fakat bununla birlikte beraberlerinde yeni sorunları da getirmeyi ihmal etmediler. Bunun en bilinen örneği ise yüksek dosya boyutları nedeniyle kısa sürede dosyaları depolayamaz hale gelmek. Örnek vermek gerekirse eğer yakın zamanda bir arkadaşımın yaşadığı bir durumdan bahsetmek isterim. Kendisi bir iPhone kullanıcısı olarak telefonunu aldığı günden beridir aralıksız her etkinliğimizde grup fotoğraflarımızı çeker ve kendisi de bunun haricinde fotoğraf çekmeyi sever. Sonuç olarak kısa bir süre içerisinde telefonunun tüm hafızasını (çoğunluğu fotoğraf olmak üzere) doldurdu. Çektiği fotoğrafları çok sevdiğinden ve hiçbirini silmek istemediğinden dolayı bana bir çözüm bulmam için geldi. Ben de bu süreçte kendisine önerdiğim çözümleri ve bu süreçte yaptığım açıklamaları bir rehber haline getirip size sunmak istedim. 

Bu yazıda, modern görüntü sıkıştırma araçlarından ikisi olan avifenc ve cwebp'yi ele alacağız. Özellikle web geliştiricileri (benim tanışma şeklimde de böyle oldu) ve tasarımcılar için değerli olan bu araçlar, AVIF ve WebP formatlarında yüksek verimli sıkıştırma sunar. Her iki araç da ücretsiz ve açık kaynaklı olup, geniş bir kullanıcı topluluğu tarafından desteklenmektedir. WebP formatı Google tarafından geliştirilmekte olup Open Media Alliance tarafından geliştirilmektedir. 

# Fotoğraf Sıkıştırma Teknikleri: Detaylı Bir İnceleme

BU yazıda fotoğraf sıkıştırma tekniklerinin temellerini ve bu tekniklerin nasıl çalıştığını inceleyeceğiz. Öncelikle kayıplı ve kayıpsız sıkıştırma arasındaki farkları, ardından her iki tür sıkıştırmada kullanılan yöntemleri ele alacağız. Ardında da avifenc ve cwebp araçlarının nasıl kullanılacağını, avantajlarını ve en iyi uygulama yöntemlerini inceleyeceğiz. Bu kısım biraz teknik ilerleyecek olup yazının sonraki kısmında ise her zaman olduğu gibi bu sürecin başkaca bir yazılım ile hızlı ve çok teknik detaya girmeden uygulanmasından da bahsedeceğim. Bu kısmı isterseniz atlayıp direkt yandan "Imagemagick Yazılımı" ile ilgili başlığa tıklayabilirsiniz. 

## Kayıplı ve Kayıpsız Sıkıştırma Arasındaki Fark

### Kayıpsız Sıkıştırma

Kayıpsız sıkıştırma, görüntü kalitesini hiç değiştirmeden veri boyutunu azaltmayı amaçlar. Bu yöntemde, orijinal veri sıkıştırıldıktan sonra tam olarak geri dönüştürülebilir. Kayıpsız sıkıştırma özellikle yüksek kaliteden az da olsa ödün vermek istemeyen aynı zamanda da mümkün olan bir miktarda yer kazanmayı isteyen kişiler için uygundur. Ayrıca kayıpsız sıkıştırma genellikle pikseller arası korelasyonun düşük olduğu şekil gibi görüntülerde kullanıma daha müsaittir.

### Kayıplı Sıkıştırma

Kayıplı sıkıştırma dosya boyutunu daha fazla küçültmek için bir miktar veri kaybına izin verir. Bu yöntemde sıkıştırma sonrası görüntü orijinaline çok benzerdir fakat birebir aynı değildir. Mesela renk uzayı, bazı renklerin kodlanması ve bir takım detaylarda değişmeler/azalmalar olması muhtemeldir. Kayıplı sıkıştırma genel olarak webde paylaşılacak görüntülerde, sosyal medya ve genel fotoğraf depolama çözümleri için yaygın olarak kullanılır. Kayıplı sıkıştırma ile dosya boyutunu önemli ölçüde düşürme karmaşık ve yüksek korelasyona sahip görüntülerde daha çok kullanılmaktadır. 

## Kayıpsız Sıkıştırmada Kullanılan Yöntemler

### Run-Length Encoding (RLE)

Run-Length Encoding, veri dizilerinde tekrarlanan öğeleri sıkıştırmak için kullanılır. Örneğin, bir görüntüde ardışık olarak gelen aynı renkteki pikseller, tek bir veri değeri ve bu değerin tekrar sayısı ile temsil edilir. Bu yöntem, uzun tekrarlanan piksellerin olduğu görüntülerde oldukça etkilidir. Sıralı renk tekrarlarının olduğu homojen renk dağılımlı görüntülerde RLE algoritması başarılı bir sıkıştırma yapabilmektedir. Diğer taraftan tekrarların az olduğu veya renklerin homojen bir dağılım göstermediği görüntülerde algoritmanın başarısı düşük kalabilmektedir. Örneğin aynı meyve/sebzelerden oluşan bir tabağın çekilen fotoğrafı bu yöntem için oldukça uygun bir seçimdir.

### Huffman Coding

Zamanında MIT'de öğrenci olan David Huffman tarafından hocasının verdiği bir ödev üzerine geliştirilmiştir. Yaygın olarak kullanılan sıkıştırma yöntemlerinde son işlem olarak kullanılır ve muhtemelen sıkıştırma algoritmalarında en yaygın olarak kullanılan bileşendir. Huffman kodlamanın temel amacı, verideki frekanslarına göre veri parçalarının her birine değişken uzunluklarda kodlar atayarak verinin toplam kod uzunluğunu en aza indirmektir. Yüksek frekanslı parçalara daha kısa kodlar, düşük frekanslı parçalara ise nispeten daha uzun kodlar atanarak dosya boyutu düşürülmüş olur. Örneğin 0001 olarak kodlanan bir parçanın karşımıza çıkma olasılığı (mesela) 00000001 olarak kodlanmış bir parçadan daha yüksektir. Buna göre ilk parçaya daha kısa bir kod atanırken diğer parçaya daha uzun bir kod atanacaktır.

### LZW (Lempel-Ziv-Welch)

LZW, veri dizilerindeki tekrarları tespit eder ve bu tekrarları sıkıştırılmış bir kod olarak saklar. Daha kısa kod yerine geçtiği dizeden daha az yer kaplar ve daha küçük bir dosya elde edilir. Girdi verilerinde uzun veya tekrarlayan kelimelerin sayısı arttıkça algoritmanın verimliliği de artar. Grafik dosya formatları (GIF) ve çeşitli arşiv formatları (ZIP) gibi birçok uygulamada kullanılır. Bunu LZ77/78 olarak biliyor veya duymuş da olabilirsiniz. Daha önce 7zip kullanmış iseniz bununla karşılaşmış olmanız pek muhtemel. 

### Flate/Deflate

Flate/Deflate Phil Katz tarafından 90’lı yılların ortalarında geliştirilmiş kayıpsız veri sıkıştırma formatıdır. Huffman Kodlaması ve LZ77 algoritmasının bir bileşimidir. PNG kayıpsız görüntü sıkıştırma standardı Deflate algoritmasını kullanmaktadır. Sıkıştırılacak olan veri birbirini takip eden bloklar kümesi olarak düşünülür. Her blok LZ77 algoritması ve Huffman kodlamasının birlikte kullanılması ile sıkıştırılır. Her blok için oluşturulan Huffman ağacı bir önceki ve bir sonraki bloktan bağımsızdır. Sıkıştırılabilen blokların büyüklüğü değişkendir. Deflate algoritması Huffman ağacının etkili kodlama yapamayacak kadar büyüdüğünü gördüğünde, yeni bir Huffman ağacı oluşturmak için o bloğu sonlandırarak yeni bir blok başlatır. Böylece daha verimli bir sıkıştırma yapmayı amaçlar. 

### Color Quantization (Kısmen)

Renk sayısının veya derinliğinin azaltılmasıyla ya da renk uzayında yapılan değişikliklerle sıkıştırma sağlanır. Örneğin, bir görüntüde kullanılan renk sayısını 256 ile sınırlayarak dosya boyutu düşürülebilir. Bu teknik, genellikle GIF formatında kullanılır. Kayıpsız sıkıştırma yöntemlerinin sonuna bunu da eklememin sebebi kısmi bir bakış açısına göre kayıpsız sayılabileceği yönünde. Burada K-Means denilen bir terim devreye giriyor ve düzgün uyarlandığında tek başına dosya boyutunu 100'de 1 oranına kadar (aşırı ve bence gerçek dışı bir oran) düşürebildiğinden bahsediliyor.

## Kayıplı Sıkıştırmada Kullanılan Yöntemler

### Transform Kodlama (DCT)

Transform kodlama, görüntü verilerini frekans bileşenlerine dönüştürerek sıkıştırma sağlar. JPEG sıkıştırmasında yaygın olarak kullanılan Discrete Cosine Transform (DCT) buna bir örnektir.Sıkıştırma aşamalarında ilk olarak görsele RGB-YUV renk uzayı dönüşümü uygulanır. Sonrasında görsel 8×8 bloklara ayrılır ve her bloğa DCT uygulanır. DCT uygulanmış blok, 1-100 arasında, 1 en düşük kalite ve 100 en yüksek kalite olmak üzere kullanıcı tarafından seçilebilen bir kalite parametresine karşılık gelen kuantizasyon matrisi kullanılarak kuantalanır.

Bununla ilgili Computerphile videosu: https://www.youtube.com/watch?v=Q2aEzeMDHMA

### Chroma Subsampling

Chroma subsampling, insan gözünün renk değişikliklerine duyarlılığının düşük olmasını kullanarak renk bilgilerini azaltır. YCbCr renk modelinde renk bileşenlerinin (Chroma) yatay ve/veya dikey çözünürlüğünü düşürerek sıkıştırma yapar. İlerleyen adımlarda size göstereceğim örnek görsellerden .yuv uzantılı olan 4:2:0 olarak sample edilmişken .y4m ve .ycbcr 4:4:4 olarak kabul edilebilir. Bu da herhangi başka bir işlem yapmandan 4:4:4 --> 4:2:0 veya 4:1:1 dönüşümünde %50 4:4:4: --> 4:2:2 dönüşümünde %33 civarında bir dosya boyut azalması sağlıyor. Bu bildiğimiz anlamda bir sıkıştırma yapmadan elde edilen ve gerçekten çok yüksek bir oran olan bir kazanç.

### Fraktal Sıkıştırma

Fraktal sıkıştırma, görüntüdeki tekrarlayan desenleri matematiksel fraktal algoritmalarla sıkıştırır. Fraktal sıkıştırmanın tekrarlanan fonksiyonlar ile gerçeklenebileceği fikri ilk defa Michael Barnsley ve Alan Sloan tarafından ortaya atılmıştır.  Fraktal tekniğinin en önemli avantajı, açma işleminin basit ve hızlı olmasıdır. Fakat sıkıştırma işlemi, açmanın tam tersine çok karmaşıktır. Bir resimdeki tekrar eden fraktalları bulmak milyonlarca, milyarlarca karşılaştırma işleminin yapılmasını gerektirebilir. Sıkıştırma işleminin çok zaman alması nedeniyle fraktal tekniği henüz yeterince yaygınlaşamamıştır. Bu yöntem, yüksek sıkıştırma oranları sağlar ancak bahsedildiği gibi hesaplama süreci genellikle daha uzundur.

## Kayıpsız (veya kısmen kayıpsız) Görüntü Çözümleri

### YCBCR

YCBCR, bir renk kodlama şemasıdır ve tek başına bir görüntü formatı değildir. Genellikle dijital video ve fotoğrafçılıkta kullanılır. Kayıpsız veya kayıplı sıkıştırma ile kullanılabilir. JPEG gibi kayıplı formatlarda da (çok mantıklı olmasa da) YCBCR renk alanını kullanabilir.

### YUV

YUV (ycbcr de olduğu gibi) bir renk alanı ve aynı zamanda video verilerini saklamak için kullanılan bir format olabilir. Veriler sıkıştırılmamış veya kayıplı/kayıpsız sıkıştırma ile saklanabilir. YUV dosyalarının kayıpsız olup olmadığı kullanılan sıkıştırma yöntemine bağlıdır. Bugün biz sıkıştırılmamış olan halinden faydalanacağız.

### Y4M

Y4M, YUV4MPEG2 video formatının bir dosya uzantısıdır. Bu format sıkıştırılmamış YUV video verilerini içerir ve bundan dolayı kayıpsızdır. Y4M genellikle video/fotoğraf işleme ve düzenleme yazılımlarında ara format olarak kullanılır. Biz de bugün bu amaçla kullanacağız.

### PPM

PPM  sıkıştırılmamış (dolayısıyla kayıpsız) bir görüntü formatıdır. PPM'de her pikselin kırmızı, yeşil ve mavi (RGB) bileşenlerini ASCII veya ikili formda saklanır. Kanaviçe yapar gibi düşünebilirsiniz. Sıkıştırılmamış olduğu için dosya boyutları oldukça büyük olabilir. Konumuzda kullanılacak ana giriş formatı bu olacaktır. Diğer üç format ise ara format veya kontrol formatı olarak kullanılacaktır. 

## Kayıplı Sıkıştırma Kullanan Görüntü Formatları

### WebP

Google tarafından geliştirilen WebP, hem kayıplı hem de kayıpsız sıkıştırma destekleyen modern bir görüntü formatıdır. JPEG'e kıyasla daha yüksek sıkıştırma oranları ve daha düşük dosya boyutları sunar. Modern tarayıcıların büyük kısmı tarafından hali hazırda kullanılan ve ek bir bileşene gerek bırakmayan bir formattır.

### HEIC (High Efficiency Image Coding)

HEIC/HEIF özellikle Apple cihazlarda yaygın olarak kullanılan bir görüntü formatıdır. Yüksek verimlilikte sıkıştırma sağlamak için HEVC (High Efficiency Video Coding) teknolojisini kullanır. Fakat Apple burada farklı bir bakış açısı ile şöyle bir amaç gütmüştür. Normalde (örneğin) 5 MB olacak görüntü dosya boyutunu 1 MB'a indirmek yerine görüntü dosya boyutunu gene 5 MB olarak hedefleyip içine sıkıştırılmadan önceki hali 25 MB olan bir görüntü sıkıştırmayı hedeflemişlerdir. Dolayısıyla siz dosya boyutu açısından net bir fark göremesenizde sanki fotoğraflarınız daha kaliteli çıkıyormuş izlenimine kapılıyorsunuz. Bu nedenle bu formatı rehberin ilerleyen kısımlarında kullanmayacağız.

### AVIF (AV1 Image File Format)

AVIF AV1 video codec'inin (önceki rehberimizde bajsettiğimiz) sıkıştırma tekniklerini kullanan modern bir görüntü formatıdır. Hem kayıplı hem de kayıpsız sıkıştırma seçenekleri sunar ve yüksek sıkıştırma oranları ile dikkat çeker. Modern tarayıcılar tarafından destekleniyor olsa da işletim sistemi ve dağıtıma göre bazen bir paket/eklenti yüklemek gerekebiliyor. Örneğin Windows'da Store üzerinden bir eklenti yüklenmesinden sonra bu formatla üretilmiş görüntüleri açabiliyorsunuz.

# Sıkıştırma Tekniklerinin Kullanımı

## Testlerde Kullanılacak Görüntüler

Testlerde kullanılacak görüntüler için https://imagecompression.info/ adlı websitesini tercih ettim. Kalite farkının ve dosya boyutundaki olabilecek maksimum durumun net belirli olması açısından çok yüksek kaliteli ve hiç sıkıştırılmamış görüntüler seçtim. Kullandığım görüntü grubu [RGB-8Bit](https://imagecompression.info/test_images/) olarak belirtilmiş 14 adet renkli görüntüden ibaret. Bu görüntüler ayrıca aklımdaki çeşitli test koşullarının hepsine de uygun olduğu için ayrıca kendim görüntü üretmek zorunda kalmadım. Bu görseller websayfasında da belirtildiği üzere herhangi bir engelleyici telif hakkı kısıtlaması olmaksızın kullanılabilir.

> These Images are available without any prohibitive copyright restrictions.
> These images are (c) there respective owners. You are granted full redistribution and publication rights on these images provided

Görüntülerin bilgileri aşağıdaki gibidir:

| No | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) |
| :---: | :---: | :---: | :---: |
| 1 | artificial.ppm | 18874385 | 19M | 
| 2 | big_building.ppm | 117158993 | 112M |
| 3 | big_tree.ppm | 83101217 | 80M | 
| 4 | bridge.ppm | 33392120 | 32M | 
| 5 | cathedral.ppm | 18048017 | 18M | 
| 6 | deer.ppm | 32032706 | 31M | 
| 7 | fireworks.ppm | 22127633 | 22M | 
| 8 | flower_foveon.ppm | 10287665 | 9.9M | 
| 9 | hdr.ppm | 18874385 | 19M | 
| 10 | leaves_iso_1600.ppm | 18048017 | 18M | 
| 11 | leaves_iso_200.ppm | 18048017 | 18M | 
| 12 | nightshot_iso_100.ppm | 22127633 | 22M | 
| 13 | nightshot_iso_1600.ppm | 22127633 | 22M | 
| 14 | spider_web.ppm | 36363281 | 35M | 
| \ | \ | / | / | 
| Toplam | --- | 470611702 | 471M | 

## WebP Formatı (cwebp aracı)

Bu formatta bir görüntü üretmek için önce gerekli olan `cwebp` aracını kurmamız gerekiyor. Kullandığınız dağıtıma göre değişmekle beraber Ubuntu/Fedora/Arch gibi ana dağıtımların repolarında direkt mevcut olup Windows kullanıcıları ise ilgili [websitesinden](https://developers.google.com/speed/webp/download?hl=tr) indirebileceklerdir. 

### Kurulum adımları

1. **Ubuntu**

```
sudo apt-get install libwebp
```

2. **Fedora**

```
sudo dnf install libwebp-tools
```

3. **Arch**

```
sudo pacman -S libwebp
```

### Kullanılacak parametreler

Cwebp aracının manual sayfasına baktığınızda çok fazla bir parametresi olmadığını göreceksiniz. Standart kullanım girdisi şu şekildedir:

```
cwebp input_file -o output_file.webp [options]
```

Bu hem son kullanıcı açısından oluşacak kafa karışıklığını ortadan kaldırıyor hem de araç ne vaadediyorsa sadece onu yapıyor. Bizim için gerekli olabilecek parametreler ise aşağıdaki gibidir:

`-q sayı`: Çıktı olarak verilecek görüntünün kalite değerini ayarlamanızı sağlıyor. Değer olarak float girişi yapabilirsiniz fakat o kadar virgüllü sayılarla uğraşmaya değeceğini zannetmiyorum. Default olarak gelen değer değiştirmezseniz 75. Yani normal görüntüdeki detayların %75'i korunuyor.

`-pass sayı`: Görüntünün yeniden oluşturulması sırasında yapılacak olan geçiş sayısını belirtiyor. Değer 0 ile 10 arasında bir tam sayı olarak belirtilmeli. Default değer 6. Dosya boyutuna ve kaliteye bir miktar etki ediyor. Değer ne kadar yüksek ise o kadar küçük dosya boyutunda o kadar yüksek kalite elde edilecektir. Sonuç olarak da maalesef dosya başına ayrılan işleme zamanı artacaktır. 

`-metadata metin`: (isteğe bağlı) Forumda sorulduğu için ekstra yer veriyorum. Metadata verilerinin ne kadarının tutulup tutulmayacağı konusunda karar vermenize yarayan parametre. Mümkün olan değerler `all`, `none`, `exif`, `icc`, `xmp`. Default değer ise `none`. Yani özellikle gerekmediği sürece değiştirmenizi önermemem.

Sonuç olarak benim kullandığım komut mevcut parametreler dahil olmak üzere:

```
cwebp input_file -o output_file.webp -q 75 -pass 10
```

## Avif (avifenc aracı)

Bu formatta bir görüntü üretmek için de bir önceki formatımızda olduğu gibi bir araç gerektiriyor. Gerekli olan aracımızın adı `avifenc`. Gene kullandığınız dağıtıma göre değişmekle beraber Ubuntu/Fedora/Arch gibi ana dağıtımların repolarında direkt mevcut. Windows kullanıcıları bu sefer projenin [Github sayfasından](https://github.com/AOMediaCodec/libavif/releases) son sunulmuş release'i indirmesi gerekecektir. 

### Kurulum adımları

1. **Ubuntu**

```
sudo apt-get install libavif-bin
```

2. **Fedora**

```
sudo dnf install libavif-tools
```

3. **Arch**

```
sudo pacman -S libavif
```

### Kullanılacak parametreler

Avifenc aracının manual sayfası önceki araca göre biraz daha uzun ve bence kafa karıştırabilecek olmasına rağmen ihtiyacımız olacak parametreler gene çok fazla değil. Örneğin standart kullanımı gene benzer şekilde:

```
avifenc input_file output.avif [options]
```

Bu bize her iki araç açısından bir benzerlik sunup nispeten işimizi kolaylaştırıyor. Bu sefer ihtiyacımız olabilecek parametreler ise aşağıdaki gibi:

`--min sayı`: Renk için minimum quantizer değerini (niceleyiciyi) belirlememize yarayan değer. Sayı büyüdükçe kalite azalmakta dosya boyutu ise küçülmektedir. Kabul edilen değer aralığı 0 ile 63 arasıdır. 0 girilmesi durumunda kayıpsız çeviri yapılmaya çalışılır. Default değer 10'dur.

`--max sayı`: Renk için minimum quantizer değerini (niceleyiciyi) belirlememize yarayan değer. Sayı büyüdükçe diğer parametre ile aynı etkiyi yapar. Kabul edilen değer aralığı yine 0 ile 63 arasıdır. 0 girilmesi durumunda kayıpsız çeviri yapılmaya çalışılır. Default değer 40'dur.

Bir önceki aracımızın aksine avifenc tekil bir kalite değeri yerine bir kalite aralığını girdi olarak kabul ediyor. Bu şekilde de bazı yerlerde daha fazla sıkıştırma yaparken diğer yerlerde durumun özelliğine göre daha az sıkıştırma yaparak optimal dosya boyutu/kalite oranına ulaşmayı hedefler. Yaptığım testlere göre ama (net bilgi olmamakla birlikte) yaklaşık %60-65 civarına tekabül edecek bir kaliteden bahsedebiliriz.

`--speed sayı`: Dosya değerlendirilirken kodlayıcının ne kadar hızlı hareket etmesi gerektiğini belirten değer. Ne kadar yavaş hareket ederse o kadar kaliteli yeniden kodlama ve düşük dosya boyutuna ulaşabilecekken tam tersi şekilde daha uzun bir süre (gerçekten uzun oluyor bazen) sonucunu doğuracaktır. Kabul edilen değer aralığı 0 ile 10 arasındadır. 0 en yavaş 10 en hızlıdır. Default değer 6'dır.

`--jobs sayı`: Bu görev için oluşturulacak iş sayısını veya kullanılacak core/thread sayısını belirten değer. Linux kullanıcıları bu parametre ile aşinadır diye düşünüyorum. Girilebilecek değer aralığı CPU'nuza göre değişecek olup minimum değer 1 olup maksimum ve aynı zamanda da Default değer de `all` dır

Sonuç olarak benim kullandığım komut mevcut parametreler dahil olmak üzere:

```
avifenc input_file output.avif --jobs all --speed 0 --min 10 --max 40
```

## Çeviri Sonuçları

Çeviri sırasında girdi olarak `.ppm` uzantılı ana test dosyalarımızın aynı zamanda `.ycbcr`, `.y4m`, `.tiff`, `.yuv` ve `.png` uzantılı kopyaları üretilmiştir. Bu kopyalar hem farklı formatlardan çeviri kalitesinin test edilmesi hem de ara formatların kullanılmasını gerektiren durumlar için hazırlanmıştır. Dosyaların hepsini websitesinde yer kaplamaması adına bir [Github Reposu'na](https://github.com/wiseweb-works/fotograf-cevirme) yükledim. ORadan direkt ulaşabilir ve isterseniz indirebilirisiniz.

Avif açısından farklı `--speed` değerlerinin etkisinin de anlaşılması için 0, 6 ve 10 değerleri için ayrı ayrı çeviriler yapılmıştır. 

Webp açısından ise speed parametresi olmadığı ve pass parametresi hızı çok etkilemediği için tüm çevirilerde pass değeri 10 olarak alınmıştır.

Aşağıda her bir örnek fotoğraf açısından çeviri sonuçlarını bulabilirsiniz. Çok yer kaplaması için ilk fotoğraftan sonrasını görmek için genişlet butonuna tıklamanız gerekmektedir.

| Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
| :---: | :---: | :---: | :---: |
| artificial.ppm | 18874385 | 19M | Dosya aslı |
| artificial.ycbcr | 18874368 | 19M | 100,00 |
| artificial.y4m | 18874446 | 19M | 100,00 |
| artificial.tiff | 18874784 | 19M | 100,00 |
| artificial.yuv | 12582912 | 12M | 66,67 |
| artificial.png | 1656508 | 1.6M | 8,78 |
| artificial_speed0.avif | 149028 | 146K | 0,79 |
| artificial_speed10.avif | 299051 | 293K | 1,58 |
| artificial_speed6.avif | 168926 | 165K | 0,90 |
| artificial.webp | 229228 | 224K | 1,21 |

<details>
  <summary>Diğer görüntüler için tıklayınız</summary>
  
  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | big_building.ppm | 117158993 | 112M | Dosya aslı | |
  | big_building.ycbcr | 117158976 | 112M | 100,00| |
  | big_building.y4m | 117159054 | 112M | 100,00| |
  | big_building.tiff | 117160144 | 112M | 100,00| |
  | big_building.yuv | 78105984 | 75M | 66,67| |
  | big_building.png | 62150284 | 60M | 53,05| |
  | big_building_speed0.avif | 3014156 | 2.9M | 2,57| |
  | big_building_speed10.avif | 2959255 | 2.9M | 2,53| |
  | big_building_speed6.avif | 2943252 | 2.9M | 2,51| |
  | big_building.webp | 3414240 | 3.3M | 2,91| |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | big_tree.ppm | 83101217 | 80M | Dosya aslı |
  | big_tree.ycbcr | 83101200 | 80M | 100,00 |
  | big_tree.y4m | 83101278 | 80M | 100,00 |
  | big_tree.tiff | 83102224 | 80M | 100,00 |
  | big_tree.yuv | 55400800 | 53M | 66,67 |
  | big_tree.png | 48100895 | 46M | 57,88 |
  | big_tree_speed0.avif | 3253303 | 3.2M | 3,91 |
  | big_tree_speed10.avif | 2982856 | 2.9M | 3,59 |
  | big_tree_speed6.avif | 3033840 | 2.9M | 3,65 |
  | big_tree.webp | 2167850 | 2.1M | 2,61 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | bridge.ppm | 33392120 | 32M | Dosya aslı |
  | bridge.ycbcr | 33392103 | 32M | 100,00 |
  | bridge.y4m | 33392181 | 32M | 100,00 |
  | bridge.tiff | 33392624 | 32M | 100,00 |
  | bridge.yuv | 22269500 | 22M | 66,69 |
  | bridge.png | 19747093 | 19M | 59,14 |
  | bridge_speed0.avif | 1411838 | 1.4M | 4,23 |
  | bridge_speed10.avif | 1262682 | 1.3M | 3,78 |
  | bridge_speed6.avif | 1323802 | 1.3M | 3,96 |
  | bridge.webp | 1083864 | 1.1M | 3,25 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | cathedral.ppm | 18048017 | 18M | Dosya aslı |
  | cathedral.ycbcr | 18048000 | 18M | 100,00 |
  | cathedral.y4m | 18048078 | 18M | 100,00 |
  | cathedral.tiff | 18048416 | 18M | 100,00 |
  | cathedral.yuv | 12032000 | 12M | 66,67 |
  | cathedral.png | 9352750 | 9.0M | 51,82 |
  | cathedral_speed0.avif | 374017 | 366K | 2,07 |
  | cathedral_speed10.avif | 349586 | 342K | 1,94 |
  | cathedral_speed6.avif | 345661 | 338K | 1,92 |
  | cathedral.webp | 361172 | 353K | 2,00 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | deer.ppm | 32032706 | 31M | Dosya aslı |
  | deer.ycbcr | 32032689 | 31M | 100,00 |
  | deer.y4m | 32032767 | 31M | 100,00 |
  | deer.tiff | 32033226 | 31M | 100,00 |
  | deer.yuv | 21360408 | 21M | 66,68 |
  | deer.png | 21196494 | 21M | 66,17 |
  | deer_speed0.avif | 1730016 | 1.7M | 5,40 |
  | deer_speed10.avif | 1713279 | 1.7M | 5,35 |
  | deer_speed6.avif | 1660326 | 1.6M | 5,18 |
  | deer.webp | 994750 | 972K | 3,11 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | fireworks.ppm | 22127633 | 22M | Dosya aslı |
  | fireworks.ycbcr | 22127616 | 22M | 100,00 |
  | fireworks.y4m | 22127694 | 22M | 100,00 |
  | fireworks.tiff | 22128048 | 22M | 100,00 |
  | fireworks.yuv | 14751744 | 15M | 66,67 |
  | fireworks.png | 5642881 | 5.4M | 25,50 |
  | fireworks_speed0.avif | 149166 | 146K | 0,67 |
  | fireworks_speed10.avif | 187413 | 184K | 0,85 |
  | fireworks_speed6.avif | 154956 | 152K | 0,70 |
  | fireworks.webp | 183674 | 180K | 0,83 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | flower_foveon.ppm | 10287665 | 9.9M | Dosya aslı |
  | flower_foveon.ycbcr | 10287648 | 9.9M | 100,00 |
  | flower_foveon.y4m | 10287726 | 9.9M | 100,00 |
  | flower_foveon.tiff | 10288000 | 9.9M | 100,00 |
  | flower_foveon.yuv | 6858432 | 6.6M | 66,67 |
  | flower_foveon.png | 3131970 | 3.0M | 30,44 |
  | flower_foveon_speed0.avif | 54972 | 54K | 0,53 |
  | flower_foveon_speed10.avif | 68146 | 67K | 0,66 |
  | flower_foveon_speed6.avif | 58684 | 58K | 0,57 |
  | flower_foveon.webp | 70382 | 69K | 0,68 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | hdr.ppm | 18874385 | 19M | Dosya aslı |
  | hdr.ycbcr | 18874368 | 19M | 100,00 |
  | hdr.y4m | 18874446 | 19M | 100,00 |
  | hdr.tiff | 18874784 | 19M | 100,00 |
  | hdr.yuv | 12582912 | 12M | 66,67 |
  | hdr.png | 6974764 | 6.7M | 36,95 |
  | hdr_speed0.avif | 114743 | 113K | 0,61 |
  | hdr_speed10.avif | 132916 | 130K | 0,70 |
  | hdr_speed6.avif | 117136 | 115K | 0,62 |
  | hdr.webp | 143366 | 141K | 0,76 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | leaves_iso_1600.ppm | 18048017 | 18M | Dosya aslı |
  | leaves_iso_1600.ycbcr | 18048000 | 18M | 100,00 |
  | leaves_iso_1600.y4m | 18048078 | 18M | 100,00 |
  | leaves_iso_1600.tiff | 18048408 | 18M | 100,00 |
  | leaves_iso_1600.yuv | 12032000 | 12M | 66,67 |
  | leaves_iso_1600.png | 11327915 | 11M | 62,77 |
  | leaves_iso_1600_speed0.avif | 1108271 | 1.1M | 6,14 |
  | leaves_iso_1600_speed10.avif | 964810 | 943K | 5,35 |
  | leaves_iso_1600_speed6.avif | 1013569 | 990K | 5,62 |
  | leaves_iso_1600.webp | 883776 | 864K | 4,90 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | leaves_iso_200.ppm | 18048017 | 18M | Dosya aslı |
  | leaves_iso_200.ycbcr | 18048000 | 18M | 100,00 |
  | leaves_iso_200.y4m | 18048078 | 18M | 100,00 |
  | leaves_iso_200.tiff | 18048408 | 18M | 100,00 |
  | leaves_iso_200.yuv | 12032000 | 12M | 66,67 |
  | leaves_iso_200.png | 10110529 | 9.7M | 56,02 |
  | leaves_iso_200_speed0.avif | 780224 | 762K | 4,32 |
  | leaves_iso_200_speed10.avif | 757813 | 741K | 4,20 |
  | leaves_iso_200_speed6.avif | 762472 | 745K | 4,22 |
  | leaves_iso_200.webp | 720154 | 704K | 3,99 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | nightshot_iso_100.ppm | 22127633 | 22M | Dosya aslı |
  | nightshot_iso_100.ycbcr | 22127616 | 22M | 100,00 |
  | nightshot_iso_100.y4m | 22127694 | 22M | 100,00 |
  | nightshot_iso_100.tiff | 22128048 | 22M | 100,00 |
  | nightshot_iso_100.yuv | 14751744 | 15M | 66,67 |
  | nightshot_iso_100.png | 7635953 | 7.3M | 34,51 |
  | nightshot_iso_100_speed0.avif | 157003 | 154K | 0,71 |
  | nightshot_iso_100_speed10.avif | 213297 | 209K | 0,96 |
  | nightshot_iso_100_speed6.avif | 161694 | 158K | 0,73 |
  | nightshot_iso_100.webp | 223064 | 218K | 1,01 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | nightshot_iso_1600.ppm | 22127633 | 22M | Dosya aslı |
  | nightshot_iso_1600.ycbcr | 22127616 | 22M | 100,00 |
  | nightshot_iso_1600.y4m | 22127694 | 22M | 100,00 |
  | nightshot_iso_1600.tiff | 22128048 | 22M | 100,00 |
  | nightshot_iso_1600.yuv | 14751744 | 15M | 66,67 |
  | nightshot_iso_1600.png | 12520345 | 12M | 56,58 |
  | nightshot_iso_1600_speed0.avif | 789712 | 772K | 3,57 |
  | nightshot_iso_1600_speed10.avif | 686661 | 671K | 3,10 |
  | nightshot_iso_1600_speed6.avif | 703014 | 687K | 3,18 |
  | nightshot_iso_1600.webp | 576746 | 564K | 2,61 |

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | spider_web.ppm | 36363281 | 35M | Dosya aslı |
  | spider_web.ycbcr | 36363264 | 35M | 100,00 |
  | spider_web.y4m | 36363342 | 35M | 100,00 |
  | spider_web.tiff | 36363816 | 35M | 100,00 |
  | spider_web.yuv | 24242176 | 24M | 66,67 |
  | spider_web.png | 10272309 | 9.8M | 28,25 |
  | spider_web_speed0.avif | 106255 | 104K | 0,29 |
  | spider_web_speed10.avif | 126188 | 124K | 0,35 |
  | spider_web_speed6.avif | 113351 | 111K | 0,31 |
  | spider_web.webp | 165030 | 162K | 0,45 |

</details>

Sonuçlara bakıldığında dosya boyutunu (en büyük dosyaya göre) %1-5'ine kadar azaltabildiğini görüyoruz. Fakat gerçek dünyada bu tip RAW dosya formatları veya hiç sıkıştırılmamış dosyalar kullanmaıdğımız için bu derece bir boyut azalmasını görmeniz zor. Fakat png veya jpg uzantıları açısından değerlendirirsek dosya boyutunı %10-33 aralığına kadar düşürebildiğini çok rahat söyleyebiliriz. Gerçek dünya testlerinde de bu sonuçları görmeniz çok muhtemel.

# Otomatikleştirilmiş Hali: Imagemagick

|||
|:---:|:---:|
![1](/images/fotograf-compress-magick/mid1.png) | ![2](/images/fotograf-compress-magick/mid2.png)

Imagemagick [ImageMagick Studio LLC](https://imagemagick.org/) tarafından geliştirilen ve [açık kaynaklı](https://github.com/ImageMagick/ImageMagick) bir yazılımdır. Yazılımın lisansı mevcut olarak Github'da kullanılan o veya şu lisans diye net olarak söyleyemiyorum. Bir kısım yerlerde Apache 2.0 Lisansına çok yakın olduğunu söylemiş olsalar da tam bilgi için [LICENSE](https://raw.githubusercontent.com/ImageMagick/ImageMagick/main/LICENSE) dosyasına göz atmanızı öneririm.

Yazılımın odaklandığı ve çözmek istediği sorun fotoğraf ve benzeri dağıtım formatlarını dönüştürürken ve yukarıda bahsettiğim gibi yazılımları kullanmadan, teknik bilgilerle çok boğuşmadan ve istediğimiz sonuca bizi ulaştıracak bir program üretmek. Yazılımı herhangi bir linux dağıtımında indirmek istediğiniz zaman bağımlılık olarak birçok görüntü formatı/kütüphanesi birlikte iniyor ve sizi ek yazılımları tespit edip indirme gerekliliğinden kurtarıyor. Yazılım rehberin paylaşıldığı tarih itibariyle çalışma mantığı tüm araçlar açısından tekil bir parametre sistemi getirme ve gerekirse sizin verdiğiniz parametreyi gerekli aracın anlayacağı şekle dönüştürmek. Örneğin bir araç kalite için `-q` diğeri `--quality` diğeri `-Q` kullanıyor dahi olsa program bunları ezberlemenizi gerektirmiyor. Siz hangi formattan hangi formata dönüştürecek olursanız olun `-quality değer` parametresi ile bu sonuca ulaşabiliyorsunuz. İlk ve en önemli özelliği bu.

Bu yazılımı bulmadan önce kendim tüm bu araçlar açısından gerekli kütüphaneleri elle yüklüyor, hangi araç hangi formatları kabul ediyor bunu takip ediyor ve gerekirse ara formatlar üretiyordum. Mesela avif ppm den dönüşüme izin vermezken y4m den izin veriyor. Tam tersine cwebp ppm den dönüşüme izin verirken mesela ycbcr den dönüşüme izin vermiyor gibi. Fakat imagemagick aracını kullanırken bunları düşünmenize gerek yok. Çünkü o sizin için ara formatları kayıpszı bir şekilde oluşturup dosyanızı hedef formata dönüştürüyor ve gereksiz geçici dosyaları siliyor. Tüm bu anlattıklarım ise birkaç saniye içinde oluyor. Bu da sizin işinizi kolaylaştıracak ikinci güzel özelliği.

Bahsetmeyi değer bulduğum üçüncü özelliği ise toplu dosya çevirme veya başka tabirle batch convert. Bahsettiğim diğer araçların ve bahsi geçmeyen birçok aracın en büyük eksikliği toplu olarak dosya seçmenize izin vermemesi. Hepsi kendi için tek tek dosya çevirme üzerine odaklanmışlardır. Aşağıdakine benzer bir komut ile toplu çeviri yapmaları mümkün olmasına rağmen bu hem son kullanıcı açısından anlaması pek kolay olmadığı gibi her zaman için de uygulanması mümkün olmamaktadır.

Örnek komut (jpg --> webp): `find ./ -type f -name '*.jpg' -exec sh -c 'cwebp $1 -o "${1%.jpg}.webp" [options]' _ {} \;`

Fakat imagemagick aracımızda ise bu süreç çok daha kolay. Örnek komut (jpg --> webp): `magick mogrify -format webp -quality 90 *.jpg` ne kadar anlaşılır ve kolay değil mi gerçekten. Giriş verisi olarak birden fazla dosya seçilebildiği gibi çıkış formatı olarak da birden fazla dosya formatı seçilebilir. Örneğin bir klasördeki tüm JPG/PNG uzantılı fotoğraflarınızı webp ve avif uzantısına tek komut ile çevirebiliyorsunuz. Bu gerçekten süper bir imkan.

## Yazılımın indirilmesi ve/veya kurulması

Yazılım Linux, Mac, iOS ve Windows'u desteklemektedir. Windows için [exe uzantılı dosyayı](https://imagemagick.org/script/download.php#windows) indirip kullanabilirsiniz. 

Arch kullanıyorsanız:

```
sudo pacman -S imagemagick
```

Ubuntu kullanıyorsanız:

```
sudo apt-get install imagemagick
```

Fedora kullanıyorsanız:

```
sudo dnf install ImageMagick
```
 
Diğer linux dağıtımı kullanıcıları ise binary dosyası olarak indirebilir veya kendisi derleyebilir. Yükleme ile ilgili [talimatlara](https://imagemagick.org/script/install-source.php) Web sayfasından ulaşabilirsiniz.

## Yazılımın ayarları

Yazılımı kurduktan sonra `man magick` komutu ile yardım kısmına veya ayrıntılı bilgiye ulaşabilirsiniz. Sizi yaklaşık olarak (bir kısmını kırptım) şöyle bir ekran karşılayacaktır:

```
magick(1)
General Commands Manual
magick(1)

NAME
       magick - convert between image formats as well as resize an image, blur, crop, despeckle, dither, draw on, flip, join, re-sample, and much more.

SYNOPSIS
       magick [input-options] input-file [output-options] output-file
....

DESCRIPTION
....

         -define format:option
....
         -preview type        image preview type
         -quality value       JPEG/MIFF/PNG compression level
....
         -quiet               suppress all warning messages
         -verbose             print detailed information about the image
         -auto-orient         automatically orient image
         -strip               strip image of all profiles and comments
....
       Miscellaneous Options:
         -debug events        display copious debugging information
         -help                print program options
         -log format          format of debugging information
         -list type           print a list of supported option arguments
         -version             print version information

       Use any setting or operator as an output-option.  Only a limited number of setting are  input-option. They include: -antialias, -caption, -density, -define, -encoding, -font, -pointsize, -size, and -texture as well as any of the
       miscellaneous options.

       By  default,  the image format of ‘file' is determined by its magic number.  To specify a particular image format, precede the filename with an image format name and a colon (i.e. ps:image) or specify the image type as the file‐
       name suffix (i.e. image.ps).  Specify 'file' as '-' for standard input or output.

SEE ALSO
       ImageMagick(1)

COPYRIGHT
       Copyright (C) 1999 ImageMagick Studio LLC. Additional copyrights and licenses apply to this software, see file:///usr/share/doc/ImageMagick-7/www/license.html or https://imagemagick.org/script/license.php                                                                             
```

### Tekil dosya çevirme

En basit kullanım senaryosunda tek dosya çevirmek isterseniz aşağıdaki gibi kullanabilirsiniz.

JPG/JPEG'den WebP'ye dönüştürme
```
magick input.jpg -quality 75 output.webp
```

JPG/JPEG'den Avif'e dönüştürme
```
magick input.jpg -quality 75 output.avif
```

Gördüğünüz üzere komut çok az değişti. Quality değerini örnek olarak o şekilde belirledim. Siz birkaç fotoğraf üzerinden deneme yapıp sizin için en uygun değeri rahatlıkla bulabilirsiniz. Peki ben nasıl kullanıyorum veya hangi parametreleri kullanıyorum diye soracak olursanız eğer:

WebP için:

```
magick input.jpg -define webp:pass=10 -quality 75 output.webp
```

Avif için:

```
magick input.jpg -define avif:speed=0 -quality 75 output.avif
```

Buradaki `-define format:parametre` girdisi ilgili araç için ek bir özel parametre girmek istiyorsanız kullanılıyor. Bu iki araç açısından gerekli parametreleri daha iyi bildiğim için halen eski düzen böyle devam ediyorum.

### Çoklu dosya çevirme

Çoklu dosya çevirmek istediğiniz senaryoda gerekli en basit komutlar aşağıdaki gibidir:


JPG/JPEG'den WebP'ye dönüştürme
```
magick mogrify -format webp -quality 75 *.jpg
```

JPG/JPEG'den Avif'e dönüştürme
```
magick mogrify -format avif -quality 75 *.jpg
```

Komut gene çok az değişti ve okunabilirlik açısından da halen çok iyi bir durumda. Benim kullandığım tam komut ne diye soracak olursanız gene aynı parametreleri eklemiş olacağım ve şöyle görünecek:

WebP için:

```
magick mogrify -format webp -define webp:pass=10 -quality 75 *.jpg
```

Avif için:

```
magick mogrify -format avif -define avif:speed=0 -quality 75 *.jpg
```

### Gerçek dünya testleri

Imagemagick aracı kağıt üstünde diğer araçların yaptığı şeyin aynısını yapıyor görünüyor. Fakat akıldaki hesap her zaman gerçekle uyuşmadığı için ve bunca yazının da gerçekten işe yarayıp yaramadığından emin olmak için test etmek iyi olacaktır. Bu nedenle bir haftasonu gezisi sırasında çekmiş olduğum boyutları 1.6M ile 4.4M arasında değişen 8-Bit/RGB/JPG/4096x2304 özelliklerine sahip 36 adet fotoğrafı bu araç ile sıkıştıracağım.

| Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
| :---: | :---: | :---: | :---: |
| IMG-1.jpg | 3123535 | 3.0M | Dosya Aslı |
| IMG-1.avif | 1504064 | 1.5M | 48,15 |
| IMG-1.webp | 1076958 | 1.1M | 34,48 |
| IMG-2.jpg | 2790604 | 2.7M | Dosya Aslı |
| IMG-2.avif | 550187 | 538K | 19,72 |
| IMG-2.webp | 391096 | 382K | 14,01 |
| IMG-3.jpg | 3150447 | 3.1M | Dosya Aslı |
| IMG-3.avif | 766041 | 749K | 24,32 |
| IMG-3.webp | 546460 | 534K | 17,35 |
| IMG-4.jpg | 4526481 | 4.4M | Dosya Aslı |
| IMG-4.avif | 1743551 | 1.7M | 38,52 |
| IMG-4.webp | 1218140 | 1.2M | 26,91 |

<details>
  <summary>Diğer görüntüler için lütfen tıklayınız</summary>

  | Dosya Adı | Dosya Boyutu (Uzun) | Dosya Boyutu (Kısa) | Oran % |
  | :---: | :---: | :---: | :---: |
  | IMG-5.jpg | 4229066 | 4.1M | Dosya Aslı |
  | IMG-5.avif | 1519081 | 1.5M | 35,92 |
  | IMG-5.webp | 1063014 | 1.1M | 25,14 |
  | IMG-6.jpg | 3159689 | 3.1M | Dosya Aslı |
  | IMG-6.avif | 1500718 | 1.5M | 47,50 |
  | IMG-6.webp | 1077274 | 1.1M | 34,09 |
  | IMG-7.jpg | 3173451 | 3.1M | Dosya Aslı |
  | IMG-7.avif | 1503358 | 1.5M | 47,37 |
  | IMG-7.webp | 1069566 | 1.1M | 33,70 |
  | IMG-8.jpg | 3757624 | 3.6M | Dosya Aslı |
  | IMG-8.avif | 1986912 | 1.9M | 52,88 |
  | IMG-8.webp | 1465148 | 1.4M | 38,99 |
  | IMG-9.jpg | 3039928 | 2.9M | Dosya Aslı |
  | IMG-9.avif | 1424021 | 1.4M | 46,84 |
  | IMG-9.webp | 1028336 | 1005K | 33,83 |
  | IMG-10.jpg | 2441091 | 2.4M | Dosya Aslı |
  | IMG-10.avif | 902915 | 882K | 36,99 |
  | IMG-10.webp | 611696 | 598K | 25,06 |
  | IMG-11.jpg | 2526240 | 2.5M | Dosya Aslı |
  | IMG-11.avif | 971978 | 950K | 38,48 |
  | IMG-11.webp | 672250 | 657K | 26,61 |
  | IMG-12.jpg | 3010868 | 2.9M | Dosya Aslı |
  | IMG-12.avif | 1363342 | 1.4M | 45,28 |
  | IMG-12.webp | 1003464 | 980K | 33,33 |
  | IMG-13.jpg | 2894166 | 2.8M | Dosya Aslı |
  | IMG-13.avif | 1262222 | 1.3M | 43,61 |
  | IMG-13.webp | 903482 | 883K | 31,22 |
  | IMG-14.jpg | 2794211 | 2.7M | Dosya Aslı |
  | IMG-14.avif | 1180628 | 1.2M | 42,25 |
  | IMG-14.webp | 845948 | 827K | 30,28 |
  | IMG-15.jpg | 2698751 | 2.6M | Dosya Aslı |
  | IMG-15.avif | 1625281 | 1.6M | 60,22 |
  | IMG-15.webp | 1147432 | 1.1M | 42,52 |
  | IMG-16.jpg | 2858864 | 2.8M | Dosya Aslı |
  | IMG-16.avif | 1778962 | 1.7M | 62,23 |
  | IMG-16.webp | 1269262 | 1.3M | 44,40 |
  | IMG-17.jpg | 2679597 | 2.6M | Dosya Aslı |
  | IMG-17.avif | 1604076 | 1.6M | 59,86 |
  | IMG-17.webp | 1127914 | 1.1M | 42,09 |
  | IMG-18.jpg | 1664310 | 1.6M | Dosya Aslı |
  | IMG-18.avif | 305830 | 299K | 18,38 |
  | IMG-18.webp | 211652 | 207K | 12,72 |
  | IMG-19.jpg | 3007566 | 2.9M | Dosya Aslı |
  | IMG-19.avif | 1393500 | 1.4M | 46,33 |
  | IMG-19.webp | 941406 | 920K | 31,30 |
  | IMG-20.jpg | 2097444 | 2.1M | Dosya Aslı |
  | IMG-20.avif | 582060 | 569K | 27,75 |
  | IMG-20.webp | 456522 | 446K | 21,77 |
  | IMG-21.jpg | 2852271 | 2.8M | Dosya Aslı |
  | IMG-21.avif | 1275593 | 1.3M | 44,72 |
  | IMG-21.webp | 887504 | 867K | 31,12 |
  | IMG-22.jpg | 2757359 | 2.7M | Dosya Aslı |
  | IMG-22.avif | 1181807 | 1.2M | 42,86 |
  | IMG-22.webp | 847800 | 828K | 30,75 |
  | IMG-23.jpg | 2312096 | 2.3M | Dosya Aslı |
  | IMG-23.avif | 817076 | 798K | 35,34 |
  | IMG-23.webp | 579860 | 567K | 25,08 |
  | IMG-24.jpg | 3550795 | 3.4M | Dosya Aslı |
  | IMG-24.avif | 1871401 | 1.8M | 52,70 |
  | IMG-24.webp | 1404298 | 1.4M | 39,55 |
  | IMG-25.jpg | 3203733 | 3.1M | Dosya Aslı |
  | IMG-25.avif | 1381699 | 1.4M | 43,13 |
  | IMG-25.webp | 1184984 | 1.2M | 36,99 |
  | IMG-26.jpg | 3174471 | 3.1M | Dosya Aslı |
  | IMG-26.avif | 1354947 | 1.3M | 42,68 |
  | IMG-26.webp | 1148438 | 1.1M | 36,18 |
  | IMG-27.jpg | 3341253 | 3.2M | Dosya Aslı |
  | IMG-27.avif | 1511043 | 1.5M | 45,22 |
  | IMG-27.webp | 1236658 | 1.2M | 37,01 |
  | IMG-28.jpg | 3588628 | 3.5M | Dosya Aslı |
  | IMG-28.avif | 1916504 | 1.9M | 53,40 |
  | IMG-28.webp | 1454832 | 1.4M | 40,54 |
  | IMG-29.jpg | 2356713 | 2.3M | Dosya Aslı |
  | IMG-29.avif | 1431777 | 1.4M | 60,75 |
  | IMG-29.webp | 1030610 | 1007K | 43,73 |
  | IMG-30.jpg | 3075593 | 3.0M | Dosya Aslı |
  | IMG-30.avif | 1487599 | 1.5M | 48,37 |
  | IMG-30.webp | 1069446 | 1.1M | 34,77 |
  | IMG-31.jpg | 2620141 | 2.5M | Dosya Aslı |
  | IMG-31.avif | 1053561 | 1.1M | 40,21 |
  | IMG-31.webp | 788630 | 771K | 30,10 |
  | IMG-32.jpg | 2793081 | 2.7M | Dosya Aslı |
  | IMG-32.avif | 1234183 | 1.2M | 44,19 |
  | IMG-32.webp | 903448 | 883K | 32,35 |
  | IMG-33.jpg | 2648478 | 2.6M | Dosya Aslı |
  | IMG-33.avif | 1100654 | 1.1M | 41,56 |
  | IMG-33.webp | 826504 | 808K | 31,21 |
  | IMG-34.jpg | 3181983 | 3.1M | Dosya Aslı |
  | IMG-34.avif | 1497755 | 1.5M | 47,07 |
  | IMG-34.webp | 1130016 | 1.1M | 35,51 |
  | IMG-35.jpg | 2580106 | 2.5M | Dosya Aslı |
  | IMG-35.avif | 1050044 | 1.1M | 40,70 |
  | IMG-35.webp | 790866 | 773K | 30,65 |
  | IMG-36.jpg | 2922206 | 2.8M | Dosya Aslı |
  | IMG-36.avif | 1338502 | 1.3M | 45,80 |
  | IMG-36.webp | 980454 | 958K | 33,55 |

</details>

Sonuç olarak:
* JPG'de toplam dosya boyutu: 106582840 yani 107M
* WebP ile toplam dosya boyutu: 34391368 yani 35M (%32,26)
* Avif ile toplam dosya boyutu: 46972872 yani 47M (%44,07)

Kısa bir değerlendirme ile orijinal araçların yaptığı kadar yüksek verimliliğe ulaşamasa da dikkate ve uygulamaya değer bir kazanım elde edebiliyoruz.

## Anlatım dışı bırakılan kısımlar

Bu rehberde elde edilen verim yeterli görüldüğü için `Color Space`, `Chroma Subsampling`, `Sampling Factor Geometry`, `Posterize`, `Iteration`, `Transparent Color` ve `Solorize` kavramları ile çalışılmamış default olan değerler kullanılmıştır. Bu kavramlara biraz daha aşina olup isteğe göre daha iyi sonuçlar elde etmek mümkün olup tek başına `Posterize` değerini değiştirerek bile çok güzel sonuçlar elde ettim. Fakat anlatması ve uygun değerin tespit edilmesi çok kolay olmadığı için bu rehber kapsamı dışında bırakılmıştır.

## Fotoğraf sıkıştırma ile ilgili forumda açılmış konular

Bu rehberin haricinde spesifik olarak (diğer rehberlerde de olduğu üzere) fotoğraf çevirme konuları ve imagemagick ilgili yaşanan sorunları veya ek rehberleri bu konu açısından fayda göstermesi durumunda atıf yapacağım. Ayrıca benim eklediğim konular dışındaki imagemagick ile ilgili tüm konulara #imagemagick veya #magick etiketine tıklayarak ulaşabilirsiniz.

# Son Söz ve Kaynakça

Artık elimdeki bu fotoğrafları ne yapacağım acaba bir hard disk mi alsam gibi soruları kendinize sormanıza gerek kalmadı. Bu rehber sonucunda sizi bir süre daha götürecek kadar yer kazanmış oldunuz. Ayrıca sadece jpg değil kullanamadığınız tüm formatlar özelinde istediğiniz formata kolayca çevirmeyi de öğrenmiş oldunuz. Umarım keyifli bir rehber olmuştur. Rehberin teknik açıklaması veya içeriği ile ilgili bir eksiklik/hata görmeniz durumunda bana bildirebilir veya düzeltilmesi için cevap olarak yazabilirsiniz. Bir sonraki rehbere kadar iyi seyirler ve esenlikler dileğiyle.

Kaynaklar:
1. https://tez.yok.gov.tr/UlusalTezMerkezi/ Tez No: 183894 (Veri sıkıştırmada yeni yöntemler, ALTAN MESUT, 2006)
2. https://tez.yok.gov.tr/UlusalTezMerkezi/ Tez No: 565227 (Tekrarlı uzunluk kodlaması (RLE) ve ayrıştırma esaslı görüntü sıkıştırma, MUSTAFA BURAK KALKAN, 2019)
3. https://pnrsolution.org/Datacenter/Vol3/Issue2/129.pdf (Image Compression Techniques: Lossy and Lossless, ROJATKAR/BORKAR/NAIK/PEDDIWAR, 2015)
4. https://arxiv.org/pdf/1101.0395 (Improving the Performance of K-Means for Color Quantization, M. Emre CELEBİ, 2011)
5. https://scikit-learn.org/stable/auto_examples/cluster/plot_color_quantization.html
6. https://developers.google.com/speed/webp/docs/cwebp
7. https://github.com/AOMediaCodec/libavif/blob/main/doc/avifenc.1.md
8. https://imagemagick.org/script/magick.php
9. https://imagemagick.org/script/convert.php
10. https://www.youtube.com/watch?v=Q2aEzeMDHMA (JPEG DCT, Discrete Cosine Transform (JPEG Pt2) | Computerphile)
11. https://www.youtube.com/watch?v=F1kYBnY6mwg (Image compression deep-dive | Chrome for Developers)
12. https://www.youtube.com/watch?v=_ciMMmxNIGw (Image Compression - EE 274, Data Compression, L15 | Stanford Research Talks)
13. İlk Yayım: [BTT](https://btt.community/t/fotograf-sikistirma-yontemleri-inceleme-ve-rehber/8315)
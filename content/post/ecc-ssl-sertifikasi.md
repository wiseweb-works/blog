---
title: "Linux Sunucuda ECC SSL Sertifikası Üretme"
date: 2022-03-20
tags: ["linux", "ssl", "security", "ecc", "elliptic curve"]
author: "Wise"
draft: false
---
# Giriş ve Özet

Bugün sizlerle yönettiğiniz bir web sitesi veya uygulama sunucusu ile ziyaretçileriniz arasındaki trafiğin gizli/güvenilir ve doğrulanabilir olmasını sağlamak için SSL sertifikası üretmeyi öğreneceğiz. Ürettiğiniz sertifikayı nasıl ve hangi konfigürasyon ile deploy edeceğinizi önceki yazılarımda anlatmıştım. Bu yazıda ise az ekmek çok köfte denklemini nasıl kurabileceğimizi yani daha hızlı ve daha güvenli bir SSL sertifikası üretmeyi göstereceğim. Normalde Let's Encrypt'in ACME protokolüne aşina iseniz (yazının yazıldığı tarih itibariyle) RSA asimetrik anahtar yapısı ile 1024-4098 (çok zorlarsanız belki 8196) bitlik bir sertifika ürettirmeniz ve bunu görece olarak 90 gün kullanmanız mümkündür. Fakat bu kadar büyük bir anahtarın üretilmesi, ürettikten sonra TLS handshake sırasında kullanılması ve ziyaretçilerin kullandığı cihazlar ile uyumlu olması çoğu senaryoda sorun çıkarmaktadır. Örneğin 2048 bit yerine 4096 bit kullanıldığı zaman bazı denemelerimde 0.4-0.8 sn daha uzun handshake süreleri ile karşı karşıya kalıyorum. Sadece handshake in bu kadar uzaması sorun değilmiş gibi sunucuya da ekstra bir yük bindiriyor. Fakat 4096 bit RSA yerine 384 bit ECC sertifikası ürettiğiniz zaman çok daha hızlı bir sertifikaya sahip olduğunuz gibi aynı zamanda da 7680 bit RSA'ya (öyle bir boyut olsaydı) eşit bir güvenlik elde ediyorsunuz.

Peki iyi güzel anlattın da bu işin aması nerede dediğinizi duyar gibiyim. Sizi üzeceğim fakat bu işin aması yok. Olmamasının sebebi ise işin arka plandaki matematikte saklı. Kısaca her iki sertifika üretim ve kullanımındaki ufak farklardan bahsedip, bunların nasıl ve neden büyük farklara neden olduğunu açıklayıp son kısımda da bonus olarak başlıkta yazmayan bir şeyden bahsedeceğim. (Sonuna kadar okumanız gerekecek bonus için :D)

![https://www.globalsign.com/en/blog/elliptic-curve-cryptography (Erişim Tarihi: 08.04.2023)](/images/ecc-ssl/key-size-comparison.jpg)

## ECC Sertifikasının üretim süreci

Öncelikle (her zaman olduğu gibi) içinde bulunduğumuz Linux sürümünün paket yöneticisi ile son güncellemeleri konsol üzerinden yüklememiz gerekmektedir.

```bash
Ubuntu için: sudo apt update && sudo apt upgrade -y

Fedora için: sudo yum update -y

Arch Linux için: sudo pacman -Syyu
```

Güncellemeler yüklendikten sonra ise sunucunuzdaki (Benim olayımda Ubuntu) nginx servisini (ki bu servis dışarıdan HTTP/HTTPS bağlantıları almanıza yarayan servistir) yapılandırmaya başlıyoruz. Öncelikle çok karıştırılması nedeniyle belirtmek gerekir ki apache, nginx ve litespeed servisleri aynı işi yapan farklı servislerdir. Ben yönetimi daha kolay ve topluluk desteği daha çok diye NGINX'i terchi ettim.

## Özel anahtarı oluşturalım

İlk olarak, OpenSSL ile özel anahtarı oluşturuyoruz. Kullanacağımız OpenSSL komutu `ecparam` (EC parametre manipülasyonu) ve konfigürasyon parametrelerini bu komuta geçirmek için:

```bash
openssl ecparam -genkey -name secp384r1 -out privkey.pem
```

* `-genkey` seçeneği, OpenSSL'ye bir EC anahtarı oluşturmasını söyler.
* `-name` parametresi OpenSSL'ye hangi eğrinin kullanılacağını söyler.
* `-out` parametresi OpenSSL'ye çıktıyı bir dosyaya yazmasını söyler.

OpenSSL'nin çıktısını varsayılan olarak PEM biçiminde yazdığını unutmayın. EC anahtarlarını işleyen `ec` komutuyla OpenSSL'nin doğru şeyi yaptığını kontrol edebiliriz:

```bash
openssl ec -in privkey.pem -noout -text
```

* `-in` girdi dosyasıdır
* `-noout`, OpenSSL'ye anahtarı çıkarmamasını söyler, bu da privkey.pem'i stdout'a anlamsızca yazdırır.
* `-text`, OpenSSL'ye anahtar hakkındaki bilgileri düz metin biçiminde yazmasını söyler

Her şey yolunda giderse ve anahtar doğru şekilde oluşturulduysa, OpenSSL aşağıdakine benzer bir şey gösterecektir:

```text
read EC key
Private-Key: (384 bit)
priv:
    [gizli]
pub:
    [gizli]
ASN1 OID: secp384r1
NIST CURVE: P-384
```

Bu, anahtarın P-384 eğrisi ile oluşturulduğunu doğrular. Neden P-384 yerine P-512 kullanmıyoruz derseniz Let's Encrypt ekliptik eğrilerde 384 bitten daha yüksek olursa imzalamıyor ve Google Chrome gibi modern tarayıcılar 512 bitlik ekliptik eğrileri kullanan internet sitelerini geçersiz olarak işaretliyor. Kısa cevap bu.

## Sertifika için OpenSSL yapılandırması oluşturalım

Şimdi TLS sertifikası almak istediğimiz etki alanına özgü parametreleri içeren bir OpenSSL yapılandırma dosyası oluşturmalıyız. Bu örnekte, bir `openssl.cnf` dosyasına aşağıdaki konfigürasyonu gireceğiz:

```text
[ req ]
prompt = no
encrypt_key = no
default_md = sha512
distinguished_name = dname
req_extensions = reqext

[ dname ]
CN = example.com
emailAddress = admin@example.com

[ reqext ]
subjectAltName = DNS:example.com, DNS:www.example.com
```

Bu yapılandırma seçeneklerinin kısa bir açıklaması:

Gerekli `[ req ]` bölümünde:

* `prompt = no`, OpenSSL'ye yapılandırma dosyasından olabildiğince fazla yapılandırma almasını söyler
* `encrypt_key = no`, OpenSSL'ye özel anahtarı bir parola ile şifrelememesini söyler. (Şifreli özel anahtarlar Nginx tarafından desteklenir, ancak ben onları kullanmıyorum.)
* `default_md = sha512`, OpenSSL'ye CSR'yi SHA512 ile imzalamasını söyler. (Bildiğim kadarıyla, Let's Encrypt, imzaları için yalnızca SHA256'lı RSA'yı destekler, ancak bu, CSR'de daha güçlü şifreleme kullanamayacağımız anlamına gelmez.)
* `distinguished_name = dname`, OpenSSL'ye Ayırt Edici Ad yapılandırma seçenekleri için bir `[ dname ]` bölümü aramasını söyler.
* `req_extensions = reqext`, OpenSSL'ye, Konu Alternatif Adlarının (SAN'lar) yapılandırılmak istenen uzantılar için yapılandırma seçeneklerinde bir `[ reqext ]` bölümü aramasını söyler.

Ayırt Edici Ad `[ dname ]` bölümünde:

* `CN = example.com`, sertifikanın Ortak Adını belirtir.
* `emailAddress = admin@example.com` e-posta adresiniz belirgin olmalıdır.
İstenen Uzantılar `[ reqext ]` bölümünde, konuAltName, sertifika için SAN'ların listesini sağlar. (Chrome, v58'den itibaren, Ortak Adın SAN'lar listesine dahil edilmesini gerektirir).

Let's Encrypt v2, joker alan adlarını destekler, bu nedenle bu örnekte, apeks dışındaki ana bilgisayarlar için tek düzeyli bir joker karakter kullanabilirsiniz (*.example.com).

## Sertifika İmzalama İsteği Oluşturalım

İstemci tarafındaki son adım, OpenSSL kullanarak Sertifika İmzalama Talebi oluşturmaktır, ardından bunu imzalamak için Let's Encrypt'e ileteceğiz ve imzalı sertifikayı geri alacağız.

Bir CSR oluşturmak için gereken OpenSSL komutu `req` 'dir.

```bash
openssl req -new -config openssl.cnf -key privkey.pem -out csr.pem
```

* `-new`, OpenSSL'ye bir CSR oluşturduğumuzu söyler (ve mevcut bir CSR'yi incelemeyiz)
* `-config` openssl.cnf, yukarıda oluşturduğumuz yapılandırma dosyasını belirtir
* `-key privkey.pem`, yukarıda oluşturduğumuz özel anahtarı belirtir
* `-out csr.pem` OpenSSL'ye CSR'yi bir çıktı dosyasına yazmasını söyler (stdout yerine)

CSR'yi doğru şekilde oluşturduğumuzu doğrulayabiliriz:

```bash
openssl req -in csr.pem -noout -text -verify
```

* `-verify` OpenSSL'nin CSR'deki imzayı doğrulamasını ister

Bu, çıktıda beklenen şu sonuçları üretmelidir:

```text
verify OK
Certificate Request:
    Data:
        Version: 1 (0x0)
        Subject: CN = example.com, emailAddress = admin@example.com
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (384 bit)
                pub:
                    [gizli]
                ASN1 OID: secp384r1
                NIST CURVE: P-384
        Attributes:
        Requested Extensions:
            X509v3 Subject Alternative Name:
                DNS:example.com, DNS:www.example.com
    Signature Algorithm: ecdsa-with-SHA512
         [gizli]
```

## Let's Encrypt'ten sertifikamızı imzalamasını isteyin

Son adım, CSR'yi bir ACME istemcisiyle Let's Encrypt'e imzalaması için göndermektir, bu iş için `certbot` en yaygın istemcidir.

`Certbot` istemcisine iletilen komut satırı seçenekleri, kurulumumuza, alan adımızın kayıtlı olduğu kişiye vb. bağlı olarak değişir. Genellikle `certonly` komutunu kullanmamız gerekir ve asterisks (*) kullandıysanız certbot DNS eklentilerinden birini kullanmanız gerekir.

Örneğin, `example.com` alan adı Cloudflare'de kayıtlıysa, son derece uygun olan ve sürece manuel müdahale gerektirmeyen doğrulamayı işlemek için ilgili eklentiyi kullanabiliriz. (Cloudflare eklentisini gizli token bilgileriyle yapılandırmak bu makalenin kapsamı dışındadır.)

Her şeyin yolunda olduğundan emin olmak için önce `--dry-run` ile düzgün sonuç alınacağından emin olunması genellikle tavsiye edilir.

```bash
certbot nginx certonly --dry-run --domain "example.com" --domain "www.example.com" --csr csr.pem
```

* Hatalı işlemeleri önlemek için karakterlerin etrafında tırnak işaretleri gereklidir ve genel olarak bunlar iyi bir fikirdir.
* `--csr csr.pem` certbot'a zaten bir sertifikamız olduğunu ve bizim için imzalaması için Let's Encrypt'e ihtiyacımız olduğunu söyler.

Certbot istemcisi, komut satırında istenen alan adları listesinin sertifikada listelenen alan adlarıyla eşleşip eşleşmediğini kontrol edecek ve alan adının bize ait olduğunu doğrulamak için Certbot NGINX eklentisini kullanacak ve herhangi bir sorun olup olmadığını bize bildirecektir.

Hiçbir şey yanlış değilse, size şunu söyleyecektir:

```text
IMPORTANT NOTES:
 - The dry run was successful.
```

Gerçek komut hemen aşağıdaki gibidir:

```bash
certbot nginx certonly --domain "example.com" --domain "www.example.com" --csr csr.pem
```

(Uzun) bir gecikmeden sonra, istemci çıktı olarak şunları üretecektir:

1. İmzalı sertifika: `0000_cert.pem`
2. Kök ve ara sertifikalar: `0000_chain.pem`
3. Sertifika + ara ürünler: `0001_chain.pem`
Bu noktada, CSR `csr.pem` silinebilir.

Merak ediyorsak, `x509` komutunu kullanarak istemci tarafından OpenSSL ile döndürülen sertifikaları inceleyebiliriz:

```bash
openssl x509 -in 0001_chain.pem -noout -text
```

Ne yazık ki, yukarıda açıklandığı gibi Let's Encrypt'in sertifikamızı bir SHA256 imzasıyla imzaladığını keşfedeceğiz. (Daha güvenli olmasının yanı sıra, SHA512, modern 64-bit CPU'larda SHA256'dan daha iyi performans gösterir.) Ancak açık anahtarımız yine de ECDSA kullanmalıdır.

Bu dosyalar sıradan değildir, bu yüzden onları daha bilgilendirici bir şekilde taşımalı ve düzenlemeliyiz.

Debian Linux'ta, özel anahtarımı `/home/KULLANICI_ADI/SSL/private/example.com/privkey.pem` içinde tutarak etki alanlarım için alt dizinler oluşturmayı seviyorum ve sertifikalar:

* `/home/KULLANICI_ADI/SSL/certs/example.com/cert.pem`
* `/home/KULLANICI_ADI/SSL/certs/example.com/chain.pem`
* `/home/KULLANICI_ADI/SSL/certs/example.com/fullchain.pem`

# SON

Her şeyi doğru yaptıysak, sertifikayı Chrome gibi bir web tarayıcısı ile incelediğimizde, bunun bir EC sertifikası olduğunu onaylayacaktır:

![https://dev.to/benjaminblack/obtaining-an-elliptic-curve-dsa-certificate-with-lets-encrypt-51bc (Erişim Tarihi: 08.04.2023)](/images/ecc-ssl/ecc-sll-key-chrome.png)

Mozilla Gözlemevi de bize A+ notu verecek!

![https://dev.to/benjaminblack/obtaining-an-elliptic-curve-dsa-certificate-with-lets-encrypt-51bc (Erişim Tarihi: 08.04.2023)](/images/ecc-ssl/ecc-ssl-key-mozilla.png)

Ayrıca SSL Labs'ın rapor sonucunda 384 Bitlik bir ECC sertifikası'nın kullanıldığını görebiliyoruz.

![SSL Labs Test Sonucu](/images/ecc-ssl/ecc-ssl-key-ssllabs.png)

NOT: Bu yazıda [Benjamin Black](https://dev.to/benjaminblack/obtaining-an-elliptic-curve-dsa-certificate-with-lets-encrypt-51bc)'in aynı konulu yazısından faydalanılmıştır.

---
title: "Actalis ile ücretsiz S/MIME sertifikası alınması"
date: 2023-10-01
tags: ["Actalis", "S/MIME", "Sertifika"]
author: "Wise"
draft: false
---
# Ücretsiz S/MIME Sertifikası Nasıl Alınır: Adım Adım Rehber

Günümüzde e-posta iletişimi günlük yaşamın ayrılmaz bir parçası haline geldi. Ancak e-posta gönderirken veya alırken güvenlik endişeleri her zaman mevcuttur. S/MIME (Secure/Multipurpose Internet Mail Extensions), e-postalarınızı daha güvenli hale getirmenize yardımcı olan bir teknolojidir. S/MIME sertifikaları, e-postalarınızı şifrelemek ve kimlik doğrulama sağlamak için kullanılır. Neyse ki, ücretsiz S/MIME sertifikaları edinmek oldukça kolaydır. Bu forum rehberinde, bu işlemi adım adım anlatacağız.

## Adım 1: Actalis Extrassl Sayfasına Gidin

İlk adımda, ücretsiz bir S/MIME sertifikası almak için [Actalis Extrassl](https://extrassl.actalis.it/portal/uapub/freemail) web sitesini ziyaret edin. Bu sertifikayı almak için bu siteyi kullanacağız. Siteye girdikten sonra sizde bir e-posta adresi isteyecek. Bu adres s/mime sertifikasını üreteceğiniz adres olması gerekli.

![Actalis Extrassl](/images/actalis/1.png)

## (Opsiyonel) Adım 2: Dil Seçeneğini Ayarlayın

Sitenin üst sağ köşesinde, dil seçeneğini ayarlayabilirsiniz. Eğer sizde de başlangıçta İtalyanca çıkıyor ise dil seçeneğini İngilizce olarak ayarlamak isteyebilirsiniz, böylece prosedürü daha iyi anlayabilirsiniz.

## Adım 3: E-Posta Adresinizi Doğrulayın
![Gelen mail](/images/actalis/2.png)

Bir önceki adımda girmiş olduğunuz mail adresinize görseldeki gibi bir kod gönderecekler. Bu kod girmiş olduğumuz mail adresinin bize ait olduğunu ve gönderilen mailleri alabildiğimizi teyit etmek amacıyla gönderiliyor.

![Doğrulama kodu girme ekranı](/images/actalis/3.png)

Size gelen kodu ilgili fotoda görülen Verification code alanını girmeniz ve her iki kutucuğu da seçtikten sonra Submit request butonuna tıklamanız gerekiyor.

## Adım 4: Sertifikayı İndirin ve Kurun

Doğrulama işlemi tamamlandığında, S/MIME sertifikası hazır olacak ve size verilen talimatları izleyerek sertifikayı indirip kurabileceksiniz.

![Onay Ekranı](/images/actalis/4.png)

Fakat öncelikle yukarıdaki gibi bir onaylama ekranı gördüğünüzden emin olmanız ve tüm süreç bitene kadar bu ekranı kapatmamanız gerekiyor. Ekrandaki onaylama metninin altında (ben ilk seferinde gözden kaçırmıştım) bir şifre beliriyor ve bunu bir yere not etmeniz gerekiyor.

![Sertifika Geldi](/images/actalis/5.png)

Daha sonra mail adresinize gelen şifreli sertifika dosyasını ilgili zip dosyasından çıkarmanız gerekiyor. Size verilen şifre zip dosyası için değil sertifikayı içeri aktarırken kullanılacak. Zip dosyasının bir şifresi yok.

## Adım 5: E-Posta İstemcisi Ayarlarınızı Yapın

Son olarak, e-posta istemcinizde S/MIME sertifikanızı kullanmak için gerekli ayarları yapmanız gerekecektir. Bu adımlar, kullandığınız e-posta istemcisine göre değişebilir. O yüzden ayrı ayrı anlatmak yerine ilgili linkleri aşağıya bırakıyorum.

* [Thunderbird](https://www.ssl.com/how-to/installing-an-s-mime-certificate-and-sending-secure-email-in-mozilla-thunderbird-on-windows-10/)
* [Apple Cihazları](https://support.apple.com/en-ie/HT202345)
* [Outlook Web](https://support.microsoft.com/en-au/office/encrypt-messages-by-using-s-mime-in-outlook-on-the-web-878c79fc-7088-4b39-966f-14512658f480)
* [Outlook Uygulaması](https://support.microsoft.com/en-us/office/encrypt-email-messages-373339cb-bf1a-4509-b296-802a39d801dc)
* [Kleopatra (Manuel kullanım için)](https://docs.kde.org/stable5/en/kleopatra/kleopatra/configuration-smime-validation.html)

## Son Söz

Artık ücretsiz S/MIME sertifikasına sahipsiniz ve e-postalarınızı daha güvenli hale getirebilirsiniz. Bu sertifikalar, e-postalarınızın şifrelenmesine ve kimlik doğrulamasına yardımcı olur, böylece kişisel bilgilerinizin güvende olduğundan emin olabilirsiniz. Son bir hatırlatma olarak da S/MIME sertifikaları belirli bir süreyle sınırlı olmaktadır, bu nedenle süresi dolduğunda yeniden sertifika almanız gerekecektir. Bu siteden aldığınız sertifikalar genelde 1 senelik oluyor.

### Kaynakça

E-posta güvenliği hakkında daha fazla bilgi edinmek için bu yazıda fazlasıyla yararlandığım [Franky's Web](https://www.frankysweb.de/tipp-kostenloses-s-mime-zertifikat-neu/) sitesini ziyaret edebilirsiniz.

İlk yayım: [BTT Community](https://btt.community/t/actalis-ile-ucretsiz-s-mime-sertifikasi-alinmasi/1927)
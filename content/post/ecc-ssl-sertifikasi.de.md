---
title: "Generieren eines ECC-SSL-Zertifikats auf einem Linux-Server"
date: 2022-03-20
tags: ["linux", "ssl", "sicherheit", "ecc", "elliptische kurve"]
author: "Wise"
draft: false
---
# Einführung und Zusammenfassung

Heute lernen wir, wie man SSL-Zertifikate generiert, um sicherzustellen, dass der Datenverkehr zwischen einer von Ihnen verwalteten Website oder einem Anwendungsserver und Ihren Besuchern vertraulich / zuverlässig und überprüfbar ist. In meinen vorherigen Artikeln habe ich erklärt, wie und mit welcher Konfiguration Sie das von Ihnen erstellte Zertifikat bereitstellen würden. In diesem Artikel zeige ich Ihnen, wie Sie die Gleichung „weniger Brot, mehr Frikadellen“ aufstellen, also ein schnelleres und sichereres SSL-Zertifikat erstellen. Wenn Sie mit dem ACME-Protokoll von Let’s Encrypt vertraut sind (zum Zeitpunkt des Schreibens), ist es normalerweise möglich, ein 1024-4098 (wenn Sie sich zu sehr anstrengen, vielleicht 8196) Bit-Zertifikat mit einer asymmetrischen RSA-Schlüsselstruktur zu generieren und es für zu verwenden 90 Tage relativ. Das Generieren eines so großen Schlüssels, dessen Verwendung während des TLS-Handshakes nach der Generierung und die Kompatibilität mit den von den Besuchern verwendeten Geräten verursachen jedoch in den meisten Szenarien Probleme. Wenn beispielsweise 4096 Bit anstelle von 2048 Bit verwendet werden, bin ich bei einigen meiner Versuche mit 0,4-0,8 Sekunden längeren Handshake-Zeiten konfrontiert. Als ob es in Ordnung wäre, dass der Handshake so lange dauert, wird der Server dadurch zusätzlich belastet. Aber wenn Sie ein 384-Bit-ECC-Zertifikat anstelle von 4096-Bit-RSA generieren, erhalten Sie ein viel schnelleres Zertifikat und gleichzeitig eine Sicherheit, die 7680-Bit-RSA entspricht (wenn es diese Größe hätte).

Nun, Sie haben es gut erklärt, aber wo ist der Sinn dieser Arbeit, scheine ich Sie sagen zu hören. Ich werde Sie verärgern, aber dieses Geschäft hat keinen Sinn. Der Grund, warum dies nicht der Fall ist, ist in der Hintergrundmathematik verborgen. Ich werde kurz auf die kleinen Unterschiede in der Herstellung und Verwendung beider Zertifikate eingehen, erklären, wie und warum sie große Unterschiede verursachen, und im letzten Teil werde ich über etwas sprechen, das nicht als Bonus im Titel steht. (Für den Bonus musst du bis zum Ende lesen :D)

![https://www.globalsign.com/en/blog/elliptic-curve-cryptography (Datum: 08.04.2023)](/images/ecc-ssl/key-size-comparison.jpg)

## Produktionsprozess des ECC-Zertifikats

Zuerst müssen wir (wie immer) die neuesten Updates über die Konsole mit dem Paketmanager der Linux-Version installieren, in der wir uns befinden.

```bash
Ubuntu: sudo apt update && sudo apt upgrade -y

Fedora: sudo yum update -y

Arch Linux: sudo pacman -Syyu
```

Nachdem die Updates installiert sind, beginnen wir mit der Konfiguration des nginx-Dienstes (das ist der Dienst, der es Ihnen ermöglicht, externe HTTP/HTTPS-Verbindungen zu empfangen) auf Ihrem Server (in meinem Fall Ubuntu). Zunächst einmal sollte angemerkt werden, dass es sich aufgrund der Verwirrung bei Apache-, Nginx- und Litespeed-Diensten um unterschiedliche Dienste handelt, die die gleiche Aufgabe erfüllen. Ich habe mich für NGINX entschieden, weil es einfacher zu verwalten ist und mehr Community-Unterstützung bietet.

## Lassen Sie uns den privaten Schlüssel generieren

Zuerst generieren wir den privaten Schlüssel mit OpenSSL. Der OpenSSL-Befehl, den wir verwenden werden, ist „ecparam“ (EC-Parametermanipulation) und um die Konfigurationsparameter an diesen Befehl zu übergeben:

```bash
openssl ecparam -genkey -name secp384r1 -out privkey.pem
```

* Die Option `-genkey` weist OpenSSL an, einen EC-Schlüssel zu generieren.
* Der Parameter `-name` teilt OpenSSL mit, welche Kurve verwendet werden soll.
* Der Parameter `-out` weist OpenSSL an, die Ausgabe in eine Datei zu schreiben.

Beachten Sie, dass OpenSSL seine Ausgabe standardmäßig im PEM-Format schreibt. Wir können überprüfen, ob OpenSSL das Richtige tut, mit dem Befehl `ec`, der EC-Schlüssel verarbeitet:

```bash
openssl ec -in privkey.pem -noout -text
```

* `-in` ist eine Eingabedatei
* Das `-noout` weist OpenSSL an, den Schlüssel nicht zu extrahieren, und gibt sinnlos privkey.pem nach stdout aus.
* `-text` weist OpenSSL an, Informationen über den Schlüssel im Klartextformat zu schreiben

Wenn alles gut geht und der Schlüssel korrekt generiert wurde, zeigt OpenSSL etwa Folgendes:

```text
read EC key
Private-Key: (384 bit)
priv:
    [secret]
pub:
    [secret]
ASN1 OID: secp384r1
NIST CURVE: P-384
```

Dadurch wird bestätigt, dass der Schlüssel mit der P-384-Kurve erstellt wurde. Wenn Sie sich fragen, warum wir nicht P-512 statt P-384 verwenden, Let's Encrypt signiert nicht, wenn die Ekliptikkurven höher als 384 Bit sind, und moderne Browser wie Google Chrome markieren Websites, die 512-Bit-Ekliptikkurven verwenden, als ungültig . Das ist die kurze Antwort.

## Erstellen wir eine OpenSSL-Konfiguration für das Zertifikat

Jetzt müssen wir eine OpenSSL-Konfigurationsdatei erstellen, die die domänenspezifischen Parameter enthält, für die wir das TLS-Zertifikat erhalten möchten. In diesem Beispiel tragen wir die folgende Konfiguration in eine `openssl.cnf`-Datei ein:

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

Hier ist eine kurze Beschreibung dieser Konfigurationsoptionen:

Im erforderlichen `[ req ]`-Abschnitt:

* `prompt=no` weist OpenSSL an, so viel Konfiguration wie möglich aus der Konfigurationsdatei zu holen
* `encrypt_key = no` weist OpenSSL an, den privaten Schlüssel nicht mit einem Passwort zu verschlüsseln. (Verschlüsselte private Schlüssel werden von Nginx unterstützt, aber ich verwende sie nicht.)
* `default_md=sha512` weist OpenSSL an, die CSR mit SHA512 zu signieren. (Soweit ich weiß, unterstützt Let's Encrypt nur RSA mit SHA256 für seine Signaturen, aber das bedeutet nicht, dass wir in CSR keine stärkere Verschlüsselung verwenden können.)
* `distinguished_name=dname` weist OpenSSL an, nach einem `[ dname ]`-Abschnitt für Konfigurationsoptionen für Distinguished Name zu suchen.
* „req_extensions=reqext“ weist OpenSSL an, in den Konfigurationsoptionen nach „Subject Alternative Names“ (SANs)-Erweiterungen, die es konfigurieren möchte, nach einem „[ reqext ]“-Abschnitt zu suchen.

Im Abschnitt Distinguished Name `[ dname ]`:

* „CN = example.com“ gibt den Common Name des Zertifikats an.
* Ihre `emailAddress = admin@example.com` E-Mail-Adresse muss prominent sein.
Gewünschte Erweiterungen Im Abschnitt „[ reqext ]“ stellt subjectAltName die Liste der SANs für das Zertifikat bereit. (Chrome ab v58 erfordert, dass der Common Name in der Liste der SANs enthalten ist).

Let's Encrypt v2 unterstützt Platzhalterdomänen, daher können Sie in diesem Beispiel einen einstufigen Platzhalter für Nicht-Apex-Hosts (*.example.com) verwenden.

## Lassen Sie uns eine Zertifikatsignieranforderung erstellen

Der letzte Schritt auf der Client-Seite besteht darin, die Zertifikatsignieranforderung mit OpenSSL zu generieren, dann leiten wir sie zum Signieren an Let’s Encrypt weiter und rufen das signierte Zertifikat ab.

Der zum Generieren einer CSR erforderliche OpenSSL-Befehl lautet `req` .

```bash
openssl req -new -config openssl.cnf -key privkey.pem -out csr.pem
```

* `-new` teilt OpenSSL mit, dass wir eine CSR erstellt haben (und wir keine bestehende CSR untersuchen)
* `-config` openssl.cnf gibt die Konfigurationsdatei an, die wir oben erstellt haben
* `-key privkey.pem` gibt den privaten Schlüssel an, den wir oben erstellt haben
* `-out csr.pem` weist OpenSSL an, die CSR in eine Ausgabedatei zu schreiben (anstelle von stdout)

Wir können überprüfen, ob wir die CSR korrekt erstellt haben:

```bash
openssl req -in csr.pem -noout -text -verify
```

* `-verify` fordert OpenSSL auf, die Signatur in der CSR zu verifizieren

Dies sollte die folgenden erwarteten Ergebnisse in der Ausgabe erzeugen:

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
                    [ommited]
                ASN1 OID: secp384r1
                NIST CURVE: P-384
        Attributes:
        Requested Extensions:
            X509v3 Subject Alternative Name:
                DNS:example.com, DNS:www.example.com
    Signature Algorithm: ecdsa-with-SHA512
         [ommited]
```

## Bitten Sie Let's Encrypt, unser Zertifikat zu signieren

Der letzte Schritt besteht darin, die CSR mit einem ACME-Client zum Signieren an Let's Encrypt zu senden, "certbot" ist der häufigste Client für diesen Job.

Befehlszeilenoptionen, die an den „Certbot“-Client übergeben werden, hängen von unserem Setup, der Person, für die unsere Domain registriert ist, usw. ab. Normalerweise müssen wir den Befehl "certonly" verwenden, und wenn Sie Sternchen (*) verwendet haben, müssen Sie eines der certbot-DNS-Plugins verwenden.

Ist beispielsweise die Domain „example.com“ bei Cloudflare registriert, können wir die Verifizierung über das entsprechende Plugin durchführen, was äußerst komfortabel ist und keinen manuellen Eingriff in den Vorgang erfordert. (Das Konfigurieren des Cloudflare-Plugins mit geheimen Token-Informationen würde den Rahmen dieses Artikels sprengen.)

Es wird normalerweise empfohlen, zuerst mit `--dry-run` sicherzustellen, dass alles in Ordnung ist, um sicherzustellen, dass alles in Ordnung ist.

```bash
certbot nginx certonly --dry-run --domain "example.com" --domain "www.example.com" --csr csr.pem
```

* Anführungszeichen sind um Zeichen herum erforderlich, um fehlerhafte Manipulationen zu vermeiden, und sind im Allgemeinen eine gute Idee.
* `--csr csr.pem` teilt certbot mit, dass wir bereits ein Zertifikat haben und Let’s Encrypt benötigen, um es für uns zu signieren.

Der Certbot-Client überprüft auf der Befehlszeile, ob die angeforderte Liste der Domänen mit den im Zertifikat aufgeführten Domänen übereinstimmt, und verwendet das Certbot-NGINX-Plug-in, um zu überprüfen, ob die Domäne unsere ist, und teilt uns mit, falls es Probleme gibt.

Wenn nichts falsch ist, wird es Ihnen sagen:

```text
WICHTIGE NOTIZEN:
 - Der Probelauf war erfolgreich.
```

Der eigentliche Befehl lautet wie folgt:

```bash
certbot nginx certonly --domain "example.com" --domain "www.example.com" --csr csr.pem
```

Nach einer (langen) Verzögerung gibt der Client Folgendes aus:

1. Signiertes Zertifikat: `0000_cert.pem`
2. Stamm- und Zwischenzertifikate: „0000_chain.pem“.
3. Zertifikat + Zwischenprodukte: `0001_chain.pem`
An dieser Stelle kann die CSR `csr.pem` gelöscht werden.

Wenn wir neugierig sind, können wir die vom Client zurückgegebenen Zertifikate mit OpenSSL mit dem Befehl "x509" überprüfen:

```bash
openssl x509 -in 0001_chain.pem -noout -text
```

Leider werden wir feststellen, dass Let’s Encrypt wie oben beschrieben unser Zertifikat mit einer SHA256-Signatur signiert. (SHA512 ist nicht nur sicherer, sondern übertrifft SHA256 auf modernen 64-Bit-CPUs.) Aber unser öffentlicher Schlüssel sollte immer noch ECDSA verwenden.

Diese Dateien sind nicht gewöhnlich, daher müssen wir sie auf informativere Weise verschieben und bearbeiten.

Unter Debian Linux erstelle ich gerne Unterverzeichnisse für meine Domains, indem ich meinen privaten Schlüssel in `/home/USER_NAME/SSL/private/example.com/privkey.pem` und Zertifikate behalte:

* `/home/USER_NAME/SSL/certs/example.com/cert.pem`
* `/home/USER_NAME/SSL/certs/example.com/chain.pem`
* `/home/USER_NAME/SSL/certs/example.com/fullchain.pem`

# ENDE

Wenn wir alles richtig gemacht haben, bestätigt die Überprüfung des Zertifikats mit einem Webbrowser wie Chrome, dass es sich um ein EC-Zertifikat handelt:

![https://dev.to/benjaminblack/obtaining-an-elliptic-curve-dsa-certificate-with-lets-encrypt-51bc (Datum: 08.04.2023)](/images/ecc-ssl/ecc-sll-key-chrome.png)

Mozilla Observatory wird uns auch eine A+ Bewertung geben!

![https://dev.to/benjaminblack/obtaining-an-elliptic-curve-dsa-certificate-with-lets-encrypt-51bc (Datum: 08.04.2023)](/images/ecc-ssl/ecc-ssl-key-mozilla.png)

Darüber hinaus können wir als Ergebnis des SSL Labs-Berichts sehen, dass ein 384-Bit-ECC-Zertifikat verwendet wurde.

![SSL Labs Test](/images/ecc-ssl/ecc-ssl-key-ssllabs.png)

HINWEIS: Dieser Artikel profitiert vom Artikel von [Benjamin Black](https://dev.to/benjaminblack/obtaining-an-elliptic-curve-dsa-certificate-with-lets-encrypt-51bc) zum gleichen Thema.

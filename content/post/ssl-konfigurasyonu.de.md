---
title: "Erhöhung der SSL-Sicherheit auf Linux-Servern"
date: 2021-10-12
tags: ["linux", "ssl", "sicherheit", "audit"]
author: "Wise"
draft: false
---
# Erhöhung der SSL-Sicherheit auf Linux-Servern

Wenn Sie heute eine Website und/oder App auf Ihrem aktuellen Server betreiben, werde ich über die benötigte SSL-Verbindung und die damit verbundene openssl-Bibliothek sprechen. SSL (Secure Socket Layer) und TLS (Transport Layer Security) sind eine Form der Verbindung, die es Personen, die sich mit Ihrem Server verbinden möchten, ermöglicht, sicher mit Ihrer Website zu kommunizieren. In der Vergangenheit gab es Versionen von SSL v1 bis v3, und während Websites im Allgemeinen diese SSL-Versionen verwenden, wurde SSL jetzt von den Websites aufgegeben und durch das sicherere TLS ersetzt. Wir müssen jedoch weiterhin das Wort "ssl" im erläuternden Teil und beim Bearbeiten der Konfigurationsdateien verwenden. Um Ihnen das mit ein wenig Humor zu sagen, haben Sie jemals darüber nachgedacht, warum, wenn Sie die 64-Bit-Version einer Anwendung herunterladen möchten, warum sie "amd_64" heißt? Da AMD als erster auf 64 Bit umgestiegen ist, blieb diese Namensgebung als Zeichen des Respekts und/oder der Gewohnheit bei amd_64. Auch wenn wir derzeit TLS verwenden, bleiben die Benennungs- und Konfigurationsparameter „SSL“.

Wie in unserem vorherigen Artikel werde ich den Prozess noch einmal unter drei verschiedenen Überschriften als einfach, empfohlen und fortgeschritten erklären. Titelinhalte werden sukzessive nach persönlichen Anforderungen berücksichtigt. Obwohl die Titel miteinander verknüpft sind, ist es kein Problem, sie in einem gewünschten Stadium zu belassen.

## Einfache Konfiguration

Zuerst müssen wir die Updates über die Konsole mit dem Paketmanager der Linux-Version installieren, in der wir uns befinden.

```
Ubuntu: sudo apt update && sudo apt upgrade -y

Fedora: sudo yum update -y

Arch Linux: sudo pacman -Syyu
```

Nachdem die Updates installiert sind, beginnen wir mit der Konfiguration des nginx/Apache-Dienstes (das ist der Dienst, der es Ihnen ermöglicht, externe Webverbindungen zu empfangen) auf Ihrem Server (in meinem Fall Ubuntu). Die Datei, in der die Nginx-Diensteinstellungen gespeichert sind, befindet sich im Allgemeinen unter „/etc/nginx/nginx.conf“. Wir müssen es mit einem der von uns verwendeten Texteditoren öffnen, jedoch mit einem Benutzer mit sudo-Rechten (dh Administratorrechten).

Wenn wir mit Ubuntu fortfahren (Single IP for Single Server Configuration)

```
sudo nano /etc/nginx/nginx.config # Befehl zum Öffnen der Einstellungsdatei
```

```
Zu ergänzende Titel (gegebenenfalls geändert)
hören 443 ssl http2; >> Es dient dazu, die Anfragen zu erfüllen, die über IPv4 mit dem http2-Protokoll an Port 443 kommen, und eine SSL-Verbindung aufzubauen.

hören [::]:443 ssl http2; >> Es dient dazu, die an Port 443 eingehenden Anfragen über IPv6 mit http2-Protokoll zu erfüllen und eine SSL-Verbindung herzustellen. (Wenn Sie keine IPv6-Unterstützung haben oder sie nicht nativ unterstützen möchten, können Sie sie entfernen.)

server_name IHR SERVER_NAME; >> Wenn Sie Ihren Servernamen nicht als Standard festlegen möchten, können Sie einen Servernamensindikator angeben. Dies dient nur dazu, die an Ihren Servernamen eingehenden Anforderungen zu erfüllen, anstatt alle eingehenden Anforderungen zu erfüllen.

ssl_certificate /etc/letsencrypt/live/YOURSERVER_NAME/fullchain.pem; >> Wenn Sie Let's Encrypt für kostenloses SSL verwenden, ist dies der Standardspeicherort für das Zertifikat. Andernfalls ersetzen Sie es durch Ihre eigene Zertifikatsdatei.

ssl_certificate_key /etc/letsencrypt/live/YOURSERVER_NAME/privkey.pem; >> Wenn Sie Let's Encrypt für kostenloses SSL verwenden, ist dies der Standardspeicherort für private Schlüssel. Ersetzen Sie es andernfalls durch Ihren eigenen Speicherort der privaten Schlüsseldatei.

ssl_protocols TLSv1.3 TLSv1.2; >> Erforderlicher Befehl, um nur die neuesten und zuverlässigsten TLS-Protokolle zu akzeptieren.

ssl_prefer_server_ciphers on; >> Während des Gesprächs zwischen dem Server und dem Client, schätze ich, dass sie über "ok, mal sehen, was wir haben" gesprochen haben. Kurz gesagt, wenn es für Sie funktioniert, wenn es für Sie nicht funktioniert, rede ich nicht.

ssl_ecdh_curve secp521r1:secp384r1; >> Es ist der Befehl, der uns sagt, welche Kurven wir bevorzugen, wenn wir Ekliptikkurven verwenden müssen.

ssl_chiffren DH-RSA-AES256-SHA:DH-RSA-AES256-SHA256:DHE-RSA-AES256-GCM-SHA384:DH-RSA-AES256-GCM-SHA384:TLS_AES_256_GCM_SHA384:TLS_CHACHA20_PODSADHESA:25_SHA256 -ECDSA-AES256 -RSA-AES256-SHA:AECDH-AES256-SHA:ECDHE-ECDSA-AES256-SHA384:ECDH-ECDSA-AES256-SHA384:ECDH-RSA-AES256-SHA384:ECDHE-ECDSA-AES256 -GCM-SHA384:ECDH-ECDSA -AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDH-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-CCM:DHE-RSA-AES256-CCM8 :ECDHE-ECDSA-AES256-CCM :ECDHE-ECDSA-AES256-CCM8:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-CHACHA20-POLY1305:DHE-RSA-CHACHA20-POLY1305; >> Der Code, der den Server anweist, nur die SSL-Algorithmen zu verwenden, die ich am zuverlässigsten finde.
```
Alle Chiffren für diejenigen, die einzeln recherchieren wollen: "https://testssl.sh/openssl-iana.mapping.html"

Wenn Sie nach dem Vornehmen der Einstellungen überprüfen möchten: Sie können den Befehl „sudo nginx -t“ verwenden. Wenn Sie keine Fehlermeldung sehen, können Sie die Einstellungen übernehmen und den Dienst mit dem Befehl "sudo systemctl restart nginx" oder "sudo service nginx restart" neu starten.

## Empfohlene Einstellungen
Zusätzlich zu den vorherigen Einstellungen werden wir einige Leistungsverbesserungen sowie einige zusätzliche Konfigurationen vornehmen, die es Ihrer Website ermöglichen, auf SSL-Testseiten einen höheren Rang einzunehmen. Danach werden wir einige Verbesserungen vornehmen, um sicherzustellen, dass einige Header und Ressourcen Ihrer Website nicht von Websites Dritter ausgenutzt werden, was für den Benutzerzugriff Ihrer Website von Vorteil ist.

```
Zu ergänzende Titel (gegebenenfalls geändert)
ssl_session_cache freigegeben:TLS:2m; >> Code, der angibt, wie TLS-Verbindungen auf Worker (nginx-Worker) verteilt werden und wie lange die Verbindungen geteilt werden

ssl_buffer_size 4k; >> Der Code, der angibt, in wie viele Container die Pakete aufgeteilt werden, wenn auf SSL-Anfragen geantwortet und Pakete nach dem Handshake gesendet werden. Ein niedrigerer Wert bedeutet, dass mehr Pakete gesendet werden, aber weniger Overhead.

ssl_stapling an; >> Aktiviert die OCSP-Heftfunktion

ssl_stapling_verify an; Aktiviert die Möglichkeit, das OCSP-Heften zu überprüfen, einschließlich auf übergeordneten und Stammservern.

Resolver 1.1.1.1 1.0.0.1 2606:4700:4700::1111 2606:4700:4700::1001; >> Aktiviert die OCSP-Stapling-Überprüfung mit Cloudlfare. Wenn Sie IPv6 nicht verwenden oder es nicht nativ unterstützen möchten, können Sie die IPv6-Adressen löschen.

add_header X-Content-Type-Options "nosniff" immer; >> Es ist der Header-Wert, der Browser daran hindert, MIME-Inhalte zu verstehen.

add_header X-Xss-Protection "1; mode=block" immer; >> Es handelt sich um einen Titel, der die Schwachstelle bis zu einem gewissen Grad verhindert, indem er Benutzern ermöglicht, einen weißen Bildschirm in einer möglichen XSS-Schwachstelle zu sehen.

add_header X-Frame-Optionen "SAMEORIGIN" immer; >> Es verhindert in irgendeiner Weise, dass eine Seite Ihres Servers auf einer anderen Seite angezeigt und/oder nacheinander mit einem i-Frame-Code usw. veröffentlicht wird. Nur Sie können ein Fenster von Ihrer eigenen Site innerhalb Ihrer eigenen Site veröffentlichen.

add_header Referrer-Policy "no-referrer-when-downgrade" immer; >> Wenn Sie auf eine Website mit geringeren Sicherheitsmaßnahmen umleiten oder verlinken, wird nicht automatisch ein Referrer-Header hinzugefügt und es ist nicht klar, dass der Datenverkehr von Ihrer Website kommt.

add_header Content-Security-Policy "default-src 'self' https: data: 'unsafe-inline' 'unsafe-eval';" immer; >> Der Titel, der die Bedingungen regelt, unter denen Anfragen, die Sie und andere Benutzer von außen anrufen können, aufgerufen werden können. Standardmäßig vertraue ich einigen Quellen, die über https kommen.

add_header Berechtigungsrichtlinie "Kamera=(), Vollbild=(self), Geolocation=(), Mikrofon=(), Zahlung=()" immer; >> Es verhindert das Sammeln von Informationen auf Ihrer Website mit verschiedenen Arten von Vergiftung (Cache-Vergiftung oder js-Vergiftung), indem es angibt, welche Berechtigungen Sie für den Browser wünschen oder welche Sie überhaupt nicht benötigen.
```

Wenn Sie nach dem Vornehmen der Einstellungen überprüfen möchten: Sie können den Befehl „sudo nginx -t“ verwenden. Wenn Sie keine Fehlermeldung sehen, können Sie die Einstellungen übernehmen und den Dienst mit dem Befehl „sudo systemctl restart nginx“ oder „sudo service nginx restart“ neu starten.

## Erweiterte Einstellungen

Zunächst fügen wir Ihrer Website einen Header hinzu, um anzuzeigen, dass sie nur über SSL verbunden werden soll. Auf diese Weise können diejenigen, die Ihre Website zuvor aufgerufen haben, und diejenigen, die diesen Titel bereits in ihrem Browser haben, nicht ohne SSL auf Ihre Website zugreifen, selbst wenn sie dies wünschen. Dann werden wir die SSL-Zertifikate heften, die in HTTP-Sitzungen verwendet werden sollen, und wir werden im Voraus angeben, welche Zertifikate neben der vorherigen Methode verbunden werden müssen. Selbst wenn Sie ein autorisierter Top-Zertifikatsmanager oder Root-Manager sind, können diese auf diese Weise keine Verbindung zu Ihrer Site mit dem in Ihrem Namen signierten Zertifikat herstellen. E-Tugra-Stammzertifikatanbieter mit Wohnsitz in der Türkei zu diesem Zeitpunkt unterzeichnete ein Zertifikat für *.google.com. Wenn Sie ein wenig recherchieren, werden Sie herausfinden, wann es passiert ist und warum (wie schlimm es sein könnte). Beginnen wir nun mit dem letzten Konfigurationsteil.

Stellen Sie zunächst sicher, dass Ihre Website problemlos über SSL aufgerufen werden kann. Fügen Sie dann gemäß Ihrer Anfrage einen der folgenden Header zur nginx-Konfigurationsdatei hinzu. Aber Achtung, nur einer.

```
add_header Strict-Transport-Security "max-age=2592000;" immer; >> Header, der besagt, dass auf Ihre Website 30 Tage lang nur über HTTPS zugegriffen werden kann. (ohne Subdomains)

add_header Strict-Transport-Security "max-age=2592000; includeSubDomains;" immer; >> Header, der besagt, dass Ihre Website 30 Tage lang nur über HTTPS aufgerufen werden kann, einschließlich Subdomains.

add_header Strict-Transport-Security "max-age=31536000; includeSubDomains;" immer; >> Header, der besagt, dass auf Ihre Website 1 Jahr lang nur über HTTPS zugegriffen werden kann, einschließlich Subdomains.

add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" immer; >> Der Header, der angibt, dass auf Ihre Website 1 Jahr lang nur über HTTPS zugegriffen werden kann, einschließlich Subdomains, und dass dieser Header von Browsern zwischengespeichert wird. Darüber hinaus werden neue Browser diesen Titel erkennen, auch wenn sie Ihre Website noch nie zuvor besucht haben.

add_header Strict-Transport-Security "max-age=0; includeSubDomains"; >> Titel für das vollständige Entfernen der HSTS-Funktion und der Mitgliedschaft in der Preload-Liste.
```

Nachdem Sie den oben erwähnten Header hinzugefügt haben, ist es jetzt an der Zeit, den Hash des verwendeten SSL-Zertifikats an die HTTP-Sitzung anzuheften. In diesem Stadium müssen wir einen Hash Ihres aktuellen Zertifikats extrahieren, das Zertifikat der obersten Unterzeichnungsstelle hashen und diesen Hash-Prozess fortsetzen, bis wir die gesamte Kette einschließlich der obersten Stammzertifizierungsstelle abgeschlossen haben. Aus diesem Grund führen wir die folgenden Befehle jeweils mit einem Root-Benutzer oder einem Benutzer mit sudo-Berechtigung aus. (Der Vortrag wurde speziell für Let's Encrypt gemacht.)

```
1] cat /etc/letsencrypt/live/IHR SERVERNAME/cert.pem | openssl x509 -pubkey | openssl pkey -pubin -outform der | openssl dgst -sha256 -binär | base64 >> Dieser Befehl extrahiert den Hash des Zertifikats Ihrer Website. Kopieren Sie den Ergebniswert irgendwo hin.
2] curl -s https://letsencrypt.org/certs/lets-encrypt-x4-cross-signed.pem | openssl x509 -pubkey | openssl pkey -pubin -outform der | openssl dgst -sha256 -binär | base64 >> Dieser Befehl extrahiert eines der mehrfach signierten Zertifikate von letsencrypt.
3] curl -s https://letsencrypt.org/certs/lets-encrypt-x3-cross-signed.pem | openssl x509 -pubkey | openssl pkey -pubin -outform der | openssl dgst -sha256 -binär | base64 >> Dieser Befehl extrahiert eines der mehrfach signierten Zertifikate von letsencrypt.
4] curl -s https://letsencrypt.org/certs/isrgrootx1.pem | openssl x509 -pubkey | openssl pkey -pubin -outform der | openssl dgst -sha256 -binär | base64 >> Dieser Befehl extrahiert das Stammzertifikat (oberstes Zertifikat) von letsencrypt.

Der folgende Wert wird der Nginx-Konfigurationsdatei hinzugefügt
5] add_header Public-Key-Pins 'pin-sha256="ERSTE_ERGEBNIS"; Pin-sha256 = "ZWEITES_ERGEBNIS"; pin-sha256="TIP_END"; pin-sha256="FINISH_RESULT"; Maximalalter = 2592000; includeSubDomains' immer; >> Sie können sich nur mit dem angegebenen Zertifikat 30 Tage lang mit Ihrer Website verbinden. Sie können den max-age-Wert optional erhöhen. Bevor die Gültigkeitsdauer Ihres Zertifikats weniger als 30 Tage beträgt, müssen Sie die Kopfzeile deaktivieren oder ein neues Zertifikat anfordern und es als fünften Wert hinzufügen.

Als Bonus möchte ich Ihnen einen weiteren Befehl zeigen, dessen Ausführung lange dauert, aber sehr nützlich ist.
6] openssl dhparam -out /etc/nginx/dhparams.pem 4096 >> Die Ausführung dieses Befehls kann zwischen 15 Minuten und 1 Stunde dauern.

Nachdem der Vorgang abgeschlossen ist, müssen Sie den folgenden Befehl zur nginx-Konfigurationsdatei hinzufügen.
ssl_dhparam /etc/nginx/dhparam.pem; >> Der Befehl zum Ändern der während des Diffie-Hellman-Schlüsselaustauschalgorithmus zu verwendenden Werte mit den gerade erstellten geheimen Werten, abgesehen von den Standardwerten.
```
Nachdem Sie die Einstellungen vorgenommen haben, übernehmen Sie die Einstellungen mit dem Befehl „sudo nginx -t“ und dann, wenn Sie keine Fehlermeldung sehen, „sudo service nginx restart“ und starten Sie den Dienst neu. Nun wird die Verbindung mit der von Ihnen festgelegten Konfiguration und Bedingungen bereitgestellt. Wenn Sie den Vorher/Nachher-Bewertungsunterschied sehen möchten, können Sie sich die Bilder unten ansehen oder Ihre eigene Website unter „https://www.ssllabs.com/ssltest/index.html“ testen.

VOR
{{< img src="/images/ssl-ilk-hali-ssllabs.png" >}}

NACH
{{< img src="/images/ssl-son-hali-ssllabs.png" >}}

Wenn Sie fragen, warum die Cipher-Stärke nicht 100 % beträgt, ist es derzeit nicht möglich, 100 % zu erreichen, da „TLS_AES_128_GCM_SHA256 (0x1301)“ automatisch mit TLS 1.3 geliefert wird und hinzugefügt wird, auch wenn wir es nicht möchten. Wenn Sie denken, dass ich TLS 1.3 abschalte, dann wird es nicht kommen, dann sind Ihre Punkte leider woanders weg.

# Ende

Dieser Artikel wurde zuvor unter https://teknolojirehberleri.xyz veröffentlicht. Um ein persönliches Portfolio zu erstellen, hatte ich das Bedürfnis, es auf meiner persönlichen Website erneut zu veröffentlichen.

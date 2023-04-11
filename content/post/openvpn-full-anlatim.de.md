---
title: "Eingehender OpenVPN-Test"
date: 2022-03-27
tags: ["linux", "ssl", "security", "ecc", "elliptic curve", "openvpn", "tls", "aes"]
author: "Wise"
draft: false
---
# Einführung und Zusammenfassung

Heute werden wir OpenVPN, meiner Meinung nach eine der wichtigsten Software der letzten Zeit, ausführlich überprüfen. In diesem Test werden wir zuerst darüber sprechen, wofür OpenVPN verwendet wird. Dann untersuchen wir, was wir zum Ausführen des Programms benötigen und was vor dem ersten Ausführen zu tun ist. Abschließend werde ich versuchen zu erklären, was vom ersten Moment des Verbindungsaufbaus bis zum letzten Schritt, wenn die Daten entschlüsselt werden, im Hintergrund vor sich geht. Daher schätze ich, dass unser Artikel aus 3 Teilen und gegebenenfalls einem Frage-Antwort-Abschnitt bestehen wird. Jetzt schnallen wir uns an und machen einen Ausflug in die tiefe und düstere Welt des Internets.

## Was ist OpenVPN und wofür wird es verwendet?

Heutzutage gibt es fast kein Geschäft, das nicht online erledigt werden kann. Sogar unsere Arbeit, die normalerweise nicht online ist, hat sich aufgrund der Pandemie und der neuen Normalität auf die Arbeit von zu Hause aus entwickelt. Es gab jedoch große Probleme, sowohl weil es eine Arbeitsmethode ist, an die wir nicht gewöhnt sind, als auch weil unsere Leute nicht sehr gut mit Technologie umgehen können. Bevor klar wurde, dass die Menschen sich von ihren Heimcomputern mit ihren Bürocomputern verbinden mussten, kamen einige Unternehmen auf frivole Ideen, wie zum Beispiel, Bürocomputer zu den Häusern der Mitarbeiter zu schicken. Sie haben sehr gut verstanden, wie falsch das war, aus dem Feedback, das sie in kurzer Zeit erhalten haben. Kurz gesagt, es wurde endlich akzeptiert, dass elektronische Geräte im Büro bleiben und irgendwie eine sichere und nachhaltige Verbindung aus der Ferne hergestellt werden sollte. Natürlich befanden sich Institutionen auch schon früher in solchen Nöten, aber eine solche Großsituation kam damals nicht in Frage. Vor der Pandemie wurden verschiedene Protokolle wie PPTP, L2TP, IPSec, IKev2, SSTP und schließlich OpenVPN verwendet. Dies sind normalerweise Abkürzungen für bestimmte lange und ausgefallene Wörter, und ihre grundlegende Logik besteht darin, zwei oder mehr Geräte zu verbinden und sie so zu verhalten, als ob sie sich im selben Netzwerk befinden. Ich werde nicht viel sagen, da Protokolle vor OpenVPN gewisse Schwächen, Langsamkeit und technische Schwierigkeiten bei der Implementierung mit sich brachten. OpenVPN ist der Name des Protokolls und Programms, das es mindestens 2 Geräten in der Rolle von Server und Client ermöglicht, sich miteinander zu verbinden, und zwar auf eine Weise, die Industriestandards entspricht. Ich verwende ein Remote-Desktop-Programm. Ich höre Sie anscheinend sagen, was die Notwendigkeit dafür ist. Leider müssen er und alle anderen Programme wie er grundsätzlich dieses Protokoll verwenden. Wenn Sie das Schildsymbol oder die Verbindungsdetails in TeamViewer, einem der bekanntesten, drücken, können Sie das OpenVPN-Protokoll sehen.

## Was brauchen wir, um eine OpenVPN-Verbindung aufzubauen?

Zunächst muss OpenVPN sowohl auf der Server- als auch auf der Clientseite (dem zu verbindenden Gerät) installiert werden. Dann sollte eine Einstellungsdatei (Konfigurationsdatei) bearbeitet werden, die die Bedingungen zeigt, unter denen die Geräte kommunizieren. Das Hauptereignis ist, dass diese Konfigurationsdatei vom Client generiert und verwendet wird. Diese Konfigurationsdatei ist unterteilt in server_config, die vom Server verwendet wird, und client_config, die vom Client verwendet wird.

### Die vom Server verwaltete Einstellungsdatei enthält die folgenden Einträge

- `Port 1194` gibt an, welcher Port eine Verbindungsanfrage zum Herstellen der OpenVPN-Verbindung erhält.
- `proto tcp` Verbindung über TCP oder UDP möglich. Einstellungseintrag zur Auswahl eingetragen.
- `dev tun` TAP- oder TUN-Schnittstelle kann verwendet werden. Dies sind virtuelle Schnittstellen. TAP-Schicht 2 stellt eine Verbindung her, während TUN-Schicht 3 eine Verbindung herstellt.
- `user none` Ermöglicht das Verbinden von Benutzern mit einem nicht autorisierten Benutzer auf dem Server.
- `group $NOGROUP` Ermöglicht es, sich verbindende Benutzer mit einer nicht autorisierten Gruppe auf dem Server als Gruppe zu verknüpfen.
- `persist-key` Eine Autorisierungseinstellung für die Erstellung und den Neustart der virtuellen Schnittstelle
- `persist-tun` Ebenfalls eine Berechtigungseinstellung für die Erstellung und den Neustart der virtuellen Schnittstelle
- `keepalive 10 120` Eine Einstellung dafür, wie viele Verbindungen aktiv gehalten werden und wie lange die aktive Verbindung beendet wird, wenn keine Kommunikation hergestellt wird.
- `ifconfig-pool-persist ipp.txt` Eine Einstellung, um die von OpenVPN an Clients im virtuellen Netzwerk vergebenen IP-Adressen beizubehalten und ihnen dieselben Adressen zuzuweisen, wenn sie sich wieder verbinden
- `push "dhcp-option DNS 1.1.1.1"` Eine DNS-Einstellung, die der Server beim Verlassen des Netzwerks verwenden soll
- `compress` Der Teil, in dem Komprimierungsoptionen festgelegt werden
- `dh none` Eine Einstellung zum Ein- oder Ausschalten von Diffie-Hellman
- `ecdh-curve` Wenn Sie Elliptic Curve Diffie-Hellman verwenden, wird die Einstellung angepasst, neben der die Kurve ausgewählt werden muss
- `dh dh.pem`-Einstellung, die den Speicherort der PEM-Datei angibt, die Sie vorher erstellen müssen, wenn Sie Diffie-Hellman verwenden
- `tls-crypt tls-crypt.key` Erforderliche Einstellung, um die TLS-Schicht noch vor dem Pre-Shared Master zu verschlüsseln
- `tls-auth tls-auth.key 0`Einstellung, die es ermöglicht, Parteien über die Verschlüsselung hinaus in der Pre-Handshake-Phase der TLS-Schicht zu authentifizieren
- `crl-verify crl.pem` Einstellung um zu prüfen, ob die generierten Zertifikate über die CRL-Liste widerrufen werden oder nicht
- `ca ca.crt` Eine Einstellung, die den Speicherort des Zertifikats der Zertifizierungsstelle des generierten Zertifikats meldet
- `cert $SERVER_NAME.crt` Eine Einstellung, die den Speicherort des Zertifikats des Servers angibt
- `key $SERVER_NAME.key` Eine Einstellung, die den Speicherort des erforderlichen asymmetrischen geheimen Schlüssels neben dem Zertifikat des Servers angibt
- `auth $HMAC_ALG` Eine Einstellung, die beschreibt, welcher Hash-Algorithmus für den Datenkanal verwendet werden soll, und ggf. `tls-auth`
- `cipher $CIPHER` Eine Einstellung, die angibt, welcher Verschlüsselungsalgorithmus für den Datenkanal verwendet werden soll
- `ncp-ciphers $CIPHER` Eine Einstellung, die angibt, welche Verschlüsselungsalgorithmen der Server verwenden kann
- `tls-server` Eine Einstellung, die den Server anweist, den TLS-Kanal zu verwenden
- Eine Einstellung `tls-version-min 1.2` meldet die niedrigste Version, die auf dem TLS-Kanal verwendet werden soll
- `tls-cipher $CC_CIPHER` Die Verschlüsselung wird auch auf der TLS-Schicht verwendet, mit Ausnahme des Datenkanals, der die Einstellung ist, die die Verschlüsselung des Steuerkanals deklariert
- `client-config-dir /etc/openvpn/ccd` Einstellung, die angibt, wo Client-Einstellungsdateien aufbewahrt werden
- `status /var/log/openvpn/status.log` Einstellung, die angibt, wo Statusberichte geschrieben und wo Protokolldateien aufbewahrt werden
- `verb 3` Diese Einstellung, die die Abkürzung des Wortes Verbose ist, ist die Einstellung, wie detailliert der Statusbericht gegeben werden soll.

### Die clientseitige Einstellungsdatei enthält die folgenden Einträge

- `client` gibt an, dass sich das Gerät in der Client-Rolle befindet
- `proto tcp-client` meldet TCP als Protokoll
- `remote $IP $PORT` Der Teil, in dem die IP-Adresse und die Portnummer des/der zu verbindenden Server(s) festgelegt werden
- `dev tun` legt fest, welche der TUN/TAP-Schnittstellen verwendet werden sollen
- `resolve-retry infinite` Wir sagen Ihnen, wie lange Sie warten müssen, wenn sich die Adressauflösung aufgrund von IP oder DNS verzögert
- `nobind`-Einstellung, um keine Verbindung zu einer Adresse im lokalen Netzwerk herzustellen
- `persist-key` Ermöglicht das Lesen von Schlüsseldateien ohne zusätzliche Autorisierung beim Neustart
- `persist-tun` Es ermöglicht auch, dass die TUN/TAP-Schnittstelle beim Neustart ohne Autorisierung aufgeweckt wird
- `remote-cert-tls server` Überprüft das Zertifikat des verbundenen Servers auf TLS-Ebene
- `verify-x509-name $SERVER_NAME name` Befehl, der den Namen im Zertifikat angibt, zu dem der Server zurückkehren wird, und wie der Name des Servers lauten soll
- `auth $HMAC_ALG` Befehl, der angibt, welcher Algorithmus für die Validierung verwendet werden soll
- `auth-nocache` Speichert das für die Anmeldung erforderliche Passwort nicht
- `cipher $CIPHER` Befehl zur Auswahl des für die Verschlüsselung zu verwendenden Algorithmus
- `tls-client` aktiviert TLS während der TLS-Kommunikation und übernimmt die Client-Rolle
- `tls-version-min 1.2` Legt die niedrigste TLS-Version fest
- `tls-cipher $CC_CIPHER` wählt den Verschlüsselungsalgorithmus aus, der im TLS-Steuerkanal verwendet werden soll
- `ignore-unknown-option block-outside-dns` Verhindert, dass unbekannte DNS-Adressen verwendet werden
- `setenv opt block-outside-dns` blockiert DNS-Leaks für Windows 10
- `Verb 3` Bestimmt den Grad der Berichterstattung
- `compress` Hier werden die Einstellungen des Komprimierungsalgorithmus gemeldet
- `"<ca>/etc/openvpn/easy-rsa/pki/ca.crt</ca>"` Hartcodierte Einbettung der erwarteten Server-Zertifizierungsstellendatei
- `"<cert>/etc/openvpn/easy-rsa/pki/issued/$CLIENT.crt</cert>"` Hartcodierte Einbettung der Client-Zertifikatsdatei
- `"<key>/etc/openvpn/easy-rsa/pki/private/$CLIENT.key</key>"` Hartcodierte Einbettung des asymmetrischen geheimen Schlüssels des Clients
- `"<tls-crypt>/etc/openvpn/tls-crypt.key</tls-crypt>"` Angabe der Schlüsseldatei für TLS-Crypt
- `"<tls-auth>/etc/openvpn/tls-auth.key</tls-auth>"` Angabe der Schlüsseldatei für die TLS-Authentifizierung
- `key-direction 1` weist Client und Server Rollen für die TLS-Layer-Verschlüsselung zu (0 und 1)

Eine ausführliche Dokumentation dieser Einstellungen und mehr finden Sie auf der Webseite [OpenVPN](https://openvpn.net/community-resources/reference-manual-for-openvpn-2-4/).

## Was passiert beim Aufbau einer OpenVPN-Verbindung?

Jedes Mal, wenn ich mich mit OpenVPN verbinde, fühle ich mich, als würde R2-D2 ihre Pläne der Sternenflotte entführen. Die Leute wollen sich nicht immer einer genauen Prüfung unterziehen und bitten vielleicht jemanden, ihnen zu erklären, was und wie. In meiner Absicht, diesen Artikel zu schreiben, habe ich mir diese Frage tatsächlich gestellt und mich sehr bemüht, die Antwort zu bekommen. Ich möchte nicht, dass Sie sich so viel Mühe geben, aber ich kann nicht sagen, dass Sie es sofort hochladen sollen, denken Sie nicht an den Rest, es ist mein Job. Wie eingangs versprochen, werde ich Ihnen diesen Vorgang ausführlich erläutern und Ihnen die Entscheidung überlassen. So funktioniert der Prozess in einer OpenVPN-Verbindung, Vor- und Nachteile (wie ich es bisher herausfinden konnte). Sie bauen zunächst eine TCP/UDP-Verbindung auf. Sie führen einen Prozess wie jede Anwendung aus, die TCP verwendet, und wechseln dann zur TLS-Schicht. Sie führen einen Handshake und eine Authentifizierung auf der TLS-Schicht durch. Diese Schicht wird auch Steuerkanal genannt. Dann wird eine bestimmte Kommunikation festgelegt und der Datenkanal weitergegeben. Der Prozess der Ver- und Entschlüsselung der diesmal zu versendenden Datenpakete im Daten- bzw. Datenkanal beginnt. Dazu sprechen Geräte miteinander und Daten werden unter bestimmten gemeinsamen Bedingungen gesendet. Kurz gesagt, am Ende des von mir so beschriebenen Prozesses endet die von uns bei 0 begonnene Kommunikation mit dem sicheren und erwünschten Datenzugriff auf uns, oder es werden erneut Anfragen gesendet, diesmal auf umgekehrtem Weg, über die Verbindung that offen gehalten wird. So entsteht ein System wie ineinander verschachtelte Rohre. Wenn Sie hier das einzig Wichtige für den Artikel schreiben müssen:

- Es wird `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P512` sein.
  - Hier gibt die Eingabe `TLS` an, dass der Kontrollkanal über die TLS-Schicht ausgeführt wird. Andere Alternativen sind `SSL` oder `NULL`.
  - Die Eingabe `ECDHE` gibt an, dass der erste Vorschlüssel unter Verwendung des elliptischen Diffie-Hellman-Algorithmus generiert wird. Andere Alternativen sind die Verwendung von `DHE`, `DH` oder nicht.
  - Die `ECDSA`-Daten geben an, dass der Elliptic Digital Signature Certificate Algorithm für die gegenseitige Authentifizierung und den asymmetrischen Schlüssel verwendet wird. Eine weitere verfügbare Alternative ist `RSA`. Die anderen braucht man nicht zu zählen.
  - Gibt den Verschlüsselungsalgorithmus an, der auf dem Datenkanal `AES_256_GCM` verwendet werden soll. Andere Alternativen sind `AES-128-CBC`, `AES-128-GCM` und `AES-256-CBC`.
  - `SHA384` gibt den zu verwendenden Hash-Algorithmus an. Die andere Alternative ist `SHA256`.
  - `P512` ermöglicht die Auswahl der elliptischen Kurve als Prime-512. Andere Alternativen sind `P-256` und `P-384`.

### Der Prozess des Aufbaus der TCP-Verbindung

Wenn Sie nun ein ungefähres Bild des Prozesses im Kopf haben, beginne ich mit der Erklärung des TCP-Prozesses. Nehmen wir an, wir haben in unserem Fall einen Client und einen Server, und die Verbindung besteht nur aus diesen beiden. Der Client sendet ein SYN (m)-Paket an den Server, mit dem er sich verbinden möchte. Als Antwort sendet der Server ein SYN (n)-Paket und ein ACK (m+1)-Paket über denselben Port. Der Client, der dies empfängt, gibt ebenfalls als Antwort ACK (n+1) zurück, und es findet ein 3-Wege-TCP-Handshake oder ein 3-Wege-TCP-Handshake statt. Somit haben wir einen offenen Kanal zwischen Client und Server über den angegebenen Port.

![https://blog.shiftasia.com/what-happen-when-access-website (Datum: 08.04.2023)](/images/openvpn-full/TCP-Handshake.jpg)

---
![https://www.netscout.com/blog/asert/ddos-attacks-ssl-something-old-something-new (Datum: 08.04.2023)](/images/openvpn-full/TCP-Handshake-2.png)

Wie auf den Fotos zu sehen, kann bei reibungslosem Ablauf die Kommunikation in 3 Schritten erfolgen. Aber wenn Sie fragen, warum wir das in 3 Schritten machen, geht das nicht auch kürzer? Ich würde (vorerst) nein sagen, nein, für eine Vollduplex-Kommunikation müssen beide Parteien SYN- und ACK-Pakete senden. Vielleicht erzähle ich Ihnen in Zukunft andere Wege, aber im Moment ist es so. Wie auch immer, der TCP/UDP-Teil ist immer kurz und einfach.

### Prozess, der in der TLS-Schicht ausgeführt wird

Nach dem Aufbau einer Kommunikation über TCP ist der Client die Person, die die Konversation auf eine andere Ebene bringt. Clients fordern immer etwas vom Server an oder fordern eine Antwort an. Im Allgemeinen reagieren Server nicht auf eine Anfrage, die nicht bei ihnen angekommen ist. Der Prozess läuft nach dem Prinzip `Erst Nachfrage, dann Angebot` ab. Ja, die Parteien befinden sich jetzt auf der TLS-Schicht. Der Client begrüßt zuerst den Server. Kein Scherz, es ist echt. Das erste vom Client gesendete Paket wird als `Client-Hello`-Paket bezeichnet. Neben diesem Paket (um den Vorgang zu beschleunigen) das Paket `Supported-Chipers`, das die unterstützten Verschlüsselungsalgorithmen angibt, eine vom Client zufällig generierte Nummer, ein `SNI`-Servernamensindikator, wenn mehr als ein Dienst vorhanden ist die auf derselben IP-Adresse ausgeführt werden, und ggf. die Sitzungs-ID. . Die Antwort des Servers darauf ist zunächst einmal ein höfliches Hallo. Denn das erste Paket, das der Server als Antwort sendet, heißt `Server-Hello`-Paket. Neben diesem Paket sendet das `Selected-Chiper`-Paket, das das Serverzertifikat, die unterstützten Verschlüsselungsalgorithmen und den ausgewählten Algorithmus angibt, eine von ihm generierte Zufallszahl, gegebenenfalls die Sitzungs-ID und eine SNI-ähnliche ID wenn sich mehr als ein Client über dieselbe IP verbindet. . Der Client verifiziert zunächst anhand des Serverzertifikats, ob es sich wirklich um die Person handelt, auf die er wartet, indem er die Kommunikation startet. Außerdem prüft der Server in manchen Fällen mit einem Zertifikat, ob der Client einer der Clients ist, die er erwartet. Wenn dieser gegenseitige Authentifizierungsprozess positiv ist, wird die nächste Stufe bestanden. Der Schlüsselerzeugungs- und Austauschprozess wird ausgelöst. In dieser Phase greift der Client erneut ein und sagt, dass er den Schlüssel mit dem Algorithmus ändern möchte, den er während dieser Kommunikation festgelegt hat, was als unsicher gilt. Die Parteien beginnen mit der Generierung eines Prekeys mit Diffie-Hellman oder ECDHE. Dazu werden Pre-Secrets von Client und Server geteilt. Die Antworten, die durch Ausführen einer Reihe mathematischer Operationen gefunden wurden, werden nach oben gesendet, und das gleiche Ergebnis wird durch erneutes Ausführen mathematischer Operationen erreicht. Das Ergebnis ist der erste Fore-Key, den sie sicher zwischen ihnen erstellt haben. Danach, mit dem von ihnen bestimmten Verschlüsselungsalgorithmus, Ein anderer Datenkanal als der Steuerkanal wird zur Kommunikation erstellt, und der Prozess wird von dort aus fortgesetzt.

![https://www.researchgate.net/publication/298065605_A_multi-level_framework_to_identify_HTTPS_services (Datum: 08.04.2023)](/images/openvpn-full/TLS-Handshake.png)

---
![](/images/openvpn-full/TLS-Handshake-2.png)

Wie auf den Fotos zu sehen ist, ist der Vorgang fast derselbe wie beim Herstellen einer Verbindung zu einer Webseite. Je nach Bedarf werden nur bestimmte Stufen hinzugefügt, entfernt oder geändert. Beispielsweise übermitteln die Parteien gemäß PFS, was für Advanced Privacy steht, den Front-Key nicht mit dem asymmetrischen Schlüssel des Servers. Da in diesem Fall für jede Sitzung derselbe Schlüssel verwendet wird, werden die Daten gespeichert und sind dann rückwirkend lesbar, indem auf einen Tag gewartet wird, an dem der Schlüssel enthüllt wird. Deshalb wurde diese Änderung vorgenommen. Auch hier möchte ich gemäß dem Zero-Trust-Bedrohungsmodell, dass jede Schicht und jeder Prozess den Prozess vorantreibt, ohne darauf zu vertrauen, dass die andere ihre Arbeit korrekt erledigt. Aus diesem Grund wollen wir, dass die Pakete gemäß der Funktion `tls-auth` verschlüsselt werden und die Integrität der ein- und ausgehenden Daten bereits im ersten Kommunikationsmoment in der TLS-Schicht überprüft wird. Vom ersten Moment an, in dem Sie Hallo sagen, können Dritte nicht verstehen, wovon Sie sprechen und in welcher Phase Sie sich befinden. Dazu wird die erste Kommunikation mit einem oder mehreren vorgegebenen Schlüsseln gestartet und bei Bedarf werden diese Schlüssel in regelmäßigen Abständen erneuert. So wird auch bis zur Erstellung des ersten Pre-Keys in der TLS-Schicht die Vertraulichkeit nicht kompromittiert und Unbefugte nicht umsonst erstellt.

### Prozess, der in der Datenschicht ausgeführt wird

Wenn dieser gesamte Vorgang erfolgreich abgeschlossen und der Datenkanal bestanden wurde, sind Sie nun beim besten Teil der Arbeit angelangt. Die Daten werden mit der AES-Verschlüsselungsmethode verschlüsselt. Während der Verschlüsselung werden die Tabellen gemäß dem CBC-GCM-Zählermodus gemäß Ihrer Auswahl gemischt, und bei diesem Vorgang wird gemäß Ihrer Auswahl ein 128- oder 256-Bit-Verschlüsselungsschlüssel verwendet. Unabhängig von Ihrer Wahl beträgt die Verschlüsselungsblocklänge natürlich 128 Bit. Nur die Länge des Verschlüsselungsschlüssels ändert sich. AES-256-GCM, das ich für diese Erklärung ausgewählt habe, ist ein AEAD-Verschlüsselungstyp. Es fasst die von ihm gesendeten Daten unabhängig von anderen Kanälen und Prozessen zu einem bestimmten Zeitpunkt zusammen und versendet sie zusammen mit der Zusammenfassung. Somit werden Authentifizierungs- und Verschlüsselungsfunktionen in AEAD erfüllt, was für `Authentication Encryption with Associated Data` steht. Es gibt ein Problem, das hier eine Unterscheidung erfordert. In welcher Phase und in welcher Reihenfolge werden wir die Verschlüsselungs- und Hash-Algorithmen verwenden?

||||
|:---:|:---:|:---:|
| ![Encrypt-then-MAC (EtM)](/images/openvpn-full/EtM.png) | ![Encrypt-and-MAC (E-and-M)](/images/openvpn-full/EaM.png) | ![MAC-then-Encrypt (MtE)](/images/openvpn-full/MtE.png) |

> https://en.wikipedia.org/wiki/Authenticated_encryption (Datum: 08.04.2023)

- Laut EtM, dem ersten Ansatz, werden die Daten zuerst verschlüsselt, dann als Ergebnis des Digests mit einem anderen Schlüssel verschlüsselt und das resultierende Ergebnis in Blöcken zusammen gesendet. Wenn wir uns reale Lösungen ansehen, die es verwenden, fällt uns zuerst das IPSec-Protokoll ein. Dies ist die einzige Methode, die die höchste Sicherheitsdefinition in AE erreichen kann, dies kann jedoch nur erreicht werden, wenn der verwendete MAC-Algorithmus frei von Korruption ist oder noch nicht geknackt wurde. Für SSHv2 sind auch verschiedene EtM-Cipher-Suites verfügbar. Beachten Sie jedoch, dass für Daten und Digest eine Schlüsseltrennung zwingend erforderlich ist (für Verschlüsselung und Schlüssel-Hashing müssen unterschiedliche Schlüssel verwendet werden), da Sie sonst je nach verwendeter Verschlüsselungsmethode und Hash-Funktion möglicherweise ein unsicheres Ergebnis erhalten.

- Gemäß dem zweiten Ansatz, E&M, werden Klartextdaten verschlüsselt und daneben wird eine Zusammenfassung des Verschlüsselungszustands der Klartextdaten hinzugefügt. Obwohl hier nur ein Schlüssel verwendet wird, zeigt die Tatsache, dass zwei unterschiedliche Ergebnisse (Verschlüsselungsergebnis und Digest-Ergebnis) für dieselben Daten vorliegen, deutlich, dass die Sicherheit nicht gut genug ist. Als reale Lösung mit diesem System können wir die ersten Versionen von SSH als Beispiel anführen. Um dies zu verbessern, wurden Methoden wie das Verschlüsseln der gesendeten Zusammenfassungsdatei mit demselben Schlüssel ausprobiert.

- Laut MtE, dem dritten und letzten mir bekannten Ansatz, wird eine Zusammenfassungsdatei auf Basis von Klartext generiert. Dann werden der Klartext und die Digest-Datei zusammen mit dem Schlüssel verschlüsselt. Der Chiffretext und die Chiffretextdatei werden zusammen gesendet. Wenn wir uns reale Lösungen ansehen, die es verwenden, sind das in erster Linie SSL/TLS-Implementierungen. Wir alle wissen, wie zuverlässig und nachhaltig SSL/TLS-Anwendungen an sich sind. Darüber hinaus wurden im Laufe der Jahre Verbesserungen wie `MAC-then-pad-then-encrypt` vorgenommen, um die Sicherheit zu erhöhen. Gemäß dieser Verbesserung wird zuerst der Klartext verdaut, dann auf die Blockgröße aufgefüllt und dann die Verschlüsselung durchgeführt. Dies führt zu einem noch zuverlässigeren Verschlüsselungsergebnis. Aber es gibt Fälle, in denen der Padding-Mechanismus Angriffe wie Padding Oracle verursacht, wenn er bestimmte Fehler macht.

![https://community.openvpn.net/openvpn/wiki/Gigabit_Networks_Linux (Datum: 08.04.2023)](/images/openvpn-full/TAP-TUN.png)

Nach der Auswahl des zu verwendenden AEAD-Ansatzes wird je nach Verwendung von TAP oder TUN der in der obigen Grafik dargestellte Weg beschritten. Gemäß diesem Pfad geht die im Benutzerbereich ausgeführte/gewünschte Aktion zu den TAP/TUN-Adaptern auf Kernel-Ebene. Da sich diese Adapter auf der Kernel-Ebene befinden, arbeiten sie sehr schnell. Dann führen die virtuellen Adapter die notwendige Verschlüsselung mit der entsprechenden Bibliothek durch, fügen ggf. den Digest hinzu und stellen die Paketgröße ein. Dann sendet der Server Pakete sequentiell über die Ethernet-Schnittstelle an die Ethernet-Schnittstelle des Clients. Der Client, der es erhält, konfiguriert die Pakete neu, organisiert sie, kombiniert sie bei Bedarf und entschlüsselt sie mit den erforderlichen Bibliotheken. Nach der Entschlüsselung überträgt es den Client über den virtuellen Adapter an den Endbenutzer. Als Ergebnis all dieser mathematischen Operationen und Bemühungen erreichte der Benutzer also nach einigen Zyklen den gewünschten Inhalt. Es ist ziemlich lang zu erklären, aber sehr einfach zu bedienen, liebe Leser. Sie müssen nur die entsprechende [Skriptseite](https://github.com/wiseweb-works/openvpn-most-secure-install/) auf meiner GitHub-Seite besuchen. Das zugehörige Skript nimmt all diese Anpassungen interaktiv für Sie vor. Alles, was Sie tun müssen, ist sich zurückzulehnen und zu genießen.

# FAQ und Ende

Erhielt mich per [Mail](mailto:wisewebworks@outlook.com), [Fosstodon](https://fosstodon.org/@wise) oder [GitHub](https://github.com/wiseweb-works) I werde versuchen, hier von Zeit zu Zeit Fragen hinzuzufügen. So kommen Sie historisch gesehen direkt zum Ergebnis, ohne darüber nachzudenken, welche Art von Fragen zu welchem ​​Zeitpunkt aufgetreten sind oder ob es eine Lösung gibt. Abgesehen davon, wenn es Fragen gibt, die einer zusätzlichen Erklärung bedürfen, ohne das technische Dokument zu ändern, denke ich, sie in diesen Abschnitt aufzunehmen.

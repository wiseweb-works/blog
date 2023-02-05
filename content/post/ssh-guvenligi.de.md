---
title: "Erhöhung der SSH-Sicherheit auf Linux-Servern"
date: 2021-10-05
tags: ["linux", "ssh", "sicherheit", "audit"]
author: "Wise"
draft: false
---
# Erhöhung der SSH-Sicherheit auf Linux-Servern

Heute werde ich darüber sprechen, wie wir den SSH-Dienst, der es uns ermöglicht, eine sichere Verbindung herzustellen, wenn Sie einen Server mieten, sicherer und fern von böswilligen Anfragen von außen halten können. In erster Linie werde ich den Prozess unter drei verschiedenen Überschriften beschreiben: einfach, empfohlen und fortgeschritten. Titelinhalte werden sukzessive nach persönlichen Anforderungen berücksichtigt. Obwohl die Titel miteinander verknüpft sind, ist es kein Problem, sie in einem gewünschten Stadium zu belassen.

## Einfache Sicherheitsvorkehrungen

Zuerst müssen wir die Updates über die Konsole mit dem Paketmanager der Linux-Version installieren, in der wir uns befinden.

```
Ubuntu: sudo apt update && sudo apt upgrade -y

Fedora: sudo yum update -y

Arch-Linux: sudo pacman -Syyu
```

Nachdem die Updates installiert sind, beginnen wir mit der Konfiguration des SSHD-Dienstes (das ist der Dienst, der es Ihnen ermöglicht, SSH-Verbindungen von außen zu empfangen) auf Ihrem Server (in meinem Fall Ubuntu). Vor allem sei aufgrund der Verwirrung angemerkt, dass es sich bei dem ssh-Dienst und dem SSHD-Dienst um unterschiedliche Dienste handelt. Während der SSH-Dienst der Dienst ist, der den Server mit anderen Computern oder Servern verbindet, ist der SSHD-Dienst der Dienst, der es anderen Computern ermöglicht, sich mit Ihrem Server zu verbinden, d. h. eingehende Verbindungen.

Die Datei, die die Einstellungen des SSHD-Dienstes enthält, befindet sich im Allgemeinen unter „/etc/ssh/sshd_config“. Wir müssen es mit einem der von uns verwendeten Texteditoren öffnen, jedoch mit einem Benutzer mit sudo-Rechten (dh Administratorrechten).

Wenn wir weitermachen, Ubuntu

```
sudo nano /etc/ssh/sshd_config # Befehl zum Öffnen der Einstellungsdatei
```

Titel werden wir finden und ersetzen

```
#ClientAliveInterval 0 >> Wir entfernen den Hashtag am Anfang und ändern den Wert von „0“ auf 300. Dieser Befehl regelt das automatische Schließen der Verbindung nach wie vielen Sekunden, wenn die Verbindung nicht genutzt wird (sie bleibt im Ruhezustand) .
#PermitEmptyPasswords nein >> Wir entfernen das Hashtag am Anfang und ändern den "nein"-Wert nicht. Dieser Befehl verhindert, dass leere oder unverschlüsselte Benutzer eine Verbindung herstellen. Es erlaubt also keine leeren Passwörter.
X11Forwarding yes >> Wenn am Anfang ein Hashtag steht, entfernen wir es, andernfalls ändern wir den „yes“-Wert auf no. Obwohl dieser Befehl das Ausführen von Anwendungen mit GUI-Schnittstelle auf dem Server erleichtert, schließen wir ihn, da er missbraucht werden kann.
#MaxAuthTries 6 >> Wir entfernen den Hashtag am Anfang und ändern den Wert von „6“ auf 3. Dieser Befehl regelt, wie oft sie Ihr Passwort versuchen dürfen. Die Zahl 6 ist ein unnötig hoher Wert und die allgemeine Akzeptanz liegt bei 3 oder 4.
#Protokol 2 >> Wenn am Anfang ein Hashtag steht, entfernen wir es, wenn es keinen solchen Eintrag gibt, fügen wir den Wert in Form von „Protokol 2“ an das erste Leerzeichen an, das wir finden. Dieser Befehl stellt sicher, dass Verbindungen nur mit dem neuesten SSH-Protokoll hergestellt werden, und schützt Sie so vor Schwachstellen des alten Protokolls.
```

Wenn Sie nach dem Vornehmen der Einstellungen überprüfen möchten: Sie können den Befehl „sudo sshd -t“ verwenden. Wenn Sie keine Fehlermeldung sehen, können Sie die Einstellungen übernehmen und den Dienst mit dem Befehl "sudo systemctl restart sshd" oder "sudo service sshd restart" neu starten.

## Empfohlene Einstellungen

Zusätzlich zu den vorherigen Einstellungen werden wir zusätzliche Einstellungen vornehmen, wie z. B. benutzerbasierte Anmeldung, Anmeldung nur mit sicherem Schlüssel und Einschränkung der Anmeldung mit Root-Benutzer. Zuerst müssen Sie einen geheimen Schlüssel für Ihren Benutzer auf Ihrem lokalen Computer generieren. Dazu, wenn Sie ein Linux-basiertes Betriebssystem verwenden

```
Nachdem Sie ssh-keygen -t rsa -b 4096 # eingegeben haben, geben Sie das gewünschte Passwort als Antwort auf die folgenden Fragen ein
>>>> Passphrase eingeben (leer für keine Passphrase): [Entertaste drücken]
>>>> Gleiche Passphrase erneut eingeben: [Entertaste drücken]

oder

ssh-keygen -t ecdsa -b 521 # Erzeugt einen sichereren und schnelleren Ekliptikschlüssel. Geben Sie das gewünschte Passwort als Antwort auf die folgenden Fragen ein
>>>> Passphrase eingeben (leer für keine Passphrase): [Entertaste drücken]
>>>> Gleiche Passphrase erneut eingeben: [Entertaste drücken]
```

Wenn Sie andere Fragen mit der Eingabetaste beantwortet und keinen bestimmten Speicherort für den Schlüssel eingegeben haben, wird Ihr Schlüsselpaar (.pub=öffentlicher Schlüssel) / privater Schlüssel ohne Erweiterung) im Ordner /home/USER_NAME/.ssh gespeichert. Sie können den folgenden Code verwenden, um den von Ihnen generierten Schlüssel auf Ihrem Server einzuführen.

```
ssh-copy-id -i ~/.ssh/KEY_NAME.pub Nachdem Sie USER_NAME@SERVER_IP_ADRESS # auf dem SERVER eingegeben haben, werden Sie nach dem Passwort Ihres Benutzers gefragt und wenn Sie es richtig eingeben, sehen Sie eine Bestätigungsnachricht.
```

Nachdem Sie Ihren eigenen Schlüssel generiert haben, greifen Sie nur mit dem Schlüssel auf den Server zu;
Titel werden wir finden und ersetzen

```
#PasswordAuthentication no >> Wir entfernen den Hashtag am Anfang und belassen den „no“-Wert auf no. Dieser Befehl verhindert, dass eine Verbindung zum Server mit einem Kennwort hergestellt wird. Benutzer können sich nur mit ssh_keys verbinden.
PubkeyAuthentication yes >> Falls ja, entfernen wir das Rautezeichen am Anfang und setzen den Wert auf yes. Dieser Befehl ist der Befehl, mit dem Sie sich mit dem gerade erstellten geheimen Schlüssel anmelden können.
#PermitRootLogin no >> Wir entfernen den Hashtag am Anfang und belassen den „no“-Wert auf no. Dieser Befehl verhindert, dass der ROOT-Benutzer, der am stärksten autorisierte Benutzer ist, auf den Server zugreift. Sie können jedoch mit einem anderen Benutzer darauf zugreifen und dann zum ROOT-Benutzer wechseln.
AllowUsers IHR BENUTZERNAME >> Falls noch nicht vorhanden, fügen wir einen solchen Eintrag hinzu. Es hindert andere Benutzer daran, sich mit dem Server zu verbinden und/oder einen Benutzernamen zu versuchen.
LoginGraceTime 20 >> Wenn nicht, fügen wir einen solchen Datensatz hinzu und setzen den Wert auf 20. Bestimmt die für die Anmeldung erforderliche Zeit in Sekunden. Wir verhindern, dass sie den Server mit einer langen Anmeldeanfrage beschäftigen.
ChallengeResponseAuthentication no >> Falls ja, entfernen wir das Rautezeichen am Anfang und setzen den Wert auf no. Dieser Befehl deaktiviert andere Anmeldemethoden.
KerberosAuthentication no >> Falls ja, entfernen wir das Rautezeichen am Anfang und setzen den Wert auf no. Dieser Befehl deaktiviert andere Anmeldemethoden.
GSSAPIAuthentication no >> Falls ja, entfernen wir das Rautezeichen am Anfang und setzen den Wert auf no. Dieser Befehl deaktiviert andere Anmeldemethoden.
```

Wenn Sie nach dem Vornehmen der Einstellungen überprüfen möchten: Sie können den Befehl „sudo sshd -t“ verwenden. Wenn Sie keine Fehlermeldung sehen, können Sie die Einstellungen übernehmen und den Dienst mit dem Befehl "sudo systemctl restart sshd" oder "sudo service sshd restart" neu starten.

## Erweiterte Einstellungen

Zunächst werden wir den Standard-SSH-Verbindungsport 22 ändern und dann die während der SSH-Verbindung verwendeten Verschlüsselungstools noch stärker machen. Anschließend legen wir fest, wie die Protokolle (Registrierungsdatensätze) des Server-SSH-Dienstes geführt werden.

Titel werden wir finden und ersetzen

```
#Port 22 >> Wir finden das und entfernen das Rautezeichen am Anfang und schreiben dort eine Portnummer, die von keinem anderen Dienst verwendet wird und offen ist. Zum Beispiel könnte es 2992 sein.
LogLevel INFO >> Wir finden diese Einstellung und ändern sie, wenn nicht, fügen wir sie hinzu. Es hilft uns, den Aufnahmepegel einzustellen.
AllowAgentForwarding no >> Wir finden diese Einstellung und ändern sie, wenn nicht, fügen wir sie hinzu. Deaktiviert alternative Routing-Methoden.
AllowTcpForwarding no >> Wir finden diese Einstellung und ändern sie, wenn nicht, fügen wir sie hinzu. Deaktiviert alternative Routing-Methoden.
PrintMotd nein >> Wir finden diese Einstellung und ändern sie, wenn nicht, fügen wir sie hinzu. Um zu verhindern, dass sie etwas über den Server erfahren, bevor die Verbindung hergestellt wird.
PermitUserEnvironment nein >> Wir finden diese Einstellung und ändern sie, wenn nicht, fügen wir sie hinzu.
PermitTunnel no >> Wir finden diese Einstellung und ändern sie, wenn nicht, fügen wir sie hinzu.
```

Wir gehen noch weiter und bestimmen, welcher der Verschlüsselungsalgorithmen, Authentifizierungsalgorithmen, Schlüsselaustauschalgorithmen und anderen Algorithmen, die während der SSH-Verbindung verwendet werden, der Standard sein sollte.

Wir führen die folgenden Befehle jeweils mit einem Root-Benutzer oder einem Benutzer mit sudo-Berechtigung aus.

```
1] rm /etc/ssh/ssh_host_*
2] ssh-keygen -t rsa -b 4096 -f /etc/ssh/ssh_host_rsa_key -N ""
3] ssh-keygen -t ed25519 -f /etc/ssh/ssh_host_ed25519_key -N ""
4] awk '$5 >= 3072' /etc/ssh/moduli > /etc/ssh/moduli.tmp
5] mv /etc/ssh/moduli.tmp /etc/ssh/moduli
6] sed -i 's/^\#HostKey \/etc\/ssh\/ssh_host_\(rsa\|ed25519\)_key$/HostKey \/etc\/ssh\/ssh_host_\1_key/g' /etc/ ssh/sshd_config
7] echo -e "KexAlgorithms curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha256\nCiphers chacha20-poly1305@ openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr\nMACs hmac-sha2-256-etm@openssh.com,hmac-sha2-512 -etm@openssh.com,umac-128-etm@openssh.com\nHostKeyAlgorithms ssh-ed25519,ssh-ed25519-cert-v01@openssh.com,sk-ssh-ed25519@openssh.com,sk-ssh-ed25519- cert-v01@openssh.com,rsa-sha2-256,rsa-sha2-512,rsa-sha2-256-cert-v01@openssh.com,rsa-sha2-512-cert-v01@openssh.com" > / etc/ssh/sshd_config.d/ssh-hardening.conf
```

Nachdem Sie die Einstellungen vorgenommen haben, übernehmen Sie die Einstellungen mit dem Befehl „sudo sshd -t“ und dann, wenn Sie keine Fehlermeldung sehen, „sudo service sshd restart“ und starten Sie den Dienst neu. Jetzt können Sie sich über den gerade definierten Port und nur mit Ihrem privaten Schlüssel mit dem Server verbinden. Verbinden;

```
Sie können den Befehl ssh -i ~/.ssh/KEY_NAME USER_NAME@SERVER_IP_ADRESS -p PORT_NUMBER # verwenden. Sie können fortfahren, indem Sie bei den eingehenden Benachrichtigungen die Eingabetaste sagen.
```

# Ende

Dieser Artikel wurde zuvor unter <https://teknolojirehberleri.xyz> veröffentlicht. Um ein persönliches Portfolio zu erstellen, hatte ich das Bedürfnis, es auf meiner persönlichen Website erneut zu veröffentlichen.

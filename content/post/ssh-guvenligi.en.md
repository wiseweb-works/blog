---
title: "Increasing SSH security on Linux Servers"
date: 2021-10-05
tags: ["linux", "ssh", "security", "audit"]
author: "Wise"
draft: false
---
# Increasing SSH security on Linux Servers

Today, I will talk about how we can keep the SSH service, which allows us to connect securely when you rent a server, more secure and away from malicious requests from outside. Foremost, I will describe the process under three different headings: simple, recommended and advanced. Title contents are gradually considered according to personal requirements. Although the titles are linked to each other, leaving them at a desired stage will not pose a problem.

## Simple Safety Precautions

First, we need to install the updates via the console with the package manager of the Linux version we are in.
```
Ubuntu: sudo apt update && sudo apt upgrade -y

Fedora: sudo yum update -y

Arch Linux: sudo pacman -Syyu
```

After the updates are installed, we start configuring the SSHD service (which is the service that allows you to receive ssh connections from outside) on your server (Ubuntu in my case). Foremost, due to the confusion, it should be noted that the ssh service and the SSHD service are different services. While the SSH service is the service that connects the server to other computers or servers, the SSHD service is the service that allows other computers to connect to your server, that is, incoming connections.

The file that keeps the settings of the SSHD service is generally located at “/etc/ssh/sshd_config”. We need to open it with any of the text editors we use, but with a user with sudo (ie administrator) privileges.

If we continue on, Ubuntu

```
sudo nano /etc/ssh/sshd_config # Command to open the settings file
```
Titles we will find and replace

```
#ClientAliveInterval 0 >> We remove the hashtag at the beginning and change the value from "0" to 300. This command regulates the automatic closing of the connection after how many seconds when the connection is not used (it stays in the idle position).
#PermitEmptyPasswords no >> We remove the hashtag at the beginning and do not change the "no" value. This command prevents blank or unencrypted users from connecting. So it doesn't allow empty passwords.
X11Forwarding yes >> If there is a hashtag at the beginning, we remove it, otherwise we change the "yes" value to no. Although this command provides ease of running applications with GUI interface on the server, we close it because it is possible to abuse it.
#MaxAuthTries 6 >> We remove the hashtag at the beginning and change the value from "6" to 3. This command regulates how many times they can try your password. The number 6 is an unnecessarily high value and the general acceptance is 3 or 4.
#Protocol 2 >> If there is a hashtag at the beginning, we remove it, if there is no such record, we add the value in the form of "Protocol 2" to the first space we find. This command ensures that connections are made only with the latest SSH protocol, protecting you from vulnerabilities of the old protocol.
```
If you want to check after making the settings: You can use the command "sudo sshd -t". If you do not see an error message, you can apply the settings and restart the service with the command "sudo systemctl restart sshd" or "sudo service sshd restart"

## Recommended settings

In addition to the previous settings, we will make additional settings such as user-based login, login using only secure key and restrict login with Root user. First you need to generate a secret key for your user on your local computer. For this, if you are using a linux-based operating system

```
After typing ssh-keygen -t rsa -b 4096 #, enter the password you want in response to the questions below
>>>> Enter passphrase (empty for no passphrase): [Press enter key]
>>>> Enter same passphrase again: [Press enter key]

or

ssh-keygen -t ecdsa -b 521 # Generates more secure and faster ecliptic key. Enter the password you want in response to the questions below
>>>> Enter passphrase (empty for no passphrase): [Press enter key]
>>>> Enter same passphrase again: [Press enter key]
```

If you answered other questions by pressing Enter and did not type a specific location for the key, your key pair (.pub= public key) / private key without extension) is saved in the /home/USER_NAME/.ssh folder. You can use the code below to introduce the key you generated to your server.

```
ssh-copy-id -i ~/.ssh/KEY_NAME.pub After typing USER_NAME@SERVER_IP_ADRESS # on the SERVER, it will ask for your user's password and if you enter it correctly, you will see a confirmation message.
```

After generating your own key, to access the server only with the key;
Titles we will find and replace

```
#PasswordAuthentication no >> We remove the hashtag at the beginning and leave the "no" value as no. This command prevents connecting to the server with a password. Users can only connect with ssh_keys.
PubkeyAuthentication yes >> If there is, we remove the hash mark at the beginning and set the value as yes. This command is the command that allows you to log in with the secret key you just created.
#PermitRootLogin no >> We remove the hashtag at the beginning and leave the "no" value as no. This command prevents the ROOT user, who is the most authorized user, from accessing the server. However, you can access it with another user and then switch to the ROOT user.
AllowUsers YOUR USERNAME >> If it doesn't exist, we add such a record. It prevents other users from connecting to the server and/or attempting a username.
LoginGraceTime 20 >> If not, we add such a record and set the value to 20. Determines the time required to log in, in seconds. We prevent them from keeping the server busy with a long login request.
ChallengeResponseAuthentication no >> If there is, we remove the hash mark at the beginning and set the value as no. This command disables other login methods.
KerberosAuthentication no >> If there is, we remove the hash mark at the beginning and set the value as no. This command disables other login methods.
GSSAPIAuthentication no >> If there is, we remove the hash mark at the beginning and set the value as no. This command disables other login methods.
```

If you want to check after making the settings: You can use the command "sudo sshd -t". If you do not see an error message, you can apply the settings and restart the service with the command "sudo systemctl restart sshd" or "sudo service sshd restart"

## Advanced Settings

First of all, we will change the standard SSH connection port, 22, and then make the encryption tools used during SSH connection even stronger. Then we will determine how the logs (registry records) of the server SSH service are kept.

Titles we will find and replace

```
#Port 22 >> We find this and remove the hash mark at the beginning and write there a port number that is not used by any other service and is open. For example, it could be 2992.
LogLevel INFO >> We find this setting and change it, if not, we add it. It helps us to set the recording level.
AllowAgentForwarding no >> We find this setting and change it, if not, we add it. Disables alternative routing methods.
AllowTcpForwarding no >> We find this setting and change it, if not, we add it. Disables alternative routing methods.
PrintMotd no >> We find this setting and change it, if not, we add it. To prevent them from knowing about the server before the connection is established.
PermitUserEnvironment no >> We find this setting and change it, if not, we add it.
PermitTunnel no >> We find this setting and change it, if not, we add it.
```

We go further and determine which of the encryption algorithms, authentication algorithms, key exchange algorithms and other algorithms used during the SSH connection should be the default.

We run the following commands respectively with a root user or a user with sudo authority.

```
1] rm /etc/ssh/ssh_host_*
2] ssh-keygen -t rsa -b 4096 -f /etc/ssh/ssh_host_rsa_key -N ""
3] ssh-keygen -t ed25519 -f /etc/ssh/ssh_host_ed25519_key -N ""
4] awk '$5 >= 3072' /etc/ssh/moduli > /etc/ssh/moduli.tmp
5] mv /etc/ssh/moduli.tmp /etc/ssh/moduli
6] sed -i 's/^\#HostKey \/etc\/ssh\/ssh_host_\(rsa\|ed25519\)_key$/HostKey \/etc\/ssh\/ssh_host_\1_key/g' /etc/ssh/sshd_config
7] echo -e "KexAlgorithms curve25519-sha256,curve25519-sha256@libssh.org,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group-exchange-sha256\nCiphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr\nMACs hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,umac-128-etm@openssh.com\nHostKeyAlgorithms ssh-ed25519,ssh-ed25519-cert-v01@openssh.com,sk-ssh-ed25519@openssh.com,sk-ssh-ed25519-cert-v01@openssh.com,rsa-sha2-256,rsa-sha2-512,rsa-sha2-256-cert-v01@openssh.com,rsa-sha2-512-cert-v01@openssh.com" > /etc/ssh/sshd_config.d/ssh-hardening.conf
```

After making the settings, apply the settings with the command "sudo sshd -t" and then, if you do not see an error message, "sudo service sshd restart" and restart the service. Now you will be able to connect to the server via the port you have just defined and only with your private key. To connect;
```
You can use the command ssh -i ~/.ssh/KEY_NAME USER_NAME@SERVER_IP_ADRESS -p PORT_NUMBER #. You can continue by saying Enter to the incoming notifications.
```

# End

This article was previously published at https://teknolojirehberleri.xyz. In order to create a personal portfolio, I felt the need to republish it on my personal site.

---
title: "Skapa ett SSH-nyckelpar för virtuella Linux-datorer på Azure | Microsoft Docs"
description: "Skapa säkert ett SSH-nyckelpar med en offentlig och en privat nyckel för virtuella Linux-datorer i Azure."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: rasquill
experiment_id: rasquill-ssh-20170308
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19acd4efca7ef043f31b436b96f9129caee9591b
ms.lasthandoff: 04/03/2017



---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>Skapa ett SSH-nyckelpar med en offentlig och en privat nyckel för virtuella Linux-datorer

I den här artikeln visas hur du kan skapa ett RSA-nyckelfilpar med SSH-protokollversion 2 med en offentlig och en privat nyckel för användning med virtuella Linux-datorer.  Med ett SSH-nyckelpar kan du skapa virtuella datorer i Azure som använder SSH-nycklar för autentisering som standard, vilket gör att inga lösenord krävs för att logga in.  Det går att gissa sig fram till lösenord och dina virtuella datorer blir sårbara för råstyrkeattacker som försöker gissa ditt lösenord. Virtuella datorer som skapas med Azure-mallar eller `azure-cli` kan inkludera din offentliga SSH-nyckel som en del av distributionen, så att inga postdistributionskonfigurationssteg krävs för att inaktivera lösenordsinloggningar för SSH.

## <a name="quick-commands"></a>Snabbkommandon

Kör följande kommandon från ett Bash-skal, och byt ut exemplen mot dina egna alternativ.

Den offentliga SSH-nyckelfilen skapas som standard på `~/.ssh/id_rsa.pub`. När du får en uppmaning med följande kommande ska du skapa en "lösenfras" för att skydda den privata nyckeln. (Lösenfrasen är ett lösenord som används för att kryptera den privata nyckeln.)

```bash
ssh-keygen -t rsa -b 2048 
```

Lägg till den nyligen skapade nyckeln till `ssh-agent`:

```bash
ssh-add ~/.ssh/id_rsa
```

> [!IMPORTANT] 
> Kommandona ovan fungerar i nästan alla typer av Linux-operativsystem, men inte nödvändigtvis i behållare eftersom miljön kan vara kraftigt begränsad. 

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

Användningen av offentliga och privata SSH-nycklar är det enklaste sättet att logga in på dina Linux-servrar. [Kryptografi med offentliga nycklar](https://en.wikipedia.org/wiki/Public-key_cryptography) är ett säkrare sätt att logga in på en Linux- eller BSD-baserad virtuell dator i Azure än lösenord, som är mer sårbara för råstyrkeattacker.

> [!IMPORTANT]
> Din offentliga nyckel kan delas med alla, men bara du (eller din lokala säkerhetsinfrastruktur) har tillgång till den privata nyckeln.  Den privata SSH-nyckeln bör ha ett [mycket säkert lösenord ](https://www.xkcd.com/936/) (källa:[xkcd.com](https://xkcd.com)) som skyddar den.  Det här lösenordet används bara för att få åtkomst till den privata SSH-nyckeln och **är inte** lösenordet för användarkontot.  När du lägger till ett lösenord för SSH-nyckeln krypteras den privata nyckeln med hjälp av 128-bitars AES, så att den inte kan användas utan lösenordet som krävs för att kryptera upp den.  Om en angripare stjäl din privata nyckel och om nyckeln inte skyddas med ett lösenord, kan angriparen använda den privata nyckeln för att logga in på alla servrar som den offentliga nyckeln används för.  Om den privata nyckeln är lösenordsskyddad kan den inte användas av angriparen, vilket ger infrastrukturen i Azure ett extra säkerhetslager.

Den här artikeln skapar offentliga och privata nyckelfiler med SSH-protokollversion 2, vilket rekommenderas för distributioner i Resource Manager.  *ssh-rsa*-nycklar krävs på [portalen](https://portal.azure.com) för både klassiska distributioner och Resource Manager-distributioner.

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>Inaktivera SSH-lösenord genom att använda SSH-nycklar

Azure kräver offentliga och privata nycklar med minst 2048 bitar i ssh-rsa-format. Skapa nycklarna med `ssh-keygen`, som ställer ett antal frågor och sedan skriver en privat nyckel och en matchande offentlig nyckel. När en virtuell Azure-dator skapas kopieras den offentliga nyckeln till `~/.ssh/authorized_keys`.  SSH-nycklar i `~/.ssh/authorized_keys` används för att tvinga klienten att matcha motsvarande privata nyckel vid en SSH-inloggningsanslutning.  När en virtuell Azure Linux-dator har skapats med hjälp av SSH-nycklar för autentisering, så konfigurerar Azure SSHD-servern för att den inte tillåter lösenordsinloggningar, utan enbart SSH-nycklar.  Därför kan du genom att skapa virtuella datorer i Azure Linux med SSH-nycklar skydda VM-distributionen och bespara dig vanliga konfigurationssteg efter distribution för inaktiveringen av lösenord i konfigurationsfilen sshd_config.

## <a name="using-ssh-keygen"></a>Använda ssh-keygen

Med det här kommandot skapas ett lösenordsskyddat (krypterat) SSH-nyckelpar med 2048 bitars RSA. Kommentarer har lagts till som gör det lättare att identifiera det.  

SSH-nycklarna sparas som standard i `~/.ssh`-katalogen.  Om du inte har någon `~/.ssh`-katalog skapar `ssh-keygen`-kommandot en åt dig med rätt behörigheter.

```bash
ssh-keygen \
-t rsa \
-b 2048 
```

*Kommandot förklarat*

`ssh-keygen` = det program som används för att skapa nycklarna

`-t rsa` = typen av nyckel som ska skapas som är RSA-formatet [wikipedia](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = bitar i nyckeln


## <a name="classic-portal-and-x509-certs"></a>Klassisk portal och X.509-certifikat

Om du använder Azure [klassiska portal](https://manage.windowsazure.com/), krävs X.509-certifikat för SSH-nycklarna.  Inga andra typer av offentliga SSH-nycklar tillåts, de *måste* vara X.509-certifikat.

Skapa ett X.509-certifikat från din befintliga privata SSH-RSA-nyckel:

```bash
openssl req -x509 \
-key ~/.ssh/id_rsa \
-nodes \
-days 365 \
-newkey rsa:2048 \
-out ~/.ssh/id_rsa.pem
```

## <a name="classic-deploy-using-asm"></a>Klassisk distribution med hjälp av `asm`

Om du använder den klassiska distributionsmodellen (Azure service management CLI `asm`), kan du använda en offentlig SSH-RSA-nyckel eller en RFC4716-formaterad nyckel i en **.pem**-behållare.  Den offentliga nyckeln för SSH-RSA är den som har skapats tidigare i den här artikeln med hjälp av `ssh-keygen`.

Så här skapar du en RFC4716-formaterad nyckel från en befintlig offentlig SSH-nyckel:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Exempel på ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ahmet/.ssh/id_rsa.
Your public key has been saved in /home/ahmet/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Sparade nyckelfiler:

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): ~/.ssh/id_rsa`

Namnet på nyckelparet i den här artikeln.  Ett nyckelpar med namnet **id_rsa** är standard och vissa verktyg kan förvänta sig att namnet på filen för den privata nyckeln är **id_rsa**. Därför är det en bra idé att använda det. Katalogen `~/.ssh/` är standardplatsen för SSH-nyckelpar och SSH-konfigurationsfilen.  Om detta inte anges med en fullständig sökväg, skapar `ssh-keygen` nycklarna i den aktuella arbetskatalogen, inte standard `~/.ssh`.

En lista över `~/.ssh`-katalogen.

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```

Nyckellösenord:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` hänvisar till ett lösenord som används för att kryptera den privata nyckeln som "en lösenfras".  Vi rekommenderar *starkt* att du skapar en lösenfras för dina nyckelpar. Utan en lösenfras som skyddar den privata nyckeln kan vem som helst som har tillgång till nyckelfilen använda den för att logga in på en server som har motsvarande offentliga nyckel. Genom att lägga till en lösenfras får du bättre skydd om någon lyckas få åtkomst till den privata nyckelfilen, så att du har tid att ändra de nycklar som används för att autentisera dig.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Lagra lösenordet för din privata nyckel med hjälp av ssh-agent

Om du inte vill skriva lösenfrasen för den privata nyckelfilen vid varje SSH-inloggning kan du cachelagra lösenordet med hjälp av `ssh-agent`. Om du använder en Mac skyddas lösenorden för privata nycklar i OSX-nyckelringen när du anropar `ssh-agent`.

Kontrollera och använd `ssh-agent` och `ssh-add` för att informera SSH-systemet om nyckelfiler så att lösenfrasen inte behöver användas interaktivt.

```bash
eval "$(ssh-agent -s)"
```

Lägg nu till den privata nyckeln i `ssh-agent` med hjälp av kommandot `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Nu lagras lösenordet för den privata nyckeln i `ssh-agent`.

## <a name="using-ssh-copy-id-to-install-the-new-key"></a>Använd `ssh-copy-id` för att installera den nya nyckeln
Om du redan har skapat en virtuell dator kan du installera den nya offentliga SSH-nyckeln på den virtuella Linux-datorn med följande kommande, vilket ersätter den virtuella datorns användarnamn och serveradress med dina egna värden:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Skapa och konfigurera en SSH-konfigurationsfil

Som bästa praxis bör du skapa och konfigurera en `~/.ssh/config`-fil för att påskynda inloggningar och för att optimera SSH-klientbeteendet.

Följande exempel illustrerar en standardkonfiguration.

### <a name="create-the-file"></a>Skapa filen

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Redigera filen för att lägga till den nya SSH-konfigurationen:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>`~/.ssh/config`-exempelfil:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Den här SSH-konfigurationsfilen innehåller avsnitt för varje server så att var och en kan ha ett eget dedikerat nyckelpar. Standardinställningarna (`Host *`) är avsedda för värdar som inte matchar någon av de specifika värdarna högre upp i konfigurationsfilen.

### <a name="config-file-explained"></a>Konfigurationsfilen förklarad

`Host` = namnet på värddatorn som anropas på terminalen.  `ssh fedora22` beordrar `SSH` att använda värdena i inställningsblocket med etiketten  `Host fedora22` OBS! Värden kan vara vilken etikett som helst som är logisk för din användning och som inte representerar själva värdnamnet för en server.

`Hostname 102.160.203.241` = IP-adressen eller DNS-namnet för servern som du ansluter till.

`User ahmet` = fjärranvändarkontot som ska användas när du loggar in på servern.

`PubKeyAuthentication yes` = meddelar SSH att du vill använda en SSH-nyckel för att logga in.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = den privata SSH-nyckeln och motsvarande offentliga nyckel som ska användas för autentisering.

## <a name="ssh-into-linux-without-a-password"></a>SSH i Linux utan lösenord

Nu när du har ett SSH-nyckelpar och en konfigurerad SSH-konfigurationsfil kan du snabbt och säkert logga in på din virtuella Linux-dator. Första gången du loggar in till en server med en SSH-nyckel frågar kommandot efter lösenfrasen för nyckelfilen.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Kommandot förklarat

När `ssh fedora22` körs letar SSH först upp och läser in inställningarna från `Host fedora22`-blocket och läser sedan in de återstående inställningarna från det sista blocket, `Host *`.

## <a name="next-steps"></a>Nästa steg

Nästa uppgift är att skapa virtuella Azure Linux-datorer med den nya offentliga SSH-nyckeln.  Virtuella Azure-datorer som skapas med en offentlig SSH-nyckel för inloggning är bättre skyddade än virtuella datorer som skapas med vanliga lösenordsbaserade inloggningsmetoder.  Lösenord är som standard inaktiverade för Virtuella Azure-datorer som skapas med SSH-nycklar för att undvika råstyrkeattacker som försöker gissa lösenord. Om du behöver mer hjälp för att skapa SSH-nyckelpar eller om du behöver fler certifikat, till exempel för användning med den klassiska portalen, finns [detaljerade steg för att skapa SSH-nyckelpar och certifikat](create-ssh-keys-detailed.md).

* [Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en säker virtuell Linux-dator med hjälp av Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en säker virtuell Linux-dator med hjälp av Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


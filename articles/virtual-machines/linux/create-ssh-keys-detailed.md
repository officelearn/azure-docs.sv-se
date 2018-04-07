---
title: Detaljerade steg för att skapa ett SSH-nyckelpar för virtuella Linux-datorer i Azure | Microsoft Docs
description: Lär dig ytterligare steg för att skapa ett offentligt och privat SSH-nyckelpar för virtuella Linux-datorer i Azure, tillsammans med specifika certifikat för olika användningsfall.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 6/28/2017
ms.author: danlep
ms.openlocfilehash: 20d36f5e377f2d5af588319cee2be1808571f905
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="detailed-walk-through-to-create-an-ssh-key-pair-and-additional-certificates-for-a-linux-vm-in-azure"></a>Detaljerad genomgång för att skapa ett SSH-nyckelpar och ytterligare certifikat för en virtuell Linux-dator i Azure
Med ett SSH-nyckelpar kan du skapa virtuella datorer i Azure som använder SSH-nycklar för autentisering som standard, vilket gör att inga lösenord krävs för att logga in. Det går att gissa sig fram till lösenord och dina virtuella datorer blir sårbara för råstyrkeattacker som försöker gissa ditt lösenord. Virtuella datorer som skapas med Azure CLI- eller Resource Manager-mallar kan omfatta din offentliga SSH-nyckel som en del av distributionen, så att inga postdistributionskonfigurationssteg krävs för att inaktivera lösenordsinloggningar för SSH. Den här artikeln innehåller detaljerade anvisningar och ytterligare exempel på genererar certifikat, såsom för användning med virtuella Linux-datorer. Om du snabbt vill skapa och använda ett SSH-nyckelpar kan du se [Skapa ett offentligt och ett privat SSH-nyckelpar för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md).

## <a name="understanding-ssh-keys"></a>Förstå SSH-nycklar

Användningen av offentliga och privata SSH-nycklar är det enklaste sättet att logga in på dina Linux-servrar. [Kryptografi med offentliga nycklar](https://en.wikipedia.org/wiki/Public-key_cryptography) är ett säkrare sätt att logga in på en Linux- eller BSD-baserad virtuell dator i Azure än lösenord, som är mer sårbara för råstyrkeattacker.

Din offentliga nyckel kan delas med alla, men bara du (eller din lokala säkerhetsinfrastruktur) har tillgång till den privata nyckeln.  Den privata SSH-nyckeln bör ha ett [mycket säkert lösenord ](https://www.xkcd.com/936/) (källa:[xkcd.com](https://xkcd.com)) som skyddar den.  Det här lösenordet används bara för att få åtkomst till den privata SSH-nyckelfilen och **är inte** lösenordet för användarkontot.  När du lägger till ett lösenord för SSH-nyckeln krypteras den privata nyckeln med hjälp av 128-bitars AES, så att den inte kan användas utan lösenordet som krävs för att kryptera upp den.  Om en angripare stjäl din privata nyckel och om nyckeln inte skyddas med ett lösenord, kan angriparen använda den privata nyckeln för att logga in på alla servrar som den offentliga nyckeln används för.  Om den privata nyckeln är lösenordsskyddad kan den inte användas av angriparen, vilket ger infrastrukturen i Azure ett extra säkerhetslager.

Den här artikeln skapar ett offentligt och privat nyckelfilpar med SSH-protokollversion 2 (kallas även ”ssh-rsa”-nycklar), vilket rekommenderas för distributioner med Azure Resource Manager. *ssh-rsa*-nycklar krävs på [portalen](https://portal.azure.com) för både klassiska distributioner och Resource Manager-distributioner.

## <a name="ssh-keys-use-and-benefits"></a>Användning och fördelar med SSH-nycklar

Azure kräver offentliga och privata nycklar med minst 2 048 bitar, SSH-protokollversion 2 i RSA-format. Den offentliga nyckelfilen har behållarformatet `.pub`. Skapa nycklarna med `ssh-keygen`, som ställer ett antal frågor och sedan skriver en privat nyckel och en matchande offentlig nyckel. När en virtuell Azure-dator skapas kopierar Azure den offentliga nyckeln till mappen `~/.ssh/authorized_keys` på den virtuella datorn. SSH-nycklar i `~/.ssh/authorized_keys` används för att tvinga klienten att matcha motsvarande privata nyckel vid en SSH-inloggningsanslutning.  När en virtuell Azure Linux-dator har skapats med hjälp av SSH-nycklar för autentisering, så konfigurerar Azure SSHD-servern för att den inte tillåter lösenordsinloggningar, utan enbart SSH-nycklar.  Därför kan du genom att skapa virtuella datorer i Azure Linux med SSH-nycklar skydda VM-distributionen och slippa de vanliga konfigurationsstegen efter distribution för att inaktivera lösenord i filen **sshd_config**.

## <a name="using-ssh-keygen"></a>Använda ssh-keygen

Med det här kommandot skapas ett lösenordsskyddat (krypterat) SSH-nyckelpar med 2048 bitars RSA. Kommentarer har lagts till som gör det lättare att identifiera det.  

SSH-nycklarna sparas som standard i `~/.ssh`-katalogen.  Om du inte har någon `~/.ssh`-katalog skapar `ssh-keygen`-kommandot en åt dig med rätt behörigheter.

```bash
ssh-keygen \
    -t rsa \
    -b 2048 \
    -C "azureuser@myserver" \
    -f ~/.ssh/id_rsa \
    -N mypassword
```

*Kommandot förklarat*

`ssh-keygen` = det program som används för att skapa nycklarna

`-t rsa` = typen av nyckel ska skapas som är den [RSA-formatet](https://en.wikipedia.org/wiki/RSA_(cryptosystem)) 
 `-b 2048` = bitar i nyckeln

`-C "azureuser@myserver"` = en kommentar i slutet av filen för den offentliga nyckeln som gör det lätt att identifiera den.  Normalt används en e-postadress som kommentaren, men du kan använda det som fungerar bäst för din infrastruktur.

## <a name="classic-deploy-using-asm"></a>Klassisk distribution med hjälp av `asm`

Om du använder den klassiska distributionsmodellen (`asm` CLI-läge), du kan använda en offentlig SSH-RSA-nyckel eller en RFC4716 formaterad nyckel i en pem-behållare.  Den offentliga nyckeln för SSH-RSA är den som har skapats tidigare i den här artikeln med hjälp av `ssh-keygen`.

Så här skapar du en RFC4716-formaterad nyckel från en befintlig offentlig SSH-nyckel:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Exempel på ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
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

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Namnet på nyckelparet i den här artikeln.  Ett nyckelpar med namnet **id_rsa** är standard och vissa verktyg kan förvänta sig att namnet på filen för den privata nyckeln är **id_rsa**. Därför är det en bra idé att använda det. Katalogen `~/.ssh/` är standardplatsen för SSH-nyckelpar och SSH-konfigurationsfilen.  Om detta inte anges med en fullständig sökväg skapar `ssh-keygen` nycklarna i den aktuella arbetskatalogen, inte standarden `~/.ssh`.

En lista över `~/.ssh`-katalogen.

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

Nyckellösenord:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` kallar ett lösenord för den privata nyckeln "en lösenfras".  Vi rekommenderar *starkt* att du skapar ett lösenord för den privata nyckeln. Utan ett lösenord som skyddar nyckelfilen kan vem som helst som har tillgång till den logga in på en server som har motsvarande offentliga nyckel. Genom att lägga till ett lösenord (lösenfras) har du därför bättre skydd om någon lyckas få åtkomst till filen med din privata nyckel, så att du har tid att ändra nycklarna som används för att autentisera dig.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Lagra lösenordet för din privata nyckel med hjälp av ssh-agent

Om du inte vill skriva lösenordet för filen med den privata nyckeln vid varje SSH-inloggning kan du cachelagra lösenordet med hjälp av `ssh-agent`. Om du använder en Mac skyddas lösenorden för privata nycklar i OSX-nyckelringen när du anropar `ssh-agent`.

Kontrollera och använd ssh-agent och ssh-add till för att informera SSH-systemet om viktiga filer så att lösenfrasen inte behöver användas interaktivt.

```bash
eval "$(ssh-agent -s)"
```

Lägg nu till den privata nyckeln i `ssh-agent` med hjälp av kommandot `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Nu lagras lösenordet för den privata nyckeln i `ssh-agent`.

## <a name="using-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Använda `ssh-copy-id` för att kopiera nyckeln till en befintlig virtuell dator
Om du redan har skapat en virtuell dator kan du installera den nya offentliga nyckeln till din virtuella Linux-dator med:

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

Nästa uppgift är att skapa virtuella Azure Linux-datorer med den nya offentliga SSH-nyckeln.  Virtuella Azure-datorer som skapas med en offentlig SSH-nyckel för inloggning är bättre skyddade än virtuella datorer som skapas med vanliga lösenordsbaserade inloggningsmetoder.  Lösenord är som standard inaktiverade för Virtuella Azure-datorer som skapas med SSH-nycklar för att undvika råstyrkeattacker som försöker gissa lösenord.

* [Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en säker virtuell Linux-dator med hjälp av Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en säker virtuell Linux-dator med hjälp av Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

<properties
    pageTitle="Skapa SSH-nycklar för Linux och Mac | Microsoft Azure"
    description="Generera och använd SSH-nycklar för Linux och Mac för Resource Manager och klassiska distributionsmodeller i Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="v-livech"/>

# Skapa SSH-nycklar för Linux och Mac för virtuella Linux-datorer i Azure

För att kunna skapa en lösenordsskyddad offentlig och privat SSH-nyckel måste du ha en öppen terminal på din arbetsstation.  När du har skapat SSH-nycklar kan du skapa nya virtuella datorer med nycklarna som standard eller lägga till den offentliga nyckeln till befintliga virtuella datorer med hjälp av både Azure CLI och Azure-mallar.  Detta gör att du kan använda inloggningar utan lösenord via SSH och använda den mycket säkrare autentiseringsmetoden med nycklar i stället för lösenord.

## Snabb kommandolista

I följande kommandoexempel ersätter du värdena mellan &lt; och &gt; med värden från din egen miljö.

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

Ange namnet på filen som ska sparas i `~/.ssh/`-katalogen:

```bash
<azure_fedora_id_rsa>
```

Ange lösenfrasen för azure_fedora_id_rsa:

```bash
<correct horse battery staple>
```

Lägg till den nya nyckeln i `ssh-agent` på Linux och Mac (läggs också till i OS X-nyckelringen):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure_fedora_id_rsa
```

Kopiera den offentliga SSH-nyckeln till din Linux-server:

```bash
ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>
```

Testa inloggningen med nycklar i stället för med ett lösenord:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## Introduktion

Det enklaste sättet att logga in på Linux-servrar är att använda offentliga och privata SSH-nycklar, men [kryptografi med offentliga nycklar](https://en.wikipedia.org/wiki/Public-key_cryptography) är också ett mycket säkrare sätt att logga in på din virtuella Linux- eller BSD-dator i Azure än med lösenord, som mycket enklare kan knäckas med nyckelsökningsattacker. Din offentliga nyckel kan delas med alla, men bara du (eller din lokala säkerhetsinfrastruktur) har tillgång till den privata nyckeln.  Den privata SSH-nyckeln som skapas skyddas med ett [säkert lösenord](https://www.xkcd.com/936/). Detta lösenord är bara avsett för att få åtkomst till den privata SSH-nyckeln och **är inte** användarkontots lösenord.  När du lägger till ett lösenord för SSH-nyckeln krypteras den privata nyckeln så att den inte kan användas utan lösenordet som krävs för att låsa upp den.  Om en angripare lyckas stjäla din privata nyckel och nyckeln saknar lösenord skulle angriparen kunna använda den privata nyckeln för att logga in på dina servrar där tillhörande offentliga nyckel är installerad.  Om den privata nyckeln är lösenordsskyddad kan den inte användas av angriparen, vilket ger ett extra säkerhetslager för infrastrukturen i Azure.


I den här artikeln skapar vi nyckelfiler med *ssh-rsa*-format eftersom dessa rekommenderas för distributioner med Resource Manager och krävs på [portalen](https://portal.azure.com) för både klassiska distributioner och Resource Manager-distributioner.


## Skapa SSH-nycklarna

Azure kräver offentliga och privata nycklar med minst 2048 bitar i ssh-rsa-format. För att skapa paret ska vi använda `ssh-keygen`, som ställer ett antal frågor och sedan skriver en privat nyckel och en matchande offentlig nyckel. När du skapar din virtuella Azure-dator överför du innehållet i den offentliga nyckeln, som kopieras till den virtuella Linux-datorn och som används med din lokala och säkert lagrade privata nyckel för att autentisera dig när du loggar in.

## Använda ssh-keygen

Med det här kommandot skapas ett lösenordsskyddat (krypterat) SSH-nyckelpar med 2 048 bitars RSA. Kommentarer har lagts till som gör det lättare att identifiera det.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

_Kommandot förklarat_

`ssh-keygen` = det program som används för att skapa nycklarna

`-t rsa` = typen av nyckel som ska skapas som är [RSA-formatet](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = bitar i nyckeln

`-C "ahmet@fedoraVMAzure"` = en kommentar i slutet av filen för den offentliga nyckeln som gör det lätt att identifiera den.  Normalt används en e-postadress som kommentaren, men du kan använda det som fungerar bäst för din infrastruktur.

## Genomgång av ssh-keygen

Varje steg förklarat i detalj.  Börja genom att köra `ssh-keygen`.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
The key's randomart image is:
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

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa`

Namnet på nyckelparet i den här artikeln.  Ett nyckelpar med namnet **id_rsa** är standard och vissa verktyg kan förvänta sig att namnet på filen för den privata nyckeln är **id_rsa**. Därför är det en bra idé att använda det. (`~/.ssh/` är den typiska standardplatsen för alla dina SSH-nyckelpar och SSH-konfigurationsfilen.)

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
Detta visar dina nya nyckelpar och deras behörigheter. `ssh-keygen` skapar `~/.ssh`-katalogen om den inte finns och anger även rätt ägarskap och fillägen.

Nyckellösenord:

`Enter passphrase (empty for no passphrase):`

Vi rekommenderar starkt att du lägger till ett lösenord (`ssh-keygen` kallar det för en ”lösenfras”) till dina nyckelpar. Utan ett lösenord som skyddar nyckelparet kan alla med en kopia av filen med den privata nyckeln använda den för att logga in på dina servrar, där den tillhörande offentliga nyckeln är installerad. Genom att lägga till ett lösenord har du därför mycket bättre skydd om någon lyckas få åtkomst till filen med din privata nyckel, så att du har tid att ändra nycklarna som används för att autentisera dig.

## Lagra lösenordet för din privata nyckel med hjälp av ssh-agent

Om du vill undvika att skriva lösenordet till filen med din privata nyckel vid varje SSH-inloggning kan du använda `ssh-agent` för att cachelagra lösenordet till filen med din privata nyckel så att du snabbt och säkert kan logga in på din virtuella Linux-dator.  Om du använder OS X lagrar nyckelringen lösenordet för din privata nyckel på ett säkert sätt när du anropar `ssh-agent`.

Kontrollera först att `ssh-agent` körs

```bash
eval "$(ssh-agent -s)"
```

Lägg nu till den privata nyckeln till `ssh-agent` med hjälp av kommandot `ssh-add`. (I OSX startar nyckelringen som lagrar autentiseringsuppgifterna.)

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

Nu lagras lösenordet för den privata nyckeln och du behöver inte ange det vid varje SSH-inloggning.

## Skapa och konfigurera en SSH-konfigurationsfil

Även om det inte är absolut nödvändigt för att komma igång med en virtuell Linux-dator är det en bra idé att skapa och konfigurera en `~/.ssh/config`-fil så att du inte oavsiktligt använder lösenord för att logga in med dina virtuella datorer, att automatisera användningen av olika nyckelpar för olika virtuella Azure-datorer samt att konfigurera andra program, till exempel **git**, mot flera servrar.

Följande exempel illustrerar en standardkonfiguration.

### Skapa filen

```bash
touch ~/.ssh/config
```

### Redigera filen för att lägga till den nya SSH-konfigurationen:

```bash
vim ~/.ssh/config
```

### `~/.ssh/config`-exempelfil:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

Denna SSH-config innehåller avsnitt för varje tjänst och aktiverar var och en så att de har sitt eget dedikerade nyckelpar. Standardinställningarna är avsedda för alla värdar som du är inloggad på som inte matchar någon av grupperna ovan.


### Konfigurationsfilen förklarad

`Host` = namnet på värddatorn som anropas på terminalen.  `ssh fedora22` beordrar `SSH` att använda värdena i inställningsblocket med etiketten `Host fedora22`  Obs! Detta kan vara vilken etikett som helst som är logisk för din användning och som inte representerar själva värdnamnet för en server.

`Hostname 102.160.203.241` = IP-adressen eller DNS-namnet för servern som du loggar in till. Detta används för dirigering till servern och kan vara en extern IP-adress som mappar till en intern IP-adress.

`User git` = fjärranvändarkontot som ska användas när du loggar in på den virtuella Azure-datorn.

`PubKeyAuthentication yes` = detta meddelar SSH att du vill använda en SSH-nyckeln för att logga in.

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` = detta meddelar SSH vilket nyckelpar som ska presenteras för servern för att autentisera inloggningen.


## SSH i Linux utan lösenord

Nu när du har en SSH-nyckel och en konfigurerad SSH-konfigurationsfil kan du logga in på din virtuella Linux-dator snabbt och säkert. Första gången du loggar in till en server med en SSH-nyckel frågar kommandot efter lösenfrasen för nyckelfilen.

```bash
ssh fedora22
```

### Kommandot förklarat

När `ssh fedora22` körs letar SSH först upp och läser in inställningarna från `Host fedora22`-blocket och läser sedan in de återstående inställningarna från det sista blocket, `Host *`.

## Nästa steg

Nästa uppgift är att skapa virtuella Azure Linux-datorer med den nya offentliga SSH-nyckeln.  Virtuella Azure-datorer som skapas med en offentlig SSH-nyckel för inloggning är bättre skyddade än de som skapas med vanliga lösenordsbaserade inloggningsmetoder.  Virtuella Azure-datorer som använder SSH-nycklar för inloggning är som standard konfigurerade att inaktivera lösenordsinloggning för att undvika nyckelsökningsattacker.

- [Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Skapa en säker virtuell Linux-dator med hjälp av Azure Portal](virtual-machines-linux-quick-create-portal.md)
- [Skapa en säker virtuell Linux-dator med hjälp av Azure CLI](virtual-machines-linux-quick-create-cli.md)



<!--HONumber=Jun16_HO2-->



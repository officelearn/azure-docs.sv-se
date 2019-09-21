---
title: Detaljerade steg – SSH-nyckelpar för virtuella Azure Linux-datorer | Microsoft Docs
description: Lär dig mer om hur du skapar och hanterar ett offentligt och privat SSH-nyckelpar för virtuella Linux-datorer i Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/17/2018
ms.author: cynthn
ms.openlocfilehash: da1454518cff12c2ae05f717b0165c02f437ee74
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168629"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Detaljerade steg: Skapa och hantera SSH-nycklar för autentisering till en virtuell Linux-dator i Azure 
Med ett SSH-nyckelpar (Secure Shell) kan du skapa en virtuell Linux-dator på Azure som använder SSH-nycklar som standard för autentisering, vilket eliminerar behovet av lösen ord för att logga in. Virtuella datorer som har skapats med Azure Portal, Azure CLI, Resource Manager-mallar eller andra verktyg kan innehålla din offentliga SSH-nyckel som en del av distributionen, som konfigurerar SSH-nyckel-autentisering för SSH-anslutningar. 

Den här artikeln innehåller detaljerad bakgrund och steg för att skapa och hantera ett SSH RSA-filpar med offentlig och privat nyckel för SSH-klientanslutningar. Om du vill ha snabb kommandon, se [hur du skapar ett offentligt privat privat nyckel par för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md).

Ytterligare sätt att skapa och använda SSH-nycklar på en Windows-dator finns i [så här använder du SSH-nycklar med Windows på Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Lösen fras för privat nyckel
Den privata SSH-nyckeln bör ha en mycket säker lösen fras för att skydda den. Den här lösen frasen är bara till för att komma åt den privata SSH-nyckelfilen och *är inte* användar kontots lösen ord. När du lägger till en lösen fras i SSH-nyckeln krypteras den privata nyckeln med hjälp av 128-bitars AES, så att den privata nyckeln är oanvändbar utan lösen frasen för att dekryptera den. Om en angripare stjäl din privata nyckel och nyckeln inte har någon lösen fras, skulle de kunna använda den privata nyckeln för att logga in på alla servrar som har motsvarande offentliga nyckel. Om en privat nyckel skyddas av en lösen fras kan den inte användas av angriparen, vilket ger ett extra säkerhets lager för din infrastruktur på Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Användning och fördelar med SSH-nycklar

När du skapar en virtuell Azure-dator genom att ange den offentliga nyckeln, kopierar Azure den offentliga nyckeln `.pub` (i formatet) `~/.ssh/authorized_keys` till mappen på den virtuella datorn. SSH-nycklar `~/.ssh/authorized_keys` i används för att anropa klienten att matcha motsvarande privata nyckel på en SSH-anslutning. I en virtuell Azure Linux-dator som använder SSH-nycklar för autentisering konfigurerar Azure SSHD-servern så att den inte tillåter inloggning med lösen ord, endast SSH-nycklar. Därför kan du genom att skapa en virtuell Azure Linux-dator med SSH-nycklar hjälpa till att skydda VM-distributionen och spara det vanliga konfigurations steget efter distribution för att `sshd_config` inaktivera lösen ord i filen.

Om du inte vill använda SSH-nycklar kan du konfigurera din virtuella Linux-dator för att använda lösenordsautentisering. Om din virtuella dator inte är exponerad för Internet kan det räcka att använda lösen ord. Du måste dock fortfarande hantera dina lösen ord för varje virtuell Linux-dator och upprätthålla hälso tillstånd och metoder för lösen ord, till exempel minsta längd på lösen ord och regelbundna uppdateringar. Genom att använda SSH-nycklar minskar du komplexiteten vid hantering av enskilda autentiseringsuppgifter på flera virtuella datorer.

## <a name="generate-keys-with-ssh-keygen"></a>Generera nycklar med ssh-keygen

För att skapa nycklarna är `ssh-keygen`ett önskat kommando, som är tillgängligt med openssh-verktyg i Azure Cloud Shell, en MacOS-eller Linux-värd, [Windows-undersystemet för Linux](https://docs.microsoft.com/windows/wsl/about)och andra verktyg. `ssh-keygen`ställer en rad frågor och skriver sedan en privat nyckel och en matchande offentlig nyckel. 

SSH-nycklarna sparas som standard i `~/.ssh`-katalogen.  Om du inte har någon `~/.ssh`-katalog skapar `ssh-keygen`-kommandot en åt dig med rätt behörigheter.

### <a name="basic-example"></a>Grundläggande exempel

Följande `ssh-keygen` kommando genererar 2048-bitars SSH RSA-RSA-filer och privata nyckelfiler som standard `~/.ssh` i katalogen. Om det finns ett SSH-nyckelpar på den aktuella platsen skrivs filerna över.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Detaljerat exempel
I följande exempel visas ytterligare kommando alternativ för att skapa ett SSH RSA-nyckelpar. Om det finns ett SSH-nyckelpar på den aktuella platsen skrivs filerna över. 

```bash
ssh-keygen \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Kommandot förklarat**

`ssh-keygen` = det program som används för att skapa nycklarna

`-t rsa`= typen av nyckel som ska skapas, i det här fallet i RSA-format

`-b 4096`= antalet bitar i nyckeln, i det här fallet 4096

`-C "azureuser@myserver"` = en kommentar i slutet av filen för den offentliga nyckeln som gör det lätt att identifiera den. Normalt används en e-postadress som kommentar, men använder det som fungerar bäst för din infrastruktur.

`-f ~/.ssh/mykeys/myprivatekey`= fil namnet på den privata nyckel filen, om du väljer att inte använda standard namnet. En motsvarande offentlig nyckel fil som läggs till `.pub` med genereras i samma katalog. Katalogen måste finnas.

`-N mypassphrase`= ytterligare en lösen fras som används för att få åtkomst till den privata nyckel filen. 

### <a name="example-of-ssh-keygen"></a>Exempel på ssh-keygen

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

#### <a name="saved-key-files"></a>Sparade nyckelfiler

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Namnet på nyckelparet i den här artikeln. Att ha ett nyckel par `id_rsa` med namnet är standardvärdet. vissa verktyg `id_rsa` kan förväntas ha fil namnet för den privata nyckeln, så det kan vara en bra idé. Katalogen `~/.ssh/` är standardplatsen för SSH-nyckelpar och SSH-konfigurationsfilen. Om detta inte anges med en fullständig sökväg skapar `ssh-keygen` nycklarna i den aktuella arbetskatalogen, inte standarden `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista över `~/.ssh` katalogen

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Nyckel lösen fras

`Enter passphrase (empty for no passphrase):`

Vi rekommenderar *starkt* att du lägger till en lösen fras till din privata nyckel. Utan en lösen fras för att skydda nyckel filen kan alla med-filen använda den för att logga in på alla servrar som har motsvarande offentliga nyckel. Genom att lägga till en lösen fras får du mer skydd om någon kan få åtkomst till din privata nyckel fil, vilket ger dig tid att ändra nycklarna.

## <a name="generate-keys-automatically-during-deployment"></a>Generera nycklar automatiskt under distributionen

Om du använder [Azure CLI](/cli/azure) för att skapa en virtuell dator kan du välja att generera SSH offentliga och privata nyckelfiler genom att köra `--generate-ssh-keys` kommandot [AZ VM Create](/cli/azure/vm) med alternativet. Nycklarna lagras i katalogen ~/.ssh. Observera att detta kommando alternativ inte skriver över nycklar om de redan finns på den platsen.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Tillhandahåll Offentlig SSH-nyckel vid distribution av en virtuell dator

Om du vill skapa en virtuell Linux-dator som använder SSH-nycklar för autentisering, anger du din offentliga SSH-nyckel när du skapar den virtuella datorn med hjälp av Azure Portal, CLI, Resource Manager-mallar eller andra metoder. När du använder portalen anger du själva den offentliga nyckeln. Om du använder [Azure CLI](/cli/azure) för att skapa en virtuell dator med en befintlig offentlig nyckel anger du värdet eller platsen för den offentliga nyckeln genom att köra `--ssh-key-value` kommandot [AZ VM Create](/cli/azure/vm) med alternativet. 

Om du inte är bekant med formatet på en offentlig SSH-nyckel kan du se din offentliga nyckel genom att köra `cat` enligt följande och ersätta `~/.ssh/id_rsa.pub` med din egen plats för offentlig nyckel fil:

```bash
cat ~/.ssh/id_rsa.pub
```

Utdata liknar följande (här förredigerat):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Om du kopierar och klistrar in innehållet i den offentliga nyckel filen i Azure Portal eller en Resource Manager-mall ser du till att du inte kopierar ytterligare blank steg eller lägger till ytterligare rad brytningar. Om du till exempel använder MacOS kan du skicka vidare den offentliga nyckel filen (som standard `~/.ssh/id_rsa.pub`) till **pbcopy** för att kopiera innehållet (det finns andra Linux-program som gör samma `xclip`sak, till exempel).

Om du föredrar att använda en offentlig nyckel som är i ett Multiline-format kan du generera en RFC4716-formaterad nyckel i en PEM-behållare från den offentliga nyckeln som du skapade tidigare.

Så här skapar du en RFC4716-formaterad nyckel från en befintlig offentlig SSH-nyckel:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH till den virtuella datorn med en SSH-klient
Med den offentliga nyckeln distribuerad på din virtuella Azure-dator och den privata nyckeln i det lokala systemet, med hjälp av den virtuella datorns IP-adress eller DNS-namn. Ersätt *azureuser* och *myvm.westus.cloudapp.Azure.com* i följande kommando med administratörs användar namnet och det fullständigt kvalificerade domän namnet (eller IP-adressen):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du angav en lösen fras när du skapade nyckel paret, anger du lösen frasen när du uppmanas att göra det under inloggnings processen. (Servern läggs till i din `~/.ssh/known_hosts`-mapp, och du blir inte ombedd att ansluta igen förrän den offentliga nyckeln på din virtuella Azure-dator ändras eller när servernamnet tas bort från `~/.ssh/known_hosts`.)

Om den virtuella datorn använder just-in-Time-åtkomst-principen måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med just-in-time-principen](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Använd SSH-agenten för att lagra lösen frasen för din privata nyckel

Om du inte vill skriva lösen frasen för den privata nyckel filen med varje ssh-inloggning `ssh-agent` kan du använda för att cachelagra din privata nyckel lösen fras. Om du använder en Mac lagrar macOS nyckel ringen säkert lösen frasen för den privata nyckeln när du anropar `ssh-agent`.

Verifiera och Använd `ssh-agent` och `ssh-add` för att informera SSH-systemet om viktiga filer så att du inte behöver använda lösen frasen interaktivt.

```bash
eval "$(ssh-agent -s)"
```

Lägg nu till den privata nyckeln i `ssh-agent` med hjälp av kommandot `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Lösen frasen för den privata nyckeln lagras `ssh-agent`nu i.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Använd SSH-Copy-ID för att kopiera nyckeln till en befintlig virtuell dator
Om du redan har skapat en virtuell dator kan du installera den nya offentliga SSH-nyckeln till din virtuella Linux-dator med ett kommando som liknar följande:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Skapa och konfigurera en SSH-konfigurationsfil

Du kan skapa och konfigurera en SSH config-fil`~/.ssh/config`() för att snabba upp inloggningar och optimera SSH-klientens beteende. 

I följande exempel visas en enkel konfiguration som du kan använda för att snabbt logga in som en användare till en speciell virtuell dator med hjälp av den privata standard-SSH-nyckeln. 

### <a name="create-the-file"></a>Skapa filen

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Redigera filen för att lägga till den nya SSH-konfigurationen

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Exempelkonfiguration

Lägg till de konfigurations inställningar som är lämpliga för den virtuella värddatorn.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Du kan lägga till konfigurationer för ytterligare värdar för att tillåta var och en att använda ett eget dedikerat nyckel par. Mer avancerade konfigurations alternativ finns i [SSH-konfigurationsfilen](https://www.ssh.com/ssh/config/) .

Nu när du har ett SSH-nyckelpar och en konfigurerad SSH-konfigurationsfil kan du snabbt och säkert logga in på din virtuella Linux-dator. När du kör följande kommando letar SSH upp och läser in alla inställningar från `Host myvm` blocket i SSH-konfigurationsfilen.

```bash
ssh myvm
```

Första gången du loggar in på en server med en SSH-nyckel, uppmanas du att ange lösen frasen för nyckel filen.

## <a name="next-steps"></a>Nästa steg

Nästa uppgift är att skapa virtuella Azure Linux-datorer med den nya offentliga SSH-nyckeln. Virtuella Azure-datorer som skapas med en offentlig SSH-nyckel som inloggning är bättre skyddade än virtuella datorer som skapats med standard inloggnings metoden, lösen ord.

* [Skapa en virtuell Linux-dator med Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en virtuell Linux-dator med Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en virtuell Linux-dator med en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

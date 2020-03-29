---
title: Detaljerade steg för att skapa ett SSH-nyckelpar
description: Lär dig detaljerade steg för att skapa och hantera ett SSH-offentligt och privat nyckelpar för virtuella Linux-datorer i Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c34a88c39104d3af2c5747d1cd6d3dea6929379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969538"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Detaljerade steg: Skapa och hantera SSH-nycklar för autentisering till en Virtuell Linux-dator i Azure 
Med ett SSH-nyckelpar (Secure Shell) kan du skapa en virtuell Linux-dator på Azure som som standard använder SSH-nycklar för autentisering, vilket eliminerar behovet av lösenord för att logga in. Virtuella datorer som skapats med Azure-portalen, Azure CLI, Resource Manager-mallar eller andra verktyg kan inkludera din SSH-offentliga nyckel som en del av distributionen, som ställer in SSH-nyckelautentisering för SSH-anslutningar. 

Den här artikeln innehåller detaljerad bakgrund och steg för att skapa och hantera ett SSH RSA-offentligt-privat nyckelfilpar för SSH-klientanslutningar. Om du vill ha snabbkommandon läser du [Så här skapar du ett SSH-offentligt-privat nyckelpar för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md).

Mer information om hur du skapar och använder SSH-nycklar på en Windows-dator finns i [Så här använder du SSH-nycklar med Windows på Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Lösenfras för privat nyckel
Den privata nyckeln SSH bör ha en mycket säker lösenfras för att skydda den. Den här lösenfrasen är bara för att komma åt den privata SSH-nyckelfilen och *är inte* användarkontots lösenord. När du lägger till en lösenfras för SSH-nyckeln krypteras den privata nyckeln med 128-bitars AES, så att den inte kan användas utan lösenfrasen som krävs för att dekryptera den. Om en angripare stal din privata nyckel och nyckeln inte hade någon lösenfras, skulle de kunna använda den privata nyckeln för att logga in på alla servrar som har motsvarande offentliga nyckel. Om en privat nyckel skyddas av en lösenfras kan den inte användas av angriparen, vilket ger ett extra säkerhetslager för din infrastruktur på Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Användning och fördelar med SSH-nycklar

När du skapar en virtuell Azure-dator genom att ange den `.pub` offentliga nyckeln `~/.ssh/authorized_keys` kopierar Azure den offentliga nyckeln (i formatet) till mappen på den virtuella datorn. SSH-nycklar `~/.ssh/authorized_keys` i används för att utmana klienten att matcha motsvarande privata nyckel på en SSH-anslutning. I en virtuell Azure Linux-dator som använder SSH-nycklar för autentisering konfigurerar Azure SSHD-servern så att den inte tillåter lösenordsinskrivning, bara SSH-nycklar. Genom att skapa en virtuell Azure Linux-dator med SSH-nycklar kan du därför hjälpa till att skydda `sshd_config` vm-distributionen och spara dig själv i det typiska konfigurationssteget efter distributionen för att inaktivera lösenord i filen.

Om du inte vill använda SSH-nycklar kan du konfigurera din Virtuella Linux-dator så att den använder lösenordsautentisering. Om den virtuella datorn inte exponeras för Internet kan det vara tillräckligt att använda lösenord. Du måste dock fortfarande hantera dina lösenord för varje Virtuell Linux-dator och upprätthålla felfria lösenordsprinciper och metoder, till exempel minsta lösenordslängd och regelbundna uppdateringar. Om du använder SSH-nycklar minskar komplexiteten i att hantera enskilda autentiseringsuppgifter för flera virtuella datorer.

## <a name="generate-keys-with-ssh-keygen"></a>Generera nycklar med ssh-keygen

För att skapa nycklarna är `ssh-keygen`ett önskat kommando , som är tillgängligt med OpenSSH-verktyg i Azure Cloud Shell, en macOS- eller Linux-värd, [Windows-undersystemet för Linux](https://docs.microsoft.com/windows/wsl/about)och andra verktyg. `ssh-keygen`ställer en rad frågor och skriver sedan en privat nyckel och en matchande offentlig nyckel. 

SSH-nycklarna sparas som standard i `~/.ssh`-katalogen.  Om du inte har någon `~/.ssh`-katalog skapar `ssh-keygen`-kommandot en åt dig med rätt behörigheter.

### <a name="basic-example"></a>Grundläggande exempel

Följande `ssh-keygen` kommando genererar 2048-bitars SSH RSA offentliga och `~/.ssh` privata nyckelfiler som standard i katalogen. Om det finns ett SSH-nyckelpar på den aktuella platsen skrivs dessa filer över.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Detaljerat exempel
I följande exempel visas ytterligare kommandoalternativ för att skapa ett SSH RSA-nyckelpar. Om det finns ett SSH-nyckelpar på den aktuella platsen skrivs dessa filer över. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Kommandot förklarat**

`ssh-keygen` = det program som används för att skapa nycklarna

`-m PEM`= formatera nyckeln som PEM

`-t rsa`= typ av nyckel att skapa, i det här fallet i RSA-format

`-b 4096`= antalet bitar i nyckeln, i detta fall 4096

`-C "azureuser@myserver"` = en kommentar i slutet av filen för den offentliga nyckeln som gör det lätt att identifiera den. Normalt används en e-postadress som kommentar, men använd det som fungerar bäst för din infrastruktur.

`-f ~/.ssh/mykeys/myprivatekey`= filnamnet på den privata nyckelfilen, om du väljer att inte använda standardnamnet. En motsvarande offentlig nyckelfil `.pub` som läggs till med genereras i samma katalog. Katalogen måste finnas.

`-N mypassphrase`= ytterligare en lösenfras som används för att komma åt den privata nyckelfilen. 

### <a name="example-of-ssh-keygen"></a>Exempel på ssh-keygen

```bash
ssh-keygen -t -m PEM rsa -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Sparade nyckelfiler

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Namnet på nyckelparet i den här artikeln. Att ha ett `id_rsa` nyckelpar som heter är standard. vissa verktyg kan `id_rsa` förvänta sig den privata nyckel filnamn, så att ha en är en bra idé. Katalogen `~/.ssh/` är standardplatsen för SSH-nyckelpar och SSH-konfigurationsfilen. Om detta inte anges med en fullständig sökväg skapar `ssh-keygen` nycklarna i den aktuella arbetskatalogen, inte standarden `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista över `~/.ssh` katalogen

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Nyckelformfras

`Enter passphrase (empty for no passphrase):`

Vi rekommenderar *starkt* att du lägger till en lösenfras i din privata nyckel. Utan en lösenfras för att skydda nyckelfilen kan vem som helst med filen använda den för att logga in på en server som har motsvarande offentliga nyckel. Genom att lägga till en lösenfras får du mer skydd om någon kan få tillgång till din privata nyckelfil, vilket ger dig tid att ändra nycklarna.

## <a name="generate-keys-automatically-during-deployment"></a>Generera nycklar automatiskt under distributionen

Om du använder [Azure CLI](/cli/azure) för att skapa din virtuella dator kan du eventuellt generera SSH-offentliga och privata nyckelfiler genom att köra kommandot az [vm create](/cli/azure/vm) med `--generate-ssh-keys` alternativet . Nycklarna lagras i katalogen ~/.ssh. Observera att det här kommandoalternativet inte skriver över nycklar om de redan finns på den platsen.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Ange allmän SSH-nyckel vid distribution av en virtuell dator

Om du vill skapa en virtuell Linux-dator som använder SSH-nycklar för autentisering anger du den offentliga SSH-nyckeln när du skapar den virtuella datorn med hjälp av Azure-portalen, CLI, Resource Manager-mallar eller andra metoder. När du använder portalen anger du själva den offentliga nyckeln. Om du använder [Azure CLI](/cli/azure) för att skapa din virtuella dator med en befintlig offentlig nyckel anger du värdet eller platsen för den här offentliga nyckeln genom att köra kommandot az [vm create](/cli/azure/vm) med `--ssh-key-value` alternativet . 

Om du inte är bekant med formatet för en SSH-offentlig nyckel `cat` kan du `~/.ssh/id_rsa.pub` se din offentliga nyckel genom att köra på följande sätt och ersätta med din egen offentliga nyckelfilplats:

```bash
cat ~/.ssh/id_rsa.pub
```

Utdata liknar följande (här redigerad):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Om du kopierar och klistrar in innehållet i den offentliga nyckelfilen i Azure-portalen eller en Resource Manager-mall kontrollerar du att du inte kopierar ytterligare blanktecken eller introducerar ytterligare radbrytningar. Om du till exempel använder macOS kan du leda den `~/.ssh/id_rsa.pub`offentliga nyckelfilen (som standard) till **pbcopy** för att kopiera `xclip`innehållet (det finns andra Linux-program som gör samma sak, till exempel ).

Om du föredrar att använda en offentlig nyckel som är i ett flerradsformat kan du generera en RFC4716-formaterad nyckel i en pem-behållare från den offentliga nyckel som du tidigare skapade.

Så här skapar du en RFC4716-formaterad nyckel från en befintlig offentlig SSH-nyckel:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH till din virtuella dator med en SSH-klient
Med den offentliga nyckeln distribuerad på din virtuella Azure-dator och den privata nyckeln på ditt lokala system, SSH till din virtuella dator med IP-adressen eller DNS-namnet på din virtuella dator. Ersätt *azureuser* och *myvm.westus.cloudapp.azure.com* i följande kommando med administratörens användarnamn och det fullständigt kvalificerade domännamnet (eller IP-adressen):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du angav en lösenfras när du skapade nyckelparet anger du lösenfrasen när du uppmanas att göra det under inloggningsprocessen. (Servern läggs till i din `~/.ssh/known_hosts`-mapp, och du blir inte ombedd att ansluta igen förrän den offentliga nyckeln på din virtuella Azure-dator ändras eller när servernamnet tas bort från `~/.ssh/known_hosts`.)

Om den virtuella datorn använder just-in-time-åtkomstprincipen måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med hjälp av just-in-time-principen](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Använd ssh-agent för att lagra din privata nyckel lösenfras

Om du vill undvika att skriva lösenfrasen för privata nyckelfiler med varje SSH-inloggning kan du använda `ssh-agent` för att cachelagra lösenfrasen för privata nyckelfiler. Om du använder en Mac lagrar macOS-nyckelringen säkert den privata nyckelformfrasen när du anropar `ssh-agent`.

Verifiera och `ssh-agent` `ssh-add` använd och informera SSH-systemet om nyckelfilerna så att du inte behöver använda lösenfrasen interaktivt.

```bash
eval "$(ssh-agent -s)"
```

Lägg nu till den privata nyckeln i `ssh-agent` med hjälp av kommandot `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Den privata nyckelformfrasen lagras `ssh-agent`nu i .

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Använda ssh-copy-id för att kopiera nyckeln till en befintlig virtuell dator
Om du redan har skapat en virtuell dator kan du installera den nya SSH-offentliga nyckeln på din virtuella Linux-dator med ett kommando som liknar följande:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Skapa och konfigurera en SSH-konfigurationsfil

Du kan skapa och konfigurera en`~/.ssh/config`SSH-config-fil ( ) för att snabba upp inloggningar och för att optimera din SSH-klientfunktion. 

I följande exempel visas en enkel konfiguration som du kan använda för att snabbt logga in som användare på en viss virtuell dator med standardnyckeln SSH. 

### <a name="create-the-file"></a>Skapa filen

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Redigera filen för att lägga till den nya SSH-konfigurationen

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Exempelkonfiguration

Lägg till konfigurationsinställningar som är lämpliga för värddatorn.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Du kan lägga till konfigurationer för ytterligare värdar så att var och en kan använda sitt eget dedikerade nyckelpar. Se [SSH-konfigurationsfilen](https://www.ssh.com/ssh/config/) för mer avancerade konfigurationsalternativ.

Nu när du har ett SSH-nyckelpar och en konfigurerad SSH-konfigurationsfil kan du logga in på din Virtuella Linux-dator snabbt och säkert. När du kör följande kommando hittar och läser SSH in några inställningar från `Host myvm` blocket i SSH-konfigurationsfilen.

```bash
ssh myvm
```

Första gången du loggar in på en server med en SSH-nyckel, uppmanas du att ange lösenfrasen för nyckelfilen.

## <a name="next-steps"></a>Nästa steg

Nästa uppgift är att skapa virtuella Azure Linux-datorer med den nya offentliga SSH-nyckeln. Virtuella Azure-datorer som skapas med en SSH-offentlig nyckel som inloggning är bättre skyddade än virtuella datorer som skapats med standard inloggningsmetoden, lösenord.

* [Skapa en virtuell Linux-dator med Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en virtuell Linux-dator med Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en virtuell Linux-dator med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

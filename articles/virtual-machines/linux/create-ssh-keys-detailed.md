---
title: Detaljerade steg - SSH-nyckelpar för virtuella Linux-datorer | Microsoft Docs
description: Lär dig mer detaljerade anvisningar för att skapa och hantera ett SSH offentliga och privata nyckelpar för virtuella Linux-datorer i Azure.
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
ms.date: 04/17/2018
ms.author: danlep
ms.openlocfilehash: 7167e31261ce029a6a0a6fe070232d1086942162
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297709"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Detaljerade anvisningar: Skapa och hantera SSH-nycklar för autentisering till en Linux-VM i Azure 
Du kan skapa en Linux-dator på Azure som använder SSH-nycklar för autentisering, vilket eliminerar behovet av lösenord för att logga in med ett secure shell (SSH)-nyckelpar. Virtuella datorer som har skapats med Azure portal, Azure CLI, Resource Manager mallar eller andra verktyg kan inkludera din offentliga SSH-nyckel som en del av distributionen, som konfigurerar SSH-nyckelautentisering för SSH-anslutningar. 

Den här artikeln innehåller detaljerade bakgrund och steg för att skapa och hantera ett SSH RSA-nyckelpar med offentliga och den privata nyckelfilen för SSH-klientanslutningar. Om du vill snabbkommandon, se [skapa ett SSH offentligt / privat nyckelpar för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md).

Ytterligare sätt att skapa och använda SSH-nycklar på en Windows-dator, se [hur du använder SSH-nycklar med Windows på Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

### <a name="private-key-passphrase"></a>Privata nyckeln
Den privata SSH-nyckeln bör ha en mycket säker lösenfras som skyddar den. Lösenfrasen används bara för att komma åt den privata SSH-nyckelfilen och *är inte* lösenordet för användarkontot. När du lägger till en lösenfras för SSH-nyckeln krypteras den privata nyckeln med hjälp av 128-bitars AES, så att den privata nyckeln är oanvändbara utan lösenfrasen att dekryptera den. Om en angripare stjäl din privata nyckel och nyckeln har inte en lösenfras, skulle de att kunna använda den privata nyckeln för att logga in på alla servrar som har motsvarande offentliga nyckel. Om en privat nyckel skyddas av en lösenfras, kan inte användas av angriparen, vilket ger ett extra säkerhetslager för infrastrukturen i Azure.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Användning och fördelar med SSH-nycklar

När du skapar en Azure-dator genom att ange den offentliga nyckeln, kopierar Azure den offentliga nyckeln (i den `.pub` format) till den `~/.ssh/authorized_keys` mapp på den virtuella datorn. SSH-nycklar i `~/.ssh/authorized_keys` används för att tvinga klienten att matcha motsvarande privata nyckel på en SSH-anslutning. I en virtuell Linux-dator som använder SSH-nycklar för autentisering, konfigurerar Azure SSHD-servern för att inte tillåta lösenord för inloggning, SSH-nycklar. Därför genom att skapa en virtuell Linux-dator med SSH-nycklar kan du skydda VM-distributionen och bespara dig vanliga konfigurationsstegen efter distribution steg för att inaktivera lösenord i den `sshd_config` filen.

Om du inte vill använda SSH-nycklar kan du konfigurera din Linux-VM du använder lösenordsautentisering. Om den virtuella datorn inte är exponerad för Internet kan med hjälp av lösenord vara tillräckligt. Du behöver dock fortfarande att hantera dina lösenord för varje Linux-VM och underhålla felfri lösenordsprinciper och praxis som minsta längd på lösenord och regelbundna uppdateringar. Använda SSH-nycklar minskar komplexiteten med att hantera enskilda autentiseringsuppgifter mellan flera virtuella datorer.

## <a name="generate-keys-with-ssh-keygen"></a>Generera nycklar med ssh-keygen

För att skapa nycklar, en önskad kommandot är `ssh-keygen`, som är tillgänglig med OpenSSH-verktyg i Azure Cloud Shell, ett macOS eller Linux-värden, den [Windows-undersystem for Linux](https://docs.microsoft.com/windows/wsl/about), och andra verktyg. `ssh-keygen` ställer ett antal frågor och sedan skriver en privat nyckel och en matchande offentlig nyckel. 

SSH-nycklarna sparas som standard i `~/.ssh`-katalogen.  Om du inte har någon `~/.ssh`-katalog skapar `ssh-keygen`-kommandot en åt dig med rätt behörigheter.

### <a name="basic-example"></a>Grundläggande exempel

Följande `ssh-keygen` kommandot genererar ett 2048-bitars SSH RSA offentliga och privata nyckelfiler som standard i den `~/.ssh` directory. Om en SSH-nyckelpar finns i den aktuella platsen, kommer dessa filer skrivs över.

```bash
ssh-keygen -t rsa -b 2048
```

### <a name="detailed-example"></a>Detaljerat exempel
I följande exempel visas ytterligare kommandoalternativ för att skapa en SSH RSA-nyckelpar. Om en SSH-nyckelpar finns i den aktuella platsen, kommer dessa filer skrivs över. 

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

`-t rsa` = typen av nyckel som ska skapas i det här fallet i RSA-format

`-b 4096` = antalet bitar i nyckeln i det här fallet 4096

`-C "azureuser@myserver"` = en kommentar i slutet av filen för den offentliga nyckeln som gör det lätt att identifiera den. Används normalt en e-postadress som kommentaren, men använda det som fungerar bäst för din infrastruktur.

`-f ~/.ssh/mykeys/myprivatekey` = filnamnet på den privata nyckelfilen, om du väljer att inte använda standardnamnet. En motsvarande offentlig nyckelfil läggas till med `.pub` skapas i samma katalog. Katalogen måste finnas.

`-N mypassphrase` = en ytterligare lösenfras som används för åtkomst till den privata nyckelfilen. 

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

Namnet på nyckelparet i den här artikeln. Ett nyckelpar med namnet `id_rsa` är standard; vissa verktyg kan förvänta sig den `id_rsa` privata nyckelfilen namn, är det en bra idé. Katalogen `~/.ssh/` är standardplatsen för SSH-nyckelpar och SSH-konfigurationsfilen. Om detta inte anges med en fullständig sökväg skapar `ssh-keygen` nycklarna i den aktuella arbetskatalogen, inte standarden `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Lista över de `~/.ssh` directory

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Nyckeln

`Enter passphrase (empty for no passphrase):`

Det är *starkt* bör du lägga till en lösenfras för den privata nyckeln. Vem kan använda det för att logga in på en server som har motsvarande offentliga nyckel utan en lösenfras att skydda viktiga filen. Att lägga till en lösenfras får du bättre skydd om någon lyckas få åtkomst till filen med din privata nyckel, vilket ger dig möjlighet för att ändra nycklarna.

## <a name="generate-keys-automatically-during-deployment"></a>Generera nycklar automatiskt under distributionen

Om du använder den [Azure CLI](/cli/azure) för att skapa den virtuella datorn, kan du också generera SSH offentliga och privata nyckelfiler genom att köra den [az vm skapa](/cli/azure/vm#az_vm_create) med den `--generate-ssh-keys` alternativet. Nycklarna lagras i katalogen ~/.ssh. Observera att det här kommandoalternativet skriver inte över nycklar om de redan finns på den platsen.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Ange offentlig SSH-nyckel när du distribuerar en virtuell dator

Om du vill skapa en Linux-VM som använder SSH-nycklar för autentisering, ange din offentliga SSH-nyckel när du skapar den virtuella datorn med hjälp av Azure portal, CLI, Resource Manager-mallar eller andra metoder. När du använder portalen, kan du ange den offentliga nyckeln själv. Om du använder den [Azure CLI](/cli/azure) för att skapa den virtuella datorn med en befintlig offentlig nyckel, anger du värdet eller platsen för den offentliga nyckeln genom att köra den [az vm skapa](/cli/azure/vm#az_vm_create) med den `--ssh-key-value` alternativet. 

Om du inte är bekant med formatet för en offentlig SSH-nyckel kan du se din offentliga nyckel genom att köra `cat` enligt följande, där du ersätter `~/.ssh/id_rsa.pub` med din egen plats för offentlig nyckelfil:

```bash
cat ~/.ssh/id_rsa.pub
```

Utdata liknar följande (borttagen här):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Om du kopierar och klistrar in innehållet i den offentliga nyckelfilen i Azure-portalen eller en Resource Manager-mall, kontrollera att du inte kopiera några extra blanksteg eller införa ytterligare radbrytningar. Om du använder macOS kan du till exempel skicka den offentliga nyckelfilen (som standard `~/.ssh/id_rsa.pub`) till **pbcopy** att kopiera innehållet (det finns andra Linux-program som gör samma sak, till exempel `xclip`).

Om du föredrar att använda en offentlig nyckel som är i multiline-format kan du generera en RFC4716-formaterad nyckel i en pem-behållare från den offentliga nyckeln som du skapade tidigare.

Så här skapar du en RFC4716-formaterad nyckel från en befintlig offentlig SSH-nyckel:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>SSH till den virtuella datorn med en SSH-klient
Med den offentliga nyckeln som distribuerats på den virtuella Azure-datorn och den privata nyckeln på din lokala dator, SSH till den virtuella datorn med IP-adressen eller DNS-namnet på den virtuella datorn. Ersätt *azureuser* och *myvm.westus.cloudapp.azure.com* i följande kommando med administratörens användarnamn och fullständigt kvalificerade domännamnet (eller IP-adress):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du angav en lösenfras när du skapade ditt nyckelpar, kan du ange lösenfrasen när frågan kommer under inloggningen. (Servern läggs till i din `~/.ssh/known_hosts`-mapp, och du blir inte ombedd att ansluta igen förrän den offentliga nyckeln på din virtuella Azure-dator ändras eller när servernamnet tas bort från `~/.ssh/known_hosts`.)

Om den virtuella datorn använder just-in-time-åtkomstprincip, måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [hantera VM-åtkomst med hjälp av just i time-princip](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Använda ssh-agent för att lagra dina privata nyckeln

Du kan använda för att undvika att skriva lösenfrasen fil för privat nyckel med varje SSH-inloggning kan `ssh-agent` cachelagra lösenfrasen fil för privat nyckel. Om du använder en Mac, nyckelring för macOS på ett säkert sätt lagras den privata nyckeln när du anropar `ssh-agent`.

Kontrollera och Använd `ssh-agent` och `ssh-add` att informera SSH-systemet om nyckelfiler så att du inte behöver använda lösenfrasen interaktivt.

```bash
eval "$(ssh-agent -s)"
```

Lägg nu till den privata nyckeln i `ssh-agent` med hjälp av kommandot `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Den privata nyckeln lagras nu på `ssh-agent`.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Använda ssh-kopia-id för att kopiera nyckeln till en befintlig virtuell dator
Om du redan har skapat en virtuell dator kan du installera den nya offentliga SSH-nyckeln till din Linux-VM med ett kommando som liknar följande:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Skapa och konfigurera en SSH-konfigurationsfil

Du kan skapa och konfigurera en SSH-konfigurationsfil (`~/.ssh/config`) att snabba upp log-moduler och för att optimera SSH-klientbeteendet. 

I följande exempel visar en enkel konfiguration som du kan använda för att snabbt logga in som en användare till en specifik virtuell dator med hjälp av standard SSH-privata nyckeln. 

### <a name="create-the-file"></a>Skapa filen

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Redigera filen för att lägga till den nya SSH-konfigurationen

```bash
vim ~/.ssh/config
```

### <a name="example-configuration"></a>Exempelkonfiguration

Lägg till konfigurationsinställningar som är lämpliga för din Virtuella värddator.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Du kan lägga till konfigurationer för ytterligare värdar så att var och en att använda ett eget dedikerat nyckelpar. Se [SSH-konfigurationsfil](https://www.ssh.com/ssh/config/) för mer avancerade konfigurationsalternativ.

Nu när du har en SSH-nyckelpar och en konfigurerad SSH-konfigurationsfil kan kan du logga in på din Linux-VM snabbt och säkert. När du kör följande kommando, SSH hittar och läser in inställningarna från den `Host myvm` blockera i SSH-konfigurationsfilen.

```bash
ssh myvm
```

Första gången du loggar in på en server med en SSH-nyckel frågar kommandot du efter lösenfrasen för nyckelfilen.

## <a name="next-steps"></a>Nästa steg

Nästa uppgift är att skapa virtuella Azure Linux-datorer med den nya offentliga SSH-nyckeln. Azure virtuella datorer som skapas med en offentlig SSH-nyckel som inloggningen är bättre skyddade än virtuella datorer som skapats med inloggning standardmetoden, lösenord.

* [Skapa en Linux-dator med Azure portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en Linux-dator med Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en Linux-VM med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

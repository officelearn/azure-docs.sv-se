---
title: "Med Docker VM-tillägget för Linux på Azure"
description: "Beskriver Docker och tillägg för virtuella datorer i Azure och visar hur du skapar programmatiskt virtuella datorer i Azure som är docker-värdar från kommandoraden med hjälp av Azure CLI."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: eaff75e3-d929-4931-a4a1-8c377a8e7302
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: rasquill
ms.openlocfilehash: a542332c921862241f1f000e6a8f0a0ae0e8a934
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>Använd tillägget för virtuella datorer med Docker genom kommandoradsgränssnittet för Azure (Azure CLI)
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Information om hur du använder Docker VM-tillägget med Resource Manager-modellen finns [här](../dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Det här avsnittet beskriver hur du skapar en virtuell dator med Docker-tillägg för virtuell dator från service management (asm) läge i Azure CLI på valfri plattform. [Docker](https://www.docker.com/) är en av de mest populära virtualisering metoder som använder [Linux behållare](http://en.wikipedia.org/wiki/LXC) i stället för virtuella datorer som ett sätt att isolera data och databehandling i delade resurser. Du kan använda Docker VM-tillägget och [Azure Linux-agenten](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) att skapa en Docker virtuell dator som är värd för valfritt antal behållare för dina program på Azure. En utförlig beskrivning av behållare och deras fördelar finns på [Docker hög nivå Whiteboard](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="how-to-use-the-docker-vm-extension-with-azure"></a>Använda Docker VM-tillägget med Azure
Om du vill använda Docker VM-tillägget med Azure måste du installera en version av den [Azure-kommandoradsgränssnittet](https://github.com/Azure/azure-sdk-tools-xplat) (Azure CLI) högre än 0.8.6 (som den aktuella versionen av detta skrivs är 0.10.0). Du kan installera Azure CLI för Mac, Linux och Windows.

Fullständig processen att använda Docker på Azure är enkel:

* Installera Azure CLI och dess beroenden på den dator som du vill styra Azure (på Windows, detta är en Linux-distribution som körs som en virtuell dator)
* Använda Azure CLI Docker-kommandon för att skapa en VM Docker-värd i Azure
* Använda lokala Docker-kommandon för att hantera din Docker-behållare i Docker-dator i Azure.

### <a name="install-the-azure-command-line-interface-azure-cli"></a>Installera Azure kommandoradsgränssnittet (Azure CLI)
För att installera och konfigurera Azure CLI, se [så här installerar du Azure-kommandoradsgränssnittet](../../../cli-install-nodejs.md). För att bekräfta installationen skriver `azure` vid kommandotolken och efter en kort stund bör du se Azure CLI ASCII-bilder som innehåller grundläggande kommandon tillgängliga för dig. Om installationen fungerade du ska kunna ange `azure help vm` och se något av kommandona listan finns ”docker”.

> [!NOTE]
> Docker har verktyg för Windows, [Docker datorn](https://docs.docker.com/installation/windows/), som också kan användas för att automatisera skapandet av en docker-klient som du kan använda för att arbeta med virtuella Azure-datorer som docker-värdar.
> 
> 

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Azure CLI för att ansluta till ditt Azure-konto
Innan du kan använda Azure CLI måste du koppla dina Azure-autentiseringsuppgifter med Azure CLI på din plattform. Avsnittet [hur du ansluter till din Azure-prenumeration](../../../xplat-cli-connect.md) förklarar hur du antingen hämta och importera din **.publishsettings** filen eller associera Azure CLI med ett organisations-id.

> [!NOTE]
> Det finns vissa skillnader i beteende när du använder en eller andra metoder för autentisering, så behöver vara noga med att läsa dokumentet ovan för att förstå de olika funktionerna.
> 
> 

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Installera Docker och använda Docker VM-tillägget för Azure
Följ den [Docker Installationsinstruktioner](https://docs.docker.com/installation/#installation) att installera Docker lokalt på datorn.

Om du vill använda Docker med en virtuell dator i Azure, Linux-bild som används för den virtuella datorn måste ha den [Virtuella Azure Linux-agenten](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) installerad. Det finns för närvarande bara två typer av avbildningar som innehåller detta:

* En Ubuntu-avbildning från Azure-galleriet avbildningen eller
* En anpassad avbildning i Linux som du har skapat med Azure Linux VM-agenten installeras och konfigureras. Se [Virtuella Azure Linux-agenten](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mer information om hur du skapar en anpassad Linux VM med Azure VM-agenten.

### <a name="using-the-azure-image-gallery"></a>Med hjälp av Azure-galleriet bild
Använd kommandot Azure CLI hitta senaste Ubuntu bilden i VM-galleriet genom att skriva från en Bash eller en terminalsession

`azure vm image list | grep Ubuntu-14_04`

och välj något av namnen, exempelvis `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`, och använder du följande kommando för att skapa en ny virtuell dator med hjälp av avbildningen.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

Där:

* *&lt;namn på VM-cloudservice&gt;*  är namnet på den virtuella datorn som ska vara värddator för Docker-behållaren i Azure
* *&lt;användarnamnet&gt;*  är användarnamnet för standardanvändaren för roten av den virtuella datorn
* *&lt;lösenordet&gt;*  är lösenordet för den *användarnamn* kontot som uppfyller kraven på komplexitet för Azure

> [!NOTE]
> För närvarande ett lösenord måste vara minst 8 tecken, innehålla en gemen och en versal, en siffra och ett specialtecken, till exempel ett av följande tecken: `!@#$%^&+=`. Nej, punkt i slutet av föregående mening är inte ett specialtecken.
> 
> 

Om kommandot lyckades, bör du se något som liknar följande, beroende på de alternativ som du använde och exakt argument:

![](media/cli-use-docker/dockercreateresults.png)

> [!NOTE]
> Skapar en virtuell dator kan ta några minuter, men när den har etablerats (värdet state är `ReadyRole`) Docker-daemon (Docker-tjänsten) startar och du kan ansluta till Docker behållare värden.
> 
> 

Testa Docker-VM som du har skapat i Azure

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

där  *&lt;vm-namn-du-används&gt;*  är namnet på den virtuella dator som du använde i anrop till `azure vm docker create`. Du bör se något som liknar följande, vilket betyder att den virtuella datorn Docker-värden är igång och körs i Azure och väntar på att dina kommandon. 

Nu kan du testa att ansluta med docker-klienten för att hämta information om (i vissa inställningar för Docker-klient, till exempel som på Mac du kan behöva använda `sudo`):

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Du kan undersöka den virtuella datorn för Docker-tillägg för att vara säker på att det är alla fungerande:

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Docker VM autentiseringstyp
Förutom att skapa Docker-VM den `azure vm docker create` kommando också automatiskt skapar nödvändiga certifikat för att tillåta Docker klientdatorn att ansluta till Azure-behållaren värden med hjälp av HTTPS och certifikat som lagras på datorer på både klienten och värden efter behov. Vid efterföljande försök befintliga certifikat återanvändas och delas med den nya värden.

Som standard certifikat placeras i `~/.docker`, och Docker konfigureras för att köras på port **2376**. Om du vill använda en annan port eller en katalog, så du kan använda något av följande `azure vm docker create` kommandoradsalternativ för att konfigurera din dockerbehållare värd åt virtuella datorn att använda en annan port eller annat certifikat för anslutande klienter:

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Docker-daemon på värden är konfigurerad för att lyssna efter och autentisera klientanslutningar på den angivna porten med de certifikat som genereras av den `azure vm docker create` kommando. Klientdatorn måste ha certifikaten för att få åtkomst till Docker-värden.

> [!NOTE]
> En nätverksansluten värd som kör utan dessa certifikat ska vara utsatt för alla som kan använda för att ansluta till datorn. Se till att du förstår risker för dina datorer och program innan du ändrar standardkonfigurationen.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Du är redo att gå till den [Docker användarhandboken] och använda Docker-VM. Om du vill skapa en Docker-aktiverad virtuell dator i den nya portalen finns [använda Docker VM-tillägget med portalen].
* Azure Docker VM-tillägg stöder också Docker Compose som använder en deklarativ YAML-fil att ta en utvecklare modelleras program över alla miljöer och skapa en konsekvent distribution. Se [Kom igång med Docker och skriv för att definiera och köra ett program för flera behållare på en virtuell dator i Azure].  

<!--Anchors-->
[Subheading 1]:#subheading-1
[Subheading 2]:#subheading-2
[Subheading 3]:#subheading-3
[Next steps]:#next-steps

[How to use the Docker VM Extension with Azure]:#How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]:#Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]:#Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]:../../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]:../../../app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md
[använda Docker VM-tillägget med portalen]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Docker användarhandboken]:https://docs.docker.com/userguide/

[Kom igång med Docker och skriv för att definiera och köra ett program för flera behållare på en virtuell dator i Azure]:../docker-compose-quickstart.md

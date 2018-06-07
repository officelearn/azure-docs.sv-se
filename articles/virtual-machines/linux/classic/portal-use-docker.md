---
title: Med Docker VM-tillägget för Linux | Microsoft Docs
description: Beskriver Docker och tillägg för virtuella datorer i Azure och hur du skapar Azure virtuella datorer som är docker-värdar med hjälp av Azure CLI i klassiska distributionsmodellen.
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: 19cf64e8-f92c-43ad-a120-8976cd9102ac
ms.service: virtual-machines-linux
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/27/2016
ms.author: rasquill
ms.openlocfilehash: 76497f58678e5ecfbab7d263b3adb4c475763cd8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653594"
---
# <a name="using-the-docker-vm-extension-with-the-azure-portal"></a>Med Docker VM-tillägget med Azure-portalen
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[Docker](https://www.docker.com/) är en av de mest populära virtualisering metoder som använder [Linux behållare](http://en.wikipedia.org/wiki/LXC) i stället för virtuella datorer som ett sätt att isolera data och databehandling i delade resurser. Du kan använda Docker VM-tillägget som hanteras av [Azure Linux-Agent] att skapa en Docker virtuell dator som är värd för valfritt antal behållare för dina program på Azure.

> [!NOTE]
> Det här avsnittet beskriver hur du skapar en Docker-VM från Azure-portalen. Information om hur du skapar en virtuell dator Docker på kommandoraden finns [Hur du använder Docker VM-tillägget från Azure-kommandoradsgränssnittet (Azure CLI)]. En utförlig beskrivning av behållare och deras fördelar finns på [Docker hög nivå Whiteboard](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).
> 
> 

## <a name="create-a-new-vm-from-the-image-gallery"></a>Skapa en ny virtuell dator från galleriet bild
Det första steget kräver en Azure-dator från en Linux-avbildning som har stöd för Docker VM-tillägget, som använder en Ubuntu 14.04 LTS bild från galleriet avbildningen som ett exempel serveravbildning och Ubuntu 14.04 skrivbordet som en klient. I portalen klickar du på **+ ny** att skapa en ny VM-instans och välja en Ubuntu 14.04 LTS bild på tillgängliga alternativ eller från galleriet klar bild som visas nedan.

> [!NOTE]
> För närvarande stöder endast Ubuntu 14.04 LTS bilder nyare än juli 2014 Docker VM-tillägget.
> 
> 

![Skapa en ny Ubuntu-avbildning](./media/portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Skapa Docker-certifikat
Se till att Docker är installerat på klientdatorn när den virtuella datorn har skapats. (Mer information finns i [Docker Installationsinstruktioner](https://docs.docker.com/installation/#installation).)

Skapa certifikat och nyckel för Docker kommunikation enligt [Med Docker med https] och placera dem i den **`~/.docker`** på klientdatorn.

> [!NOTE]
> Docker VM-tillägget i portalen kräver för närvarande autentiseringsuppgifter som är base64-kodad.
> 
> 

På kommandoraden, Använd **`base64`** eller något annat favorit kodning verktyg för att skapa base64-kodad avsnitt. Gör detta med en enkel uppsättning certifikatet och nyckel kan se ut ungefär så här:

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Lägg till Docker VM-tillägget
Om du vill lägga till Docker VM-tillägget, hitta VM-instans som du skapade och rulla ned till **tillägg** och klicka på den så att VM-tillägg som visas nedan.

![](media/portal-use-docker/ClickExtensions.png)

### <a name="add-an-extension"></a>Lägga till ett tillägg
Klicka på den **+ Lägg till** att visa de möjliga VM-tillägg som du kan lägga till den här virtuella datorn.

![](media/portal-use-docker/ClickAdd.png)

### <a name="select-the-docker-vm-extension"></a>Välj Docker VM-tillägget
Välj Docker VM-tillägg som ger Docker beskrivning och viktiga länkar, och klicka sedan på **skapa** längst ned för att påbörja installationen.

![](media/portal-use-docker/ChooseDockerExtension.png)

![](media/portal-use-docker/CreateButtonFocus.png)

### <a name="add-your-certificate-and-key-files"></a>Lägg till ditt certifikat och nyckelfiler:
Ange base64-kodad-versioner av CA-certifikat, servercertifikatet och din Server-nyckel i formulärfält, enligt följande bild.

![](media/portal-use-docker/AddExtensionFormFilled.png)

> [!NOTE]
> Observera att (som i föregående bild) i 2376 fylls i som standard. Du kan ange valfri slutpunkt här, men nästa steg är att öppna matchande slutpunkten. Om du ändrar standardinställningen, se till att öppna matchande slutpunkten i nästa steg.
> 
> 

## <a name="add-the-docker-communication-endpoint"></a>Lägg till Docker kommunikationsslutpunkt
När du visar resursgruppen som du har skapat, markera Nätverkssäkerhetsgruppen som är kopplade till den virtuella datorn och på **inkommande säkerhetsregler** att visa reglerna som visas här.

![](media/portal-use-docker/AddingEndpoint.png)

Klicka på **+ Lägg till** att lägga till en annan regel och ange ett namn för slutpunkten i standardfallet, (i det här exemplet **Docker**), och 2376 'Målportintervall'. Ange protokollet värdet visar **TCP**, och klicka på **OK** att skapa regeln.

![](media/portal-use-docker/AddEndpointFormFilledOut.png)

## <a name="test-your-docker-client-and-azure-docker-host"></a>Testa din Docker-klienten och Azure Docker-värden
Leta upp och kopiera namnet på den Virtuella datorns domän och på kommandoraden för klientdatorn typen `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (där *dockerextension* har ersatts av underdomänen för den virtuella datorn).

Resultatet bör se ut ungefär så här:

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

När du har slutfört ovanstående steg nu har du en fullt fungerande Docker-värd som kör på en Azure VM som konfigurerats för att anslutas till via fjärranslutning från andra klienter.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
Du är redo att gå till den [Docker-användarhandboken] och använda Docker-VM. Om du vill automatisera skapar Docker-värdar på Azure Virtual Machines via kommandoradsgränssnittet [Hur du använder Docker VM-tillägget från Azure-kommandoradsgränssnittet (Azure CLI)]

<!--Anchors-->
[Create a new VM from the Image Gallery]:#createvm
[Create Docker Certificates]:#dockercerts
[Add the Docker VM Extension]:#adddockerextension
[Test Docker Client and Azure Docker Host]:#testclientandserver
[Next steps]:#next-steps

<!--Image references-->
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[StartingPoint]:./media/StartingPoint.png
[6]:./media/markdown-template-for-new-articles/pretty49.png
[7]:./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Hur du använder Docker VM-tillägget från Azure-kommandoradsgränssnittet (Azure CLI)]:http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Azure Linux-Agent]:../../extensions/agent-linux.md
[Link 3 to another azure.microsoft.com documentation topic]:../storage-whatis-account.md

[Med Docker med https]:http://docs.docker.com/articles/https/
[Docker-användarhandboken]:https://docs.docker.com/userguide/

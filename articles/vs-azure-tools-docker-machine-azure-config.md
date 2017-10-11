---
title: "Skapa Docker-värdar i Azure med Docker-dator | Microsoft Docs"
description: "Beskriver användningen av Docker-datorn för att skapa docker-värdar i Azure."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 766d327a87ed13e04166d71c3d9ae0a1e7a66d19
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Skapa Docker-värdar i Azure med Docker-dator
Kör [Docker](https://www.docker.com/) behållare kräver en virtuell dator som kör daemonen docker-värd.
Det här avsnittet beskriver hur du använder den [docker-datorn](https://docs.docker.com/machine/) kommando för att skapa nya Linux virtuella datorer kan konfigureras med Docker-daemon körs i Azure. 

**Obs!** 

* *Den här artikeln beror på docker-datorn version 0.9.0-rc2 eller större*
* *Windows-behållare stöds via docker-datorn i den nära framtiden*

## <a name="create-vms-with-docker-machine"></a>Skapa virtuella datorer med Docker-dator
Skapa docker värden virtuella datorer i Azure med den `docker-machine create` med den `azure` drivrutin. 

Azure drivrutinen kräver ditt prenumerations-ID. Du kan använda den [Azure CLI](cli-install-nodejs.md) eller [Azure Portal](https://portal.azure.com) att hämta din Azure-prenumeration. 

**Med Azure-portalen**

* Välj **prenumerationer** från navigeringen till vänster sida och kopiera prenumerations-id.

**Använda Azure CLI**

* Typen ```azure account list``` och kopiera prenumerations-id.

Typen `docker-machine create --driver azure` alternativen och standardvärdena.
Du kan också se den [Docker Azure Driver-dokumentationen](https://docs.docker.com/machine/drivers/azure/) för mer information. 

I följande exempel använder det [standardvärdena](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), men den om du vill ange dessa värden: 

* Azure-DNS-namn som är associerade med den offentliga IP-Adressen och certifikat som genereras. Detta är DNS-namnet på den virtuella datorn. Den virtuella datorn kan sedan på ett säkert sätt stoppa, släpper den dynamiska IP-Adressen och ger möjlighet att återansluta efter den virtuella datorn börjar igen med en ny IP-adress. Namnprefixet måste vara unikt för regionen UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* Öppna port 80 på den virtuella datorn för utgående Internetåtkomst
* storleken på den virtuella datorn att använda snabbare premium-lagring
* Premium-lagring som används för den virtuella disken

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Välj en docker-värd med docker-dator
När du har en post i docker-datorn för värden, kan du ange standardvärden när du kör docker-kommandon.

## <a name="using-powershell"></a>Använda PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Med hjälp av Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Du kan nu köra docker kommandon mot den angivna värden

```
docker ps
docker info
```

## <a name="run-a-container"></a>Kör en behållare
Med en värd som har konfigurerats, kan du nu köra en enkel webbserver för att testa om värden har konfigurerats korrekt.
Här vi använder en standard nginx-bild och ange att den ska lyssna på port 80. Om den Virtuella värddatorn har startats om kommer behållaren starta om samt (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

Resultatet bör se ut ungefär så här:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>Testa behållaren
Granska körs behållare med `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Och om du vill visa behållaren körs, Skriv `docker-machine ip <VM name>` att hitta IP-adressen anger i webbläsaren:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Körs ngnix behållare](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Sammanfattning
Du kan enkelt etablera docker-värdar i Azure för din enskilda docker värden verifieringar med docker-datorn.
Produktion av behållare, finns det [Azure Container Service](http://aka.ms/AzureContainerService)

För att utveckla .NET Core-program med Visual Studio finns [Docker Tools för Visual Studio](http://aka.ms/DockerToolsForVS)


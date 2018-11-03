---
title: Skapa Docker-värdar i Azure med Docker Machine | Microsoft Docs
description: Beskriver användningen av Docker-dator för att skapa docker-värdar i Azure.
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 98b2d76f4b8ba9ba8316f3a33fd2ff5ed5899833
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969533"
---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Skapa Docker-värdar i Azure med Docker-dator
Kör [Docker](https://www.docker.com/) behållare kräver en virtuell dator som kör docker-daemon-värd.
Det här avsnittet beskriver hur du använder den [docker-dator](https://docs.docker.com/machine/) kommando för att skapa nya virtuella Linux-datorer konfigurerade med Docker-daemon körs i Azure. 

**Obs:** 

* *Den här artikeln beror på docker-dator version 0.9.0-rc2 eller större*
* *Windows-behållare stöds via docker-dator inom en snar framtid*

## <a name="create-vms-with-docker-machine"></a>Skapa virtuella datorer med Docker-dator
Skapa docker värden virtuella datorer i Azure med den `docker-machine create` kommando med hjälp av den `azure` drivrutinen. 

Azure-drivrutinen kräver ditt prenumerations-ID. Du kan använda den [Azure CLI](cli-install-nodejs.md) eller [Azure-portalen](https://portal.azure.com) att hämta din Azure-prenumeration. 

**Med Azure Portal**

* Välj **prenumerationer** från den vänstra sidan och kopiera prenumerations-id.

**Använda Azure CLI**

* Typ ```azure account list``` och kopiera prenumerations-id.

Typ `docker-machine create --driver azure` att se alternativen och deras standardvärden.
Du kan också se den [Docker Azure Driver-dokumentationen](https://docs.docker.com/machine/drivers/azure/) för mer information. 

I följande exempel bygger på den [standardvärdena](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22), men den om du vill ange dessa värden: 

* Azure-DNS-namn som är associerade med den offentliga IP-Adressen och certifikat som genereras. Det här är DNS-namnet på den virtuella datorn. Den virtuella datorn kan och sedan på ett säkert sätt stoppa, släpp den dynamiska IP-Adressen och ger möjlighet att ansluta igen när den virtuella datorn startas igen med en ny IP-adress. Namnprefix måste vara unikt för regionen UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com.
* Öppna port 80 på den virtuella datorn för utgående internet-åtkomst
* storleken på den virtuella datorn ska kunna använda snabbare premiumlagring
* premiumlagring som används för den virtuella datordisken

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>Välj en docker-värd med docker-dator
När du har en post i docker-dator för din värd kan ange du standardvärden när du kör docker-kommandon.

## <a name="using-powershell"></a>Använda PowerShell
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Använder Bash
```bash
eval $(docker-machine env MyDockerHost)
```

Du kan nu köra docker-kommandon mot den angivna värden

```
docker ps
docker info
```

## <a name="run-a-container"></a>Köra en behållare
Med en värd som är konfigurerad, kan du nu köra en enkel webbserver för att testa om värden har konfigurerats korrekt.
Här vi använder en standard nginx-avbildning, ange att det ska lyssna på port 80, och om den Virtuella värddatorn har startats om kommer behållaren startas om samt (`--restart=always`). 

```bash
docker run -d -p 80:80 --restart=always nginx
```

Utdata bör se ut ungefär så här:

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
Granska behållare som körs med hjälp av `docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

Och för att visa behållaren som körs, Skriv `docker-machine ip <VM name>` att hitta IP-adressen anger i webbläsaren:

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![Behållaren som körs ngnix](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>Sammanfattning
Du kan enkelt etablera docker-värdar i Azure för din verifieringar för enskilda docker-värd med docker-dator.
Produktion som är värd för behållare, finns i den [Azure Container Service](http://aka.ms/AzureContainerService)

Om du vill utveckla .NET Core-program med Visual Studio, se [Docker-verktyg för Visual Studio](http://aka.ms/DockerToolsForVS)


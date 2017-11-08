---
title: Med en Azure DC/OS-klustret ACR | Microsoft Docs
description: "Använda en Azure-behållare registret med ett DC/OS-kluster i Azure Container Service"
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service, acr, azure-container-registry
keywords: "Docker, behållare, Micro-tjänster, Mesos, Azure, filresursen, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 36e57bb6ebf9f55d42c526a361fed33b4238b313
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>Använda ACR med ett DC/OS-klustret för att distribuera programmet

I den här artikeln förklarar vi hur du använder Azure-behållare registret med ett DC/OS-kluster. Med hjälp av ACR kan du lagra och hantera avbildningar för behållaren privat. Den här kursen ingår följande uppgifter:

> [!div class="checklist"]
> * Distribuera Azure-behållare registret (vid behov)
> * Konfigurera ACR autentisering på en DC/OS-klustret
> * Överföra en bild i registret för Azure-behållare
> * Kör en avbildning av behållare från registret för Azure-behållare

Du behöver en ACS DC/OS-klustret för att slutföra stegen i den här självstudiekursen. Om det behövs, [detta skriptexempel](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) kan skapa en åt dig.

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Distribuera Azure-behållaren registret

Om det behövs, skapa ett Azure-behållare registret med den [az acr skapa](/cli/azure/acr#create) kommando. 

I följande exempel skapas ett register med ett slumpmässigt Generera namn. Registret konfigureras också med en admin-kontot med den `--admin-enabled` argumentet.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

När registret har skapats, Azure CLI matar ut data som liknar följande. Anteckna den `name` och `loginServer`, dessa används i senare steg.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Hämta behållaren registret autentiseringsuppgifterna med hjälp av den [az acr autentiseringsuppgifter visa](/cli/azure/acr/credential) kommando. Ersätt den `--name` med den som anges i det sista steget. Ta del av lösenord, det behövs i ett senare steg.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Läs mer om Azure-behållare registret [introduktion till privata Docker behållare register](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Hantera ACR-autentisering

Det vanliga sättet push och pull-avbildningen från ett privat register är att först autentisera med registret. Om du vill göra det använder du den `docker login` på alla klienter som behöver åtkomst till privata registret. Eftersom ett DC/OS-kluster kan innehålla flera noder, som måste autentiseras med ACR, är det bra att automatisera processen över varje nod. 

### <a name="create-shared-storage"></a>Skapa delad lagring

Den här processen använder en Azure-filresurs som har monterats på varje nod i klustret. Om du inte redan har installerat delad lagring, se [konfigurera en filresurs i ett DC/OS-kluster](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Konfigurera ACR-autentisering

Först hämta det fullständiga Domännamnet för DC/OS-hanteraren och lagrar den i en variabel.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Skapa en SSH-anslutning med huvudservern (eller den första huvudservern) på ditt DC/OS-baserade kluster. Uppdatera användarnamnet om ett standardvärde används när klustret skapas.

```azurecli-interactive
ssh azureuser@$FQDN
```

Kör följande kommando för att logga in på Azure-behållare registret. Ersätt den `--username` med namnet på behållaren-registret och `--password` med något av de angivna lösenorden. Ersätt det sista argumentet *mycontainerregistry.azurecr.io* i exemplet med loginServer namn i registret för behållaren. 

Det här kommandot lagrar värdena autentisering lokalt under den `~/.docker` sökväg.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Skapa en komprimerad fil som innehåller autentisering för behållaren registervärden.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Kopiera filen till klusterdelade lagringen. Det här steget gör filen tillgänglig på alla noder i DC/OS-klustret.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Överför avbildningen till ACR

Nu från en utvecklingsdator, eller andra system med Docker installerat, skapa en avbildning och överföra den till Azure-behållare registret.

Skapa en behållare från avbildningen Ubuntu.

```azurecli-interactive
docker run ubuntu --name base-image
```

Samla in behållaren nu till en ny avbildning. Avbildningens namn måste innehålla den `loginServer` namnet på behållaren registrywith ett format för `loginServer/imageName`.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
````

Logga in på Azure-behållaren registret. Ersätt namnet med loginServer namn, användarnamn--med namnet på behållaren-registret och--lösenordet med något av de angivna lösenorden.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Slutligen överför avbildningen till ACR-registret. Det här exemplet överför en bild med namnet DC/OS-demonstrationen.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Kör en bild från ACR

Skapa en fil namn om du vill använda en avbildning från registret ACR *acrDemo.json* och kopiera följande text till den. Ersätt Avbildningsnamnet med behållarnamn registret loginServer och namn, till exempel `loginServer/imageName`. Anteckna den `uris` egenskapen. Den här egenskapen innehåller platsen för behållaren autentisering registerfilen, som i det här fallet är Azure-filresursen som är monterade på varje nod i DC/OS-klustret.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Distribuera programmet med DC/OC CLI.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Nästa steg

I den här kursen har du konfigurera DC/OS för att använda Azure Container registret, inklusive följande uppgifter:

> [!div class="checklist"]
> * Distribuera Azure-behållare registret (vid behov)
> * Konfigurera ACR autentisering på en DC/OS-klustret
> * Överföra en bild i registret för Azure-behållare
> * Kör en avbildning av behållare från registret för Azure-behållare

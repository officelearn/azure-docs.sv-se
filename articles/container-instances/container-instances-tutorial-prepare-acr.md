---
title: "Azure Behållarinstanser tutorial – förbereda registret för Azure-behållare"
description: "Azure Behållarinstanser tutorial – förbereda registret för Azure-behållare"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: mmacy
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 848f6cbde49efdcfe96fc58ebc4160e0ea39f3f2
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Distribuera och använda Azure Container registret

Detta är en del två av tre delar självstudiekursen. I den [föregående steg](container-instances-tutorial-prepare-app.md), en behållare avbildning har skapats för en enkel webbapp som skrivits i [Node.js](http://nodejs.org). I den här självstudiekursen push avbildningen till ett Azure Container registret. Om du inte har skapat behållaren avbildningen återgå till [kursen 1 – skapa behållaren image](container-instances-tutorial-prepare-app.md).

Azure Container registret är en Azure-baserade, privat registret för Docker behållare bilder. Den här kursen går igenom distribution av en instans av Azure-behållare registret och överföra en behållare avbildning till den. Slutfört stegen innefattar:

> [!div class="checklist"]
> * Distribuera en Azure-behållare registret-instans
> * Behållaren image-märkning för Azure-behållare registernyckeln
> * Överför avbildningen till registret för Azure-behållare

I efterföljande självstudiekurser distribuera behållaren från registret privata till Azure-Behållarinstanser.

## <a name="before-you-begin"></a>Innan du börjar

Den här kursen kräver att du använder Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

För att slutföra den här självstudien behöver du en Docker-utvecklingsmiljö. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac-](https://docs.docker.com/docker-for-mac/), [Windows-](https://docs.docker.com/docker-for-windows/) eller [Linux-](https://docs.docker.com/engine/installation/#supported-platforms)dator.

Azure Cloud Shell inkluderar inte Docker-komponenter som krävs för att slutföra varje steg den här kursen. Därför rekommenderar vi en lokal installation av Azure CLI och Docker-utvecklingsmiljö.

## <a name="deploy-azure-container-registry"></a>Distribuera Azure-behållaren registret

När du distribuerar ett Azure Container registret, måste du först en resursgrupp. En Azure-resursgrupp är en logisk samling i vilka Azure resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). I det här exemplet en resursgrupp med namnet *myResourceGroup* skapas i den *eastus* region.

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa en Azure-behållaren registret med den [az acr skapa](/cli/azure/acr#create) kommando. Registret behållarnamn **måste vara unika** i Azure, och måste innehålla 5 50 alfanumeriska tecken. Ersätt `<acrName>` med ett unikt namn för registret:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Till exempel att skapa ett Azure-behållaren registernyckel med namnet *mycontainerregistry082*:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

I resten av den här kursen använder vi `<acrName>` som platshållare för registret behållarnamn som du har valt.

## <a name="container-registry-login"></a>Behållaren registret inloggning

Du måste logga in till din ACR-instansen innan du skickar bilder till den. Använd den [az acr inloggning](/cli/azure/acr#az_acr_login) kommando för att slutföra åtgärden. Du måste ange unika namnet på behållaren registret när den skapades.

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar en `Login Succeeded` meddelande när den har slutförts.

## <a name="tag-container-image"></a>Taggen behållaren bild

Om du vill distribuera en avbildning för behållaren från ett privat register, avbildningen måste taggas med det `loginServer` namn i registret.

Om du vill se en lista över aktuella bilder i `docker images` kommando.

```bash
docker images
```

Resultat:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Kör följande kommando för att få namnet loginServer. Ersätt `<acrName>` med namnet på behållaren registret.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Exempel på utdata:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Taggen i *aci kursen app* avbildningen med loginServer behållare registret. Lägg även till `:v1` till slutet av avbildningens namn. Den här taggen anger versionsnumret för avbildningen. Ersätt `<acrLoginServer>` med resultatet av den `az acr show` kommando du precis körde.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

När taggade, köra `docker images` att bekräfta åtgärden.

```bash
docker images
```

Resultat:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Push-avbildningen till registret för Azure-behållare

Push den *aci kursen app* avbildningen till registret med den `docker push` kommando. Ersätt `<acrLoginServer>` med fullständig server inloggningsnamnet hämta i tidigare steg.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

Den `push` åtgärden bör ta några sekunder att några minuter beroende på din Internet-anslutning och utdata som liknar följande:

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Listan avbildningar i Azure Container registret

Om du vill returnera en lista över bilder som har varit pushas till Azure-behållare registret, Använd den [az acr databaslistan](/cli/azure/acr/repository#list) kommando. Uppdatera kommandot med registret behållarnamn.

```azurecli
az acr repository list --name <acrName> --output table
```

Resultat:

```azurecli
Result
----------------
aci-tutorial-app
```

Och sedan använda taggar för en viss bild visas den [az acr databasen Visa-taggar](/cli/azure/acr/repository#show-tags) kommando.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Resultat:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Nästa steg

Ett Azure Container registret förbereddes för användning med Azure Container instanser i den här självstudiekursen och behållare avbildningen har flyttas. Följande steg har slutförts:

> [!div class="checklist"]
> * Distribuera en Azure-behållare registret-instans
> * En behållare avbildning har taggats för Azure-behållare registret
> * Överföra en bild till registret för Azure-behållare

Gå vidare till nästa kurs att lära dig om att distribuera behållaren till Azure med Azure Container instanser.

> [!div class="nextstepaction"]
> [Distribuera behållare till Behållarinstanser som Azure](./container-instances-tutorial-deploy-app.md)

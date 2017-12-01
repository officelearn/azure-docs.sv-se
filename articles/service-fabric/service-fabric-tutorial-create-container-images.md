---
title: "Skapa avbildningar av behållare för Azure Service Fabric | Microsoft Docs"
description: "Lär dig skapa avbildningar av behållare för ett program för Service Fabric flera behållare."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker-behållare, Mikrotjänster, Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: ecb70b88f6548e4730bcc1578de2f748cda33b0a
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="create-container-images-for-service-fabric"></a>Skapa avbildningar av behållare för Service Fabric

Den här kursen ingår i en självstudiekurs serie som visar hur du använder behållare i en Linux Service Fabric-klustret. I den här självstudiekursen, har ett program för flera behållare förberetts för användning med Service Fabric. I efterföljande självstudiekurser används dessa avbildningar som en del av ett Service Fabric-program. I den här självstudiekursen får du lära du dig att: 

> [!div class="checklist"]
> * Programmet Klonkälla från GitHub  
> * Skapa en behållare avbildning från käll-program
> * Distribuera en Azure Container registret (ACR)-instans
> * Tagga en avbildning av behållare för ACR
> * Överför avbildningen till ACR

I den här självstudiekursen serien lär du dig hur du: 

> [!div class="checklist"]
> * Skapa avbildningar av behållare för Service Fabric
> * [Skapa och köra ett Service Fabric-program med behållare](service-fabric-tutorial-package-containers.md)
> * [Hur redundans och skalning hanteras i Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Krav

- Linux-utvecklingsmiljö ställas in för Service Fabric. Följ instruktionerna [här](service-fabric-get-started-linux.md) att konfigurera Linux-miljö. 
- Den här kursen kräver att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 
- Dessutom kräver att du har en Azure-prenumeration tillgängliga. Mer information om en kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Hämta programkod

Exempelprogrammet används i den här kursen är en röstning app. Programmet består av en frontend-webbservrar komponent och en serverdel Redis-instans. Komponenterna paketeras till behållaren bilder. 

Använda git för att hämta en kopia av programmet till din utvecklingsmiljö.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

Katalogen 'självstudie om behållaren' innehåller en mapp med namnet ”azure-röst'. Mappen 'azure-röst' innehåller frontend källkoden och en Dockerfile att skapa klientdelen. Katalogen 'självstudie om behållaren' innehåller också 'redis'-katalogen som har Dockerfile att skapa redis-bild. Dessa kataloger som innehåller nödvändiga resurser för den här självstudiekursen. 

## <a name="create-container-images"></a>Skapa avbildningar av behållare

Kör följande kommando för att skapa avbildningen för komponenten frontend-webbservrar i katalogen 'azure-röst'. Detta kommando använder Dockerfile i den här katalogen för att skapa avbildningen. 

```bash
docker build -t azure-vote-front .
```

Insidan katalogen 'redis', kör följande kommando för att skapa avbildningen för redis-serverdel. Detta kommando använder Dockerfile i katalogen för att skapa avbildningen. 

```bash
docker build -t azure-vote-back .
```

När du är klar, kan du använda den [docker bilder](https://docs.docker.com/engine/reference/commandline/images/) kommandot för att se bilderna som har skapats.

```bash
docker images
```

Observera att fyra bilder har hämtat eller skapat. Den *azure rösten sista* bilden innehåller programmet. Det har härletts från en *python* avbildningen från Docker-hubben. Redis-avbildningen hämtades från Docker-hubben.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Distribuera Azure-behållaren registret

Kör först den [az inloggningen](/cli/azure/login) kommando för att logga in på ditt Azure-konto. 

Använd sedan den [az konto](/cli/azure/account#set) kommando för att välja din prenumeration för att skapa Azure-behållare registernyckel. 

```bash
az account set --subscription <subscription_id>
```

När du distribuerar ett Azure Container registret, måste du först en resursgrupp. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). I det här exemplet en resursgrupp med namnet *myResourceGroup* skapas i den *westus* region. Välj resursgruppen i en region nära dig. 

```bash
az group create --name myResourceGroup --location westus
```

Skapa en Azure-behållare registret med den [az acr skapa](/cli/azure/acr#create) kommando. Namnet på en behållare registret **måste vara unika**.

```bash
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

I resten av den här kursen använder vi ”acrname” som platshållare för registret behållarnamn som du har valt.

## <a name="log-in-to-your-container-registry"></a>Logga in på behållaren registret

Logga in på din ACR-instansen innan du skickar bilder till den. Använd den [az acr inloggning](/cli/azure/acr?view=azure-cli-latest#az_acr_login) kommando för att slutföra åtgärden. Ange unika namnet på behållaren registret när den skapades.

```bash
az acr login --name <acrName>
```

Kommandot returnerar ett inloggningen lyckades meddelande när den har slutförts.

## <a name="tag-container-images"></a>Taggen behållaren bilder

Varje behållare avbildning måste taggas med loginServer namn i registret. Den här etiketten används för routning vid push-installation behållaren avbildningar till en bild-registret.

Om du vill se en lista över aktuella bilder i [docker bilder](https://docs.docker.com/engine/reference/commandline/images/) kommando.

```bash
docker images
```

Resultat:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

För att få namnet loginServer, kör du följande kommando:

```bash
az acr show --name <acrName> --query loginServer --output table
```

Tagga nu den *azure rösten sista* avbildningen med loginServer av registret i behållaren. Lägg även till `:v1` till slutet av avbildningens namn. Den här taggen anger den bildversionen.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Tagga sedan den *azure rösten tillbaka* avbildningen med loginServer av registret i behållaren. Lägg även till `:v1` till slutet av avbildningens namn. Den här taggen anger den bildversionen.

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

När taggade, köra docker-bilder att bekräfta åtgärden.


Resultat:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                        latest              bf9a858a9269        22 minutes ago      107MB
<acrName>.azurecr.io/azure-vote-back    v1                  bf9a858a9269        22 minutes ago      107MB
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf        23 minutes ago      708MB
redis                                  latest              9813a7e8fcc0        2 days ago          107MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Push-avbildningar till registret

Push den *azure rösten sista* avbildningen till registret. 

Med följande exempel ersätta ACR loginServer namn med loginServer från din miljö.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Push den *azure rösten tillbaka* avbildningen till registret. 

Med följande exempel ersätta ACR loginServer namn med loginServer från din miljö.

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

Kommandona docker push ta ett par minuter att slutföra.

## <a name="list-images-in-registry"></a>Lista över bilder i registret

Om du vill returnera en lista över bilder som har varit pushas till Azure-behållare registret, Använd den [az acr databaslistan](/cli/azure/acr/repository#list) kommando. Uppdatera kommandot med namnet på ACR-instansen.

```bash
az acr repository list --name <acrName> --output table
```

Resultat:

```bash
Result
----------------
azure-vote-back
azure-vote-front
```

Vid självstudiekursen slut har behållaren avbildningen lagrats i en privat Azure-behållare registret-instans. Den här avbildningen distribueras från ACR till ett Service Fabric-kluster i efterföljande självstudiekurser.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen, ett program som hämtades från Github och behållare bilder har skapats och pushas till ett register. Följande steg har slutförts:

> [!div class="checklist"]
> * Programmet Klonkälla från GitHub  
> * Skapa en behållare avbildning från käll-program
> * Distribuera en Azure Container registret (ACR)-instans
> * Tagga en avbildning av behållare för ACR
> * Överför avbildningen till ACR

Gå vidare till nästa kurs vill veta mer om paketering behållare till ett Service Fabric-program med hjälp av Yeoman. 

> [!div class="nextstepaction"]
> [Paketera och distribuera behållare som ett Service Fabric-program](service-fabric-tutorial-package-containers.md)

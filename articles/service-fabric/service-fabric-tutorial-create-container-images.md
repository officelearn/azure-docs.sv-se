---
title: Skapa containeravbildningar för Service Fabric i Azure | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du skapar behållaravbildningar för ett Service Fabric-program med flera behållare.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, Containers, Microservices, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: a2814ff299d1bfb003b6133e2b75b47a312f8728
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114048"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>Självstudie: Skapa containeravbildningar i ett Service Fabric-kluster i Linux

Den här självstudien ingår i en serie som visar hur du använder behållare i ett Linux Service Fabric-kluster. I den här självstudien förbereds ett program med flera behållare för användning med Service Fabric. I efterföljande självstudier används de här avbildningarna som en del i ett Service Fabric-program. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * klona programmets källkod från GitHub
> * skapa en behållaravbildning från programkällkoden
> * distribuera en ACR-instans (Azure Container Registry)
> * tagga en behållaravbildning för ACR
> * ladda upp avbildningen till ACR.

I den här självstudieserien får du lära du dig att:

> [!div class="checklist"]
> * skapa behållaravbildningar för Service Fabric
> * [skapa och köra ett Service Fabric-program med behållare](service-fabric-tutorial-package-containers.md)
> * [hantera redundans och skalning i Service Fabric](service-fabric-tutorial-containers-failover.md).

## <a name="prerequisites"></a>Förutsättningar

* Linux-utvecklingsmiljö konfigurerad för Service Fabric. Följ instruktionerna [här](service-fabric-get-started-linux.md) för att konfigurera din Linux-miljö.
* I den här självstudien krävs att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).
* Dessutom måste du ha en Azure-prenumeration tillgänglig. Mer information om en kostnadsfri utvärderingsversion finns [här](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Hämta programkod

Exempelprogrammet som används i den här självstudien är en röstningsapp. Programmet består av en webbkomponent på klientsidan och en Redis-instans på serversidan. Komponenterna är paketerade i behållaravbildningar.

Använd git och ladda ned en kopia av programmet till utvecklingsmiljön.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

Lösningen innehåller två mappar och filen docker-compose.yml. Mappen azure-vote innehåller Python-tjänsten för klientdelen och den dockerfil som används till att skapa avbildningen. Katalogen Voting innehåller Service Fabric-programpaketet som distribueras till klustret. Dessa kataloger innehåller de resurser som behövs i den här självstudien.

## <a name="create-container-images"></a>Skapa behållaravbildningar

Öppna katalogen **azure-vote** och kör följande kommando för att skapa avbildningen för webbkomponenten på klientsidan. I kommandot används dockerfilen i katalogen till att skapa avbildningen.

```bash
docker build -t azure-vote-front .
```

Det här kommandot kan ta lite tid eftersom alla nödvändiga beroenden måste hämtas från Docker Hub. När kommandot har körts kan du använda kommandot [docker images](https://docs.docker.com/engine/reference/commandline/images/) till att se de avbildningar som skapats.

```bash
docker images
```

Observera att två avbildningar har laddats ned eller skapats. Avbildningen *azure-vote-front* innehåller själva programmet. Det har härletts från en *Python*-avbildning från Docker Hub.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Distribuera Azure Container Registry

Kör först kommandot **az login** för att logga in på ditt Azure-konto.

```bash
az login
```

Använd sedan kommandot **az account** till att välja din prenumeration och skapa Azure-behållarregistret. Du måste ange ID:t för din Azure-prenumeration i stället för <subscription_id>.

```bash
az account set --subscription <subscription_id>
```

När du distribuerar ett Azure Container Registry behöver du först en resursgrupp. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot **az group create**. I det här exemplet skapas en resursgrupp med namnet *myResourceGroup* i regionen *westus*.

```bash
az group create --name <myResourceGroup> --location westus
```

Skapa ett Azure-behållarregister med kommandot **az acr create**. Ersätt \<acrName> med namnet på behållarregistret du vill skapa i din prenumeration. Det här namnet måste vara alfanumeriskt och unikt.

```bash
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

I resten av den här självstudien använder vi acrName som platshållare för det behållarregisternamn du väljer. Skriv ned det här värdet.

## <a name="log-in-to-your-container-registry"></a>Logga in på ditt behållarregister

Logga in på din ACR-instans innan du push-överför avbildningar till den. Använd kommandot **az acr login** till att slutföra åtgärden. Ange det unika namn du angav för behållarregistret när det skapades.

```bash
az acr login --name <acrName>
```

Du får ett meddelande om att inloggningen lyckades när inloggningen är klar.

## <a name="tag-container-images"></a>Tagga behållaravbildningar

Varje behållaravbildning måste taggas med namnet på inloggningsservern för registret. Den här taggen används till routning när du push-överför behållaravbildningar till ett avbildningsregister.

Om du vill se en lista med aktuella avbildningar använder du kommandot [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Resultat:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Du hämtar namnet på inloggningsservern genom att köra följande kommando:

```bash
az acr show --name <acrName> --query loginServer --output table
```

Utdata är en tabell med följande resultat. Resultatet kommer att användas när du ska tagga avbildningen **azure-vote-front** innan du push-överför den till behållarregistret i nästa steg.

```bash
Result
------------------
<acrName>.azurecr.io
```

Tagga nu avbildningen *azure-vote-front* med namnet på inloggningsservern för behållarregistret. Lägg även till `:v1` i slutet av avbildningens namn. Den här taggen anger versionsnumret för avbildningen.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

När taggningen är färdig verifierar du åtgärden genom att köra ”docker images”.

Resultat:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Push-överför avbildningar till registret

Push-överför avbildningen *azure-vote-front* till registret. 

Använd följande exempel och ersätt namnet på ACR-inloggningsservern med inloggningsnamnet från din miljö.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

Docker push-kommandona tar ett par minuter att slutföra.

## <a name="list-images-in-registry"></a>Lista med avbildningar i registret

Du kan returnera en lista med avbildningar som push-överförts till Azure-behållarregistret med kommandot [az acr repository list](/cli/azure/acr/repository#az_acr_repository_list). Uppdatera kommandot med namnet på ACR-instansen.

```bash
az acr repository list --name <acrName> --output table
```

Resultat:

```bash
Result
----------------
azure-vote-front
```

När självstudien är färdig har behållaravbildningen lagrats i en privat Azure Container Registry-instans. Den här avbildningen distribueras från ACR till ett Service Fabric-kluster i efterföljande självstudier.

## <a name="next-steps"></a>Nästa steg

I den här självstudien hämtades ett program från Github. Sedan skapades och push-överfördes behållaravbildningar till ett register. Följande steg har slutförts:

> [!div class="checklist"]
> * klona programmets källkod från GitHub
> * skapa en behållaravbildning från programkällkoden
> * distribuera en ACR-instans (Azure Container Registry)
> * tagga en behållaravbildning för ACR
> * ladda upp avbildningen till ACR.

Gå vidare till nästa självstudie om du vill veta mer om paketering av behållare till ett Service Fabric-program med hjälp av Yeoman.

> [!div class="nextstepaction"]
> [Paketera och distribuera behållare som ett Service Fabric-program](service-fabric-tutorial-package-containers.md)
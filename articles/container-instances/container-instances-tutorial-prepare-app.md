---
title: "Azure Behållarinstanser tutorial – förbereda din app"
description: "Förbereda en app för distribution till Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5de53266e1dbadecb9fabb1649615fa9f4ba8b5f
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Skapa behållare för distribution till Azure Container Instances

Azure Container Instances möjliggör distribution av Docker-behållare till en Azure-infrastruktur utan att tillhandahålla några virtuella datorer eller anpassa eventuella tjänster på högre nivå. I den här självstudiekursen, skapa ett litet webbprogram i Node.js och paketet i en behållare som kan köras med Azure Container instanser. Vi går igenom:

> [!div class="checklist"]
> * Klona programkällan från GitHub
> * Skapa behållaravbildningar från en programkälla
> * Testa avbildningarna i en lokal Docker-miljö

I efterföljande självstudiekurser överför avbildningen till ett Azure Container registret och distribuera den till Azure-Behållarinstanser.

## <a name="before-you-begin"></a>Innan du börjar

Den här kursen kräver att du använder Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

Den här kursen förutsätter en grundläggande förståelse av grundläggande Docker begrepp som behållare, behållare avbildningar och basic `docker` kommandon. Om det behövs kan du läsa [Get started with Docker]( https://docs.docker.com/get-started/) (Komma igång med Docker) för att få en genomgång av grunden för behållare.

För att slutföra den här självstudien behöver du en Docker-utvecklingsmiljö. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac-](https://docs.docker.com/docker-for-mac/), [Windows-](https://docs.docker.com/docker-for-windows/) eller [Linux-](https://docs.docker.com/engine/installation/#supported-platforms)dator.

Azure Cloud Shell inkluderar inte Docker-komponenter som krävs för att slutföra varje steg den här kursen. Därför rekommenderar vi en lokal installation av Azure CLI och Docker-utvecklingsmiljö.

## <a name="get-application-code"></a>Hämta programkod

Exemplet i den här självstudien innehåller en enkel webbapp som skapats i [Node.js](http://nodejs.org). Appen har en statisk HTML-sida och ser ut så här:

![Självstudieappen visas i webbläsare][aci-tutorial-app]

Ladda ned exempelfilerna med Git:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Bygga behållaravbildningen

Den Dockerfile som finns i exempelrepon visar hur behållaren är byggd. Den börjar från en [officiell Node.js-avbildning][dockerhub-nodeimage] baserat på [Alpine Linux](https://alpinelinux.org/), en liten distribution som är lämplig för användning med behållare. Den kopierar sedan programfilerna till behållaren, installerar beroenden med Node Package Manager (nodpaketshanteraren) och startar slutligen programmet.

```Dockerfile
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Använd kommandot `docker build` för att skapa behållaravbildningen, och märk den med *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Utdata från den `docker build` kommando som liknar följande (trunkerad för läsbarhet):

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.2.0-alpine
8.2.0-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.2.0-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Använd `docker images` för att se den skapade avbildningen:

```bash
docker images
```

Resultat:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>Kör behållaren lokalt

Innan du försöker distribuera behållaren till Azure Container Instances ska du köra den lokalt för att bekräfta att den fungerar. Med växeln `-d` kan behållaren köras i bakgrunden, medan `-p` gör att du kan mappa en godtycklig port för dina beräkningar till port 80 i behållaren.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Öppna webbläsaren till http://localhost:8080 för att kontrollera att behållaren körs.

![Köra appen lokalt i webbläsaren][aci-tutorial-app-local]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en behållaravbildning som kan distribueras till Azure Container Instances. Följande steg har slutförts:

> [!div class="checklist"]
> * Klonade källan programmet från GitHub
> * Skapade behållaren bilder från programmet källa
> * Testas behållaren lokalt

Fortsätt till nästa självstudie och lär dig om att lagra behållaravbildningar i ett Azure Container Registry.

> [!div class="nextstepaction"]
> [Push-avbildningar med Azure Container Registry](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://store.docker.com/images/node

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png
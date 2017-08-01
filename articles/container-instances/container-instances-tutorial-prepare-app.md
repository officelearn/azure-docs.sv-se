---
title: "Skapa en grupp med flera behållare med Azure Container Instances | Azure Docs"
description: "Skapa en grupp med flera behållare och distribuera den till Azure Container Instances från Azure Container Registry"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: b5f16f0c4b15c02bbc0ee8a9fb9424467a39ee4d
ms.contentlocale: sv-se
ms.lasthandoff: 07/26/2017

---

# <a name="create-container-for-deployment-to-azure-container-instances"></a>Skapa behållare för distribution till Azure Container Instances

Azure Container Instances möjliggör distribution av Docker-behållare till en Azure-infrastruktur utan att tillhandahålla några virtuella datorer eller anpassa eventuella tjänster på högre nivå. I den här självstudien får du bygga en enkel webbapp i Node.js och paketera den i en behållare som kan köras med Azure Container Instances. Vi kommer att ta upp:

> [!div class="checklist"]
> * Klona programkällan från GitHub  
> * Skapa behållaravbildningar från en programkälla
> * Testa avbildningarna i en lokal Docker-miljö

I efterföljande självstudier får du ladda upp din avbildning till ett Azure Container Registry och sedan distribuera den till Azure Container Instances.

## <a name="before-you-begin"></a>Innan du börjar

Den här självstudien förutsätter grundläggande kunskaper om grundläggande Docker-begrepp som behållare, behållaravbildningar och grundläggande docker-kommandon. Om det behövs kan du läsa [Get started with Docker]( https://docs.docker.com/get-started/) (Komma igång med Docker) för att få en genomgång av grunden för behållare. 

För att slutföra den här självstudien behöver du en Docker-utvecklingsmiljö. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac-](https://docs.docker.com/docker-for-mac/), [Windows-](https://docs.docker.com/docker-for-windows/) eller [Linux-](https://docs.docker.com/engine/installation/#supported-platforms)dator.

## <a name="get-application-code"></a>Hämta programkod

Exemplet i den här självstudien innehåller en enkel webbapp som skapats i [Node.js](http://nodejs.org). Appen har en statisk HTML-sida och ser ut så här:

![Självstudieappen visas i webbläsare][aci-tutorial-app]

Ladda ned exempelfilerna med Git:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Bygga behållaravbildningen

Den Dockerfile som finns i exempelrepon visar hur behållaren är byggd. Den börjar från en [officiell Node.js-avbildning][dockerhub-nodeimage] baserat på [Alpine Linux](https://alpinelinux.org/), en liten distribution som är lämplig för användning med behållare. Den kopierar sedan programfilerna till behållaren, installerar beroenden med Node Package Manager (nodpaketshanteraren) och startar slutligen programmet.

```
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
> * Klona programkällan från GitHub  
> * Skapa behållaravbildningar från en programkälla
> * Testa behållaren lokalt

Fortsätt till nästa självstudie och lär dig om att lagra behållaravbildningar i ett Azure Container Registry.

> [!div class="nextstepaction"]
> [Push-avbildningar med Azure Container Registry](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://hub.docker.com/r/library/node/tags/8.2.0-alpine/

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

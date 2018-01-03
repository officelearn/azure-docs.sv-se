---
title: "Azure Behållarinstanser tutorial – förbereda din app"
description: "Azure Behållarinstanser självstudiekursen del 1 av 3 – Förbereda en app för distribution till Azure-Behållarinstanser"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: fc16be80e776d1472be775fa32354ba157d16545
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Skapa behållare för distribution till Azure Container Instances

Azure Container Instances möjliggör distribution av Docker-behållare till en Azure-infrastruktur utan att tillhandahålla några virtuella datorer eller anpassa eventuella tjänster på högre nivå. I den här självstudiekursen, skapa ett litet webbprogram i Node.js och paketet i en behållare som kan köras med Azure Container instanser.

I den här artikeln ingår i serien du:

> [!div class="checklist"]
> * Klona programmets källkod från GitHub
> * Skapa en behållare avbildning från programmet källa
> * Testa bilden i en lokal Docker-miljö

I efterföljande självstudiekurser överför avbildningen till ett Azure Container registret och distribuera den till Azure-Behållarinstanser.

## <a name="before-you-begin"></a>Innan du börjar

Den här kursen kräver att du använder Azure CLI version 2.0.23 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera, se [installera Azure CLI 2.0][azure-cli-install].

Den här kursen förutsätter en grundläggande förståelse av grundläggande Docker begrepp som behållare, behållare avbildningar och basic `docker` kommandon. Om det behövs, se [Kom igång med Docker] [ docker-get-started] för en introduktion om grunderna i behållaren.

Den här kursen behöver en Docker-utvecklingsmiljö installeras lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på någon [Mac][docker-mac], [Windows][docker-windows], eller [Linux] [ docker-linux] system.

Azure Cloud Shell inkluderar inte Docker-komponenter som krävs för att slutföra varje steg den här kursen. På den lokala datorn för att slutföra den här guiden måste du installera Azure CLI och Docker-utvecklingsmiljö.

## <a name="get-application-code"></a>Hämta programkod

Exemplet i den här självstudiekursen innehåller en enkel webbapp som skapats [Node.js][nodejs]. Appen har en statisk HTML-sida och ser ut så här:

![Självstudieappen visas i webbläsare][aci-tutorial-app]

Ladda ned exempelfilerna med Git:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Bygga behållaravbildningen

Den Dockerfile som finns i exempelrepon visar hur behållaren är byggd. Den startar från en [officiella Node.js bild] [ docker-hub-nodeimage] baserat på [Alpine Linux][alpine-linux], en liten distribution som är lämplig för användning med behållare. Den kopierar sedan programfilerna till behållaren, installerar beroenden med Node Package Manager (nodpaketshanteraren) och startar slutligen programmet.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Använd den [docker build] [ docker-build] kommando för att skapa behållaren bilden märkning som *aci kursen app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Utdata från den [docker build] [ docker-build] kommando som liknar följande (trunkerad för läsbarhet):

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Använd den [docker bilder] [ docker-images] kommandot för att se den inbyggda avbildningen:

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

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli

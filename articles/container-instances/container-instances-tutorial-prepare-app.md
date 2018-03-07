---
title: "Självstudiekurs om Azure Container Instances – förbereda appen"
description: "Självstudiekurs om Azure Container Instances del 1 av 3 – förbereda en app för driftsättning till Azure Container Instances"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5012412ec642a04102836274caea253635376efb
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Skapa behållare för distribution till Azure Container Instances

Azure Container Instances möjliggör distribution av Docker-behållare till en Azure-infrastruktur utan att tillhandahålla några virtuella datorer eller anpassa eventuella tjänster på högre nivå. I den här självstudiekursen får du bygga ett enkelt webbprogram i Node.js och paketera den i en behållare som kan köras med Azure Container Instances.

I den här artikeln, som är del ett i serien, får du göra följande:

> [!div class="checklist"]
> * Klona programmets källkod från GitHub
> * Skapa en behållaravbildning från programkällan
> * Testa avbildningarna i en lokal Docker-miljö

I efterföljande självstudiekurser får du överföra avbildningen till Azure Container Registry och sedan distribuera den till Azure Container Instances.

## <a name="before-you-begin"></a>Innan du börjar

För den här självstudien krävs att du kör Azure CLI version 2.0.23 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0][azure-cli-install].

Den här självstudiekursen förutsätter grundläggande kunskaper om grundläggande Docker-begrepp som behållare, behållaravbildningar och grundläggande `docker`-kommandon. Om det behövs kan du läsa om hur du [kommer igång med Docker][docker-get-started] och få en genomgång om behållare.

För att slutföra den här självstudien behöver du en Docker-utvecklingsmiljö installerad lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-dator.

Azure Cloud Shell inkluderar inte de Docker-komponenter som krävs för att slutföra stegen i den här självstudien. Du måste installera Azure CLI och Docker-utvecklingsmiljön lokalt när du ska gå igenom den här självstudien.

## <a name="get-application-code"></a>Hämta programkod

Exemplet i den här självstudiekursen omfattar ett enkelt webbprogram som skapats i [Node.js][nodejs]. Appen har en statisk HTML-sida och ser ut så här:

![Självstudieappen visas i webbläsare][aci-tutorial-app]

Ladda ned exempelfilerna med Git:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>Bygga behållaravbildningen

Den Dockerfile som finns i exempelrepon visar hur behållaren är byggd. Den börjar från en [officiell Node.js-avbildning][docker-hub-nodeimage] baserat på [Alpine Linux][alpine-linux], en liten distribution som är lämplig för användning med behållare. Den kopierar sedan programfilerna till behållaren, installerar beroenden med Node Package Manager (nodpaketshanteraren) och startar slutligen programmet.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Använd kommandot [docker build][docker-build] (dockerbygge) för att skapa behållaravbildningen. Märk med *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Utdata från kommandot [docker build][docker-build] (dockerbygge) liknar följande (trunkerat för läsbarhet):

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

Använd kommandot [dockeravbildning][docker-images] för att se den skapade avbildningen:

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
> * Klonade programkällan från GitHub
> * Skapade behållaravbildningar från en programkälla
> * Testade behållaren lokalt

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

---
title: Bygg en Azure Container Registry-avbildning från en app
description: Använd kommandot AZ ACR Pack build för att bygga en behållar avbildning från en app och skicka till Azure Container Registry, utan att använda en Dockerfile.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/22/2019
ms.author: danlep
ms.openlocfilehash: 5100418651e24d74ad747e8c436ffce53c899a92
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500901"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Bygga och skicka en avbildning från en app med hjälp av en inbyggd Cloud-Buildpack

Azure CLI-kommandot `az acr pack build` [`pack`](https://github.com/buildpack/pack) använder CLI-verktyget, från [Buildpacks](https://buildpacks.io/), för att bygga en app och skicka avbildningen till ett Azure Container Registry. Den här funktionen ger ett alternativ för att snabbt skapa en behållar avbildning från program käll koden i Node. js, Java och andra språk utan att behöva definiera en Dockerfile.

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att köra exemplen i den här artikeln. Om du vill använda det lokalt, krävs version 2.0.70 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="use-the-build-command"></a>Använda kommandot build

Om du vill skapa och skicka en behållar avbildning med hjälp av inbyggda Cloud-Buildpacks kör du kommandot [AZ ACR Pack build][az-acr-pack-build] . [AZ ACR build][az-acr-build] -kommandot skapar och skickar en avbildning från en Dockerfile källa och relaterad kod, där `az acr pack build` du anger ett program käll träd direkt.

Ange minst följande när du kör `az acr pack build`:

* Ett Azure Container Registry där du kör kommandot
* Ett avbildnings namn och en tagg för den resulterande bilden
* En av de [kontext platser som stöds](container-registry-tasks-overview.md#quick-task) för ACR-aktiviteter, till exempel en lokal katalog, en GitHub-lagrings platsen eller en fjärran sluten tarball
* Namnet på en Buildpack Builder-avbildning, till exempel `cloudfoundry/cnb:bionic`.  

`az acr pack build`stöder andra funktioner i ACR tasks-kommandon, inklusive [körning av variabler](container-registry-tasks-reference-yaml.md#run-variables) och körnings [loggar för uppgifter](container-registry-tasks-overview.md#view-task-logs) som strömmas och som också sparas för senare hämtning.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Exempel: Bygg Node. js-avbildning med Cloud Foundry Builder

I följande exempel skapas en behållar avbildning från Node. js-appen i avsnittet [Azure-samples/NodeJS-dokument-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) lagrings platsen `cloudfoundry/cnb:bionic` med hjälp av verktyget:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

I det här exemplet `node-app` skapas avbildningen `1.0` med taggen och skickas till registret för *behållar* registret. Här är mål register namnet uttryckligen anpassningsprefix till avbildnings namnet. Om detta inte anges, anpassningsprefix-registrerings-URL: en automatiskt till avbildnings namnet.

Kommandoutdata visar förloppet för att skapa och skicka avbildningen. 

När avbildningen har skapats kan du köra den med Docker, om du har den installerad. Logga först in i registret:

```azurecli
az acr login --name myregistry
```

Kör avbildningen:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Bläddra till `localhost:1337` i din favorit webbläsare för att se exempel webb programmet. Tryck `[Ctrl]+[C]` för att stoppa behållaren.

## <a name="example-build-java-image-with-heroku-builder"></a>Exempel: Bygga Java-avbildning med Heroku Builder

I följande exempel skapas en behållar avbildning från Java-appen i [buildpack/Sample-java-app](https://github.com/buildpack/sample-java-app) lagrings platsen med hjälp `heroku/buildpacks:18` av verktyget:

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

I det här exemplet `java-app` skapas en avbildning som taggats med kommandots körnings-ID och push-överför den till registret för *behållar* behållaren.

`--pull` Parametern anger att kommandot hämtar den senaste Builder-avbildningen, vilket är nödvändigt eftersom Heroku Builder-avbildningen inte cachelagras av ACR-aktiviteter.

Kommandoutdata visar förloppet för att skapa och skicka avbildningen. 

När avbildningen har skapats kan du köra den med Docker, om du har den installerad. Logga först in i registret:

```azurecli
az acr login --name myregistry
```

Kör avbildningen och ersätt din avbildnings tag för *RunId*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Bläddra till `localhost:8080` i din favorit webbläsare för att se exempel webb programmet. Tryck `[Ctrl]+[C]` för att stoppa behållaren.


## <a name="next-steps"></a>Nästa steg

När du har skapat och push-överför en `az acr pack build`behållar avbildning med kan du distribuera den som valfri avbildning till önskat mål. Azures distributions alternativ omfattar att köra det i [App Service](../app-service/containers/tutorial-custom-docker-image.md) [-eller Azure Kubernetes-tjänsten](../aks/tutorial-kubernetes-deploy-cluster.md), bland annat.

Mer information om funktioner för ACR-funktioner finns i [Automatisera behållar avbildnings versioner och underhåll med ACR-uppgifter](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

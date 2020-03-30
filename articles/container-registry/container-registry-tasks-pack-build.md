---
title: Skapa avbildning med Cloud Native Buildpack
description: Använd kommandot az acr pack build för att skapa en behållaravbildning från en app och skicka till Azure Container Registry, utan att använda en Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087074"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Skapa och skicka en avbildning från en app med hjälp av ett Cloud Native Buildpack

Azure CLI-kommandot `az acr pack build` [`pack`](https://github.com/buildpack/pack) använder CLI-verktyget, från [Buildpacks](https://buildpacks.io/), för att skapa en app och skicka sin avbildning till ett Azure-behållarregister. Den här funktionen ger ett alternativ för att snabbt skapa en behållaravbildning från programmets källkod i Node.js, Java och andra språk utan att behöva definiera en Dockerfile.

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att köra exemplen i den här artikeln. Om du vill använda den lokalt krävs version 2.0.70 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="use-the-build-command"></a>Använd kommandot build

Om du vill skapa och driva en behållaravbildning med Cloud Native Buildpacks kör du kommandot [az acr pack build.][az-acr-pack-build] Medan az [acr build-kommandot][az-acr-build] bygger och skickar en avbildning från en `az acr pack build` Dockerfile-källa och relaterad kod, med dig ange ett programkällträd direkt.

Ange minst följande när du `az acr pack build`kör:

* Ett Azure-behållarregister där du kör kommandot
* Ett bildnamn och en tagg för den resulterande bilden
* En av [kontextplatserna som stöds](container-registry-tasks-overview.md#context-locations) för ACR-uppgifter, till exempel en lokal katalog, en GitHub-repo eller en fjärrtarball
* Namnet på en Buildpack-builder-avbildning som passar ditt program. Azure Container Registry cachelagrar `cloudfoundry/cnb:0.0.34-cflinuxfs3` byggare avbildningar, till exempel för snabbare versioner.  

`az acr pack build`stöder andra funktioner i ACR-uppgifter kommandon, inklusive [kör variabler](container-registry-tasks-reference-yaml.md#run-variables) och [aktivitetskörningsloggar](container-registry-tasks-logs.md) som strömmas och även sparas för senare hämtning.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Exempel: Version Node.js-avbildning med Cloud Foundry builder

I följande exempel skapas en behållaravbildning från en Node.js-app i [Azure-Samples/nodejs-docs-hello-world repo](https://github.com/Azure-Samples/nodejs-docs-hello-world) med hjälp av `cloudfoundry/cnb:0.0.34-cflinuxfs3` byggaren. Den här byggaren cachelagras av `--pull` Azure Container Registry, så en parameter krävs inte:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Det här exemplet `node-app` bygger `1.0` avbildningen med taggen och skickar den till *registret.* I det här exemplet förbereds målregisternamnet uttryckligen till avbildningsnamnet. Om inget anges förbereds registrets inloggningsservernamn automatiskt till avbildningsnamnet.

Kommandoutdata visar förloppet för att bygga och trycka på bilden. 

När avbildningen har skapats kan du köra den med Docker om du har installerat den. Logga först in i registret:

```azurecli
az acr login --name myregistry
```

Kör bilden:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Bläddra `localhost:1337` till i din favoritwebbläsare för att se exempelwebbappen. Tryck `[Ctrl]+[C]` för att stoppa behållaren.

## <a name="example-build-java-image-with-heroku-builder"></a>Exempel: Skapa Java-avbildning med Heroku-byggare

I följande exempel skapas en behållaravbildning från Java-appen i reporäntan `heroku/buildpacks:18` [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) med hjälp av byggaren. Parametern `--pull` anger att kommandot ska hämta den senaste byggaravbildningen. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

I det här `java-app` exemplet skapas avbildningen som taggats med kommandots körnings-ID och den ska skickas till *registrets* behållarregister.

Kommandoutdata visar förloppet för att bygga och trycka på bilden. 

När avbildningen har skapats kan du köra den med Docker om du har installerat den. Logga först in i registret:

```azurecli
az acr login --name myregistry
```

Kör bilden och ersätter bildtaggen för *runid:*

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Bläddra `localhost:8080` till i din favoritwebbläsare för att se exempelwebbappen. Tryck `[Ctrl]+[C]` för att stoppa behållaren.


## <a name="next-steps"></a>Nästa steg

När du har byggt och `az acr pack build`pusha en behållaravbildning med kan du distribuera den som vilken avbildning som helst till ett mål som du väljer. Azure-distributionsalternativ inkluderar att köra den i [App Service](../app-service/containers/tutorial-custom-docker-image.md) eller [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md), bland annat.

Mer information om funktioner för ACR-uppgifter finns i [Automatisera behållaravbildningsversioner och underhåll med ACR-uppgifter](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build

---
title: Push & dra Docker bild
description: Skicka och hämta Docker-avbildningar till ett privat containerregister i Azure med hjälp av Docker CLI
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 6751a04c3c1bfe826334161704c20c1ba2e5a6d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456356"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Skicka din första avbildning till ett privat Docker-containerregister med hjälp av Docker CLI

Ett Azure-containerregister lagrar och hanterar privata [Docker](https://hub.docker.com)-containeravbildningar, på samma sätt som [Docker Hub](https://hub.docker.com/) lagrar offentliga Docker-avbildningar. Du kan använda [Docker-kommandoradsgränssnittet](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) för [inloggning,](https://docs.docker.com/engine/reference/commandline/login/) [push,](https://docs.docker.com/engine/reference/commandline/push/) [pull](https://docs.docker.com/engine/reference/commandline/pull/)och andra åtgärder i behållarregistret.

I följande steg hämtar du en officiell [Nginx-avbildning](https://store.docker.com/images/nginx) från det offentliga Docker Hub-registret, taggar den för ditt privata Azure-behållarregister, skickar det till registret och hämtar den sedan från registret.

## <a name="prerequisites"></a>Krav

* **Azure-containerregister** – Skapa ett containerregister i din Azure-prenumeration. Använd till exempel [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** - Du måste också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.

## <a name="log-in-to-a-registry"></a>Logga in i ett register

Det finns [flera sätt att autentisera](container-registry-authentication.md) till ditt privata behållarregister. Den rekommenderade metoden när du arbetar på en kommandorad är med Azure CLI-kommandot [az acr-inloggning](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Till exempel för att logga in på ett register med namnet *MyRegistry:*

```azurecli
az acr login --name myregistry
```

Du kan också logga in med [docker inloggning](https://docs.docker.com/engine/reference/commandline/login/). Du kan till exempel ha [tilldelat ett tjänsthuvudnamn](container-registry-authentication.md#service-principal) till registret för ett automatiseringsscenario. När du kör följande kommando anger du interaktivt tjänstens huvudnamnappID (användarnamn) och lösenord när du uppmanas att göra det. Metodtips för att hantera inloggningsuppgifter finns i kommandotreferens [för docker:For](https://docs.docker.com/engine/reference/commandline/login/) best practices to manage login credentials, see the docker login command reference:

```
docker login myregistry.azurecr.io
```

Båda kommandona `Login Succeeded` returneras när de är klara.

> [!TIP]
> Ange alltid det fullständigt kvalificerade registernamnet (alla `docker login` gemener) när du använder och när du taggar avbildningar för att du skickar till registret. I exemplen i den här artikeln är det fullständigt kvalificerade namnet *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Dra den officiella Nginx-bilden

Dra först den offentliga Nginx-avbildningen till den lokala datorn.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Kör containern lokalt

Kör följande [docker-körningskommando](https://docs.docker.com/engine/reference/run/) för att starta en lokal`-it`instans av Nginx-behållaren interaktivt ( ) på port 8080. Argumentet `--rm` anger att behållaren ska tas bort när du stoppar den.

```
docker run -it --rm -p 8080:80 nginx
```

Bläddra `http://localhost:8080` till om du vill visa standardwebbsidan som visas av Nginx i behållaren som körs. Du bör se en sida som liknar följande:

![Nginx på lokal dator](./media/container-registry-get-started-docker-cli/nginx.png)

Eftersom du startade behållaren `-it`interaktivt med kan du se Nginx-serverns utdata på kommandoraden efter att du har navigerat till den i webbläsaren.

Om du vill stoppa `Control` + `C`och ta bort behållaren trycker du på .

## <a name="create-an-alias-of-the-image"></a>Skapa ett alias för bilden

Använd [docker-taggen](https://docs.docker.com/engine/reference/commandline/tag/) för att skapa ett alias för avbildningen med den fullständigt kvalificerade sökvägen till registret. I det här exemplet anges `samples`-namnområdet för att undvika oreda i registrets rot.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Mer information om hur du taggar med namnområden finns i avsnittet [Databasnamnområden](container-registry-best-practices.md#repository-namespaces) [i metodtips för Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Skicka avbildningen till registret

Nu när du har taggat avbildningen med den fullt kvalificerade sökvägen till ditt privata register kan du skicka den till registret med [docker push:](https://docs.docker.com/engine/reference/commandline/push/)

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Hämta avbildningen från registret

Använd [docker pull-kommandot](https://docs.docker.com/engine/reference/commandline/pull/) för att hämta avbildningen från registret:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Starta Nginx-containern

Använd [kommandot docker run](https://docs.docker.com/engine/reference/run/) för att köra avbildningen som du har hämtat från registret:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Bläddra `http://localhost:8080` till för att visa den löpande behållaren.

Om du vill stoppa `Control` + `C`och ta bort behållaren trycker du på .

## <a name="remove-the-image-optional"></a>Ta bort bilden (valfritt)

Om du inte längre behöver Nginx-avbildningen kan du ta bort den lokalt med [kommandot docker rmi.](https://docs.docker.com/engine/reference/commandline/rmi/)

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Om du vill ta bort avbildningar från ditt Azure-behållarregister kan du använda azure CLI-kommandot [az acr-databasborttagning](/cli/azure/acr/repository#az-acr-repository-delete). Följande kommando tar till exempel bort manifestet `samples/nginx:latest` som refereras av taggen, unika lagerdata och alla andra taggar som refererar till manifestet.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet grunderna, du är redo att börja använda ditt register! Distribuera till exempel behållaravbildningar från registret till:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Installera eventuellt [Docker-tillägget för Visual Studio-kod](https://code.visualstudio.com/docs/azure/docker) och [Azure-kontotillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) för att arbeta med dina Azure-behållarregister. Hämta och skicka avbildningar till ett Azure-behållarregister eller kör ACR-uppgifter, allt i Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

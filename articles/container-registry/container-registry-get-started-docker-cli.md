---
title: Överför Docker-avbildningen till privat Azure container registry
description: Skicka och hämta Docker-avbildningar till ett privat containerregister i Azure med hjälp av Docker CLI
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 2cb401dfd68075ff0867ae3f89eee3474000b5de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828769"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Skicka din första avbildning till ett privat Docker-containerregister med hjälp av Docker CLI

Ett Azure-containerregister lagrar och hanterar privata [Docker](https://hub.docker.com)-containeravbildningar, på samma sätt som [Docker Hub](https://hub.docker.com/) lagrar offentliga Docker-avbildningar. Du kan använda den [Docker-kommandoradsgränssnittet](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) för [inloggning](https://docs.docker.com/engine/reference/commandline/login/), [push](https://docs.docker.com/engine/reference/commandline/push/), [pull](https://docs.docker.com/engine/reference/commandline/pull/), och andra åtgärder i din behållare registret.

I följande steg ska du ladda ned en officiell [Nginx-avbildningen](https://store.docker.com/images/nginx) från det offentliga Docker Hub-registret, tagga för privat Azure container registry, överför den till registret och hämta den från registret.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Azure-containerregister** – Skapa ett containerregister i din Azure-prenumeration. Till exempel använda den [Azure-portalen](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** -du måste också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.

## <a name="log-in-to-a-registry"></a>Logga in i ett register

Det finns [flera olika sätt att autentisera](container-registry-authentication.md) till ditt privata behållarregister. När du arbetar i en kommandorad rekommenderas med Azure CLI-kommando [docker login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Till exempel att logga in till ett register med namnet *myregistry*:

```azurecli
az acr login --name myregistry
```

Du kan också logga in med [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/). Du kan till exempel ha [tilldelat ett tjänstobjekt](container-registry-authentication.md#service-principal) till registret för ett automation-scenario. När du kör följande kommando interaktivt ange tjänstens huvudnamn appID (användarnamn) och lösenord när du uppmanas till detta. Metodtips att hantera autentiseringsuppgifter för inloggning, finns i den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot referens:

```
docker login myregistry.azurecr.io
```

Båda kommandona returnerar `Login Succeeded` när det har slutförts.

> [!TIP]
> Ange alltid fullständigt kvalificerade registernamnet (endast gemener) när du använder `docker login` och när du tagga bilder för push-överföring till registret. I exemplen i den här artikeln är det fullständigt kvalificerade namnet är *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Hämta den officiella Nginx-avbildningen

Först hämta den offentliga Nginx-avbildningen till den lokala datorn.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Kör containern lokalt

Kör följande [docker kör](https://docs.docker.com/engine/reference/run/) kommando för att starta en lokal instans av Nginx-behållaren interaktivt (`-it`) på port 8080. Den `--rm` argumentet anger att behållaren ska tas bort när du stoppar den.

```
docker run -it --rm -p 8080:80 nginx
```

Bläddra till `http://localhost:8080` att visa standardwebbsidan som hanteras av Nginx i behållaren som körs. Du bör se en sida som liknar följande:

![Nginx på lokal dator](./media/container-registry-get-started-docker-cli/nginx.png)

Eftersom du startade behållaren interaktivt med `-it`, du kan se utdata från Nginx-servern på kommandoraden när du har lämnat till den i webbläsaren.

Stoppa och ta bort behållaren genom att trycka på `Control` + `C`.

## <a name="create-an-alias-of-the-image"></a>Skapa ett alias för avbildningen

Använd [dockertagg](https://docs.docker.com/engine/reference/commandline/tag/) att skapa ett alias för avbildningen med den fullständiga sökvägen till registret. I det här exemplet anges `samples`-namnområdet för att undvika oreda i registrets rot.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Mer information om taggar med namnområden finns i den [namnrymder](container-registry-best-practices.md#repository-namespaces) delen av [bästa praxis för Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Överför avbildningen till registret

Nu när du har taggat avbildningen med den fullständiga sökvägen till ditt privata register, kan du skicka den till registret med [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Hämta avbildningen från registret

Använd den [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) kommando för att hämta avbildningen från registret:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Starta Nginx-behållaren

Använd den [docker kör](https://docs.docker.com/engine/reference/run/) kommando för att köra avbildningen du har samlat från ditt register:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Bläddra till `http://localhost:8080` att visa behållaren som körs.

Stoppa och ta bort behållaren genom att trycka på `Control` + `C`.

## <a name="remove-the-image-optional"></a>Ta bort avbildningen (valfritt)

Om du inte längre behöver Nginx-avbildningen kan du radera den lokalt med den [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) kommando.

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Du kan använda Azure CLI-kommando för att ta bort avbildningar från Azure container registry [az acr databasen delete](/cli/azure/acr/repository#az-acr-repository-delete). Till exempel följande kommando tar bort manifestet refererar till den `samples/nginx:latest` tagg, alla unika layer-data och alla andra taggar som refererar till manifestet.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Nästa steg

Nu när du känner till grunderna är redo du att börja använda registret! Till exempel distribuera behållaravbildningar från ditt register till:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Du kan också installera den [Docker-tillägg för Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) och [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) tillägg att arbeta med din Azure-behållarregister. Hämta och push-överför avbildningar till ett Azure container registry eller köra ACR aktiviteter, allt inom Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

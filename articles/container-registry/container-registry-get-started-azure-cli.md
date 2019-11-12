---
title: Snabb start – skapa register – Azure CLI – Azure Container Registry
description: Lär dig snabbt att skapa ett privat Docker-containerregister med Azure CLI.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 01/22/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017, mvc
ms.openlocfilehash: 562f8d41e1f91e408263809bdfc78905317912a1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931605"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Snabb start: skapa ett privat behållar register med hjälp av Azure CLI

Azure Container Registry är en hanterad Docker-behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. Den här guiden beskriver hur du skapar en Azure Container Registry-instans med hjälp av Azure CLI. Använd sedan Docker-kommandon för att skicka en containeravbildning till registret, och hämta och kör slutligen avbildningen från registret.

För den här snabbstarten krävs att du kör Azure CLI (version 2.0.55 eller senare rekommenderas). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

Du måste också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på alla [MacOS][docker-mac]-, [Windows][docker-windows]-och [Linux][docker-linux] -system.

Eftersom Azure Cloud Shell inte innehåller alla nödvändiga Docker-komponenter (`dockerd`-daemon), kan du inte använda Cloud Shell för denna snabbstart.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create][az-group-create]. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Skapa ett containerregister

I den här snabbstarten skapar du ett *Basic*-register, vilket är ett kostnadsoptimerat alternativ för utvecklare som lär sig om Azure Container Registry. Mer information om tillgängliga tjänst nivåer finns i [SKU: er för container Registry][container-registry-skus].

Skapa en ACR-instans med hjälp av kommandot [az acr create][az-acr-create]. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. I följande exempel används *myContainerRegistry007*. Uppdatera det här till ett unikt värde.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

När registret har skapats ser utdata ut ungefär så här:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Anteckna `loginServer` i utdata, vilket är de fullständigt kvalificerade registernamnet (endast gemener). I resten av den här snabbstarten använder du `<acrName>` som platshållare för namnet på containerregistret.

## <a name="log-in-to-registry"></a>Logga in till registret

Innan du skickar och hämtar containeravbildningar måste du logga in i registret. Det gör du med hjälp av kommandot [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar meddelandet `Login Succeeded` när det har slutförts.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Visa lista över containeravbildningar

I följande exempel visas lagringsplatserna i ditt register:

```azurecli
az acr repository list --name <acrName> --output table
```

Resultat:

```
Result
----------------
hello-world
```

I följande exempel visas taggarna i databasen **Hello-World** .

```azurecli
az acr repository show-tags --name <acrName> --repository hello-world --output table
```

Resultat:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete][az-group-delete] för att ta bort resurs gruppen, behållar registret och behållar avbildningarna som lagras där.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Azure Container Registry med Azure CLI, push-överförde en containeravbildning till registret och hämtade och körde avbildningen från registret. Fortsätt till självstudien om Azure Container Registry om du vill titta närmare på ACR.

> [!div class="nextstepaction"]
> [Azure Container Registry själv studie kurser][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md

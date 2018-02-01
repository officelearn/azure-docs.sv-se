---
title: "Snabbstart – skapa ett privat Docker-register i Azure med Azure CLI"
description: "Lär dig snabbt att skapa ett privat Docker-behållarregister med Azure CLI."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/07/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: a74a1ce5c9401d6445f5feec4af8d5cb771d2c64
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Skapa ett behållarregister med hjälp av Azure CLI

Azure Container Registry är en hanterad Docker-behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. Den här guiden beskriver hur du skapar en Azure Container Registry-instans med hjälp av Azure CLI.

För den här snabbstarten krävs att du kör Azure CLI version 2.0.25 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0][azure-cli].

Du måste också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-dator.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create][az-group-create]. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Skapa ett behållarregister

I den här snabbstarten skapar vi ett *Grundläggande* register. Azure Container Registry finns i flera olika SKU:er, som beskrivs kortfattat i följande tabell. Mer information om var och en finns i [SKU:er för Container Registry][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Skapa en ACR-instans med hjälp av kommandot [az acr create][az-acr-create].

Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. I följande exempel används *myContainerRegistry007*. Uppdatera det här till ett unikt värde.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

När registret har skapats ser utdata ut ungefär så här:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
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

I resten av den här snabbstarten använder vi `<acrName>` som platshållare för namnet på behållarregistret.

## <a name="log-in-to-acr"></a>Logga in på ACR

Innan du skickar och hämtar behållaravbildningar måste du logga in på ACR-instansen. Det gör du med hjälp av kommandot [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar meddelandet `Login Succeeded` när det har slutförts.

## <a name="push-image-to-acr"></a>Push-överföra avbildning till ACR

Innan du kan push-överföra en avbildning till Azure Container Registry måste du ha en avbildning. Om du inte har några lokala behållaravbildningar ännu kan du köra följande kommando för att hämta en befintlig avbildning från Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Innan du kan push-överföra en avbildning till ditt register måste du tagga den med det fullständiga namnet på din ACR-inloggningsserver. Kör följande kommando för att hämta det fullständiga namnet på inloggningsservern för ACR-instansen.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Tagga avbildningen med hjälp av kommandot [docker tag][docker-tag]. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för ACR-instansen.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Använd slutligen [docker push][docker-push] för att överföra avbildningen till ACR-instansen. Ersätt `<acrLoginServer>` med namnet på inloggningsservern för ACR-instansen.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Visa lista över behållaravbildningar

I följande exempel visas lagringsplatserna i ett register:

```azurecli
az acr repository list --name <acrName> --output table
```

Resultat:

```bash
Result
----------------
aci-helloworld
```

I följande exempel visas taggarna för lagringsplatsen **aci-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Resultat:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs kan du använda kommandot [az group delete][az-group-delete] till att ta bort resursgruppen, ACR-instansen och alla behållaravbildningar.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Azure Container Registry med Azure CLI. Om du vill använda Azure Container Registry med Azure Container Instances fortsätter du till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Självstudie för Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
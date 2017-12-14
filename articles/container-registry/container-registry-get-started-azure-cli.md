---
title: Snabbstart - skapa ett privat Docker-register i Azure med Azure CLI
description: "Lär dig snabbt skapa en privat Docker behållare registret med Azure CLI."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 12/07/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f31f4e5e2b3fe5db85873894a7f2fa9c415392c1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Skapa ett behållarregister med hjälp av Azure CLI

Azure Container Registry är en hanterad Docker-behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. Den här guiden information att skapa en Azure-behållare registret-instans med hjälp av Azure CLI.

Denna Snabbstart kräver att du använder Azure CLI version 2.0.21 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera, se [installera Azure CLI 2.0][azure-cli].

Du måste också ha Docker installeras lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på någon [Mac][docker-mac], [Windows][docker-windows], eller [Linux] [ docker-linux] system.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create][az-group-create]. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Skapa ett behållarregister

I den här snabbstarten skapar vi en *grundläggande* registret. Azure Container registret är tillgänglig i flera olika SKU: er, beskrivs kortfattat i följande tabell. Utökad information om varje finns [behållare registret SKU: er][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Skapa en ACR instans med hjälp av den [az acr skapa] [ az-acr-create] kommando.

Namnet på registret **måste vara unika**. I följande exempel *myContainerRegistry007* används. Uppdatera den till ett unikt värde.

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

I resten av den här snabbstarten vi använder `<acrName>` som platshållare för registret behållarnamn.

## <a name="log-in-to-acr"></a>Logga in på ACR

Innan du skickar och hämtar behållaravbildningar måste du logga in på ACR-instansen. Det gör du genom att använda den [az acr inloggning] [ az-acr-login] kommando.

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar en `Login Succeeded` meddelande när den har slutförts.

## <a name="push-image-to-acr"></a>Push-avbildningen till ACR

Du måste ha en bild för att vidarebefordra en avbildning till en Azure-behållare registret. Om du ännu inte har några lokala behållaren bilder, kör du följande kommando för att hämta en befintlig avbildning från Docker-hubb.

```bash
docker pull microsoft/aci-helloworld
```

Innan du kan pressa en bild i registret, måste du tagga den med det fullständigt kvalificerade namnet på din ACR inloggningsserver. Kör följande kommando för att få fullständig inloggningsnamnet server för ACR-instans.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Taggen bild med hjälp av den [docker-taggen] [ docker-tag] kommando. Ersätt `<acrLoginServer>` med inloggningen servernamnet för din ACR-instans.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Använd slutligen [docker push] [ docker-push] att skicka bilden till ACR-instans. Ersätt `<acrLoginServer>` med inloggningen servernamnet för din ACR-instans.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Visa lista över behållaravbildningar

I följande exempel visar en lista över databaser i en registret:

```azurecli
az acr repository list --name <acrName> --output table
```

Resultat:

```bash
Result
----------------
aci-helloworld
```

I följande exempel visar taggar på den **aci helloworld** databasen.

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

När du inte längre behövs kan du använda den [ta bort grupp az] [ az-group-delete] för att ta bort resursgruppen, ACR-instans och alla behållare bilder.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapat du ett Azure Container registret med Azure CLI. Om du vill använda Azure Container registret med Azure Container instanser fortsätta att Azure Behållarinstanser kursen.

> [!div class="nextstepaction"]
> [Azure Behållarinstanser självstudiekursen][container-instances-tutorial-prepare-app]

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
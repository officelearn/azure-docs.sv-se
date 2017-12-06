---
title: Snabbstart - skapa ett privat Docker-register i Azure med Azure CLI
description: "Lär dig snabbt skapa en privat Docker behållare registret med Azure CLI."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 10/16/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 5cddf0ffea256ed6d1c51d48a61ac8176d08b9cc
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Skapa ett behållarregister med hjälp av Azure CLI

Azure Container Registry är en hanterad Docker-behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. Den här guiden information att skapa en Azure-behållare registret-instans med hjälp av Azure CLI.

Denna Snabbstart kräver att du använder Azure CLI version 2.0.20 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli).

Du måste också ha Docker installeras lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac-](https://docs.docker.com/docker-for-mac/), [Windows-](https://docs.docker.com/docker-for-windows/) eller [Linux-](https://docs.docker.com/engine/installation/#supported-platforms)dator.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Skapa ett behållarregister

I den här snabbstarten skapar vi en *grundläggande* registret. Azure Container registret är tillgänglig i flera olika SKU: er, beskrivs kortfattat i följande tabell. Utökad information om varje finns [behållare registret SKU: er](container-registry-skus.md).

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Skapa en ACR-instans med hjälp av kommandot [az acr create](/cli/azure/acr#create).

Namnet på registret **måste vara unika**. I följande exempel *myContainerRegistry007* används. Uppdatera den till ett unikt värde.

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --sku Basic
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
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

I resten av den här snabbstarten vi använder `<acrname>` som platshållare för registret behållarnamn.

## <a name="log-in-to-acr"></a>Logga in på ACR

Innan du skickar och hämtar behållaravbildningar måste du logga in på ACR-instansen. Det gör du med hjälp av kommandot [az acr login](/cli/azure/acr#login).

```azurecli
az acr login --name <acrname>
```

Ett meddelande om att inloggningen lyckades returneras när inloggningen är klar.

## <a name="push-image-to-acr"></a>Push-avbildningen till ACR

Du måste ha en bild för att vidarebefordra en avbildning till en Azure-behållare registret. Om det behövs, kör du följande kommando för att hämta en avbildning som skapats i förväg från Docker-hubb.

```bash
docker pull microsoft/aci-helloworld
```

Bilden måste taggas med ACR server inloggningsnamnet. Kör följande kommando för att gå tillbaka inloggning servernamnet för ACR-instans.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Taggen bild med hjälp av den [docker-taggen](https://docs.docker.com/engine/reference/commandline/tag/) kommando. Ersätt  *<acrLoginServer>*  med inloggningen servernamnet för din ACR-instans.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Använd slutligen [docker push](https://docs.docker.com/engine/reference/commandline/push/) att skicka bilden till ACR-instans. Ersätt  *<acrLoginServer>*  med inloggningen servernamnet för din ACR-instans.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Visa lista över behållaravbildningar

I följande exempel visar en lista över databaser i en registret:

```azurecli
az acr repository list -n <acrname> -o table
```

Resultat:

```bash
Result
----------------
aci-helloworld
```

I följande exempel visar taggar på den **aci helloworld** databasen.

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

Resultat:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda den [ta bort grupp az](/cli/azure/group#delete) kommando för att ta bort resursgruppen, ACR-instans och alla behållare bilder.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapat du ett Azure Container registret med Azure CLI. Om du vill använda Azure Container registret med Azure Container instanser fortsätta att Azure Behållarinstanser kursen.

> [!div class="nextstepaction"]
> [Azure Behållarinstanser självstudiekursen](../container-instances/container-instances-tutorial-prepare-app.md)
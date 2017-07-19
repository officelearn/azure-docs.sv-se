---
title: "Skapa ett behållarregister med hjälp av Azure CLI | Microsoft Docs"
description: "Kom igång med att skapa och hantera privata Dockerbehållarregister med Azure CLI 2.0"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: sv-se
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>Skapa ett hanterat behållarregister med hjälp av Azure CLI

Azure Container Registry är en hanterad Docker-behållarregistertjänst som används för att lagra privata Docker-behållaravbildningar. Den här guiden beskriver hur du skapar en hanterad Azure Container Registry-instans med hjälp av Azure CLI.

Hanterade Azure-behållarregister finns endast som förhandsversion och är inte tillgängliga i alla regioner.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westcentralus*.

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>Skapa ett behållarregister

Skapa en ACR-instans med hjälp av kommandot [az acr create](/cli/azure/acr#create).

> [!NOTE]
> När du skapar ett register anger du ett globalt unikt domännamn på den översta nivån, som endast innehåller bokstäver och siffror.

 Registernamnet i exemplet är *myContainerRegistry1*, men du kan ersätta det med ett eget unikt namn.

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

När registret har skapats ser utdata ut ungefär så här:

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>Logga in på ACR-instansen

Innan du skickar och hämtar behållaravbildningar måste du logga in på ACR-instansen. Det gör du med hjälp av kommandot [az acr login](/cli/azure/acr#login).

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

Ett meddelande om att inloggningen lyckades returneras när inloggningen är klar.

## <a name="use-azure-container-registry"></a>Använda Azure Container Registry

### <a name="list-container-images"></a>Visa lista över behållaravbildningar

Använd `az acr` CLI-kommandona för att skicka frågor mot avbildningarna och taggarna på en lagringsplats.

> [!NOTE]
> För närvarande stöder inte Container Registry `docker search`-kommandot för att hämta information om avbildningar och taggar.

### <a name="list-repositories"></a>Visa en lista över lagringsplatser

Följande exempel visar en lista över lagringsplatser i ett register, i JSON-format (JavaScript Object Notation):

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Visa en lista över taggar

Följande exempel visar en lista med taggarna som används för **samples/nginx**-lagringsplatsen, i JSON-format:

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en hanterad Azure Container Registry-instans med hjälp av Azure CLI.

> [!div class="nextstepaction"]
> [Skicka din första avbildning med hjälp av Docker CLI](container-registry-get-started-docker-cli.md)


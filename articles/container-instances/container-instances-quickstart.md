---
title: "Skapa din första Azure Container Instances-behållare | Azure Docs"
description: "Distribuera och kom igång med Azure Container Instances"
services: container-service
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: 
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 3b15d6645b988f69f1f05b27aff6f726f34786fc
ms.openlocfilehash: 933299ce5a5d6f5b2262d40ae768019ccaf8796a
ms.contentlocale: sv-se
ms.lasthandoff: 07/26/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Skapa din första behållare i Azure Container Instances

Med Azure Container Instances är det enkelt att skapa och hantera behållare i Azure. I den här snabbstartsguiden ska du skapa en behållare i Azure och göra den tillgänglig på Internet med en offentlig IP-adress. Den här åtgärden utförs med ett enda kommando. Inom några sekunder visas det här i webbläsaren:

![App som distribuerats via Azure Container Instances visas i webbläsare][aci-app-browser]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Container Instances CLI-kommandon är för närvarande endast tillgängliga i Azure Cloud Shell.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Container Instances är Azure-resurser och måste vara placerade i en Azure-resursgrupp, en logisk samling dit Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Skapa en behållare

Du kan skapa en behållare genom att ange ett namn, en Docker-avbildning och en Azure-resursgrupp. Du kan också göra behållaren tillgänglig på Internet med en offentlig IP-adress. I så fall använder vi en behållare som är värd för en väldigt enkel webbapp skriven i [Node.js](http://nodejs.org).

Azure Container Instances CLI-kommandon är för närvarande endast tillgängliga i Azure Cloud Shell.

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public 
```

Om några sekunder bör du få ett svar på din begäran. Först har behållaren statusen **Creating** (skapas) men bör starta inom några sekunder. Du kan kontrollera statusen med kommandot `show`:

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

Längst ned i resultatet visas behållarens etableringsstatus och IP-adress:

```json
...
"ipAddress": {
      "ip": "13.88.8.148",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

När behållaren övergår i status **Succeeded** (lyckades) kan du nå den via webbläsaren med den angivna IP-adressen. 

![App som distribuerats via Azure Container Instances visas i webbläsare][aci-app-browser]

## <a name="pull-the-container-logs"></a>Hämta behållarloggarna

Du kan hämta loggarna för den behållare du skapat med kommandot `logs`:

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

Resultat:

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>Ta bort behållaren

När du är klar med behållaren kan du ta bort den med kommandot `delete`:

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

All kod för behållaren som används i den här snabbstartsguiden finns [på GitHub][app-github-repo] tillsammans med dess Dockerfile. Om du vill försöka skapa den på egen hand och distribuera den till Azure Container Instances via Azure Container Registry, går du vidare till självstudierna för Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudier](./container-instances-tutorial-prepare-app.md)


<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

---
title: Snabbstart – Skapa din första Azure Container Instances-behållare | Azure Docs
description: I den här snabbstarten använder du kommandogränssnittet i Azure för att distribuera en behållare i Azure Container Instances
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/19/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: b85c38bb561e4f1dc9a0545595590719ce1883e4
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Snabbstart: Skapa din första behållare i Azure Container Instances

Azure Container Instances gör det enkelt att skapa och hantera Docker-behållare i Azure, utan att behöva etablera virtuella datorer eller gå upp till en högre tjänstnivå. I den här snabbstarten skapar du en behållare i Azure och gör den tillgänglig på Internet med ett fullständigt kvalificerat domännamn (FQDN). Den här åtgärden utförs med ett enda kommando. Inom några sekunder visas det här i webbläsaren:

![App som distribuerats via Azure Container Instances visas i webbläsare][aci-app-browser]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][azure-account] konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att genomföra den här snabbstarten. Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.27 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0][azure-cli-install].

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Container Instances måste precis som alla Azure-resurser placeras i en Azure-resursgrupp, en logisk samling dit Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create][az-group-create].

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Skapa en behållare

Du kan skapa en behållare genom att ange ett namn, en dockeravbildning och en Azure-resursgrupp med kommandot [az container create][az-container-create]. Du kan också göra behållaren tillgänglig på Internet genom att ange en DNS-namnetikett. I den här snabbstarten distribuerar du en behållare som är värd för en liten webbapp som skrivits i [Node.js][node-js].

Kör följande kommando för att starta en instans i behållaren. Värdet `--dns-name-label` måste vara unikt i den Azure-region där du skapar instansen, så du kan behöva ändra värdet för att det ska vara unikt.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

Om några sekunder bör du få ett svar på din begäran. Först har behållaren statusen **Creating** (skapas) men den bör starta inom några sekunder. Du kan kontrollera statusen med kommandot [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

När du kör kommandot visas behållarens fullständiga domännamn (FQDN) och dess etableringsstatus:

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

När behållaren övergår i status **Lyckades** kan du nå den via webbläsaren genom att gå till dess FQDN:

![Skärmbild från webbläsaren som visar ett program som körs i en instans av Azure-behållaren][aci-app-browser]

## <a name="pull-the-container-logs"></a>Hämta behållarloggarna

Du kan hämta loggar för den behållare du skapade med hjälp av kommandot [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Du bör se utdata som liknar följande:

```console
$ az container logs --resource-group myResourceGroup -n mycontainer
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="attach-output-streams"></a>Ansluta utdataströmmar

Förutom att eftersläpa loggarna kan du ansluta dina lokala standardströmmar för utdata och fel till behållarens dataström.

Börja med att köra kommandot [az container attach][az-container-attach] för att ansluta din lokala konsol till behållarens utdataströmmar:

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

När anslutningen är klar uppdaterar du webbläsaren ett antal gånger för att generera ytterligare utdata. Avsluta med att koppla från konsolen med `Control+C`. Du bör se utdata som liknar följande:

```console
$ az container attach --resource-group myResourceGroup -n mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-15 21:17:59+00:00) pulling image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Successfully pulled image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Created container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45
(count: 1) (last timestamp: 2018-03-15 21:18:06+00:00) Started container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45

Start streaming logs:
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:44 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:47 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="delete-the-container"></a>Ta bort behållaren

När du är klar med behållaren kan du ta bort den med kommandot [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Kontrollera att behållaren har tagits bort genom att köra kommandot [az container list](/cli/azure/container#az_container_list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Behållaren **MinBehållare** ska inte visas i kommandots utdata. Om du inte har några andra behållare i resursgruppen visas inga utdata.

## <a name="next-steps"></a>Nästa steg

All kod för behållaren som används i den här snabbstartsguiden finns [på GitHub][app-github-repo] tillsammans med dess Dockerfile. Om du vill försöka skapa den på egen hand och distribuera den till Azure Container Instances via Azure Container Registry, går du vidare till självstudierna för Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudier](./container-instances-tutorial-prepare-app.md)

Om du vill testa alternativ för att köra behållare i ett orkestreringssystem på Azure kan du gå vidare till snabbstarterna om [Service Fabric][service-fabric] eller [Azure Container Service (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-create]: /cli/azure/container#az_container_create
[az-container-delete]: /cli/azure/container#az_container_delete
[az-container-list]: /cli/azure/container#az_container_list
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md

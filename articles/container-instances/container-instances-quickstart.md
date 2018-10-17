---
title: Snabbstart – Köra ett program i Azure Container Instances
description: I den här snabbstarten använder du Azure PowerShell för att distribuera ett program som körs i en Docker-container i Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 7db3d9a076fe9ff5b8bbf970705b82a3f0d5ce54
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855671"
---
# <a name="quickstart-run-an-application-in-azure-container-instances"></a>Snabbstart: Köra ett program i Azure Container Instances

Använd Azure Container Instances för att snabbt och enkelt köra Docker-containrar i Azure. Du behöver inte distribuera virtuella datorer eller använda en komplett plattform för containerorkestrering, som Kubernetes. I den här snabbstarten använder du Azure-portalen för att skapa en container i Azure och göra programmet tillgängligt med ett fullständigt kvalificerat domännamn (FQDN). Några sekunder efter att du har kört ett enskilt distributionskommando kan du gå till det program som körs:

![Program som distribuerats till Azure Container Instances visas i en webbläsare][aci-app-browser]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][azure-account] konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att genomföra den här snabbstarten. Om du vill använda den lokalt måste du ha version 2.0.27 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure-containerinstanser (liksom alla Azure-resurser) måste distribueras till en resursgrupp. Resursgrupper gör det enkelt att se och hantera relaterade Azure-resurser.

Skapa först en resursgrupp med namnet *myResourceGroup* på platsen *eastus* med följande [az group create][az-group-create]-kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Skapa en container

Nu när du har en resursgrupp kan du köra en container i Azure. Om du vill skapa en containerinstans med Azure CLI anger du ett resursgruppsnamn, ett containerinstansnamn och en Docker-containeravbildning till kommandot [az container create][az-container-create]. Du kan exponera dina containrar för internet genom att ange en eller flera portar som ska öppnas, en DNS-namnetikett eller bådadera. I den här snabbstarten distribuerar du en container med en DNS-namnetikett som är värd för en liten webbapp som skrivits i Node.js.

Kör följande kommando för att starta en instans av containern. Värdet `--dns-name-label` måste vara unikt i den Azure-region där du skapar instansen. Om du får felmeddelandet ”DNS-namnetiketten är inte tillgänglig” provar du med en annan DNS-namnetikett.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrollera distributionens status med kommandot [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

När du kör kommandot visas containerns fullständiga domännamn (FQDN) och dess etableringsstatus.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

När containerns `ProvisioningState` är **Lyckades**, navigerar du till dess fullständiga domännamn (FQDN) i webbläsaren. Om du ser en webbsida som liknar följande – grattis! Du har distribuerat ett program som körs i en Docker-container till Azure.

![Skärmbild från webbläsaren som visar ett program som körs i en instans av Azure-containern][aci-app-browser]

Om programmet inte visas direkt kan du behöva vänta några sekunder medan DNS sprids, och sedan uppdatera din webbläsare.

## <a name="pull-the-container-logs"></a>Hämta containerloggarna

Om du behöver felsöka en container eller det program som körs av containern (eller bara se dess utdata), börjar du med att granska loggarna för containerinstansen.

Hämta containerinstansloggarna med kommandot [az container logs][az-container-logs]:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Utdata visar loggarna för containern och bör även visa de HTTP GET-förfrågningar som genererades när du granskade programmet i webbläsaren.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.105 - - [01/Oct/2018:18:25:51 +0000] "GET / HTTP/1.0" 200 1663 "-" "-"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
```

## <a name="attach-output-streams"></a>Ansluta utdataströmmar

Förutom att granska loggarna kan du ansluta dina lokala standardströmmar för utdata och fel till containerns dataström.

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

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med behållaren kan du ta bort den med kommandot [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Kontrollera att behållaren har tagits bort genom att köra kommandot [az container list](/cli/azure/container#az-container-list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Containern **MinContainer** ska inte visas i kommandots utdata. Om du inte har några andra containrar i resursgruppen visas inga utdata.

Om du är klar med resursgruppen *myResourceGroup* och alla resurser i den, tar du bort den med kommandot [az group delete][az-group-delete]:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Azure-containerinstans med en avbildning som finns i det offentliga Docker Hub-registret. Om du vill skapa en container på egen hand och distribuera den från ett privat Azure-containerregister går du vidare till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudie](./container-instances-tutorial-prepare-app.md)

Om du vill testa alternativ för att köra containrar i ett orkestreringssystem på Azure kan du gå vidare till snabbstarterna om [Service Fabric][service-fabric] eller [Azure Kubernetes Service (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md

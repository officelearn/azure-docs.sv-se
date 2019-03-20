---
title: Snabbstart – distribuera Docker-behållare till Azure Container Instances – CLI
description: I den här snabbstarten använder du Azure CLI att snabbt distribuera en behållarbaserad webbapp som körs i en isolerad Azure container-instans
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7252636287d634927979d70954f48cab5aecde5d
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2019
ms.locfileid: "57732273"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Snabbstart: Distribuera en behållarinstans i Azure med hjälp av Azure CLI

Använd Azure Container Instances för att köra serverlös Docker-behållare i Azure med enkelhet och hastighet. Distribuera ett program till en container-instans på begäran när du inte behöver en fullständig behållarplattform orchestration som Azure Kubernetes Service.

I den här snabbstarten använder du Azure CLI för att distribuera en isolerad dockerbehållare och tillgängliggöra dess program med ett fullständigt kvalificerat domännamn (FQDN). Några sekunder efter du kör en enkel distribution-kommando som du kan bläddra till det program som körs i behållaren:

![App som distribuerats via Azure Container Instances visas i webbläsaren][aci-app-browser]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt][azure-account] konto innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att genomföra den här snabbstarten. Om du vill använda den lokalt, version 2.0.55 eller senare rekommenderas. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure-containerinstanser måste, precis som alla Azure-resurser, distribueras i en resursgrupp. Resursgrupper gör det enkelt att se och hantera relaterade Azure-resurser.

Skapa först en resursgrupp med namnet *myResourceGroup* på platsen *eastus* med följande [az group create][az-group-create]-kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Skapa en container

Nu när du har en resursgrupp kan du köra en container i Azure. Om du vill skapa en containerinstans med Azure CLI anger du ett resursgruppsnamn, ett containerinstansnamn och en Docker-containeravbildning till kommandot [az container create][az-container-create]. I den här snabbstarten använder du offentligt `microsoft/aci-helloworld` bild. Den här avbildningen paketerar en liten webbapp som skrivits i Node.js och som hanterar en statisk HTML-sida.

Du kan exponera dina containrar till internet genom att ange en eller flera portar som ska öppnas, en DNS-namnetikett eller båda. I den här snabbstarten distribuerar du en container med en DNS-namnetikett så att webbappen kan nås offentligt.

Köra ett kommando som liknar följande att starta en behållarinstans. Ange en `--dns-name-label` värde som är unikt i Azure-regionen där du skapar instansen. Om du får felmeddelandet ”DNS name label not available” (DNS-namnetikett inte tillgänglig) kan du prova en annan DNS-namnetikett.

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

När containerns `ProvisioningState` är **Lyckades**, navigerar du till dess fullständiga domännamn (FQDN) i webbläsaren. Om du ser en webbsida som liknar följande – grattis! Du har distribuerat ett program som körs i en dockercontainer till Azure.

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

Börja med att köra kommandot [az container attach][az-container-attach] för att ansluta din lokala konsol till containerns utdataströmmar:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

När anslutningen är klar uppdaterar du webbläsaren ett antal gånger för att generera ytterligare utdata. Avsluta med att koppla från konsolen med `Control+C`. Du bör se utdata som liknar följande:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
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

Om du vill testa alternativ för att köra containrar i ett orkestreringssystem på Azure kan du gå vidare till snabbstarterna om [Azure Kubernetes Service (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

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

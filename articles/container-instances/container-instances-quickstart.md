---
title: Snabb start – distribuera Docker-behållare till container instance – Azure CLI
description: I den här snabb starten använder du Azure CLI för att snabbt distribuera en container-webbapp som körs i en isolerad Azure Container instance
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom:
- seo-python-october2019
- seodec18
- mvc
ms.openlocfilehash: e5cad7d9141963e5062423545f7e5b94f0575152
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252191"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Snabb start: Distribuera en behållar instans i Azure med hjälp av Azure CLI

Använd Azure Container Instances för att köra Server lös Docker-behållare i Azure med enkelhet och hastighet. Distribuera ett program till en behållar instans på begäran när du inte behöver en fullständig plattform för behållar dirigering som Azure Kubernetes-tjänsten.

I den här snabb starten använder du Azure CLI för att distribuera en isolerad Docker-behållare och göra dess program tillgängligt med ett fullständigt kvalificerat domän namn (FQDN). Några sekunder efter att du kört ett enda distributions kommando kan du bläddra till programmet som körs i behållaren:

![Visa en app som distribuerats till Azure Container Instances i webbläsaren][aci-app-browser]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][azure-account] innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att genomföra den här snabbstarten. Om du vill använda det lokalt rekommenderar vi version 2.0.55 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure-containerinstanser (liksom alla Azure-resurser) måste distribueras till en resursgrupp. Resursgrupper gör det enkelt att se och hantera relaterade Azure-resurser.

Skapa först en resurs grupp med namnet *myResourceGroup* på den *östra* platsen med följande [AZ Group Create][az-group-create] -kommando:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Skapa en container

Nu när du har en resursgrupp kan du köra en container i Azure. Om du vill skapa en behållar instans med Azure CLI anger du ett resurs grupp namn, behållar instans namn och Docker-behållar avbildning till kommandot [AZ container Create][az-container-create] . I den här snabb starten använder du den offentliga `mcr.microsoft.com/azuredocs/aci-helloworld`-avbildningen. Den här avbildningen paketerar en liten webbapp som skrivits i Node.js och som hanterar en statisk HTML-sida.

Du kan exponera dina containrar för internet genom att ange en eller flera portar som ska öppnas, en DNS-namnetikett eller bådadera. I den här snabbstarten distribuerar du en container med en DNS-namnetikett så att webbappen kan nås offentligt.

Kör ett kommando som liknar följande för att starta en behållar instans. Ange ett `--dns-name-label` värde som är unikt inom den Azure-region där du skapar instansen. Om du får felmeddelandet ”DNS-namnetiketten är inte tillgänglig” provar du med en annan DNS-namnetikett.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Inom några sekunder bör du få ett svar från Azure CLI om att distributionen har slutförts. Kontrol lera statusen med kommandot [AZ container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

När du kör kommandot visas containerns fullständiga domännamn (FQDN) och dess etableringsstatus.

```output
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Om behållarens `ProvisioningState` har **slutförts**går du till dess FQDN i webbläsaren. Om du ser en webbsida som liknar följande – grattis! Du har distribuerat ett program som körs i en Docker-container till Azure.

![Visa en app som distribuerats till Azure Container Instances i webbläsaren][aci-app-browser]

Om programmet inte visas direkt kan du behöva vänta några sekunder medan DNS sprids, och sedan uppdatera din webbläsare.

## <a name="pull-the-container-logs"></a>Hämta containerloggarna

Om du behöver felsöka en container eller det program som körs av containern (eller bara se dess utdata), börjar du med att granska loggarna för containerinstansen.

Hämta behållar instans loggarna med kommandot [AZ container logs][az-container-logs] :

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Utdata visar loggarna för containern och bör även visa de HTTP GET-förfrågningar som genererades när du granskade programmet i webbläsaren.

```output
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Ansluta utdataströmmar

Förutom att granska loggarna kan du ansluta dina lokala standardströmmar för utdata och fel till containerns dataström.

Kör först kommandot [AZ container Attach][az-container-attach] för att ansluta din lokala konsol till behållarens utgående strömmar:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

När anslutningen är klar uppdaterar du webbläsaren ett antal gånger för att generera ytterligare utdata. Avsluta med att koppla från konsolen med `Control+C`. Du bör se utdata som liknar följande:

```output
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med behållaren tar du bort den med kommandot [AZ container Delete][az-container-delete] :

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Kontrollera att behållaren har tagits bort genom att köra kommandot [az container list](/cli/azure/container#az-container-list):

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Containern **MinContainer** ska inte visas i kommandots utdata. Om du inte har några andra containrar i resursgruppen visas inga utdata.

Om du är klar med resurs gruppen *myResourceGroup* och alla resurser den innehåller tar du bort den med kommandot [AZ Group Delete][az-group-delete] :

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Azure Container instance med hjälp av en offentlig Microsoft-avbildning. Om du vill skapa en avbildning av en container och distribuera den från ett privat Azure-containerregister, går du vidare till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudie](./container-instances-tutorial-prepare-app.md)

Om du vill testa alternativ för att köra behållare i ett Orchestration-system på Azure, se snabb starterna för [Azure Kubernetes service (AKS)][container-service] .

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

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

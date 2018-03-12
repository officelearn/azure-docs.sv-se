---
title: "Självstudie för Azure Container Instances – Distribuera program"
description: "Självstudie för Azure Container Instances, del 3 av 3 – Distribuera program"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0532d255b271b2155ae3115f8f96c4cbb53916e4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Distribuera en behållare till Azure Container Instances

Det här är den sista självstudien i en serie med tre delar. Tidigare i serien [skapades en behållaravbildning](container-instances-tutorial-prepare-app.md) som sedan [överfördes till Azure Container Registry](container-instances-tutorial-prepare-acr.md). I den här artikeln slutför vi självstudieserien genom att distribuera behållaren till Azure Container Instances.

I den här kursen har du:

> [!div class="checklist"]
> * Distribuera behållaren från Azure Container Registry med hjälp av Azure CLI
> * Visa programmet i webbläsaren
> * Visa behållarloggarna

## <a name="before-you-begin"></a>Innan du börjar

I den här självstudien krävs att du kör Azure CLI version 2.0.27 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0][azure-cli-install].

För att slutföra den här självstudien behöver du en Docker-utvecklingsmiljö installerad lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-dator.

Azure Cloud Shell inkluderar inte de Docker-komponenter som krävs för att slutföra stegen i den här självstudien. Du måste installera Azure CLI och Docker-utvecklingsmiljön lokalt när du ska gå igenom den här självstudien.

## <a name="deploy-the-container-using-the-azure-cli"></a>Distribuera behållaren med hjälp av Azure CLI

Med Azure CLI kan du distribuera en behållare till Azure Container Instances med ett enda kommando. Eftersom behållaravbildningen finns i det privata Azure Container Registry, måste du inkludera de autentiseringsuppgifter som krävs för att komma åt den. Hämta autentiseringsuppgifterna med hjälp av följande Azure CLI-kommandon.

Behållarregistrets inloggningsserver (uppdatera med ditt registernamn):

```azurecli
az acr show --name <acrName> --query loginServer
```

Lösenord för behållarregister:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Programmet måste ha [förberetts i förväg][prepare-app]. Kör följande [az container create][az-container-create]-kommando för att distribuera behållaravbildningen från behållarregistret med en resursbegäran om 1 processorkärna och 1 GB minne. Ersätt `<acrLoginServer>` och `<acrPassword>` med de värden som du fick från de tidigare två kommandona. Ersätt `<acrName>` med namnet på behållarregistret. Du kan även ersätta `aci-tutorial-app` med namnet du vill ge det nya programmet.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Inom några sekunder bör du få ett första svar från Azure Resource Manager. Värdet `--dns-name-label` måste vara unikt i den Azure-region där du skapar behållarinstansen. Uppdatera värdet i föregående exempel om du får ett felmeddelande för **DNS-namnetiketten** när du kör kommandot.

Du kan se statusen för distributionen med [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Upprepa kommandot [az container show][az-container-show] tills status ändras från *Väntar* till *Körs*, vilket bör ta mindre än en minut. När behållaren *Körs* fortsätter du till nästa steg.

## <a name="view-the-application-and-container-logs"></a>Visa program- och behållarloggar

När distributionen är klar kan du visa behållarens fullständiga domännamn (FQDN) med kommandot [az container show][az-container-show]:

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Exempel på utdata: `"aci-demo.eastus.azurecontainer.io"`

Gå till DNS-namnet som visas i webbläsaren om du vill se programmet som körs:

![Hello World-app i webbläsaren][aci-app-browser]

Du kan också visa loggutdata för behållaren:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Resultat:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av de resurser som du skapade i den här självstudieserien, kan du köra kommandot [az group delete][az-group-delete] för att ta bort resursgruppen och alla resurser som den innehåller. Det här kommandot tar bort behållarregistret som du skapade, den behållare som körs och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I självstudien slutförde du processen att distribuera dina behållare till Azure Container Instances. Följande steg har slutförts:

> [!div class="checklist"]
> * Distribuerade behållaren från Azure Container Registry med hjälp av Azure CLI
> * Visade programmet i webbläsaren
> * Visade behållarloggarna

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md

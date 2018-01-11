---
title: "Azure Behållarinstanser tutorial – förbereda registret för Azure-behållare"
description: "Azure Behållarinstanser självstudiekursen del 2 av 3 – Förbereda registret för Azure-behållare"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c0aad1f9bbaac9a456b34f75633faba92f57f498
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Distribuera och använda Azure Container registret

Detta är en del två av tre delar självstudiekursen. I den [föregående steg](container-instances-tutorial-prepare-app.md), en behållare avbildning har skapats för en enkel webbapp som skrivits i [Node.js][nodejs]. I den här självstudiekursen push avbildningen till ett Azure Container registret. Om du inte har skapat behållaren avbildningen återgå till [kursen 1 – skapa behållaren image](container-instances-tutorial-prepare-app.md).

Azure Container registret är en Azure-baserade, privat registret för Docker behållare bilder. Den här självstudiekursen vägleder dig genom att distribuera en instans av Azure-behållare registret och överföra en behållare avbildning till den.

I den här artikeln del två serierna du:

> [!div class="checklist"]
> * Distribuera en Azure-behållare registret-instans
> * Tagga en behållare avbildning för Azure-behållaren registret
> * Överför avbildningen till registret

I nästa artikel sista kurs i serien, distribuera behållaren från din privata registret till Behållarinstanser som Azure.

## <a name="before-you-begin"></a>Innan du börjar

Den här kursen kräver att du använder Azure CLI version 2.0.23 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera, se [installera Azure CLI 2.0][azure-cli-install].

Den här kursen behöver en Docker-utvecklingsmiljö installeras lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på någon [Mac][docker-mac], [Windows][docker-windows], eller [Linux] [ docker-linux] system.

Azure Cloud Shell inkluderar inte Docker-komponenter som krävs för att slutföra varje steg den här kursen. På den lokala datorn för att slutföra den här guiden måste du installera Azure CLI och Docker-utvecklingsmiljö.

## <a name="deploy-azure-container-registry"></a>Distribuera Azure-behållaren registret

När du distribuerar ett Azure Container registret, måste du först en resursgrupp. En Azure-resursgrupp är en logisk samling i vilka Azure resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create][az-group-create]. I det här exemplet en resursgrupp med namnet *myResourceGroup* skapas i den *eastus* region.

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa en Azure-behållaren registret med den [az acr skapa] [ az-acr-create] kommando. Registret behållarnamn **måste vara unika** i Azure, och måste innehålla 5 50 alfanumeriska tecken. Ersätt `<acrName>` med ett unikt namn för registret:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Till exempel att skapa ett Azure-behållaren registernyckel med namnet *mycontainerregistry082*:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

I resten av den här kursen använder vi `<acrName>` som platshållare för registret behållarnamn som du har valt.

## <a name="container-registry-login"></a>Behållaren registret inloggning

Du måste logga in till din instans av Azure-behållare registret innan du skickar bilder till den. Använd den [az acr inloggning] [ az-acr-login] kommando för att slutföra åtgärden. Du måste ange det unika namnet som du angav för behållaren registret när du skapade den.

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar en `Login Succeeded` meddelande när den har slutförts.

## <a name="tag-container-image"></a>Taggen behållaren bild

Om du vill distribuera en avbildning för behållaren från ett privat register, måste du tagga avbildningen med det `loginServer` namn i registret.

Om du vill se en lista över aktuella bilder i [docker bilder] [ docker-images] kommando.

```bash
docker images
```

Resultat:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

För att få namnet loginServer köra den [az acr visa] [ az-acr-show] kommando. Ersätt `<acrName>` med namnet på behållaren registret.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Exempel på utdata:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Taggen i *aci kursen app* avbildningen med loginServer behållare registret. Lägg även till `:v1` till slutet av avbildningens namn. Den här taggen anger versionsnumret för avbildningen. Ersätt `<acrLoginServer>` med resultatet av den [az acr visa] [ az-acr-show] kommando du precis körde.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

När taggade, köra `docker images` att bekräfta åtgärden.

```bash
docker images
```

Resultat:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Push-avbildningen till registret för Azure-behållare

Push den *aci kursen app* avbildningen till registret med den [docker push] [ docker-push] kommando. Ersätt `<acrLoginServer>` med fullständig server inloggningsnamnet hämta i tidigare steg.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

Den `push` åtgärden bör ta några sekunder att några minuter beroende på din internet-anslutning och utdata som liknar följande:

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Listan avbildningar i Azure Container registret

Om du vill returnera en lista över bilder som har varit pushas till Azure-behållare registret, Använd den [az acr databaslistan] [ az-acr-repository-list] kommando. Uppdatera kommandot med registret behållarnamn.

```azurecli
az acr repository list --name <acrName> --output table
```

Resultat:

```azurecli
Result
----------------
aci-tutorial-app
```

Och sedan använda taggar för en viss bild visas den [az acr databasen Visa-taggar] [ az-acr-repository-show-tags] kommando.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Resultat:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen förberedd ett Azure Container registret för användning med Azure Container instanser och pushas en avbildning av behållare till registret. Följande steg har slutförts:

> [!div class="checklist"]
> * Distribuera en Azure-behållare registret-instans
> * En behållare avbildning har taggats för Azure-behållare registret
> * Överföra en bild till registret för Azure-behållare

Gå vidare till nästa kurs att lära dig om att distribuera behållaren till Azure med Azure Container instanser.

> [!div class="nextstepaction"]
> [Distribuera behållare till Behållarinstanser som Azure](./container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: http://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli

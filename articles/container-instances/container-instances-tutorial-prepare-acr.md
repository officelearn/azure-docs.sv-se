---
title: "Självstudie om Azure Container Instances – förbereda Azure Container Registry"
description: "Självstudie om Azure Container Instances del 2 av 3 – förbereda Azure Container Registry"
services: container-instances
author: neilpeterson
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 94ecba44b8281460da4518c146aab814d2eaa850
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Distribuera och använda Azure Container Registry

Det här är del två i en serie självstudier med tre delar. I [föregående steg](container-instances-tutorial-prepare-app.md) skapade du en behållaravbildning för en enkel webbapp som skrivits i [Node.js][nodejs]. I den här självstudien push-överför du avbildningen till ett Azure Container Registry. Om du inte har skapat behållaravbildningen återgår du till [Självstudie 1 – skapa behållaravbildningen](container-instances-tutorial-prepare-app.md).

Azure Container Registry är ett Azure-baserat och privat register för Docker-behållaravbildningar. I den här självstudien får du hjälp att distribuera en instans av Azure Container Registry och att push-överföra en behållaravbildning till den.

I den här artikeln, som är del två i serien, får du göra följande:

> [!div class="checklist"]
> * distribuera en Azure Container Registry-instans
> * tagga en behållaravbildning för Azure-behållarregistret
> * överföra avbildningen till registret.

I nästa artikel, som är den sista självstudien i serien, distribuerar du behållaren från ditt privata register till Azure Container Instances.

## <a name="before-you-begin"></a>Innan du börjar

För den här självstudien krävs att du kör Azure CLI version 2.0.23 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0][azure-cli-install].

För att slutföra den här självstudien behöver du en Docker-utvecklingsmiljö installerad lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på en [Mac][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-dator.

Azure Cloud Shell inkluderar inte de Docker-komponenter som krävs för att slutföra stegen i den här självstudien. Du måste installera Azure CLI och Docker-utvecklingsmiljön lokalt när du ska gå igenom den här självstudien.

## <a name="deploy-azure-container-registry"></a>Distribuera Azure Container Registry

När du distribuerar ett Azure Container Registry behöver du först en resursgrupp. En Azure-resursgrupp är en logisk samling där du distribuerar och hanterar Azure-resurser.

Skapa en resursgrupp med kommandot [az group create][az-group-create]. I det här exemplet skapas en resursgrupp med namnet *myResourceGroup* i regionen *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa ett Azure-behållarregister med kommandot [az acr create][az-acr-create]. Namnet på behållarregistret måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Ersätt `<acrName>` med ett unikt namn för ditt register:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Så här skapar du till exempel ett Azure-behållarregister med namnet *mycontainerregistry082*:

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

I resten av den här självstudien använder vi `<acrName>` som platshållare för det behållarregisternamn du väljer.

## <a name="container-registry-login"></a>Logga in på behållarregistret

Du måste logga in på din Azure Container Registry-instans innan du push-överför avbildningar till den. Använd kommandot [az acr login][az-acr-login] till att slutföra åtgärden. Du måste ange det unika namn du gav till behållarregistret när du skapade det.

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar meddelandet `Login Succeeded` när det har slutförts.

## <a name="tag-container-image"></a>Tagga behållaravbildningen

När du ska distribuera en behållaravbildning från ett privat register måste du tagga avbildningen med `loginServer`-namnet för registret.

Om du vill se en lista med aktuella avbildningar använder du kommandot [docker images][docker-images].

```bash
docker images
```

Resultat:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Du hämtar loginServer-namnet genom att köra kommandot [az acr show][az-acr-show]. Ersätt `<acrName>` med namnet på ditt behållarregister.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Exempel på utdata:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Tagga avbildningen *aci-tutorial-app* med loginServer-värdet för behållarregistret. Lägg även till `:v1` i slutet av avbildningens namn. Den här taggen anger versionsnumret för avbildningen. Ersätt `<acrLoginServer>` med resultatet från kommandot [az acr show][az-acr-show] som du körde nyss.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

När taggningen är färdig verifierar du åtgärden genom att köra `docker images`.

```bash
docker images
```

Resultat:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

Push-överför avbildningen *aci-tutorial-app* till registret med kommandot [docker push][docker-push]. Ersätt `<acrLoginServer>` med det fullständiga namnet på inloggningsservern från det tidigare steget.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push`-åtgärden kan ta från några sekunder till några minuter beroende på din internetanslutning och utdata bör likna följande:

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

## <a name="list-images-in-azure-container-registry"></a>Lista avbildningar i Azure Container Registry

Du kan returnera en lista med avbildningar som push-överförts till Azure-behållarregistret med kommandot [az acr repository list][az-acr-repository-list]. Uppdatera kommandot med namnet på behållarregistret.

```azurecli
az acr repository list --name <acrName> --output table
```

Resultat:

```azurecli
Result
----------------
aci-tutorial-app
```

Om du sedan vill se taggar för en viss avbildning använder du kommandot [az acr repository show-tags][az-acr-repository-show-tags].

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

I den här självstudien förberedde du ett Azure Container Registry för användning med Azure Container Instances och push-överförde en behållaravbildning till registret. Följande steg har slutförts:

> [!div class="checklist"]
> * distribuera en Azure Container Registry-instans
> * tagga en behållaravbildning för Azure Container Registry
> * ladda upp en avbildning till Azure Container Registry.

Gå vidare till nästa självstudie om du vill lära dig att distribuera behållaren till Azure med Azure Container Instances.

> [!div class="nextstepaction"]
> [Distribuera behållare till Azure Container Instances](./container-instances-tutorial-deploy-app.md)

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

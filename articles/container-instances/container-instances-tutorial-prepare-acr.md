---
title: Självstudie om Azure Container Instances – förbereda Azure Container Registry
description: Självstudie om Azure Container Instances del 2 av 3 – förbereda Azure Container Registry
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: a23e6af506b503a666837f01665e2cad9d1ec5b6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167426"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Självstudier: Distribuera och använda Azure Container Registry

Det här är del två i en serie självstudier med tre delar. I [del ett](container-instances-tutorial-prepare-app.md) av självstudiekursen skapades en Docker-behållaravbildning för ett Node.js-webbprogram. I den här självstudien push-överför du avbildningen till Azure Container Registry. Om du ännu inte har skapat behållaravbildningen så gå tillbaka till [Självstudie 1 – skapa behållaravbildning](container-instances-tutorial-prepare-app.md).

Azure Container Registry är ditt privata Docker-register i Azure. I den här självstudiekursen får du skapa en Azure Container Registry-instans i din prenumeration och sedan push-överföra den tidigare skapade behållaravbildningen till den. I den här artikeln, som är del två i serien, får du göra följande:

> [!div class="checklist"]
> * Skapa en Azure Container Registry-instans
> * tagga en behållaravbildning för Azure-behållarregistret
> * Överför avbildningen till registret

I nästa artikel, som är den sista i serien, distribuerar du behållaren från ditt privata register till Azure Container Instances.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="create-azure-container-registry"></a>Skapa Azure Container Registry

Innan du skapar behållarregistret måste du ha en *resursgrupp* att distribuera den till. En resursgrupp är en logisk samling där alla Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create][az-group-create]. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i regionen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

När du har skapat resursgruppen skapar du ett Azure-behållarregister med kommandot [az acr create][az-acr-create]. Namnet på behållarregistret måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Ersätt `<acrName>` med ett unikt namn för ditt register:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

Här är exempel på utdata för ett nytt Azure-behållarregister med namnet *mycontainerregistry082* (visas här trunkerat):

```console
$ az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
...
{
  "adminUserEnabled": true,
  "creationDate": "2018-03-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

I resten av den här självstudien använder vi `<acrName>` som platshållare för det behållarregisternamn du väljer i det här steget.

## <a name="log-in-to-container-registry"></a>Logga in till behållarregistret

Du måste logga in på din Azure Container Registry-instans innan du push-överför avbildningar till den. Använd kommandot [az acr login][az-acr-login] till att slutföra åtgärden. Du måste ange det unika namn du valde för behållarregistret när du skapade det.

```azurecli
az acr login --name <acrName>
```

Kommandot returnerar `Login Succeeded` när det har slutförts:

```console
$ az acr login --name mycontainerregistry082
Login Succeeded
```

## <a name="tag-container-image"></a>Tagga behållaravbildningen

Om du vill vidarebefordra en behållaravbildning till ett privat register som Azure Container Registry, måste du först tagga avbildningen med det fullständiga namnet på registrets inloggningsserver.

Hämta först det fullständiga inloggningsservernamnet för ditt Azure-behållarregister. Kör följande [az acr show][az-acr-show]-kommando och ersätt `<acrName>` med namnet på det register som du just har skapat:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Om registret t.ex. heter *mycontainerregistry082*:

```console
$ az acr show --name mycontainerregistry082 --query loginServer --output table
Result
------------------------
mycontainerregistry082.azurecr.io
```

Visa nu listan över dina lokala avbildningar med kommandot [docker images][docker-images]:

```bash
docker images
```

Du bör se den *aci-tutorial-app*-avbildning som du skapade i den [föregående självstudiekursen](container-instances-tutorial-prepare-app.md) tillsammans med övriga bilder som finns på datorn:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Tagga avbildningen *aci-tutorial-app* med loginServer-värdet för behållarregistret. Ange även avbildningens versionsnummer genom att lägga till taggen `:v1` i slutet av avbildningsnamnet. Ersätt `<acrLoginServer>` med resultatet av kommandot [az acr show][az-acr-show] som du körde tidigare.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Verifiera taggningen genom att köra `docker images` igen:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

Nu när du har taggat avbildningen *aci kursen app* med det fullständiga serverinloggningsnamnet för ditt privata register, så kan du push-överföra den till registret med kommandot [docker push][docker-push]. Ersätt `<acrLoginServer>` med det fullständiga namnet på inloggningsservern som du erhöll i det tidigare steget.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push`-åtgärden kan ta från några sekunder till några minuter beroende på din internetanslutning och utdata bör likna följande:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
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

Verifiera att den avbildning som du just har push-överfört verkligen finns i ditt Azure-behållarregister genom att avbildningarna i registret med kommandot [az acr repository list][az-acr-repository-list]. Ersätt `<acrName>` med namnet på ditt behållarregister.

```azurecli
az acr repository list --name <acrName> --output table
```

Till exempel:

```console
$ az acr repository list --name mycontainerregistry082 --output table
Result
----------------
aci-tutorial-app
```

Om du vill se *taggarna* för en viss avbildning använder du kommandot [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Du bör se utdata som liknar följande:

```console
$ az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
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

Gå vidare till nästa självstudiekurs om du vill lära dig hur man distribuerar behållaren till Azure med Azure Container Instances:

> [!div class="nextstepaction"]
> [Distribuera en behållare till Azure Container Instances](container-instances-tutorial-deploy-app.md)

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

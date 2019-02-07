---
title: Självstudiekurs om Kubernetes i Azure – Skapa ett containerregister
description: I den här självstudien om Azure Kubernetes Service (AKS) ska du skapa en Azure Container Registry-instans och ladda upp en containeravbildning för exempelprogrammet.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1ba320a523d21beebe089084f40efff4b36dc4af
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753579"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Självstudier: Distribuera och använda Azure Container Registry

Azure Container Registry (ACR) är ett privat register för containeravbildningar. Med ett privat containerregister kan du skapa och distribuera dina program och anpassad kod på ett säkert sätt. I den här självstudien, del två av sju, ska du distribuera en ACR-instans och skicka en containeravbildning till den. Lär dig att:

> [!div class="checklist"]
> * Skapa en ACR-instans (Azure Container Registry)
> * Tagga en containeravbildning för ACR
> * ladda upp avbildningen till ACR.
> * Visa avbildningar i registret

I ytterligare självstudier integrerar du den här ACR-instansen med ett Kubernetes-kluster i AKS och distribuerar ett program från avbildningen.

## <a name="before-you-begin"></a>Innan du börjar

I [föregående självstudie][aks-tutorial-prepare-app] skapade du en containeravbildning för det enkla programmet Azure Voting. Om du inte har skapat appavbildningen för Azure Voting återgår du till [Självstudie 1 – skapa containeravbildningar][aks-tutorial-prepare-app].

För den här självstudien behöver du köra Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

För att skapa en Azure Container Registry-instans behöver du en resursgrupp. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create][az-group-create]. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i regionen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa en Azure Container Registry-instans med kommandot [az acr create][az-acr-create] och ange ett registernamn. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. I resten av den här självstudien används `<acrName>` som platshållare för namnet på containerregistret. Ange ditt eget unika registernamn. Den *grundläggande* SKU:n är en kostnadsoptimerad startpunkt för utvecklingsändamål som ger en bra balans mellan lagring och dataflöde.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Logga in till containerregistret

För att använda ACR-instansen måste du först logga in. Använd kommandot [az acr login][az-acr-login] och ange det unika namn som du gav containerregistret i föregående steg.

```azurecli
az acr login --name <acrName>
```

Du får ett meddelande om att *inloggningen lyckades* när inloggningen är klar.

## <a name="tag-a-container-image"></a>Tagga en containeravbildning

Om du vill visa en lista över dina aktuella lokala avbildningar använder du kommandot [docker images][docker-images]:

```
$ docker images

REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

För att du ska kunna använda containeravbildningen *azure-vote-front* med ACR måste avbildningen taggas med adressen för inloggningsservern för ditt register. Den här taggen används till routning när du push-överför containeravbildningar till ett avbildningsregister.

Hämta inloggningsserverns adress genom att köra kommandot [az acr list][az-acr-list] och fråga efter *loginServer* så här:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Tagga nu din lokala *azure-vote-front*-avbildning med *acrloginServer*-adressen för containerregistret. Ange versionsnumret för avbildningen genom att lägga till *:v1* i slutet av avbildningens namn:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Kontrollera att taggarna har tillämpats genom att köra [docker images][docker-images] igen. En bild taggas med ACR-instansadressen och ett versionsnummer.

```
$ docker images

REPOSITORY                                           TAG           IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest        eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry.azurecr.io/azure-vote-front      v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest        a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask         788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Push-överför avbildningar till registret

När avbildningen har skapats och taggats push-överför du avbildningen *azure-vote-front* till ACR-instansen. Använd [docker push][docker-push] och tillhandahåll din egen *acrLoginServer*-adress för avbildningsnamnet på följande sätt:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Överföringen till ACR kan ta några minuter.

## <a name="list-images-in-registry"></a>Lista med avbildningar i registret

Du kan returnera en lista med avbildningar som har överförts till ACR-instansen genom att köra kommandot [az acr repository list][az-acr-repository-list]. Ange din egen `<acrName>` på följande sätt:

```azurecli
az acr repository list --name <acrName> --output table
```

Följande exempelutdata visar *azure-vote-front*-avbildningen i registret:

```
Result
----------------
azure-vote-front
```

Om du vill visa taggarna för en viss avbildning kör du kommandot [az acr repository show-tags][az-acr-repository-show-tags] på följande sätt:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Följande exempelutdata visar *v1*-avbildningen som taggades i föregående steg:

```
Result
--------
v1
```

Nu har du en behållaravbildning som lagras i en privat Azure Container Registry-instans. Den här avbildningen distribueras från ACR till ett Kubernetes-kluster i nästa självstudiekurs.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en Azure Container Registry-instans och överfört en avbildning för användning i ett AKS-kluster. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en ACR-instans (Azure Container Registry)
> * Tagga en containeravbildning för ACR
> * ladda upp avbildningen till ACR.
> * Visa avbildningar i registret

Gå vidare till nästa självstudie och lär dig hur du distribuerar ett Kubernetes-kluster i Azure.

> [!div class="nextstepaction"]
> [Distribuera Kubernetes-kluster][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az-acr-login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md

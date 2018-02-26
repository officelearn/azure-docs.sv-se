---
title: "Självstudie om Kubernetes i Azure – förbereda ACR"
description: "AKS-självstudie – förbereda ACR"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 0bef14a03c27feb07683f4805aa4264eb91eda6e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Distribuera och använda Azure Container Registry

Azure Container Registry (ACR) är ett Azure-baserat och privat register för Docker-behållaravbildningar. I den här självstudien, som är del två av åtta, får du hjälp att distribuera en instans av Azure Container Registry och att push-överföra en behållaravbildning till den. Det här är några av stegen:

> [!div class="checklist"]
> * distribuera en ACR-instans (Azure Container Registry)
> * tagga en behållaravbildning för ACR
> * ladda upp avbildningen till ACR.

I senare självstudier är den här ACR-instansen integrerad med ett Kubernetes-kluster i AKS.

## <a name="before-you-begin"></a>Innan du börjar

I [föregående självstudie][aks-tutorial-prepare-app] skapade du en behållaravbildning för det enkla programmet Azure Voting. Om du inte har skapat appavbildningen för Azure Voting återgår du till [Självstudie 1 – skapa behållaravbildningar][aks-tutorial-prepare-app].

I den här självstudien krävs att du kör Azure CLI version 2.0.27 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="deploy-azure-container-registry"></a>Distribuera Azure Container Registry

När du distribuerar ett Azure Container Registry behöver du först en resursgrupp. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create][az-group-create]. I det här exemplet ska vi skapa en resursgrupp med namnet `myResourceGroup` i regionen `eastus`.

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa ett Azure-behållarregister med kommandot [az acr create][az-acr-create]. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

I resten av den här självstudien använder vi `<acrName>` som platshållare för behållarregisternamnet.

## <a name="container-registry-login"></a>Logga in på behållarregistret

Använd kommandot [docker login][az-acr-login] och logga in på ACR-instansen. Du måste ange det unika namn du angav för behållarregistret när det skapades.

```azurecli
az acr login --name <acrName>
```

Du får ett meddelande om att inloggningen lyckades när inloggningen är klar.

## <a name="tag-container-images"></a>Tagga behållaravbildningar

Om du vill se en lista med aktuella avbildningar använder du kommandot [docker images][docker-images].

```console
docker images
```

Resultat:

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Varje behållaravbildning måste taggas med namnet på inloggningsservern för registret. Den här taggen används till routning när du push-överför behållaravbildningar till ett avbildningsregister.

Du hämtar namnet på inloggningsservern genom att köra följande kommando:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Tagga nu avbildningen `azure-vote-front` med namnet på inloggningsservern för behållarregistret. Lägg även till `:v1` i slutet av avbildningens namn. Den här taggen anger versionsnumret för avbildningen.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

När taggningen är färdig verifierar du åtgärden genom att köra [docker-images][docker-images].

```console
docker images
```

Resultat:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Push-överför avbildningar till registret

Push-överför avbildningen `azure-vote-front`till registret.

Använd följande exempel och ersätt namnet på ACR-inloggningsservern med inloggningsnamnet från din miljö.

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Detta tar några minuter att slutföra.

## <a name="list-images-in-registry"></a>Lista med avbildningar i registret

Du kan returnera en lista med avbildningar som push-överförts till Azure-behållarregistret med kommandot [az acr repository list][az-acr-repository-list]. Uppdatera kommandot med namnet på ACR-instansen.

```azurecli
az acr repository list --name <acrName> --output table
```

Resultat:

```azurecli
Result
----------------
azure-vote-front
```

Om du sedan vill se taggar för en viss avbildning använder du kommandot [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Resultat:

```azurecli
Result
--------
v1
```

När självstudien är färdig har behållaravbildningen lagrats i en privat Azure Container Registry-instans. Den här avbildningen distribueras från ACR till ett Kubernetes-kluster i efterföljande självstudier.

## <a name="next-steps"></a>Nästa steg

I den här självstudien förbereddes ett Azure-behållarregister för användning i ett AKS-kluster. Följande steg har slutförts:

> [!div class="checklist"]
> * distribuera en Azure Container Registry-instans
> * tagga en behållaravbildning för ACR
> * ladda upp avbildningen till ACR.

Gå vidare till nästa självstudie om du vill lära dig hur du distribuerar ett Kubernetes-kluster i Azure.

> [!div class="nextstepaction"]
> [Distribuera Kubernetes-kluster][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
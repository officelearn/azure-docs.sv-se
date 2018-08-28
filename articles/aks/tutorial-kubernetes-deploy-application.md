---
title: Självstudie om Kubernetes i Azure – Distribuera ett program
description: I den här självstudien om Azure Kubernetes Service (AKS) distribuerar du ett program med flera containrar till ditt kluster med hjälp av en anpassad avbildning som lagras i Azure Container Registry.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bf817f553250ead449ec0d5db3d33acc2eff23f3
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "41917598"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Självstudie: Köra program i Azure Kubernetes Service (AKS)

Kubernetes tillhandahåller en distribuerad plattform för containerbaserade program. Du kan bygga och distribuera dina egna program och tjänster till ett Kubernetes-kluster och låta klustret hantera tillgänglighet och anslutningsfunktioner. I den här självstudien, som är del fyra av sju, distribuerar du ett exempelprogram till ett Kubernetes-kluster. Lär dig att:

> [!div class="checklist"]
> * Uppdatera en Kubernetes-manifestfil
> * Köra ett program i Kubernetes
> * Testa programmet

I senare självstudier kommer appen att skalas ut och uppdateras.

I den här självstudien förutsätter vi att du har grundläggande kunskaper om vanliga Kubernetes-begrepp. Detaljerad information om Kubernetes finns i [Kubernetes-dokumentationen][kubernetes-documentation].

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades.

I den här självstudien behöver du Kubernetes-manifestfilen `azure-vote-all-in-one-redis.yaml` som skapats i förväg. Den här filen laddades ned med källkoden för programmet i en tidigare självstudie. Kontrollera att du har klonat lagringsplatsen och att du har ändrat katalogerna i den klonade lagringsplatsen. Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa containeravbildningar][aks-tutorial-prepare-app].

I den här självstudien måste du köra Azure CLI version 2.0.44 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="update-the-manifest-file"></a>Uppdatera manifestfilen

I dessa självstudier lagrar en Azure Container Registry-instans (ACR) containeravbildningen för exempelprogrammet. För att distribuera programmet måste du uppdatera avbildningens namn i Kubernetes-manifestfilen så att det inkluderar namnet på ACR-inloggningsservern.

Du hämtar namnet på ACR-inloggningsservern med hjälp av kommandot [az acr list][az-acr-list] enligt följande:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Exempelmanifestfilen från den git-lagringsplats som klonades i den första självstudien använder inloggningsservernamnet *microsoft*. Öppna den här manifestfilen med en textredigerare, till exempel `vi`:

```console
vi azure-vote-all-in-one-redis.yaml
```

Ersätt *microsoft* med namnet ditt ACR-inloggningsservernamn. Du hittar avbildningsnamnet på rad 47 i manifestfilen. I följande exempel visas standardnamnet för avbildning:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Ange ditt eget ACR-inloggningsservernamn så att manifestfilen ser ut som i följande exempel:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Spara och stäng filen.

## <a name="deploy-the-application"></a>Distribuera programmet

För att distribuera ditt program använder du kommandot [kubectl apply][kubectl-apply]. Det här kommandot parsar manifestfilen och skapar de definierade Kubernetes-objekten. Ange exempelmanifestfilen enligt vad som visas i följande exempel:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Kubernetes-objekten skapas inom klustret på det sätt som visas i följande exempel:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testa programmet

En [Kubernetes-tjänst][kubernetes-service] skapas som gör programmet synligt på internet. Den här processen kan ta ett par minuter. Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`:

```console
kubectl get service azure-vote-front --watch
```

*EXTERNAL-IP* för *azure-vote-front*-tjänsten visas till en början som *pending* (väntande) enligt följande exempel:

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

När *EXTERNAL-IP*-adressen ändras från *pending* till en faktisk offentlig IP-adress använder du `CTRL-C` för att stoppa kubectl-övervakningsprocessen. I följande exempel visas en offentlig IP-adress som nu har tilldelats:

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

För att se hur programmet fungerar i praktiken öppnar du en webbläsare till den externa IP-adressen.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Om det inte gick att läsa in programmet kan det bero på ett auktoriseringsproblem med avbildningsregistret. Du kan visa statusen för dina containrar med hjälp av kommandot `kubectl get pods`. Om det inte går att hämta containeravbildningarna läser du avsnittet om att [tillåta åtkomst till Container Registry med en Kubernetes-hemlighet](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerades programmet Azure Voting till ett Kubernetes-kluster i AKS. Du har lärt dig att:

> [!div class="checklist"]
> * Uppdatera en Kubernetes-manifestfil
> * Köra ett program i Kubernetes
> * Testa programmet

Gå vidare till nästa självstudie om du vill lära dig hur du skalar ett Kubernetes-program och den underliggande Kubernetes-infrastrukturen.

> [!div class="nextstepaction"]
> [Skala ut ett Kubernetes-program och tillhörande infrastruktur][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
[azure-cli-install]: /cli/azure/install-azure-cli

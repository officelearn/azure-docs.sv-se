---
title: Självstudie om Kubernetes i Azure – Distribuera ett program
description: I den här självstudien om Azure Kubernetes Service (AKS) distribuerar du ett program med flera containrar till ditt kluster med hjälp av en anpassad avbildning som lagras i Azure Container Registry.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b3336f592163d793b8415d2d4f0dd79b92200c89
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727946"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Självstudier: Köra program i Azure Kubernetes Service (AKS)

Kubernetes tillhandahåller en distribuerad plattform för containerbaserade program. Du kan bygga och distribuera dina egna program och tjänster till ett Kubernetes-kluster och låta klustret hantera tillgänglighet och anslutningsfunktioner. I den här självstudien, som är del fyra av sju, distribuerar du ett exempelprogram till ett Kubernetes-kluster. Lär dig att:

> [!div class="checklist"]
> * Uppdatera en Kubernetes-manifestfil
> * Köra ett program i Kubernetes
> * Testa programmet

I senare självstudier så kommer den här appen att skalas ut och uppdateras.

Den här snabbstarten förutsätter grundläggande kunskaper om Kubernetes-begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades.

I den här självstudien behöver du Kubernetes-manifestfilen `azure-vote-all-in-one-redis.yaml` som skapats i förväg. Den här filen laddades ned med källkoden för programmet i en tidigare självstudie. Verifiera att du har klonat lagringsplatsen och att du har ändrat katalogerna i den klonade lagringsplatsen. Om du inte har utfört de här stegen och vill följa med så kan du börja med [Självstudie 1 – Skapa containeravbildningar][aks-tutorial-prepare-app].

I den här självstudien kräver att du kör Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="update-the-manifest-file"></a>Uppdatera manifestfilen

I dessa självstudier lagrar en Azure Container Registry-instans (ACR) containeravbildningen för exempelprogrammet. För att distribuera programmet måste du uppdatera avbildningens namn i Kubernetes-manifestfilen så att det inkluderar namnet på ACR-inloggningsservern.

Du hämtar namnet på ACR-inloggningsservern med hjälp av kommandot [az acr list][az-acr-list] enligt följande:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Exempelmanifestfilen från den git-lagringsplats som klonades i den första självstudien använder inloggningsservernamnet *microsoft*. Se till att du befinner dig i den klonade katalogen *azure-voting-app-redis* och öppna därefter manifestetfilen med en textredigerare, till exempel `vi`:

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

Spara och stäng filen. I `vi` använder du `:wq`.

## <a name="deploy-the-application"></a>Distribuera programmet

För att distribuera ditt program använder du kommandot [kubectl apply][kubectl-apply]. Det här kommandot parsar manifestfilen och skapar de definierade Kubernetes-objekten. Ange exempelmanifestfilen enligt vad som visas i följande exempel:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Följande exempelutdata visar de resurser som skapats på AKS-klustret:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testa programmet

När programmet körs så exponerar en Kubernetes-tjänst programmets klientdel mot Internet. Den här processen kan ta ett par minuter att slutföra.

Du kan övervaka förloppet genom att använda kommandot [kubectl get service][kubectl-get] med argumentet `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Till en början visas *EXTERNAL-IP* för *azure-vote-front*-tjänsten som *väntande*:

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

När *EXTERNAL-IP*-adressen ändras från *väntande* till en faktisk offentlig IP-adress, använder du `CTRL-C` för att stoppa `kubectl`-övervakningsprocessen. Följande exempelutdata visar en giltig offentlig IP-adress som har tilldelats tjänsten:

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Om du vill se hur programmet fungerar i praktiken så öppnar du en webbläsare till den externa IP-adressen för din tjänst:

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Om det inte gick att läsa in programmet så kan det bero på ett auktoriseringsproblem med ditt avbildningsregister. Du kan visa statusen för dina containrar med hjälp av kommandot `kubectl get pods`. Om det inte går att hämta containeravbildningarna så kan du se [Tillåta åtkomst till Container Registry med en Kubernetes-hemlighet](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Nästa steg

I den här självstudien så distribuerades ett exempel på ett Azure-röstningsprogram till ett Kubernetes-kluster i AKS. Du har lärt dig att:

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

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services

---
title: (INAKTUELL) Självstudie för Azure Container Service – Distribuera ett program
description: Självstudie för Azure Container Service – Distribuera ett program
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 2c2d50da4328ff07c3d2fda4a8721839aa3aa6e7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "78274058"
---
# <a name="deprecated-run-applications-in-kubernetes"></a>(INAKTUELL) Köra program i Kubernetes

> [!TIP]
> Den uppdaterade versionen av den här självstudien som använder Azure Kubernetes-tjänsten finns i [Självstudier: köra program i Azure Kubernetes service (AKS)](../../aks/tutorial-kubernetes-deploy-application.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

I den här självstudien, som är del fyra av sju, distribuerar du ett exempelprogram till ett Kubernetes-kluster. Det här är några av stegen:

> [!div class="checklist"]
> * Uppdatera Kubernetes-manifestfiler
> * Köra programmet i Kubernetes
> * Testa programmet

I efterföljande självstudier skalar du ut programmet, uppdaterar det och konfigurerar Log Analytics för att övervaka Kubernetes-klustret.

I den här självstudien förutsätter vi att du har grundläggande kunskaper om vanliga Kubernetes-begrepp. Detaljerad information om Kubernetes finns i [Kubernetes-dokumentationen](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades. 

I den här självstudien behöver du Kubernetes-manifestfilen `azure-vote-all-in-one-redis.yml` som skapats i förväg. Den här filen laddades ned med källkoden för programmet i en tidigare självstudie. Kontrollera att du har klonat lagringsplatsen och att du har ändrat katalogerna i den klonade lagringsplatsen.

Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa containeravbildningar](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>Uppdatera manifestfilen

Azure Container Registry (ACR) har använts till att lagra en behållaravbildning i den här självstudien. Innan du kör programmet måste du uppdatera namnet på ACR-inloggningsservern i Kubernetes-manifestfilen.

Du hämtar namnet på ACR-inloggningsservern med kommandot [az acr list](/cli/azure/acr#az-acr-list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Manifestfilen har skapats i förväg med inloggningsservern `microsoft`. Öppna filen med valfritt redigeringsprogram. I det här exemplet öppnas filen med `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Ersätt `microsoft` med namnet på ACR-inloggningsservern. Du hittar värdet på rad **47** i manifestfilen.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Spara och stäng filen.

## <a name="deploy-application"></a>Distribuera programmet

Använd kommandot [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) för att köra programmet. Det här kommandot parsar manifestfilen och skapar de definierade Kubernetes-objekten.

```console
kubectl create -f azure-vote-all-in-one-redis.yml
```

Resultat:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testa programmet

En [Kubernetes-tjänst](https://kubernetes.io/docs/concepts/services-networking/service/) skapas som gör programmet synligt på internet. Den här processen kan ta ett par minuter. 

Du kan övervaka förloppet genom att använda kommandot [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) med argumentet `--watch`.

```console
kubectl get service azure-vote-front --watch
```

Till en början visas **EXTERNAL-IP** för `azure-vote-front`-tjänsten som `pending`. När EXTERNAL-IP-adressen har ändrats från `pending` till en `IP address` använder du `CTRL-C` för att stoppa kubectl-övervakningsprocessen.

```output
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Bläddra till den externa IP-adressen för att visa programmet.

![Bild av Kubernetes-kluster i Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerades programmet Azure Voting till ett Azure Container Service Kubernetes-kluster. Det här är några av uppgifterna:  

> [!div class="checklist"]
> * Ladda ned Kubernetes-manifestfiler
> * Köra programmet i Kubernetes
> * Testa programmet

Gå vidare till nästa självstudie om du vill lära dig om att skala ut både ett Kubernetes-program och den underliggande Kubernetes-infrastrukturen. 

> [!div class="nextstepaction"]
> [Skala ut ett Kubernetes-program och tillhörande infrastruktur](./container-service-tutorial-kubernetes-scale.md)

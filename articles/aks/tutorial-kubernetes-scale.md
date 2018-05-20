---
title: Självstudie om Kubernetes i Azure – Skala program
description: Självstudie om AKS – Skala program
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 6af564a1dc33b6382e70aaad107122034377a95e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-scale-application-in-azure-kubernetes-service-aks"></a>Självstudie: Skala ett program i Azure Kubernetes Service (AKS)

Om du har följt självstudierna så har du ett fungerande Kubernetes-kluster i AKS och du har distribuerat programmet Azure Voting.

I den här självstudien, som är del fem av åtta, skalar du ut poddarna i programmet och provar autoskalning av poddar. Du får också lära dig hur du skalar ut antalet Azure VM-noder så att du ändrar klustrets kapacitet som värd för arbetsbelastningar. Det här är några av uppgifterna:

> [!div class="checklist"]
> * Skala Kubernetes Azure-noder
> * Skala Kubernetes-poddar manuellt
> * Konfigurera autoskalning av poddarna som kör appens klientdel

I efterföljande självstudier uppdaterar du Azure Vote-programmet, och konfigurerar Log Analytics för att övervaka Kubernetes-klustret.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades ett program i en behållaravbildning, avbildningen laddades upp till Azure Container Registry och ett Kubernetes-kluster skapades. Programmet kördes därefter i Kubernetes-klustret.

Om du inte har gjort det här och vill följa med återgår du till [Självstudie 1 – Skapa behållaravbildningar][aks-tutorial-prepare-app].

## <a name="scale-aks-nodes"></a>Skala AKS-noder

Om du har skapat Kubernetes-klustret med kommandona i föregående självstudie har det en nod. Du kan justera antalet noder manuellt om du planerar att ha fler eller färre arbetsbelastningar i klustret.

I följande exempel ökas antalet agentnoder till tre i Kubernetes-klustret med namn *myAKSCluster*. Det tar några minuter att slutföra kommandot.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myAKSCluster --node-count 3
```

Utdatan liknar följande:

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>Skala poddar manuellt

Hittills har vi distribuerat klientdelen av Azure Vote och Redisinstansen, var och en med en enda replik. Du kan kontrollera detta genom att köra programmet [kubectl get][kubectl-get].

```azurecli
kubectl get pods
```

Resultat:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Ändra antalet poddar i `azure-vote-front`-distributionen manuellt med kommandot [kubectl scale][kubectl-scale]. I det här exemplet ökas antalet till 5.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

Kör [kubectl get pods][kubectl-get] om du vill verifiera att Kubernetes skapar poddarna. Efter ungefär en minut körs de nya poddarna:

```azurecli
kubectl get pods
```

Resultat:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatisk skalning av poddar

Kubernetes har stöd för [horisontell autoskalning av poddar][kubernetes-hpa] så att antalet poddar i en distribution justeras beroende på CPU-användningen eller något annat mått du väljer.

Om du vill använda autoskalning måste poddarna ha definierade CPU-krav och CPU-gränser. I `azure-vote-front`-distributionen begär klientdelsbehållaren 0,25 CPU med maxgränsen 0,5 CPU. Inställningarna ser ut så här:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

I följande exempel används kommandot [kubectl autoscale][kubectl-autoscale] till att automatiskt skala antalet poddar i `azure-vote-front`-distributionen. Om processoranvändningen överskrider 50 % ökar autoskalningen antalet poddar till högst 10.


```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Om du vill se status för autoskalningen kör du följande kommando:

```azurecli
kubectl get hpa
```

Resultat:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Efter några minuter med minimal belastning på Azure Vote-appen minskar antalet poddrepliker automatiskt till 3.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använt olika skalningsfunktioner i Kubernetes-klustret. Här är några av uppgifterna:

> [!div class="checklist"]
> * Skala Kubernetes-poddar manuellt
> * Konfigurera autoskalning av poddarna som kör appens klientdel
> * Skala Kubernetes Azure-noder

Gå vidare till nästa självstudie om du vill lära dig om att uppdatera program i Kubernetes.

> [!div class="nextstepaction"]
> [Uppdatera ett program i Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md

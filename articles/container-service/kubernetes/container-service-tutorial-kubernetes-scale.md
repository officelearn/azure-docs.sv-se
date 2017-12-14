---
title: "Självstudiekurs för Azure Container Service - skala program"
description: "Självstudiekurs för Azure Container Service - skala program"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: a748e15abbc01f260349fba2678c03a40c4d7713
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Skala Kubernetes skida och Kubernetes infrastruktur

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Om du har följande självstudierna du ha en fungerande Kubernetes kluster i Azure Container Service och du har distribuerat appen Azure röstning. 

I den här självstudiekursen tillhör fem sju, skala ut skida i appen och försök baljor autoskalning. Du också lära dig hur du skalar antalet noder som Virtuella Azure-agenten att ändra klustrets kapacitet för värd för arbetsbelastningar. Aktiviteter har slutförts är:

> [!div class="checklist"]
> * Skalning Kubernetes skida manuellt
> * Konfigurera Autoskala skida kör app klientdelen
> * Skala Kubernetes Azure agent noder

Programmet Azure rösten uppdateras i efterföljande självstudiekurser och Operations Management Suite som konfigurerats för att övervaka Kubernetes klustret.

## <a name="before-you-begin"></a>Innan du börjar

I föregående självstudier, ett program som har paketerats i en behållare avbildning, avbildningen har överförts till registret för Azure-behållaren och ett Kubernetes kluster skapas. Programmet körs sedan Kubernetes klustret. 

Om du inte har gjort dessa steg och vill följa med, gå tillbaka till den [kursen 1 – skapa behållaren bilder](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="manually-scale-pods"></a>Skala skida manuellt

Därmed distribuerats långt Azure rösten frontend och Redis-instans har, var och en med en enskild replik. Du kan kontrollera genom att köra den [kubectl hämta](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) kommando.

```azurecli-interactive
kubectl get pods
```

Resultat:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Manuellt ändra antalet skida i den `azure-vote-front` distribution med den [kubectl skala](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale) kommando. Det här exemplet ökar antalet till 5.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

Kör [kubectl hämta skida](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) att verifiera att Kubernetes skapar skida. När en minut eller så kan kör ytterligare skida:

```azurecli-interactive
kubectl get pods
```

Resultat:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Autoskala skida

Har stöd för Kubernetes [vågräta baljor autoskalning](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) för att justera antalet skida i en distribution beroende på CPU-användning eller annan välja mått. 

Om du vill använda autoscaler ha din skida CPU-begäranden och gränser har definierats. I den `azure-vote-front` distribution, behållaren frontend begäranden 0,25 processor, med högst 0,5 CPU. Det ser ut som inställningarna:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

I följande exempel används den [kubectl Autoskala](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) kommandot Autoskala antalet skida i den `azure-vote-front` distribution. Om processoranvändningen överskrider 50%, ökar autoscaler här skida högst 10.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Om du vill se status för autoscaler, kör du följande kommando:

```azurecli-interactive
kubectl get hpa
```

Resultat:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Efter några minuter med minimal belastning på appen Azure röst minskar antalet baljor replikerna automatiskt till 3.

## <a name="scale-the-agents"></a>Skala agenter

Om du har skapat klustret Kubernetes standard kommandona i föregående självstudiekursen har tre agent-noder. Du kan justera antalet agenter manuellt om du planerar fler eller färre arbetsbelastningar i behållare på ditt kluster. Använd den [az acs skala](/cli/azure/acs#scale) kommando och ange hur många agenter med den `--new-agent-count` parameter.

I följande exempel ökar antalet noder som agenten till 4 i Kubernetes klustret med namnet *myK8sCluster*. Kommandot tar några minuter att slutföra.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

Kommandoutdata visar antalet agent noder i värdet för `agentPoolProfiles:count`:

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Nästa steg

I den här kursen används olika skalning funktioner i Kubernetes klustret. Uppgifter som omfattas ingår:

> [!div class="checklist"]
> * Skalning Kubernetes skida manuellt
> * Konfigurera Autoskala skida kör app klientdelen
> * Skala Kubernetes Azure agent noder

Gå vidare till nästa kurs att lära dig om att uppdatera programmet i Kubernetes.

> [!div class="nextstepaction"]
> [Uppdatera ett program i Kubernetes](./container-service-tutorial-kubernetes-app-update.md)


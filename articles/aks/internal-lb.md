---
title: Skapa en intern belastningsutjämnare i Azure Kubernetes Service (AKS)
description: Använda en intern belastningsutjämnare med Azure-Kubernetes (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 7606ce574c7ff94caef3ffa89320d682b22d8502
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097928"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Använda en intern belastningsutjämnare med Azure-Kubernetes (AKS)

Intern belastningsutjämning gör en Kubernetes tjänst tillgänglig för program som körs i samma virtuella nätverk som Kubernetes-kluster. Det här dokumentet beskriver och skapa en intern belastningsutjämnare med Azure-Kubernetes (AKS). Azure belastningsutjämning är tillgänglig i två SKU: er: Basic och Standard. AKS använder grundläggande SKU: N.

## <a name="create-internal-load-balancer"></a>Skapa en intern belastningsutjämnare

Om du vill skapa en intern belastningsutjämnare, skapa en tjänstmanifestet med tjänsttypen `LoadBalancer` och `azure-load-balancer-internal` anteckning som visas i följande exempel.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

När har distribuerats, skapas en Azure belastningsutjämnare och blir tillgängligt på samma virtuella nätverk som AKS-kluster.

![Bild av AKS interna belastningsutjämnare](media/internal-lb/internal-lb.png)

När tjänsten hämtar information, IP-adressen i det `EXTERNAL-IP` kolumnen är IP-adressen för den interna belastningsutjämnaren.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Ange en IP-adress

Om du vill använda en specifik IP-adress med den interna belastningsutjämnaren lägger du till den `loadBalancerIP` egenskapen belastningen belastningsutjämnaren-specifikationen. Den angivna IP-adressen måste finnas i samma undernät som AKS klustret och måste redan inte tilldelas till en resurs.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

När du hämtar information om tjänsten, IP-adressen på den `EXTERNAL-IP` bör avspegla den angivna IP-adressen.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>Ta bort belastningsutjämnaren

När alla tjänster med hjälp av den interna belastningsutjämnaren har tagits bort, belastningsutjämnaren sig också att tas bort.

## <a name="next-steps"></a>Nästa steg

Mer information om Kubernetes services på den [Kubernetes services dokumentationen][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

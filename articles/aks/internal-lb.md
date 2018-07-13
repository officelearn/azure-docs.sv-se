---
title: Skapa en intern belastningsutjämnare i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och använder en intern belastningsutjämnare för att exponera dina tjänster med Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 123fc08995416e0ff9c7e12a526deadc34b3a4a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001403"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Använda en intern belastningsutjämnare med Azure Kubernetes Service (AKS)

Intern belastningsutjämning gör en Kubernetes-tjänst som är tillgängligt för program som körs i samma virtuella nätverk som Kubernetes-klustret. Den här artikeln visar hur du skapar och använder en intern belastningsutjämnare med Azure Kubernetes Service (AKS). Azure Load Balancer är tillgängliga i två SKU: er: Basic och Standard. AKS använder en grundläggande SKU.

## <a name="create-an-internal-load-balancer"></a>Skapa en intern belastningsutjämnare

För att skapa en intern belastningsutjämnare, skapar du ett tjänstmanifest tjänsttypen *LoadBalancer* och *-load-balancer – intern azure* anteckning som du ser i följande exempel:

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

När den har distribuerats med `kubetctl apply`, en Azure belastningsutjämnare skapas och blir tillgängligt på samma virtuella nätverk som AKS-klustret.

![Bild av AKS intern belastningsutjämnare](media/internal-lb/internal-lb.png)

När du visar information om tjänsten, IP-adressen i den *extern IP-adress* kolumnen är IP-adressen för den interna belastningsutjämnaren som visas i följande exempel:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Ange en IP-adress

Om du vill använda en specifik IP-adress med den interna belastningsutjämnaren, lägga till den *loadBalancerIP* egenskap load balancer-specifikationen. Den angivna IP-adressen måste finnas i samma undernät som AKS-klustret och måste inte redan tilldelats en resurs.

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

När du visar information om tjänsten, IP-adressen på den *extern IP-adress* återspeglar den angivna IP-adressen:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Använda privata nätverk

När du skapar AKS-kluster kan ange du avancerade nätverksinställningar. Den här metoden låter dig distribuera klustret till ett befintligt Azure virtuellt nätverk och undernät. Ett scenario är att distribuera din AKS-kluster till ett privat nätverk som är anslutna till din lokala miljö och köra tjänster som endast är tillgängliga internt. Mer information finns i [avancerade nätverkskonfiguration i AKS][advanced-networking].

Inga ändringar i föregående steg behövs för att distribuera en intern belastningsutjämnare i ett AKS-kluster som använder ett privat nätverk. Belastningsutjämnaren skapas i samma resursgrupp som ditt AKS-kluster, men är ansluten till ditt privata virtuella nätverk och undernät, som visas i följande exempel:

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Du behöver ge tjänstens huvudnamn för AKS-klustret i *Nätverksdeltagare* rollen till resursgruppen där resurserna i Azure-nätverk har distribuerats. Visa tjänstens huvudnamn med [az aks show][az-aks-show], till exempel `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Du kan skapa en rolltilldelning med den [az-rolltilldelning skapa] [ az-role-assignment-create] kommando.

## <a name="specify-a-different-subnet"></a>Ange ett annat undernät

Om du vill ange ett undernät för belastningsutjämnaren, lägger du till den *azure-load-balancer-interna-undernätet* kommentar till din tjänst. De undernät som anges måste finnas i samma virtuella nätverk som AKS-klustret. När de distribueras belastningsutjämnaren *extern IP-adress* adressen är en del av det angivna undernätet.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="delete-the-load-balancer"></a>Ta bort belastningsutjämnaren

När alla tjänster som använder den interna belastningsutjämnaren har tagits bort, raderas också själva belastningsutjämnaren på.

## <a name="next-steps"></a>Nästa steg

Mer information om Kubernetes-tjänster på den [dokumentation för Kubernetes services][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[advanced-networking]: networking-overview.md
[deploy-advanced-networking]: networking-overview.md#configure-networking---cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
---
title: Skapa en intern belastningsutjämnare i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och använder en intern belastningsutjämnare för att exponera dina tjänster med Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 75530c38ec3ecc5719ac5759d31bc38e7e886329
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069343"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Använda en intern belastningsutjämnare med Azure Kubernetes Service (AKS)

Om du vill begränsa åtkomsten till dina program i Azure Kubernetes Service (AKS), kan du skapa och använda en intern belastningsutjämnare. En intern belastningsutjämnare gör en Kubernetes-tjänst som är tillgängligt endast för program som körs i samma virtuella nätverk som Kubernetes-klustret. Den här artikeln visar hur du skapar och använder en intern belastningsutjämnare med Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer är tillgängliga i två SKU: er - *grundläggande* och *Standard*. Mer information finns i [Azure load balancer SKU jämförelse][azure-lb-comparison]. AKS stöder för närvarande den *grundläggande* SKU. Om du vill använda den *Standard* SKU, kan du använda den överordnade [acs-engine][acs-engine].

## <a name="create-an-internal-load-balancer"></a>Skapa en intern lastbalanserare

För att skapa en intern belastningsutjämnare, skapar du ett service manifest med namnet `internal-lb.yaml` tjänsttypen *LoadBalancer* och *-load-balancer – intern azure* anteckning som du ser i följande Exempel:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

När den har distribuerats med `kubectl apply -f internal-lb.yaml`, en Azure belastningsutjämnare skapas och blir tillgängligt på samma virtuella nätverk som AKS-klustret.

När du visar information om tjänsten, IP-adressen för den interna belastningsutjämnaren visas i den *extern IP-adress* kolumn. Det kan ta en minut eller två IP-adressen ska ändras från *\<väntande\>* en faktiska interna IP-adress, som visas i följande exempel:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Ange en IP-adress

Om du vill använda en specifik IP-adress med den interna belastningsutjämnaren, lägga till den *loadBalancerIP* egenskap i load balancer YAML-manifest. Den angivna IP-adressen måste finnas i samma undernät som AKS-klustret och måste inte redan tilldelats en resurs.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

När du visar information om tjänsten, IP-adressen i den *extern IP-adress* kolumnen visar din angivna IP-adress:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Använda privata nätverk

När du skapar AKS-kluster kan ange du avancerade nätverksinställningar. Den här metoden låter dig distribuera klustret till ett befintligt Azure virtuellt nätverk och undernät. Ett scenario är att distribuera din AKS-kluster till ett privat nätverk som är anslutna till din lokala miljö och köra tjänster som endast är tillgängliga internt. Mer information finns i [avancerade nätverkskonfiguration i AKS][advanced-networking].

Inga ändringar i föregående steg behövs för att distribuera en intern belastningsutjämnare i ett AKS-kluster som använder ett privat nätverk. Belastningsutjämnaren skapas i samma resursgrupp som ditt AKS-kluster, men är ansluten till ditt privata virtuella nätverk och undernät, som visas i följande exempel:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Du behöver ge tjänstens huvudnamn för AKS-klustret i *Nätverksdeltagare* rollen till resursgruppen där resurserna i Azure-nätverk har distribuerats. Visa tjänstens huvudnamn med [az aks show][az-aks-show], till exempel `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Du kan skapa en rolltilldelning med den [az-rolltilldelning skapa] [ az-role-assignment-create] kommando.

## <a name="specify-a-different-subnet"></a>Ange ett annat undernät

Om du vill ange ett undernät för belastningsutjämnaren, lägger du till den *azure-load-balancer-interna-undernätet* kommentar till din tjänst. De undernät som anges måste finnas i samma virtuella nätverk som AKS-klustret. När de distribueras belastningsutjämnaren *extern IP-adress* adressen är en del av det angivna undernätet.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Ta bort belastningsutjämnaren

När alla tjänster som använder den interna belastningsutjämnaren har tagits bort, raderas också själva belastningsutjämnaren på.

Du kan också direkt ta bort en tjänst som med Kubernetes-resurser, till exempel `kubectl delete service internal-app`, som också sedan tar bort underliggande Azure-belastningsutjämnaren.

## <a name="next-steps"></a>Nästa steg

Mer information om Kubernetes-tjänster på den [dokumentation för Kubernetes services][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[acs-engine]: https://github.com/Azure/acs-engine

<!-- LINKS - Internal -->
[advanced-networking]: networking-overview.md
[deploy-advanced-networking]: networking-overview.md#configure-networking---cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
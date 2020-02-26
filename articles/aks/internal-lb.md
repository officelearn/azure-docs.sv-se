---
title: Skapa en intern belastningsutjämnare i Azure Kubernetes service (AKS)
description: Lär dig hur du skapar och använder en intern belastningsutjämnare för att exponera dina tjänster med Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: ff102ebe50dd4d2169090718ced9e550701b1b09
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595475"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Använda en intern belastningsutjämnare med Azure Kubernetes service (AKS)

För att begränsa åtkomsten till dina program i Azure Kubernetes service (AKS) kan du skapa och använda en intern belastningsutjämnare. En intern belastningsutjämnare gör att en Kubernetes-tjänst endast är tillgänglig för program som körs i samma virtuella nätverk som Kubernetes-klustret. Den här artikeln visar hur du skapar och använder en intern belastningsutjämnare med Azure Kubernetes service (AKS).

> [!NOTE]
> Azure Load Balancer finns i två SKU: er – *Basic* och *standard*. Standard-SKU: n används som standard när du skapar ett AKS-kluster.  När du skapar en tjänst med typ som LoadBalancer får du samma LB-typ som när du etablerar klustret. Mer information finns i [jämförelse av SKU för Azure Load Balancer][azure-lb-comparison].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

AKS-kluster tjänstens huvud namn måste ha behörighet att hantera nätverks resurser om du använder ett befintligt undernät eller en befintlig resurs grupp. I allmänhet tilldelar du rollen *nätverks deltagare* till tjänstens huvud namn på de delegerade resurserna. Mer information om behörigheter finns i [delegera AKS-åtkomst till andra Azure-resurser][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Skapa en intern lastbalanserare

Skapa en intern belastningsutjämnare genom att skapa ett tjänst manifest med namnet `internal-lb.yaml` med tjänst typen *Loadbalancer* och *Azure-belastningsutjämnaren – intern* anteckning som visas i följande exempel:

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

Distribuera den interna belastningsutjämnaren med [kubectl tillämpa][kubectl-apply] och ange namnet på ditt yaml-manifest:

```console
kubectl apply -f internal-lb.yaml
```

En Azure Load Balancer skapas i resurs gruppen för noden och är ansluten till samma virtuella nätverk som AKS-klustret.

När du visar tjänst informationen visas IP-adressen för den interna belastningsutjämnaren i kolumnen *extern IP-* adress. I det här sammanhanget är *externt* i förhållande till belastningsutjämnarens externa gränssnitt, inte att det får en offentlig, extern IP-adress. Det kan ta en minut eller två för IP-adressen att ändras från *\<väntande\>* till en faktisk intern IP-adress, som visas i följande exempel:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Ange en IP-adress

Om du vill använda en speciell IP-adress med den interna belastningsutjämnaren lägger du till egenskapen *loadBalancerIP* i yaml-manifestet för belastningsutjämnaren. Den angivna IP-adressen måste finnas i samma undernät som AKS-klustret och får inte redan tilldelas till en resurs.

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

När du har distribuerat och visar tjänst informationen, motsvarar IP-adressen i kolumnen *extern IP* din angivna IP-adress:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Använd privata nätverk

När du skapar ditt AKS-kluster kan du ange avancerade nätverks inställningar. Med den här metoden kan du distribuera klustret till ett befintligt virtuellt Azure-nätverk och undernät. Ett scenario är att distribuera ditt AKS-kluster till ett privat nätverk som är anslutet till din lokala miljö och köra tjänster som endast är tillgängliga internt. Mer information finns i Konfigurera dina egna virtuella nätverks under nät med [Kubernetes][use-kubenet] eller [Azure cni][advanced-networking].

Inga ändringar i föregående steg krävs för att distribuera en intern belastningsutjämnare i ett AKS-kluster som använder ett privat nätverk. Belastningsutjämnaren skapas i samma resurs grupp som ditt AKS-kluster, men är ansluten till ditt privata virtuella nätverk och undernät, vilket visas i följande exempel:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Du kan behöva ge tjänstens huvud namn för ditt AKS-kluster rollen *nätverks deltagare* till resurs gruppen där dina virtuella Azure-nätverks resurser distribueras. Visa tjänstens huvud namn med [AZ AKS show][az-aks-show], t. ex. `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. Om du vill skapa en roll tilldelning använder du kommandot [AZ roll tilldelning skapa][az-role-assignment-create] .

## <a name="specify-a-different-subnet"></a>Ange ett annat undernät

Om du vill ange ett undernät för belastningsutjämnaren lägger du till en *Azure-belastningsutjämnare-intern under näts* anteckning till din tjänst. Det angivna under nätet måste finnas i samma virtuella nätverk som ditt AKS-kluster. När den *externa IP* -adressen för belastningsutjämnaren är distribuerad är den en del av det angivna under nätet.

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

När alla tjänster som använder den interna belastningsutjämnaren tas bort, tas även belastningsutjämnaren bort.

Du kan också direkt ta bort en tjänst som med en Kubernetes-resurs, till exempel `kubectl delete service internal-app`, som också tar bort den underliggande Azure Load Balancer.

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes Services i [dokumentationen för Kubernetes Services][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources

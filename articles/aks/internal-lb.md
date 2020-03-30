---
title: Skapa en intern belastningsutjämnare i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och använder en intern belastningsutjämnare för att exponera dina tjänster med Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: ff102ebe50dd4d2169090718ced9e550701b1b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259414"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Använda en intern belastningsutjämnare med Azure Kubernetes Service (AKS)

Om du vill begränsa åtkomsten till dina program i Azure Kubernetes Service (AKS) kan du skapa och använda en intern belastningsutjämnare. En intern belastningsutjämnare gör en Kubernetes-tjänst endast tillgänglig för program som körs i samma virtuella nätverk som Kubernetes-klustret. Den här artikeln visar hur du skapar och använder en intern belastningsutjämnare med Azure Kubernetes Service (AKS).

> [!NOTE]
> Azure Load Balancer är tillgängligt i två SKU : *- Basic* och *Standard*. Som standard används standard-SKU när du skapar ett AKS-kluster.  När du skapar en tjänst med typen LoadBalancer får du samma LB-typ som när du etablerar klustret. Mer information finns i [Azure load balancer SKU jämförelse][azure-lb-comparison].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Du behöver också Azure CLI version 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

Aks-klustertjänstens huvudnamn behöver behörighet för att hantera nätverksresurser om du använder ett befintligt undernät eller en befintlig resursgrupp. I allmänhet tilldelar du rollen *Nätverksdeltagare* till tjänsthuvudhuvudet på de delegerade resurserna. Mer information om behörigheter finns i [Delegera AKS-åtkomst till andra Azure-resurser][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Skapa en intern lastbalanserare

Om du vill skapa en intern belastningsutjämnare skapar du ett tjänstmanifest med namnet `internal-lb.yaml` *LoadBalancer* och den *azure-load-balancer-intern* anteckningen som visas i följande exempel:

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

Distribuera den interna belastningsutjämnaren med [kubectl-ansök][kubectl-apply] och ange namnet på YAML-manifestet:

```console
kubectl apply -f internal-lb.yaml
```

En Azure-belastningsutjämnare skapas i nodresursgruppen och ansluts till samma virtuella nätverk som AKS-klustret.

När du visar tjänstinformationen visas IP-adressen för den interna belastningsutjämnaren i kolumnen *EXTERN-IP.* I det här sammanhanget är *externt* i förhållande till belastningsutjämnarens externa gränssnitt, inte att den tar emot en offentlig, extern IP-adress. Det kan ta en minut eller två för IP-adressen att ändra från * \<väntande\> * till en faktisk intern IP-adress, som visas i följande exempel:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Ange en IP-adress

Om du vill använda en specifik IP-adress med den interna belastningsutjämnaren lägger du till egenskapen *loadBalancerIP* i YAML-manifestet för belastningsutjämnare. Den angivna IP-adressen måste finnas i samma undernät som AKS-klustret och får inte redan tilldelas en resurs.

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

När du distribueras och du visar tjänstinformationen återspeglar IP-adressen i kolumnen *EXTERN-IP* din angivna IP-adress:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>Använda privata nätverk

När du skapar AKS-klustret kan du ange avancerade nätverksinställningar. Med den här metoden kan du distribuera klustret till ett befintligt virtuellt Azure-nätverk och undernät. Ett scenario är att distribuera AKS-klustret till ett privat nätverk som är anslutet till din lokala miljö och köra tjänster som endast är tillgängliga internt. Mer information finns i konfigurera dina egna virtuella nätverksundernät med [Kubenet][use-kubenet] eller [Azure CNI][advanced-networking].

Inga ändringar i föregående steg behövs för att distribuera en intern belastningsutjämnare i ett AKS-kluster som använder ett privat nätverk. Belastningsutjämnaren skapas i samma resursgrupp som AKS-klustret men ansluts till ditt privata virtuella nätverk och undernät, vilket visas i följande exempel:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Du kan behöva ge tjänstens huvudnamn för AKS-klustret rollen *Nätverksbidragsmedlem* till resursgruppen där dina virtuella Azure-nätverksresurser distribueras. Visa tjänstens huvudnamn med az `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"` [aks show][az-aks-show], till exempel . Om du vill skapa en rolltilldelning använder du kommandot [skapa az-rolltilldelning.][az-role-assignment-create]

## <a name="specify-a-different-subnet"></a>Ange ett annat undernät

Om du vill ange ett undernät för din belastningsutjämnare lägger du till annoteringen för *azure-load-balancer-internal-subnet* till din tjänst. Det angivna undernätet måste finnas i samma virtuella nätverk som AKS-klustret. När den distribueras är belastningsutjämnaren *EXTERNAL-IP-adress* en del av det angivna undernätet.

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

När alla tjänster som använder den interna belastningsutjämnaren tas bort tas även belastningsutjämnaren bort.

Du kan också direkt ta bort en tjänst som `kubectl delete service internal-app`med alla Kubernetes-resurser, till exempel , som också tar bort den underliggande Azure-belastningsutjämnaren.

## <a name="next-steps"></a>Nästa steg

Läs mer om Kubernetes-tjänster i [kubernetes-tjänsternas dokumentation][kubernetes-services].

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

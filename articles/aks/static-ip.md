---
title: Använd en statisk IP-adress med AKS-belastningsutjämnaren (Azure Kubernetes service)
description: Lär dig hur du skapar och använder en statisk IP-adress med AKS-belastningsutjämnaren (Azure Kubernetes service).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 9e32715766734bcbb150d70aeed2dc5b06a4bcbb
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614472"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Använda en statisk offentlig IP-adress med belastningsutjämnaren för Azure Kubernetes service (AKS)

Som standard är den offentliga IP-adress som tilldelats till en belastnings Utjämnings resurs som skapats av ett AKS-kluster bara giltig för livs längd för resursen. Om du tar bort Kubernetes-tjänsten raderas även den tillhör ande belastningsutjämnaren och IP-adress. Om du vill tilldela en speciell IP-adress eller behålla en IP-adress för omdistribuerade Kubernetes-tjänster kan du skapa och använda en statisk offentlig IP-adress.

Den här artikeln visar hur du skapar en statisk offentlig IP-adress och tilldelar den till din Kubernetes-tjänst.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

Du måste också ha Azure CLI-versionen 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

För närvarande stöds endast *Basic IP SKU*. Arbetet pågår för att stödja *standard-IP-resurs-* SKU: n. Mer information finns i [IP-diagramtyper och autentiseringsmetoder i Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Skapa en statisk IP-adress

När du skapar en statisk offentlig IP-adress som ska användas med AKS, ska IP-adressresursen skapas i resurs gruppen för **noden** . Om du vill separera resurserna går du till följande avsnitt för att [använda en statisk IP-adress utanför resurs gruppen för noden](#use-a-static-ip-address-outside-of-the-node-resource-group).

Börja med att hämta resurs grupp namnet för noden med kommandot [AZ AKS show][az-aks-show] och Lägg till `--query nodeResourceGroup` Frågeparametern. I följande exempel hämtas resurs gruppen för AKS kluster namnet *myAKSCluster* i resurs grupps namnet *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Skapa nu en statisk offentlig IP-adress med kommandot [AZ Network Public IP Create][az-network-public-ip-create] . Ange resurs grupp namnet för noden som hämtades i föregående kommando och ett namn för IP-adressresurs, till exempel *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

IP-adressen visas, som du ser i följande komprimerade exempel i utdata:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Du kan senare hämta den offentliga IP-adressen med kommandot [AZ Network Public-IP List][az-network-public-ip-list] . Ange namnet på den resurs grupp för noden och den offentliga IP-adress som du skapade och fråga efter *IP* -adressen som visas i följande exempel:

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Skapa en tjänst med hjälp av den statiska IP-adressen

Om du vill skapa en tjänst med den statiska offentliga IP-adressen `loadBalancerIP` lägger du till egenskapen och värdet för den statiska offentliga IP-adressen i yaml-manifestet. Skapa en fil med `load-balancer-service.yaml` namnet och kopiera i följande yaml. Ange din egen offentliga IP-adress som skapades i föregående steg.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Skapa tjänsten och distributionen med `kubectl apply` kommandot.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Använd en statisk IP-adress utanför nodens resurs grupp

Med Kubernetes 1,10 eller senare kan du använda en statisk IP-adress som har skapats utanför resurs gruppen för noden. Tjänstens huvud namn som används av AKS-klustret måste ha delegerade behörigheter till den andra resurs gruppen, vilket visas i följande exempel:

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Om du vill använda en IP-adress utanför resurs gruppen för noden lägger du till en anteckning i tjänst definitionen. I följande exempel anges anteckningen till resurs gruppen med namnet *myResourceGroup*. Ange ett eget namn på en resurs grupp:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>Felsöka

Om den statiska IP-adressen som definierats i *loadBalancerIP* -egenskapen för Kubernetes-tjänst manifestet inte finns eller inte har skapats i resurs gruppen för noden och inga ytterligare delegeringar har kon figurer ATS, Miss lyckas skapandet av tjänsten för belastningsutjämnare. Du kan felsöka genom att granska tjänstens skapande händelser med kommandot [kubectl beskriver][kubectl-describe] . Ange namnet på tjänsten enligt vad som anges i YAML-manifestet, som du ser i följande exempel:

```console
kubectl describe service azure-load-balancer
```

Information om Kubernetes-Tjänsteresursen visas. *Händelserna* i slutet av följande exempel på utdata visar att det *inte gick att hitta ANVÄNDAREns angivna IP-adress*. I dessa scenarier kontrollerar du att du har skapat den statiska offentliga IP-adressen i resurs gruppen för noden och att den IP-adress som anges i Kubernetes-tjänstens manifest är korrekt.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Nästa steg

Om du vill ha ytterligare kontroll över nätverks trafiken till dina program kan du i stället [skapa en][aks-ingress-basic]ingångs kontroll. Du kan också [skapa en ingångs kontroll enhet med en statisk offentlig IP-adress][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku

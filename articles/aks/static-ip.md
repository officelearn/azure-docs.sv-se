---
title: Använda en statisk IP-adress med belastningsutjämnare för Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och använder en statisk IP-adress med belastningsutjämnare för Azure Kubernetes Service (AKS).
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 03/04/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: d2e4314948eeda0c82c004414f894dafc4d4cff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031657"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Använda en statisk offentlig IP-adress med belastningsutjämnare för Azure Kubernetes Service (AKS)

Som standard gäller endast offentliga IP-adress som tilldelats en belastningsutjämningsresurs som skapats av ett AKS-kluster för livslängden för den här resursen. Om du tar bort Kubernetes-tjänst, raderas också associerade belastningsutjämnare och IP-adress. Om du vill tilldela en specifik IP-adress eller behålla en IP-adress för omdistribuerade Kubernetes-tjänster kan du skapa och använda en statisk offentlig IP-adress.

Den här artikeln visar hur du skapar en statisk offentlig IP-adress och tilldela den till ditt Kubernetes-tjänst.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Du också ha Azure CLI version 2.0.59 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

För närvarande bara *grundläggande IP-SKU*stöds. Arbete pågår för den *Standard IP* resource SKU. Mer information finns i [IP-adresstyper och allokeringsmetoder i Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Skapa en statisk IP-adress

När du skapar en statisk offentlig IP-adress för användning med AKS, IP-adressresurs ska skapas i den **noden** resursgrupp. Om du vill att avgränsa resurser, se följande avsnitt för att [en statisk IP-adress utanför resursgruppen noden](#use-a-static-ip-address-outside-of-the-node-resource-group).

Hämta först noden resursgruppens namn med den [az aks show] [ az-aks-show] kommandot och lägga till den `--query nodeResourceGroup` frågeparameter. I följande exempel hämtar noden resursgruppen för AKS-klusternamnet *myAKSCluster* i resursgruppens namn *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_chinaeast
```

Nu skapa en statisk offentlig IP-adress med det [az nätverket offentliga ip-skapa] [ az-network-public-ip-create] kommando. Ange noden resursgruppens namn som hämtades i föregående kommando och sedan ett namn för IP-Adressen kan du hantera resurs, som *myAKSPublicIP*:

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_chinaeast \
    --name myAKSPublicIP \
    --allocation-method static
```

IP-adressen visas enligt följande komprimerade exempel på utdata:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_chinaeast/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Du kan senare får den offentliga IP-adress med hjälp av den [az network public-ip-listan] [ az-network-public-ip-list] kommando. Ange namnet på resursgruppen för noden och offentlig IP-adress som du skapade och fråga efter den *ipAddress* som visas i följande exempel:

```azurecli
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_chinaeast --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Skapa en tjänst med statisk IP-adress

När du skapar en tjänst med den statiska offentliga IP-adressen till den `loadBalancerIP` egenskapen och värdet för den statiska offentliga IP-adress till YAML-manifestet. Skapa en fil med namnet `load-balancer-service.yaml` och kopiera följande YAML. Ange dina egna offentliga IP-adressen som skapades i föregående steg.

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

Skapa tjänsten och distribution med den `kubectl apply` kommando.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Använda en statisk IP-adress utanför resursgruppen nod

Med Kubernetes 1.10 eller senare, kan du använda en statisk IP-adress som skapas utanför resursgruppen noden. Tjänstens huvudnamn som används av AKS-klustret måste ha delegerats behörighet till andra resursgruppen, som visas i följande exempel:

```azurecli
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Om du vill använda en IP-adress utanför resursgruppen noden, att lägga till en anteckning till tjänstdefinitionen. I följande exempel anger anteckningen till resursgruppen med namnet *myResourceGroup*. Ange din egen resursgruppens namn:

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

Om den statiska IP-adressen som definierats i den *loadBalancerIP* egenskapen för Kubernetes tjänstmanifestet finns inte eller har inte skapats i resursgruppen noden och inga ytterligare delegeringar konfigurerats, tjänsten för belastningsutjämning Det går inte att skapa. Felsök genom att granska service skapande händelser med den [kubectl beskriver] [ kubectl-describe] kommando. Ange namnet på tjänsten som anges i YAML-manifest som visas i följande exempel:

```console
kubectl describe service azure-load-balancer
```

Information om Kubernetes service-resurs visas. Den *händelser* i slutet av följande Exempelutdata tyda på att den *användaren inte gick att hitta den angivna IP-adressen*. Kontrollera att du har skapat statiska offentliga IP-adress i resursgruppen noden och att den IP-adressen som anges i tjänstmanifestet Kubernetes är korrekt i dessa scenarier.

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

För ytterligare kontroll över nätverkstrafik till dina program kan du i stället [skapa en ingress-kontrollant][aks-ingress-basic]. Du kan också [skapa en ingress-kontrollant med en statisk offentlig IP-adress][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create
[az-network-public-ip-list]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-list
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
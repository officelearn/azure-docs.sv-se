---
title: Använda en statisk IP-adress med belastningsutjämnare för Azure Kubernetes Service (AKS)
description: Använda en statisk IP-adress med belastningsutjämnare för Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 87fe014d5c19be675d4f6cac876548a31a4484b4
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42060728"
---
# <a name="use-a-static-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Använda en statisk IP-adress med belastningsutjämnare för Azure Kubernetes Service (AKS)

I vissa fall, t.ex. när Azure Kubernetes Service (AKS) läser in belastningsutjämnare återskapas eller Kubernetes-tjänster med en typ av LoadBalancer återskapas kan den offentliga IP-adressen för Kubernetes-tjänst ändras. Den här dokumentet beskriver hur du konfigurerar en statisk IP-adress för Kubernetes-tjänster.

## <a name="create-static-ip-address"></a>Skapa statiska IP-adress

Skapa en statisk offentlig IP-adress för Kubernetes-tjänst. IP-adressen måste skapas i AKS **noden** resursgrupp. Hämta resursgruppens namn med den [az resource show] [ az-resource-show] kommando.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Använd den [az nätverket offentliga ip-skapa] [ az-network-public-ip-create] kommando för att skapa IP-adress.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Anteckna IP-adressen.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 Om det behövs adressen kan hämtas med hjälp av den [az network public-ip-listan] [ az-network-public-ip-list] kommando.

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv
```

```console
40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Skapa tjänst med IP-adress

När den statiska IP-adressen har etablerats, en Kubernetes-tjänst kan skapas med den `loadBalancerIP` egenskap och ett värde av den statiska IP-adressen.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>Felsökning

Om den statiska IP-adressen inte har skapats eller har skapats i fel resursgrupp, inte tjänsten skapas. Om du vill felsöka returnera service-skapande händelser med den [kubectl beskriver] [ kubectl-describe] kommando.

```azurecli-interactive
kubectl describe service azure-vote-front
```

```console
Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
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
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-resource-show]: /cli/azure/resource#az-resource-show

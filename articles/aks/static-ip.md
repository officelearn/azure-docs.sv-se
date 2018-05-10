---
title: Använda en statisk IP-adress med belastningsutjämnaren Azure Kubernetes Service (AKS)
description: Använda en statisk IP-adress med belastningsutjämnaren Azure Kubernetes Service (AKS).
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: da1fc4cd31a4b4f13da5b527e2a62e147799bfef
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="use-a-static-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Använda en statisk IP-adress med belastningsutjämnaren Azure Kubernetes Service (AKS)

I vissa fall, som när Azure Kubernetes Service (AKS) läser in belastningsutjämnaren återskapas eller Kubernetes tjänster med en typ av LoadBalancer återskapas kan tjänsten Kubernetes offentliga IP-adress ändras. Det här dokumentet beskriver hur du konfigurerar en statisk IP-adress för Kubernetes-tjänster.

## <a name="create-static-ip-address"></a>Skapa statisk IP-adress

Skapa en statisk offentlig IP-adress för tjänsten Kubernetes. IP-adressen måste skapas i den resursgrupp som har skapats automatiskt under distributionen av klustret. Mer information om olika AKS resursgrupper och hur du identifierar automatiskt skapat resursgruppen finns i [AKS vanliga frågor och svar][aks-faq-resource-group].

Använd den [az nätverket offentliga ip-skapa] [ az-network-public-ip-create] kommando för att skapa IP-adress.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGRoup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
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

 Om det behövs, adressen som kan hämtas med hjälp av den [az offentliga ip-lista över] [ az-network-public-ip-list] kommando.

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGRoup_myAKSCluster_eastus --query [0].ipAddress --output tsv
```

```console
40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Skapa tjänst med IP-adress

När den statiska IP-adressen har etablerats, en tjänst för Kubernetes kan skapas med den `loadBalancerIP` egenskapen och ett värde av den statiska IP-adressen.

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

Om den statiska IP-adressen inte har skapats eller har skapats i fel resursgrupp, misslyckas skapa en tjänst. Returnera service-skapande händelser med hur du felsöker det [kubectl beskrivs] [ kubectl-describe] kommando.

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
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az_network_public_ip_list

---
title: Godkända utgående trafik från Azure Kubernetes Service (AKS)-kluster
description: Godkända utgående trafik från ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: nepeters
ms.openlocfilehash: 0bafb62fcdc8a24084cf7170a0e0f72bfd7824b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655356"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Azure Kubernetes Service (AKS) utgång

Som standard tilldelas slumpmässigt utgång-adress från ett kluster i Azure Kubernetes Service (AKS). Den här konfigurationen är inte perfekt när du behöver identifiera en IP-adress för att komma åt externa tjänster. Det här dokumentet beskriver hur du skapar och underhåller statiskt tilldelade utgående IP-adress i ett AKS kluster.

## <a name="egress-overview"></a>Översikt över utgång

Utgående trafik från ett kluster AKS följer Azure belastningsutjämnare konventioner som finns dokumenterade [här][outbound-connections]. Före den första Kubernetes tjänsten av typen `LoadBalancer` har skapats kan agenten noder tillhör inte någon Azure belastningsutjämnare-adresspool. I den här konfigurationen är noder utan en instansnivå offentlig IP-adress. Azure översätter utgående begränsas till en offentlig IP-källadress som inte är konfigurerbara eller deterministisk.

När en Kubernetes tjänst av typen `LoadBalancer` skapas agent noder läggs till i en pool med Azure belastningsutjämnare. För utgående flöde översätter Azure den första offentliga IP-adressen konfigurerad på belastningsutjämnaren.

## <a name="create-a-static-public-ip"></a>Skapa en statisk offentlig IP-adress

Skapa en statisk IP-adress för att förhindra att slumpmässiga IP-adresser som används och kontrollera belastningsutjämnaren använder den här adressen. IP-adressen måste skapas i AKS **nod** resursgruppen.

Hämta resursgruppens namn med den [az resurs visa] [ az-resource-show] kommando. Uppdatera resursgruppens namn och klusternamnet som matchar din miljö.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Använd sedan den [az nätverket offentliga IP-skapa] [ public-ip-create] kommando för att skapa en statisk offentlig IP-adress. Uppdatera resursgruppens namn att matcha namnet gatherred i det sista steget.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Skapa en tjänst med statisk IP-adress

Nu när du har en IP-adress, skapar du en Kubernetes tjänst med vilken `LoadBalancer` och tilldela IP-adressen till tjänsten.

Skapa en fil med namnet `egress-service.yaml` och kopiera följande YAML. Uppdatera IP-adressen för att matcha din miljö.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

Skapa service och distribution med den `kubectl apply` kommando.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

Skapa den här tjänsten konfigurerar en ny IP-adress för klientdel på belastningsutjämnaren Azure. Om du inte har någon annan IP-adresser konfigurerats och **alla** utgående trafik bör nu använda den här adressen. När flera adresser är konfigurerade på Azure belastningsutjämnare använder utgång första IP-Adressen på den belastningsutjämnaren.

## <a name="verify-egress-address"></a>Kontrollera utgång adress

Om du vill verifiera att den offentliga IP-adressen används, använda en tjänst som `checkip.dyndns.org`.

Starta och ansluta till en baljor:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

Om det behövs installerar du curl i behållaren:

```console
$ apt-get update && apt-get install curl -y
```

CURL `checkip.dyndns.org`, som returnerar utgång IP-adress:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

Du bör se att IP-adressen matchar statisk IP-adress kopplad till Azure belastningsutjämnare.

## <a name="ingress-controller"></a>Meddelanden om ingångs-styrenhet

Överväg att använda ett meddelande om ingångs-domänkontrollant för att undvika att underhålla flera offentliga IP-adresser på belastningsutjämnaren Azure. Meddelanden om ingångs-styrenheter ger fördelar som belastningsutjämning, SSL/TLS-avslutning, stöd för URI omskrivningar och överordnade SSL/TLS-kryptering. Mer information om meddelanden om ingångs-domänkontrollanter i AKS finns det [konfigurera NGINX ingång domänkontrollant i ett kluster med AKS] [ ingress-aks-cluster] guide.

## <a name="next-steps"></a>Nästa steg

Läs mer om programmet visas i det här dokumentet.

- [Helm CLI][helm-cli-install]
- [NGINX ingång domänkontrollant][nginx-ingress]
- [Azure Load Balancer utgående anslutningar][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

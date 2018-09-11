---
title: Lista över tillåtna utgående trafik från kluster Azure Kubernetes Service (AKS)
description: Lista över tillåtna utgående trafik från ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: e2793a72fcbc20b79bdd564e331426fedf1ae34b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347808"
---
# <a name="azure-kubernetes-service-aks-egress"></a>Egress i Azure Kubernetes Service (AKS)

Som standard tilldelas slumpmässigt den utgående adressen från ett kluster i Azure Kubernetes Service (AKS). Den här konfigurationen är inte idealiskt om du behöver identifiera en IP-adress för att komma åt externa tjänster. Det här dokumentet beskriver hur du skapar och underhåller en statiskt tilldelad utgående IP-adress i ett AKS-kluster.

## <a name="egress-overview"></a>Översikt över utgående

Utgående trafik från ett AKS-kluster följer Azure Load Balancer-konventioner som finns dokumenterade [här][outbound-connections]. Innan den första Kubernetes-tjänsten av typen `LoadBalancer` skapas, agenten noder tillhör inte någon Azure Load Balancer-pool. I den här konfigurationen är noder utan en instansnivå offentlig IP-adress. Azure översätter utgående flödet till en offentlig källans IP-adress som inte är konfigurerbara eller deterministisk.

När en Kubernetes-tjänst av typen `LoadBalancer` skapas, agenten noder läggs till i en Azure Load Balancer-pool. För utgående flödet Omvandlar det till den första offentliga IP-adressen som har konfigurerats på belastningsutjämnaren i Azure.

## <a name="create-a-static-public-ip"></a>Skapa en statisk offentlig IP-adress

Skapa en statisk IP-adress för att förhindra att slumpmässiga IP-adresser som används och se till belastningsutjämnaren använder den här adressen. IP-adressen måste skapas i AKS **noden** resursgrupp.

Hämta resursgruppens namn med den [az resource show] [ az-resource-show] kommando. Uppdatera resursgruppens namn och klusternamnet matchar din miljö.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Använd sedan den [az nätverket offentliga ip-skapa] [ public-ip-create] kommando för att skapa en statisk offentlig IP-adress. Uppdatera resursgruppens namn som matchar namnet gatherred i det sista steget.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>Skapa en tjänst med statisk IP-adress

Nu när du har en IP-adress kan du skapa en Kubernetes-tjänst med typen `LoadBalancer` och tilldela IP-adressen till tjänsten.

Skapa en fil med namnet `egress-service.yaml` och kopiera följande YAML. Uppdatera IP-adress som matchar din miljö.

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

Skapa tjänsten och distribution med den `kubectl apply` kommando.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

Skapa den här tjänsten konfigurerar en ny frontend-IP-adress på Azure Load Balancer. Om du inte har någon annan IP-adresser konfigurerats och **alla** utgående trafik bör nu använda den här adressen. När flera adresser har konfigurerats i Azure Load Balancer, använder utgående den första IP-Adressen för den belastningsutjämnaren.

## <a name="verify-egress-address"></a>Verifiera utgående adressen

Kontrollera att den offentliga IP-adressen som används genom att använda en tjänst som `checkip.dyndns.org`.

Starta och ansluta till en pod:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

Om det behövs installerar du curl i behållaren:

```console
$ apt-get update && apt-get install curl -y
```

CURL `checkip.dyndns.org`, vilket returnerar den utgående IP-adressen:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

Du bör se att den IP-adressen matchar den statisk IP-adress som är kopplade till Azure-belastningsutjämnaren.

## <a name="ingress-controller"></a>Ingress-kontrollant

Överväg att använda en ingress-kontrollanten för att undvika underhålla flera offentliga IP-adresser i Azure-belastningsutjämnaren. Ingress-styrenheter ger fördelar som belastningsutjämning, SSL/TLS-avslutning, stöd för URI omskrivningar och överordnade SSL/TLS-kryptering. Läs mer om ingångs-domänkontrollanter i AKS i [konfigurera NGINX ingress-kontrollant i ett AKS-kluster] [ ingress-aks-cluster] guide.

## <a name="next-steps"></a>Nästa steg

Läs mer om den programvara som visas i det här dokumentet.

- [Azure CLI][helm-cli-install]
- [Ingress-kontrollanten för NGINX][nginx-ingress]
- [Azure Load Balancer utgående anslutningar][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

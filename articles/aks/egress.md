---
title: Statisk IP-adress för utgående trafik i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och använder en statisk offentlig IP-adress för utgående trafik i ett AKS-kluster (Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 5850f8dfc08ed80dfe5e5e13f49808c3fd9338c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595764"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Använda en statisk offentlig IP-adress för utgående trafik i Azure Kubernetes Service (AKS)

Som standard tilldelas utgående IP-adress från ett AKS-kluster (Azure Kubernetes Service) slumpmässigt. Den här konfigurationen är inte idealisk när du behöver identifiera en IP-adress för åtkomst till externa tjänster, till exempel. I stället kan du behöva tilldela en statisk IP-adress som kan vitlistas för tjänståtkomst.

Den här artikeln visar hur du skapar och använder en statisk offentlig IP-adress för användning med utgående trafik i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Du behöver också Azure CLI version 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="egress-traffic-overview"></a>Översikt över utgående trafik

Utgående trafik från ett AKS-kluster följer [Azure Load Balancer-konventioner][outbound-connections]. Innan den första Kubernetes-tjänsten av typen `LoadBalancer` skapas är agentnoderna i ett AKS-kluster inte en del av någon Azure Load Balancer-pool. I den här konfigurationen har noderna ingen offentlig IP-adress på instansnivå. Azure översätter det utgående flödet till en offentlig källa IP-adress som inte är konfigurerbar eller deterministisk.

När en Kubernetes-tjänst av typen `LoadBalancer` har skapats läggs agentnoder till i en Azure Load Balancer-pool. För utgående flöde översätter Azure det till den första offentliga IP-adressen som konfigurerats på belastningsutjämnaren. Den här offentliga IP-adressen är endast giltig under resursens livslängd. Om du tar bort tjänsten Kubernetes LoadBalancer tas även den associerade belastningsutjämnaren och IP-adressen bort. Om du vill tilldela en specifik IP-adress eller behålla en IP-adress för omdedelade Kubernetes-tjänster kan du skapa och använda en statisk offentlig IP-adress.

## <a name="create-a-static-public-ip"></a>Skapa en statisk offentlig IP-adress

Hämta resursgruppsnamnet med kommandot [az aks show][az-aks-show] och lägg till `--query nodeResourceGroup` frågeparametern. I följande exempel hämtar nodresursgruppen för AKS-klusternamnet *myAKSCluster* i resursgruppnamnet *myResourceGroup:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Nu skapa en statisk offentlig IP-adress med [az nätverk offentliga ip skapa][az-network-public-ip-create] kommandot. Ange namnet på nodresursgruppen som erhölls i föregående kommando och sedan ett namn på IP-adressresursen, till exempel *myAKSPublicIP:*

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

IP-adressen visas, vilket visas i följande komprimerade exempelutdata:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

Du kan senare hämta den offentliga IP-adressen med kommandot [az network public-ip list.][az-network-public-ip-list] Ange namnet på nodresursgruppen och fråga sedan efter *ipAddress* som visas i följande exempel:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Skapa en tjänst med den statiska IP-adressen

Om du vill skapa en tjänst med `loadBalancerIP` den statiska offentliga IP-adressen lägger du till egenskapen och värdet för den statiska offentliga IP-adressen i YAML-manifestet. Skapa en `egress-service.yaml` fil med namnet och kopiera i följande YAML. Ange din egen offentliga IP-adress som skapats i föregående steg.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Skapa tjänsten och distributionen `kubectl apply` med kommandot.

```console
kubectl apply -f egress-service.yaml
```

Den här tjänsten konfigurerar en ny frontend-IP på Azure Load Balancer. Om du inte har konfigurerat några andra IPs bör **all** utgående trafik nu använda den här adressen. När flera adresser konfigureras på Azure Load Balancer använder utgående den första IP-adressen för den belastningsutjämnaren.

## <a name="verify-egress-address"></a>Verifiera utgående adress

Om du vill kontrollera att den statiska offentliga IP-adressen används `checkip.dyndns.org`kan du använda DNS-uppslagstjänst, till exempel .

Starta och koppla till en grundläggande *Debianpod:*

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

Om du vill komma åt en `apt-get` webbplats `curl` inifrån behållaren använder du för att installera i behållaren.

```console
apt-get update && apt-get install curl -y
```

Använd nu curl för att komma åt *checkip.dyndns.org* webbplats. Utgående IP-adress visas, som visas i följande exempelutdata. Den här IP-adressen matchar den statiska offentliga IP-adress som skapats och definierats för loadBalancer-tjänsten:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Nästa steg

Om du vill undvika att underhålla flera offentliga IP-adresser på Azure Load Balancer kan du i stället använda en ingress-styrenhet. Ingående styrenheter ger ytterligare fördelar som SSL/TLS-avslutning, stöd för URI-omskrivningar och uppströms SSL/TLS-kryptering. Mer information finns [i Skapa en grundläggande ingress-styrenhet i AKS][ingress-aks-cluster].

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli

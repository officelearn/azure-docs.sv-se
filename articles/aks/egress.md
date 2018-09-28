---
title: Statisk IP-adress för utgående trafik i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och använder en statisk offentlig IP-adress för utgående trafik i ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: 175fa625a94626cde4d782abd1e9629530cab8b4
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408531"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>Använda en statisk offentlig IP-adress för utgående trafik i Azure Kubernetes Service (AKS)

Som standard tilldelas slumpmässigt utgående IP-adress från ett kluster i Azure Kubernetes Service (AKS). Den här konfigurationen är inte idealiskt när du behöver identifiera en IP-adress för åtkomst till externa tjänster, till exempel. Du kan i stället måste du tilldela en statisk IP-adress som kan vara godkänd för åtkomst till tjänsten.

Den här artikeln visar hur du skapar och använder en statisk offentlig IP-adress för användning med utgående trafik i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

Du också ha Azure CLI version 2.0.46 eller senare installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="egress-traffic-overview"></a>Översikt för utgående trafik

Utgående trafik från ett AKS-kluster följer [Azure Load Balancer konventioner][outbound-connections]. Innan den första Kubernetes-tjänsten av typen `LoadBalancer` skapas, agenten noder i ett AKS-kluster kan inte en del av en Azure Load Balancer-pool. I den här konfigurationen har noderna ingen instans offentlig IP-adress. Azure översätter utgående flödet till en offentlig källans IP-adress som inte är konfigurerbara eller deterministisk.

När en Kubernetes-tjänst av typen `LoadBalancer` skapas, agenten noder läggs till i en Azure Load Balancer-pool. För utgående flödet Omvandlar det till den första offentliga IP-adressen som har konfigurerats på belastningsutjämnaren i Azure. Den här offentliga IP-adressen är endast giltig för livslängden för den här resursen. Om du tar bort tjänsten Kubernetes LoadBalancer, raderas också associerade belastningsutjämnare och IP-adress. Om du vill tilldela en specifik IP-adress eller behålla en IP-adress för omdistribuerade Kubernetes-tjänster kan du skapa och använda en statisk offentlig IP-adress.

## <a name="create-a-static-public-ip"></a>Skapa en statisk offentlig IP-adress

När du skapar en statisk offentlig IP-adress för användning med AKS, IP-adressresurs måste skapas i den **noden** resursgrupp. Hämta resursgruppens namn med den [az aks show] [ az-aks-show] kommandot och lägga till den `--query nodeResourceGroup` frågeparameter. I följande exempel hämtar noden resursgruppen för AKS-klusternamnet *myAKSCluster* i resursgruppens namn *myResourceGroup*:

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Nu skapa en statisk offentlig IP-adress med det [az nätverket offentliga ip-skapa] [ az-network-public-ip-create] kommando. Ange noden resursgruppens namn som hämtades i föregående kommando och sedan ett namn för IP-Adressen kan du hantera resurs, som *myAKSPublicIP*:

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

IP-adressen visas enligt följande komprimerade exempel på utdata:

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
````

Du kan senare får den offentliga IP-adress med hjälp av den [az network public-ip-listan] [ az-network-public-ip-list] kommando. Ange namnet på resursgruppen noden och sedan fråga efter den *ipAddress* som visas i följande exempel:

```azurecli
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Skapa en tjänst med statisk IP-adress

När du skapar en tjänst med den statiska offentliga IP-adressen till den `loadBalancerIP` egenskapen och värdet för den statiska offentliga IP-adress till YAML-manifestet. Skapa en fil med namnet `egress-service.yaml` och kopiera följande YAML. Ange dina egna offentliga IP-adressen som skapades i föregående steg.

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

Skapa tjänsten och distribution med den `kubectl apply` kommando.

```console
kubectl apply -f egress-service.yaml
```

Den här tjänsten konfigurerar en ny frontend-IP-adress på Azure Load Balancer. Om du inte har någon annan IP-adresser konfigurerats och **alla** utgående trafik bör nu använda den här adressen. När flera adresser har konfigurerats i Azure Load Balancer, använder utgående den första IP-Adressen för den belastningsutjämnaren.

## <a name="verify-egress-address"></a>Verifiera utgående adressen

Kontrollera att statiska offentliga IP-adress som används genom att du kan använda DNS-sökning tjänsten som `checkip.dyndns.org`.

Starta och ansluta till en grundläggande *Debian* pod:

```console
kubectl run -it --rm aks-ip --image=debian
```

Om du vill få åtkomst till en webbplats från behållaren måste använda `apt-get` installera `curl` till behållaren.

```console
apt-get update && apt-get install curl -y
```

Nu använder vi curl för att komma åt den *checkip.dyndns.org* plats. Utgående IP-adressen visas som visas i följande Exempelutdata. Den här IP-adressen matchar statiska offentliga IP-adress skapat och definierat för loadBalancer-tjänsten:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

## <a name="next-steps"></a>Nästa steg

För att undvika att underhålla flera offentliga IP-adresser på Azure Load Balancer kan använda du i stället en ingress-kontrollant. Ingress-styrenheter omfattar ytterligare förmåner, till exempel SSL/TLS-avslutning, stöd för URI omskrivningar och överordnade SSL/TLS-kryptering. Mer information finns i [skapa en grundläggande ingress-kontrollant i AKS][ingress-aks-cluster].

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
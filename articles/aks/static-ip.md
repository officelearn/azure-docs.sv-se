---
title: Använd statisk IP med belastningsutjämnare
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skapar och använder en statisk IP-adress med Azure Kubernetes Service (AKS) belastningsutjämnare.
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 5051232f29ad51d9fee893a4a660fc81f6e60d77
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886746"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Använda en statisk offentlig IP-adress och DNS-etikett med Azure Kubernetes Service (AKS) belastningsutjämnare

Som standard är den offentliga IP-adress som tilldelats en belastningsutjämnad resurs som skapats av ett AKS-kluster endast giltig för resursens livslängd. Om du tar bort tjänsten Kubernetes tas även den associerade belastningsutjämnaren och IP-adressen bort. Om du vill tilldela en specifik IP-adress eller behålla en IP-adress för omdedelade Kubernetes-tjänster kan du skapa och använda en statisk offentlig IP-adress.

Den här artikeln visar hur du skapar en statisk offentlig IP-adress och tilldelar den till din Kubernetes-tjänst.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

Du behöver också Azure CLI version 2.0.59 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

Den här artikeln beskriver hur du använder en *Standard* SKU IP med en *Standard* SKU belastningsutjämnare. Mer information finns i [IP-adresstyper och allokeringsmetoder i Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Skapa en statisk IP-adress

Skapa en statisk offentlig IP-adress med kommandot [az network public ip create.][az-network-public-ip-create] Följande skapar en statisk IP-resurs med namnet *myAKSPublicIP* i resursgruppen *myResourceGroup:*

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Om du använder en *grundläggande* SKU-belastningsutjämnare i AKS-klustret använder du *Basic* för *parametern sku* när du definierar en offentlig IP. Endast *grundläggande* SKU-IKU-IPS fungerar med *grundläggande* SKU-belastningsutjämnar och endast *Standard* SKU-IPS fungerar med *Standard* SKU-belastningsutjämnare. 

IP-adressen visas, vilket visas i följande komprimerade exempelutdata:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Du kan senare hämta den offentliga IP-adressen med kommandot [az network public-ip list.][az-network-public-ip-list] Ange namnet på nodresursgruppen och den offentliga IP-adressen som du skapade och fråga efter *ipAddress* som visas i följande exempel:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Skapa en tjänst med den statiska IP-adressen

Innan du skapar en tjänst bör du se till att tjänstens huvudnamn som används av AKS-klustret har delegerat behörigheter till den andra resursgruppen. Ett exempel:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Du kan också använda den systemtilldelade hanterade identiteten för behörigheter i stället för tjänstens huvudnamn. Mer information finns i [Använda hanterade identiteter](use-managed-identity.md).

Om du vill skapa en *LoadBalancer-tjänst* `loadBalancerIP` med den statiska offentliga IP-adressen lägger du till egenskapen och värdet för den statiska offentliga IP-adressen i YAML-manifestet. Skapa en `load-balancer-service.yaml` fil med namnet och kopiera i följande YAML. Ange din egen offentliga IP-adress som skapats i föregående steg. I följande exempel anges också anteckningen till resursgruppen *myResourceGroup*. Ange ditt eget resursgruppnamn.

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

Skapa tjänsten och distributionen `kubectl apply` med kommandot.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Använda en DNS-etikett på tjänsten

Om tjänsten använder en dynamisk eller statisk offentlig IP-adress kan `service.beta.kubernetes.io/azure-dns-label-name` du använda tjänstenteckningen för att ange en offentlig DNS-etikett. Detta publicerar ett fullständigt kvalificerat domännamn för din tjänst med hjälp av Azures offentliga DNS-servrar och toppdomän. Anteckningsvärdet måste vara unikt på Azure-platsen, så det rekommenderas att du använder en tillräckligt kvalificerad etikett.   

Azure lägger sedan automatiskt till ett standardundernät, till exempel `<location>.cloudapp.azure.com` (där platsen är den region du valt), till det namn du anger, för att skapa det fullständigt kvalificerade DNS-namnet. Ett exempel:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Information om hur du publicerar tjänsten på din egen domän finns i [Azure DNS][azure-dns-zone] och projektet [external-dns.][external-dns]

## <a name="troubleshoot"></a>Felsöka

Om den statiska IP-adressen som definierats i egenskapen *loadBalancerIP* för Kubernetes-tjänstmanifestet inte finns eller inte har skapats i nodresursgruppen och inga ytterligare delegeringar har konfigurerats, misslyckas skapande av belastningsutjämnartjänsten. Om du vill felsöka läser du händelsen för att skapa tjänsten med kommandot [kubectl describe.][kubectl-describe] Ange namnet på tjänsten enligt yaml-manifestet, som visas i följande exempel:

```console
kubectl describe service azure-load-balancer
```

Information om Kubernetes tjänstresurs visas. *Händelserna* i slutet av följande exempelutdata anger att den *användar som angavs ip-adress inte hittades*. I dessa scenarier kontrollerar du att du har skapat den statiska offentliga IP-adressen i nodresursgruppen och att IP-adressen som anges i Kubernetes-tjänstmanifestet är korrekt.

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

Om du vill ha ytterligare kontroll över nätverkstrafiken till dina program kanske du i stället vill [skapa en ingångskontrollant][aks-ingress-basic]. Du kan också [skapa en ingående styrenhet med en statisk offentlig IP-adress][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

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

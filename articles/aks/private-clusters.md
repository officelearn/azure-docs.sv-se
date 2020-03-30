---
title: Skapa ett privat Azure Kubernetes-tjänstkluster
description: Lär dig hur du skapar ett AKS-kluster (Private Azure Kubernetes Service)
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: cdefcfe460a97f647afa05947e92fae0c4d07001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499300"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Skapa ett privat Azure Kubernetes-tjänstkluster

I ett privat kluster har kontrollplanet eller API-servern interna IP-adresser som definieras i [RFC1918 - Adressallokering för privata Internets-dokument.](https://tools.ietf.org/html/rfc1918) Genom att använda ett privat kluster kan du se till att nätverkstrafiken mellan API-servern och nodpoolerna endast finns kvar i det privata nätverket.

Kontrollplanet eller API-servern finns i en AZURE Kubernetes Service (AKS)-hanterad Azure-prenumeration. En kunds kluster- eller nodpool finns i kundens prenumeration. Servern och kluster- eller nodpoolen kan kommunicera med varandra via [Azure Private Link-tjänsten][private-link-service] i det virtuella API-serverns nätverk och en privat slutpunkt som visas i undernätet i kundens AKS-kluster.

## <a name="prerequisites"></a>Krav

* Azure CLI version 2.2.0 eller senare

## <a name="create-a-private-aks-cluster"></a>Skapa ett privat AKS-kluster

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp eller använd en befintlig resursgrupp för AKS-klustret.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Grundläggande standardnätverk 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Där *--enable-private-cluster* är en obligatorisk flagga för ett privat kluster. 

### <a name="advanced-networking"></a>Avancerade nätverk  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Där *--enable-private-cluster* är en obligatorisk flagga för ett privat kluster. 

> [!NOTE]
> Om Docker-bryggans adress CIDR (172.17.0.1/16) krockar med undernätet CIDR ändrar du Docker-bryggadressen på lämpligt sätt.

## <a name="options-for-connecting-to-the-private-cluster"></a>Alternativ för anslutning till det privata klustret

API-serverslutpunkten har ingen offentlig IP-adress. För att hantera API-servern måste du använda en virtuell dator som har åtkomst till AKS-klustrets Virtuella Nätverk (Azure Virtual Network) (VNet). Det finns flera alternativ för att upprätta nätverksanslutning till det privata klustret.

* Skapa en virtuell dator i samma Virtuella Azure-nätverk (VNet) som AKS-klustret.
* Använd en virtuell dator i ett separat nätverk och konfigurera [Virtuell nätverks peering][virtual-network-peering].  Se avsnittet nedan för mer information om det här alternativet.
* Använd en [Express Route- eller VPN-anslutning.][express-route-or-VPN]

Att skapa en virtuell dator i samma virtuella nätverk som AKS-klustret är det enklaste alternativet.  Express Route och VPN lägga till kostnader och kräver ytterligare nätverk komplexitet.  Virtuell nätverks peering kräver att du planerar nätverkets CIDR-intervall för att säkerställa att det inte finns några överlappande intervall.

## <a name="virtual-network-peering"></a>Virtuell nätverkspeering

Som nämnts är VNet-peering ett sätt att komma åt ditt privata kluster. Om du vill använda VNet-peering måste du skapa en länk mellan virtuellt nätverk och den privata DNS-zonen.
    
1. Gå till resursgruppen MC_* i Azure-portalen.  
2. Välj den privata DNS-zonen.   
3. Välj länken **Virtuellt nätverk** i den vänstra rutan.  
4. Skapa en ny länk för att lägga till det virtuella nätverket för den virtuella datorn i den privata DNS-zonen. Det tar några minuter innan DNS-zonlänken blir tillgänglig.  
5. Gå tillbaka till resursgruppen MC_* i Azure-portalen.  
6. Välj det virtuella nätverket i den högra rutan. Det virtuella nätverksnamnet finns i formuläret *aks-vnet-\**.  
7. Välj **Peerings**i den vänstra rutan .  
8. Välj **Lägg till**, lägg till det virtuella nätverket för den virtuella datorn och skapa sedan peering.  
9. Gå till det virtuella nätverket där du har den virtuella datorn, välj **Peerings,** välj AKS virtuella nätverk och skapa sedan peering. Om adressintervallen i AKS virtuella nätverk och den virtuella datorns virtuella nätverk kolliderar, misslyckas peering. Mer information finns i [Virtual Network peering][virtual-network-peering].

## <a name="dependencies"></a>Beroenden  

* Tjänsten Private Link stöds endast på Standard Azure Load Balancer. Grundläggande Azure Load Balancer stöds inte.  
* Om du vill använda en anpassad DNS-server lägger du till Azure DNS IP 168.63.129.16 som adstream-DNS-server på den anpassade DNS-servern.

## <a name="limitations"></a>Begränsningar 
* IP-auktoriserade intervall kan inte tillämpas på den privata api-serverslutpunkten, de gäller bara för den offentliga API-servern
* Tillgänglighetszoner stöds för närvarande för vissa regioner, se början av det här dokumentet 
* [Azure Private Link-tjänstbegränsningar][private-link-service] gäller för privata kluster, privata Azure-slutpunkter och slutpunkter för virtuella nätverkstjänster, som för närvarande inte stöds i samma virtuella nätverk.
* Inget stöd för virtuella noder i ett privat kluster för att snurra privata Azure Container Instances (ACI) i ett privat virtuellt Azure-nätverk
* Inget stöd för Azure DevOps-integrering utanför boxen med privata kluster
* För kunder som behöver aktivera Azure Container Registry för att arbeta med privata AKS, måste det virtuella nätverket för behållarregistret peered med agentklustrets virtuella nätverk.
* Inget aktuellt stöd för Azure Dev Spaces
* Inget stöd för att konvertera befintliga AKS-kluster till privata kluster
* Om du tar bort eller ändrar den privata slutpunkten i kundens undernät slutar klustret att fungera. 
* Azure Monitor för behållare Live Data stöds för närvarande inte.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md


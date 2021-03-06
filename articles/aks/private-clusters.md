---
title: Skapa ett privat Azure Kubernetes service-kluster
description: Lär dig hur du skapar ett privat Azure Kubernetes service-kluster (AKS)
services: container-service
ms.topic: article
ms.date: 7/17/2020
ms.openlocfilehash: 450d68e26c5a3fc1ecfbaf6a3be6b5f698ee65e3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183268"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Skapa ett privat Azure Kubernetes service-kluster

I ett privat kluster har kontroll planet eller API-servern interna IP-adresser som definieras i [RFC1918 för privat Internet-](https://tools.ietf.org/html/rfc1918) dokument. Genom att använda ett privat kluster kan du se till att nätverks trafiken mellan API-servern och noderna i pooler fortfarande finns kvar i det privata nätverket.

Kontroll planet eller API-servern finns i en Azure Kubernetes service (AKS)-hanterad Azure-prenumeration. En kunds kluster eller Node-pool är i kundens prenumeration. Servern och klustret eller noden kan kommunicera med varandra via [tjänsten Azure Private Link][private-link-service] i det virtuella nätverkets API-Server och en privat slut punkt som exponeras i under nätet för KUNDEns AKS-kluster.

## <a name="region-availability"></a>Regional tillgänglighet

Privat kluster är tillgängligt i offentliga regioner, Azure Government och Azure Kina 21Vianet-regioner där [AKS stöds](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).

> [!NOTE]
> Azure Government-platser stöds, men US Gov, Texas stöds inte för närvarande på grund av stöd för privata länkar.

## <a name="prerequisites"></a>Förutsättningar

* Azure CLI-version 2.2.0 eller senare

## <a name="create-a-private-aks-cluster"></a>Skapa ett privat AKS-kluster

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resurs grupp eller Använd en befintlig resurs grupp för ditt AKS-kluster.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Standard nätverk för grundläggande 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Där `--enable-private-cluster` är en obligatorisk flagga för ett privat kluster. 

### <a name="advanced-networking"></a>Avancerat nätverk  

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
Där `--enable-private-cluster` är en obligatorisk flagga för ett privat kluster. 

> [!NOTE]
> Om Docker-bryggan Address CIDR (172.17.0.1/16) står i konflikt med under nätets CIDR, ändra Docker-bryggans adress på lämpligt sätt.

## <a name="options-for-connecting-to-the-private-cluster"></a>Alternativ för att ansluta till det privata klustret

API-serverns slut punkt har ingen offentlig IP-adress. Om du vill hantera API-servern måste du använda en virtuell dator som har åtkomst till AKS-klustrets Azure-Virtual Network (VNet). Det finns flera alternativ för att upprätta en nätverks anslutning till det privata klustret.

* Skapa en virtuell dator i samma Azure-Virtual Network (VNet) som AKS-klustret.
* Använd en virtuell dator i ett separat nätverk och konfigurera [peering för virtuella nätverk][virtual-network-peering].  Mer information om det här alternativet finns i avsnittet nedan.
* Använd en [Express Route eller en VPN-][express-route-or-VPN] anslutning.

Att skapa en virtuell dator i samma VNET som AKS-klustret är det enklaste alternativet.  Express Route och VPN lägger till kostnader och kräver ytterligare nätverks komplexitet.  Peering av virtuella nätverk kräver att du planerar dina nätverks-CIDR-intervall för att se till att det inte finns några överlappande intervall.

## <a name="virtual-network-peering"></a>Peering för virtuella nätverk

Som nämnts är virtuell nätverks-peering ett sätt att komma åt ditt privata kluster. Om du vill använda peering för virtuella nätverk måste du konfigurera en länk mellan det virtuella nätverket och den privata DNS-zonen.
    
1. Gå till resurs gruppen för noden i Azure Portal.  
2. Välj den privata DNS-zonen.   
3. I det vänstra fönstret väljer du länken **virtuellt nätverk** .  
4. Skapa en ny länk för att lägga till det virtuella nätverket för den virtuella datorn i den privata DNS-zonen. Det tar några minuter för DNS-zon-länken att bli tillgänglig.  
5. I Azure Portal navigerar du till resurs gruppen som innehåller klustrets virtuella nätverk.  
6. Välj det virtuella nätverket i den högra rutan. Det virtuella nätverks namnet har formatet *AKS-VNet- \**.  
7. Välj **peering** i det vänstra fönstret.  
8. Välj **Lägg till**, Lägg till det virtuella nätverket för den virtuella datorn och skapa sedan peering.  
9. Gå till det virtuella nätverket där du har den virtuella datorn, Välj **peering**, Välj det virtuella AKS-nätverket och skapa sedan peer-kopplingen. Om adress intervallen för det virtuella AKS-nätverket och den virtuella DATORns virtuella nätverk är i konflikt med varandra, Miss lyckas peering. Mer information finns i  [peering för virtuella nätverk][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>Hubb och eker med anpassad DNS

[Hubbs-och eker-arkitekturer](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) används ofta för att distribuera nätverk i Azure. I många av dessa distributioner är DNS-inställningar i eker-virtuella nätverk konfigurerade för att referera till en central DNS-vidarebefordrare för att tillåta lokal och Azure-baserad DNS-matchning. När du distribuerar ett AKS-kluster till en sådan nätverks miljö finns det några särskilda överväganden som måste beaktas.

![Hubb och eker för privata kluster](media/private-clusters/aks-private-hub-spoke.png)

1. När ett privat kluster har allokerats skapas som standard en privat slut punkt (1) och en privat DNS-zon (2) i den kluster hanterade resurs gruppen. Klustret använder en A-post i den privata zonen för att matcha IP-adressen för den privata slut punkten för kommunikation till API-servern.

2. Den privata DNS-zonen är länkad till det virtuella nätverk som klusternoderna är kopplade till (3). Det innebär att den privata slut punkten bara kan matchas av värdar i den länkade VNet. I scenarier där ingen anpassad DNS har kon figurer ATS i VNet (standard), fungerar detta utan problem som värd punkt på 168.63.129.16 för DNS som kan lösa poster i den privata DNS-zonen på grund av länken.

3. I scenarier där det virtuella nätverket som innehåller klustret har anpassade DNS-inställningar (4) Miss lyckas kluster distributionen om inte den privata DNS-zonen är länkad till det virtuella nätverk som innehåller de anpassade DNS-matcharna (5). Du kan skapa den här länken manuellt när den privata zonen har skapats under kluster etableringen eller via Automation när du har identifierat att zonen har skapats med hjälp av en Event-baserad distributions mekanism (till exempel Azure Event Grid och Azure Functions).

## <a name="dependencies"></a>Beroenden  

* Tjänsten Private Link stöds endast på standard Azure Load Balancer. Basic-Azure Load Balancer stöds inte.  
* Om du vill använda en anpassad DNS-Server lägger du till Azure DNS IP-168.63.129.16 som överordnad DNS-server på den anpassade DNS-servern.

## <a name="limitations"></a>Begränsningar 
* Det går inte att använda IP-auktoriserade intervall för den privata API-serverns slut punkt, de gäller bara för den offentliga API-servern
* [Tillgänglighetszoner][availability-zones] stöds för närvarande för vissa regioner. 
* [Begränsningar för Azure Private Link-tjänsten][private-link-service] gäller för privata kluster.
* Inget stöd för Azure DevOps Microsoft-värdbaserat agenter med privata kluster. Överväg att använda [egen värdbaserade agenter][devops-agents]. 
* För kunder som behöver aktivera Azure Container Registry för att fungera med privata AKS måste det Container Registry virtuella nätverket vara peer-kopplat med agent klustrets virtuella nätverk.
* Inget aktuellt stöd för Azure dev Spaces
* Inget stöd för att konvertera befintliga AKS-kluster till privata kluster
* Om du tar bort eller ändrar den privata slut punkten i kundens undernät kommer klustret att sluta fungera. 
* Azure Monitor för behållar real tids data stöds inte för närvarande.
* SLA för drift tid stöds inte för närvarande.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md
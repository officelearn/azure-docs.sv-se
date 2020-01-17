---
title: Skapa ett privat Azure Kubernetes service-kluster
description: Lär dig hur du skapar ett privat Azure Kubernetes service-kluster (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 2344e2189d6b0f02e7fed1aab25d32551c1fedcf
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154349"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>Skapa ett privat Azure Kubernetes service-kluster (för hands version)

I ett privat kluster har kontroll planet eller API-servern interna IP-adresser som definieras i [RFC1918 för privata Internet](https://tools.ietf.org/html/rfc1918) dokument. Genom att använda ett privat kluster kan du se till att nätverks trafiken mellan API-servern och noderna i pooler fortfarande finns i det privata nätverket.

Kontroll planet eller API-servern finns i en Azure Kubernetes service (AKS)-hanterad Azure-prenumeration. En kunds kluster eller Node-pool är i kundens prenumeration. Servern och klustret eller noden kan kommunicera med varandra via [tjänsten Azure Private Link][private-link-service] i det virtuella nätverkets API-Server och en privat slut punkt som exponeras i under nätet för KUNDEns AKS-kluster.

> [!IMPORTANT]
> AKS för hands versions funktionerna är självbetjänings tjänster och erbjuds på ett valbart sätt. För hands versioner tillhandahålls och *är* *tillgängliga* och omfattas inte av service nivå avtalet (SLA) och begränsad garanti. AKS för hands versionerna omfattas delvis av kund supporten på *bästa* möjliga sätt. Därför är funktionerna inte avsedda att användas för produktion. Mer information finns i följande support artiklar:
>
> * [Support principer för AKS](support-policies.md)
> * [Vanliga frågor och svar om support för Azure](faq.md)

## <a name="prerequisites"></a>Krav

* Azure CLI-versionen 2.0.77 eller senare, och 0.4.18 för för hands versionen av Azure CLI AKS

## <a name="currently-supported-regions"></a>Regioner som stöds för närvarande
* USA, västra
* USA, västra 2
* USA, östra 2
* Kanada, centrala
* Europa, norra
* Europa, västra
* Australien, östra

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>Installera det senaste för hands tillägget för Azure CLI-AKS

Om du vill använda privata kluster behöver du Azure CLI AKS Preview Extension version 0.4.18 eller senare. Installera Azure CLI-AKS för hands versions tillägg med hjälp av kommandot [AZ Extension Add][az-extension-add] och Sök sedan efter eventuella tillgängliga uppdateringar med hjälp av följande kommando för [AZ-tillägg][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> När du registrerar en funktion på en prenumeration kan du för närvarande inte avregistrera funktionen. När du har aktiverat vissa för hands versions funktioner kan du använda standardinställningarna för alla AKS-kluster som har skapats i prenumerationen. Aktivera inte för hands versions funktioner för produktions prenumerationer. Använd en separat prenumeration för att testa för hands versions funktionerna och samla in feedback.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Det kan ta flera minuter innan registrerings statusen visas som *registrerad*. Du kan kontrol lera statusen med hjälp av följande kommando för [AZ funktions lista][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

När statusen är registrerad uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av följande [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Skapa ett privat AKS-kluster

### <a name="default-basic-networking"></a>Standard nätverk för grundläggande 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Where *--Enable-Private-Cluster* är en obligatorisk flagga för ett privat kluster. 

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
Where *--Enable-Private-Cluster* är en obligatorisk flagga för ett privat kluster. 

> [!NOTE]
> Om Docker-bryggan Address CIDR (172.17.0.1/16) står i konflikt med under nätets CIDR, ändra Docker-bryggans adress på lämpligt sätt.

## <a name="connect-to-the-private-cluster"></a>Anslut till det privata klustret
API-serverns slut punkt har ingen offentlig IP-adress. Därför måste du skapa en virtuell Azure-dator (VM) i ett virtuellt nätverk och ansluta till API-servern. Gör så här:

1. Hämta autentiseringsuppgifter för att ansluta till klustret.

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. Gör något av följande:
   * Skapa en virtuell dator i samma virtuella nätverk som AKS-klustret.  
   * Skapa en virtuell dator i ett annat virtuellt nätverk och peer-koppla det här virtuella nätverket med AKS-klustrets virtuella nätverk.

     Om du skapar en virtuell dator i ett annat virtuellt nätverk ställer du in en länk mellan det här virtuella nätverket och den privata DNS-zonen. Gör så här:
    
     a. Gå till den MC_ * resurs gruppen i Azure Portal.  
     b. Välj den privata DNS-zonen.   
     c. I det vänstra fönstret väljer du länken **virtuellt nätverk** .  
     d. Skapa en ny länk för att lägga till det virtuella nätverket för den virtuella datorn i den privata DNS-zonen. Det tar några minuter för DNS-zon-länken att bli tillgänglig.  
     e. Gå tillbaka till MC_ *-resurs gruppen i Azure Portal.  
     f. Välj det virtuella nätverket i den högra rutan. Det virtuella nätverks namnet har formatet *AKS-VNet-\** .  
     g. Välj **peering**i det vänstra fönstret.  
     h. Välj **Lägg till**, Lägg till det virtuella nätverket för den virtuella datorn och skapa sedan peering.  
     i. Gå till det virtuella nätverket där du har den virtuella datorn, Välj **peering**, Välj det virtuella AKS-nätverket och skapa sedan peer-kopplingen. Om adress intervallen för det virtuella AKS-nätverket och den virtuella DATORns virtuella nätverk är i konflikt med varandra, Miss lyckas peering. Mer information finns i [peering för virtuella nätverk][virtual-network-peering].

1. Få åtkomst till den virtuella datorn via SSH (Secure Shell).
1. Installera Kubectl-verktyget och kör Kubectl-kommandona.


## <a name="dependencies"></a>Beroenden  
* Tjänsten Private Link stöds endast på standard Azure Load Balancer. Basic-Azure Load Balancer stöds inte.  

## <a name="limitations"></a>Begränsningar 
* [Begränsningar för Azure Private Link service][private-link-service] gäller för privata kluster, Azures privata slut punkter och virtuella nätverks tjänst slut punkter som för närvarande inte stöds i samma virtuella nätverk.
* Inget stöd för virtuella noder i ett privat kluster för att snurra privat Azure Container Instances (ACI) i ett privat virtuellt Azure-nätverk.
* Det går inte att använda Azure DevOps-integrering direkt från rutan med privata kluster.
* För kunder som behöver aktivera Azure Container Registry för att fungera med privata AKS måste det Container Registry virtuella nätverket vara peer-kopplat med agent klustrets virtuella nätverk.
* Inget aktuellt stöd för Azure dev Spaces.
* Inget stöd för att konvertera befintliga AKS-kluster till privata kluster.  
* Om du tar bort eller ändrar den privata slut punkten i kundens undernät kommer klustret att sluta fungera. 
* Azure Monitor för behållar real tids data stöds inte för närvarande.
* Det finns för närvarande inte stöd för *att ta med din egen DNS* .


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md


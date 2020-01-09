---
title: Privat Azure Kubernetes service-kluster
description: Lär dig hur du skapar ett privat Azure Kubernetes service-kluster (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 12/10/2019
ms.author: mlearned
ms.openlocfilehash: 8af0f998df2a92e51078a2e23806cca07ff08ca3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480091"
---
# <a name="public-preview---private-azure-kubernetes-service-cluster"></a>Offentlig för hands version – privat Azure Kubernetes service-kluster

I ett privat kluster kommer kontroll planet/API-servern att ha interna IP-adresser definierade i [RFC1918](https://tools.ietf.org/html/rfc1918).  Genom att använda ett privat kluster kan du se till att nätverks trafiken mellan API-servern och noderna i pooler fortfarande finns kvar i det privata nätverket.

Kommunikationen mellan kontroll planet/API-servern, som finns i en AKS Azure-prenumeration och kundernas kluster/Node-pool, som finns i en kund prenumeration, kan kommunicera med varandra via [tjänsten för privata länkar][private-link-service] i API-serverns VNet och en privat slut punkt som exponeras i under nätet för kundens AKS-kluster.

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS](support-policies.md)
> * [Vanliga frågor och svar om support för Azure](faq.md)

## <a name="before-you-begin"></a>Innan du börjar

* Du behöver Azure CLI-version 2.0.77 eller senare och 0.4.18-tillägget AKS-Preview

## <a name="current-supported-regions"></a>Aktuella regioner som stöds
* USA, västra
* USA, västra 2
* USA, östra 2
* Kanada, centrala
* Europa, norra
* Europa, västra
* Australien, östra

## <a name="install-latest-aks-cli-preview-extension"></a>Installera det senaste AKS CLI Preview-tillägget

Om du vill använda privata kluster behöver du *AKS-Preview CLI-* tillägg version 0.4.18 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök sedan efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> När du registrerar en funktion på en prenumeration kan du för närvarande inte avregistrera funktionen. När du har aktiverat vissa för hands versions funktioner kan standarderna användas för alla AKS-kluster och sedan skapas i prenumerationen. Aktivera inte för hands versions funktioner för produktions prenumerationer. Använd en separat prenumeration för att testa för hands versions funktionerna och samla in feedback.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

Det kan ta flera minuter för statusen att visa *registrerad*. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

När ett tillstånd har registrerats uppdaterar du registreringen av resurs leverantören för *Microsoft. container service* med hjälp av [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>Skapa ett privat AKS-kluster

#### <a name="default-basic-networking"></a>Standard nätverk för grundläggande 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Where--Enable-Private-Cluster är en obligatorisk flagga för ett privat kluster 

#### <a name="advanced-networking"></a>Avancerat nätverk  

```azurecli-interactive
az aks create \ 
    --resource-group <private-cluster-resource-group>\ 
    --name <private-cluster-name> \ 
    --load-balancer-sku standard
    --enable-private-cluster 
    --network-plugin azure \ 
    --vnet-subnet-id <subnet-id> \ 
    --docker-bridge-address 172.17.0.1/16 \ 
    --dns-service-ip 10.2.0.10 \ 
    --service-cidr 10.2.0.0/24 \ 
```
Where--Enable-Private-Cluster är en obligatorisk flagga för ett privat kluster 

## <a name="steps-to-connect-to-the-private-cluster"></a>Steg för att ansluta till det privata klustret
Slut punkten för API-servern har ingen offentlig IP-adress. Det innebär att användarna måste skapa en virtuell Azure-dator i ett virtuellt nätverk och ansluta till API-servern. Stegen i

* Hämta autentiseringsuppgifter för att ansluta till klustret

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```
* Skapa en virtuell dator i samma VNET som AKS-klustret eller skapa en virtuell dator i ett annat VNET och peer detta VNET med AKS-klustrets VNET
* Om du skapar en virtuell dator i ett annat VNET måste du konfigurera en länk mellan det virtuella nätverket och zonen Privat DNS
    * Gå till den MC_ * resurs gruppen i portalen 
    * Klicka på Privat DNS zon 
    * Välj virtuellt nätverks länk i det vänstra fönstret
    * skapa en ny länk om du vill lägga till VNET för den virtuella datorn i Privat DNS zonen *(det tar några minuter för DNS-zon-länken att bli tillgänglig)*
* SSH till den virtuella datorn
* Installera Kubectl-verktyget och kör Kubectl-kommandon

## <a name="dependencies"></a>Beroenden  
* Standard LB – inget stöd för Basic Load Balancer  

## <a name="limitations"></a>Begränsningar 
* Samma [Azure Private Link service-begränsningar][private-link-service] gäller för privata kluster, Azures privata slut punkter och Virtual Network tjänst slut punkter stöds för närvarande inte i samma VNet
* Inget stöd för virtuella noder i ett privat kluster för att snurra privata ACI-instanser i ett privat Azure VNET
* Inget stöd för integrering av Azure DevOps-integreringen med privata kluster
* Om kunderna behöver aktivera ACR för att arbeta med privata AKS måste ACRs VNET vara peer-kopplas till agent klustrets VNET
* Inget aktuellt stöd för Azure dev Spaces
* Det finns inget stöd för att konvertera befintliga AKS-kluster till privata kluster  
* Om du tar bort eller ändrar den privata slut punkten i kundens undernät kan klustret sluta fungera 
* Azure Monitor för behållar real tids data stöds inte för närvarande
* Det finns för närvarande inte stöd för att ta med din egen DNS


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview

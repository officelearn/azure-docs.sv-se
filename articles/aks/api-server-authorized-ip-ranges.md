---
title: API-servern auktoriserade IP-intervall i Azure Kubernetes service (AKS)
description: Lär dig hur du skyddar klustret med hjälp av ett IP-adressintervall för åtkomst till API-servern i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 487940bfb5d6e7c5eebf99f804f57c3e17709377
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70276489"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>För hands version – säker åtkomst till API-servern med behöriga IP-adressintervall i Azure Kubernetes service (AKS)

I Kubernetes tar API-servern emot begär Anden för att utföra åtgärder i klustret, till exempel för att skapa resurser eller skala antalet noder. API-servern är det centrala sättet att interagera med och hantera ett kluster. För att förbättra kluster säkerheten och minimera angrepp bör API-servern endast vara tillgänglig från en begränsad uppsättning IP-adressintervall.

Den här artikeln visar hur du använder tillåtna IP-adressintervall för API-Server för att begränsa begär anden till kontroll planet. Den här funktionen är för närvarande en förhandsversion.

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS][aks-support-policies]
> * [Vanliga frågor och svar om support för Azure][aks-faq]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du arbetar med kluster som använder [Kubernetes] [Kubernetes].  Med [Azure Container Network Interface (CNI)] [cni-Networking]-baserade kluster, kommer du inte att ha den nödvändiga routningstabellen som krävs för att skydda åtkomsten.  Du måste skapa routningstabellen manuellt.  Mer information finns i [Hantera](https://docs.microsoft.com/azure/virtual-network/manage-route-table) routningstabeller.

API-serverns auktoriserade IP-intervall fungerar bara för nya AKS-kluster som du skapar. Den här artikeln visar hur du skapar ett AKS-kluster med hjälp av Azure CLI.

Du behöver Azure CLI-versionen 2.0.61 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera AKS-Preview CLI-tillägg

Om du vill konfigurera tillåtna IP-intervall för API-servern behöver du *AKS-Preview CLI-* tillägg version 0.4.1 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>Registrera funktions flagga för din prenumeration

Om du vill använda auktoriserade IP-intervall för API-Server måste du först aktivera en funktions flagga i din prenumeration. Registrera funktions flaggan *APIServerSecurityPreview* genom att använda kommandot [AZ Feature register][az-feature-register] , som visas i följande exempel:

> [!CAUTION]
> När du registrerar en funktion på en prenumeration kan du för närvarande inte avregistrera funktionen. När du har aktiverat vissa för hands versions funktioner kan standarderna användas för alla AKS-kluster och sedan skapas i prenumerationen. Aktivera inte för hands versions funktioner för produktions prenumerationer. Använd en separat prenumeration för att testa för hands versions funktionerna och samla in feedback.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du konfigurerar tillåtna IP-intervall för API-servern:

* Du kan för närvarande inte använda Azure dev Spaces eftersom kommunikationen med API-servern också är blockerad.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Översikt över tillåtna IP-intervall för API-Server

Kubernetes-API-servern är hur de underliggande Kubernetes-API: erna exponeras. Den här komponenten ger interaktion för hanterings verktyg, till `kubectl` exempel eller Kubernetes-instrumentpanelen. AKS tillhandahåller en kluster hanterare för en enda klient, med en dedikerad API-Server. Som standard tilldelas API-servern en offentlig IP-adress och du bör kontrol lera åtkomst med hjälp av rollbaserad åtkomst kontroll (RBAC).

Du kan aktivera och använda auktoriserade IP-intervall för att skydda åtkomsten till den allmänt tillgängliga AKS kontroll planet/API-servern. Dessa auktoriserade IP-adressintervall tillåter endast att definierade IP-adressintervall kommunicerar med API-servern. En begäran till API-servern från en IP-adress som inte tillhör dessa auktoriserade IP-intervall är blockerad. Du bör fortsätta att använda RBAC för att sedan auktorisera användare och de åtgärder som de begär.

Mer information om API-servern och andra kluster komponenter finns i [Kubernetes Core Concepts for AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

API-serverns auktoriserade IP-intervall fungerar bara för nya AKS-kluster. Du kan inte aktivera auktoriserade IP-intervall som en del av klustret skapa åtgärd. Om du försöker aktivera auktoriserade IP-intervall som en del av klustrets skapande process, kan inte klusternoderna komma åt API-servern under distributionen eftersom den utgående IP-adressen inte har definierats vid den punkten.

Börja med att skapa ett kluster med kommandot [AZ AKS Create][az-aks-create] . I följande exempel skapas ett kluster med en nod med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>Skapa utgående Gateway för brand Väggs regler

För att säkerställa att noder i ett kluster kan kommunicera med API-servern när du aktiverar auktoriserade IP-intervall i nästa avsnitt, skapar du en Azure-brandvägg som ska användas som utgående Gateway. IP-adressen för Azure-brandväggen läggs sedan till i listan över auktoriserade API-servrars IP-adresser i nästa avsnitt.

> [!WARNING]
> Användningen av Azure-brandväggen kan medföra betydande kostnader för en fakturerings period per månad. Kravet på att använda Azure-brandväggen bör bara vara nödvändigt i den här inledande för hands versions perioden. Mer information och kostnads planering finns i [priser för Azure-brandvägg][azure-firewall-costs].
>
> Alternativt, om klustret använder standard- [SKU: n][standard-sku-lb], behöver du inte konfigurera Azure-brandväggen som utgående Gateway. Använd [AZ Network Public-IP List][az-network-public-ip-list] och ange resurs gruppen för ditt AKS-kluster, som vanligt vis börjar med *MC_* . Detta visar den offentliga IP-adressen för klustret, som du kan vitlista. Exempel:
>
> ```azurecli-interactive
> RG=$(az aks show --resource-group myResourceGroup --name myAKSClusterSLB --query nodeResourceGroup -o tsv)
> SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
> az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSClusterSLB
> ```

Börja med att hämta *MC_* resurs grupp namn för AKS-klustret och det virtuella nätverket. Skapa sedan ett undernät med kommandot [AZ Network VNet Subnet Create][az-network-vnet-subnet-create] . I följande exempel skapas ett undernät med namnet *AzureFirewallSubnet* med CIDR-intervallet *10.200.0.0/16*:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

Om du vill skapa en Azure-brandvägg installerar du *Azure-Firewall CLI-* tillägget med kommandot [AZ Extension Add][az-extension-add] . Skapa sedan en brand vägg med hjälp av kommandot [AZ Network Firewall Create][az-network-firewall-create] . I följande exempel skapas en Azure-brand vägg med namnet *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

En Azure-brandvägg tilldelas en offentlig IP-adress som utgående trafik flödar genom. Skapa en offentlig adress med kommandot [AZ Network Public-IP Create][az-network-public-ip-create] och skapa sedan en IP-konfiguration i brand väggen med hjälp av [IP-konfigurationen för nätverks brand väggen för AZ][az-network-firewall-ip-config-create] som använder den offentliga IP-adressen:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

Skapa nu nätverks regeln för Azure-brandväggen för att *tillåta* all *TCP* -trafik med hjälp av kommandot [AZ Network Firewall Network-Rule Create][az-network-firewall-network-rule-create] . I följande exempel skapas en nätverks regel med namnet *AllowTCPOutbound* för trafik med alla käll-eller mål adresser:

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

För att associera Azure-brandväggen med nätverks vägen hämtar du den befintliga väg tabell informationen, den interna IP-adressen för Azure-brandväggen och sedan IP-adressen för API-servern. De här IP-adresserna anges i nästa avsnitt för att styra hur trafiken ska dirigeras för kluster kommunikation.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

Slutligen skapar du en väg i den befintliga AKS Network Route-tabellen med hjälp av kommandot [AZ Network Route-Table Route Create][az-network-route-table-route-create] som tillåter trafik att använda Azure Firewall-installationen för API-server kommunikation.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Anteckna den offentliga IP-adressen för din Azure Firewall-enhet. Den här adressen läggs till i listan över tillåtna IP-intervall för API-servrar i nästa avsnitt.

## <a name="enable-authorized-ip-ranges"></a>Aktivera auktoriserade IP-intervall

Om du vill aktivera API-serverns auktoriserade IP-intervall anger du en lista över tillåtna IP-adressintervall. När du anger ett CIDR-intervall börjar du med den första IP-adressen i intervallet. Till exempel är *137.117.106.90/29* ett giltigt intervall, men se till att du anger den första IP-adressen i intervallet, till exempel *137.117.106.88/29*.

Använd kommandot [AZ AKS Update][az-aks-update] och ange *--API-Server-auktoriserat IP-intervall* att tillåta. Dessa IP-adressintervall är vanligt vis adress intervall som används av dina lokala nätverk. Lägg till den offentliga IP-adressen för din egen Azure-brandvägg som hämtades i föregående steg, till exempel *20.42.25.196/32*.

I följande exempel aktive ras API-servern auktoriserade IP-intervall i klustret med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*. De IP-adressintervall som ska auktoriseras är *20.42.25.196/32* (Azure FIREWALL offentlig IP-adress), sedan *172.0.0.0/16* och *168.10.0.0/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.0/16,168.10.0.0/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Uppdatera eller inaktivera auktoriserade IP-intervall

Om du vill uppdatera eller inaktivera auktoriserade IP-intervall använder du kommandot [AZ AKS Update][az-aks-update] igen. Ange det uppdaterade CIDR-intervall som du vill tillåta, eller ange ett tomt intervall för att inaktivera tillåtna IP-intervall för API-servrar, som visas i följande exempel:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du aktiverat tillåtna IP-intervall för API-servern. Den här metoden är en del av hur du kan köra ett säkert AKS-kluster.

Mer information finns i [säkerhets begrepp för program och kluster i AKS][concepts-security] och [metod tips för kluster säkerhet och uppgraderingar i AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[standard-sku-lb]: load-balancer-standard.md

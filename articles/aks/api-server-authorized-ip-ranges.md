---
title: API-servern behörighet IP-intervall i Azure Kubernetes Service (AKS)
description: Lär dig hur du säkra kluster med en IP-adressintervall för åtkomst till API-server i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 185c16e76094fe55a54fb17bef24fcd03d7b54f0
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475151"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Förhandsgranskning – säker API-servern med behörighet till IP-adressintervall i Azure Kubernetes Service (AKS)

Tar emot begäranden för att utföra åtgärder i klustret som att skapa resurser eller skala antalet noder i Kubernetes, API-servern. API-servern är centralt sätt att interagera med och hantera ett kluster. API-servern ska endast kan nås från en begränsad uppsättning IP-adressintervall för att förbättra säkerheten för klustret och minska risken för attacker.

Den här artikeln visar hur du använder API-servern behörighet IP-adressintervall att begäranden begränsas till kontrollplanet. Den här funktionen är för närvarande en förhandsversion.

> [!IMPORTANT]
> AKS-förhandsversionsfunktioner är självbetjäning, delta i. De tillhandahålls för att samla in feedback och buggar från vår community. I förhandsversionen kan är inte dessa funktioner avsedda för användning i produktion. Funktioner i offentliga förhandsversioner omfattas ”bästa prestanda” support. Hjälp från teamen för AKS-teknisk support är tillgänglig under kontorstid Pacific tidszon (Stillahavstid) endast. Mer information finns i följande supportartiklar:
>
> * [AKS supportprinciper][aks-support-policies]
> * [Vanliga frågor om Azure-Support][aks-faq]

## <a name="before-you-begin"></a>Innan du börjar

API-servern auktoriserade IP-intervall endast arbete för nya AKS-kluster som du skapar. Den här artikeln visar hur du skapar ett AKS-kluster med Azure CLI.

Du behöver Azure CLI version 2.0.61 eller senare installerat och konfigurerat. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägg för aks-förhandsversion

CLI-kommandon för att konfigurera IP-intervall för API-servern behörighet är tillgängliga i den *aks-förhandsversion* CLI-tillägg. Installera den *förhandsversionen av aks* Azure CLI tillägget med hjälp av den [az-tillägget lägger du till] [ az-extension-add] kommandot, som visas i följande exempel:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Om du tidigare har installerat den *förhandsversionen av aks* tillägg, installera alla tillgängliga uppdateringar med hjälp av den `az extension update --name aks-preview` kommando.

### <a name="register-feature-flag-for-your-subscription"></a>Registrera funktionsflagga för din prenumeration

Om du vill använda API: et server behörighet IP-intervall måste du först aktivera en funktionsflagga i prenumerationen. Att registrera den *APIServerSecurityPreview* funktion flaggan, använda den [az funktionen registrera] [ az-feature-register] kommandot som visas i följande exempel:

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrollera statusen registrering med den [az funktionslistan] [ az-feature-list] kommando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

När du är klar kan du uppdatera registreringen av den *Microsoft.ContainerService* resursprovidern med hjälp av den [az provider register] [ az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du konfigurerar IP-intervall för API-servern behörighet:

* Du kan inte använda Azure Dev blanksteg för tillfället eftersom kommunikationen med API-servern också är spärrad.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Översikt över API-servern behörighet IP-intervall

Kubernetes API-servern är hur den underliggande Kubernetes APIs exponeras. Den här komponenten tillhandahåller interaktionen för hanteringsverktyg, till exempel `kubectl` eller Kubernetes-instrumentpanelen. AKS innehåller en enda klient kluster-mall med en dedikerad API-server. API-servern har tilldelats en offentlig IP-adress som standard, och du bör kontrollera åtkomst med hjälp av rollbaserad åtkomstkontroll (RBAC).

Att skydda åtkomsten till annars offentligt tillgänglig AKS kontrollplanet / API-servern som du kan aktivera och använda behörighet IP-intervall. Dessa auktoriserade IP-adressintervall kan endast definierade IP-adressintervall kan kommunicera med API-servern. En begäran till API-servern från en IP-adress som inte är en del av dessa auktoriserade IP-adressintervall är blockerad. Du bör fortsätta att använda RBAC för att sedan auktorisera användare och de åtgärder som de begär.

Läs mer om API-servern och andra klusterkomponenter [Kubernetes viktiga begrepp för AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

API-servern behörighet IP-intervall fungerar bara för nya AKS-kluster. Du kan inte aktivera auktoriserade IP-adressintervall som en del av klustret för att skapa. Om du försöker aktivera skapandeprocessen av auktoriserade IP-adressintervall som en del av klustret, noderna i klustret kan inte komma åt API-servern under distributionen eftersom den utgående IP-adressen har inte definierats i det här läget.

Börja med att skapa ett kluster som använder den [az aks skapa] [ az-aks-create] kommando. I följande exempel skapas ett enkelnods-kluster med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*.

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

## <a name="create-outbound-gateway-for-firewall-rules"></a>Skapa utgående gateway för brandväggsregler

Skapa en Azure-brandväggen för användning som en utgående gateway för att säkerställa att noder i ett kluster på ett tillförlitligt sätt kan kommunicera med API-servern när du aktiverar auktoriserade IP-adressintervall i nästa avsnitt. IP-adressen för Azure-brandväggen läggs sedan till i listan över auktoriserade API IP-adresser i nästa avsnitt.

> [!WARNING]
> Användning av Azure-brandväggen kan medföra betydande kostnader via en månatlig faktureringsperiod. Kravet på att använda Azure-brandväggen bör endast vara nödvändigt i den här inledande förhandsversionsperioden. Mer information och planera för kostnaden, finns i [priser för Azure-brandvägg][azure-firewall-costs].

Hämta först det *MC_* resursgruppens namn för AKS-klustret och det virtuella nätverket. Skapa sedan ett undernät med hjälp av den [az network vnet-undernät skapa] [ az-network-vnet-subnet-create] kommando. I följande exempel skapas ett undernät med namnet *AzureFirewallSubnet* med CIDR-intervall för *10.200.0.0/16*:

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

Om du vill skapa en Azure-brandvägg, installera den *azure-brandväggen* CLI tillägget med hjälp av den [az-tillägget lägger du till] [ az-extension-add] kommando. Skapa sedan en brandvägg med hjälp av den [az nätverksbrandvägg skapa] [ az-network-firewall-create] kommando. I följande exempel skapas en Azure-brandvägg med namnet *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

En Azure-brandväggen har tilldelats en offentlig IP-adress som utgående trafik flödar genom. Skapa en offentlig adress med hjälp av den [az nätverket offentliga ip-skapa] [ az-network-public-ip-create] kommandot och sedan skapa en IP-konfiguration på brandväggen med hjälp av den [az nätverket brandväggen ip-config skapa] [ az-network-firewall-ip-config-create] som gäller den offentliga IP:

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

Nu har skapat Azure brandväggsregeln nätverk till *Tillåt* alla *TCP* trafik med hjälp av den [az nätverket nätverk-brandväggsregel skapa] [ az-network-firewall-network-rule-create] kommandot. I följande exempel skapas en regel med namnet *AllowTCPOutbound* för trafik med en käll- eller målservrar adress:

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

Om du vill associera Azure-brandväggen med nätverksvägen hämtning av den befintliga tabellen väginformation, den interna IP-adressen för Azure-brandväggen och sedan IP-adressen för API-servern. Dessa IP-adresser anges i nästa avsnitt för att styra hur trafiken ska dirigeras för klusterkommunikation.

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

Skapa slutligen en väg i befintliga AKS nätverk vägen tabellen med den [az network route-table route skapa] [ az-network-route-table-route-create] kommando som tillåter trafik att använda Azure brandväggsinstallation för API-servern kommunikation.

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

Anteckna offentliga IP-adressen för din Azure-brandväggsinstallation. Den här adressen läggs till i listan över API-servern behörighet IP-adressintervall i nästa avsnitt.

## <a name="enable-authorized-ip-ranges"></a>Aktivera auktoriserade IP-adressintervall

Om du vill aktivera IP-intervall för API-servern behörighet, kan du ange en lista över godkända IP-adressintervall. När du anger en CIDR-intervall kan du börja med den första IP-adressen i intervallet. Till exempel *137.117.106.90/29* är ett giltigt intervall, men se till att du anger den första IP-adressen i intervallet, till exempel *137.117.106.88/29*.

Använd [az aks uppdatera] [ az-aks-update] kommandot och ange den *--api-server-behörighet-ip-adressintervall* att tillåta. Dessa IP-adressintervall är vanligtvis adressintervall som används av dina lokala nätverk. Lägg till offentliga IP-adressen för din egen Azure-brandväggen hämtades i föregående steg, till exempel *20.42.25.196/32*.

I följande exempel aktiveras API-servern behörighet IP-intervall på klustret med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*. IP-adressintervall att auktorisera är *20.42.25.196/32* (Azure-brandväggen offentliga IP-adress), sedan *172.0.0.10/16* och *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>Uppdatera eller inaktivera auktoriserade IP-adressintervall

Om du vill uppdatera eller inaktivera auktoriserade IP-adressintervall, du återigen att använda [az aks uppdatera] [ az-aks-update] kommando. Ange den uppdaterade CIDR-intervall som du vill tillåta eller ange ett tomt intervall att inaktivera API-servern behörighet IP-adressintervall som du ser i följande exempel:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Nästa steg

Du har aktiverat API-servern behörighet IP-intervall i den här artikeln. Den här metoden är en del av hur du kan köra ett säkert kluster i AKS.

Mer information finns i [säkerhetsbegrepp för program och -kluster i AKS] [ concepts-security] och [bästa praxis för Klustersäkerhet och uppgraderingar i AKS] [ operator-best-practices-cluster-security].

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md

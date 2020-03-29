---
title: Virtuella nätverk
titleSuffix: Azure Cognitive Services
description: Konfigurera nätverkssäkerhet i lager för dina Cognitive Services-resurser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371230"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Konfigurera virtuella Azure Cognitive Services-nätverk

Azure Cognitive Services tillhandahåller en säkerhetsmodell i flera lager. Med den här modellen kan du skydda dina Cognitive Services-konton till en viss delmängd av nätverk. När nätverksregler har konfigurerats kan endast program som begär data via den angivna uppsättningen nätverk komma åt kontot. Du kan begränsa åtkomsten till dina resurser med filtrering av begäran. Tillåter endast begäranden som kommer från angivna IP-adresser, IP-intervall eller från en lista över undernät i [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md). Om du är intresserad av det här erbjudandet måste du [begära förhandsversion.](https://aka.ms/cog-svc-vnet-signup)

Ett program som använder en Cognitive Services-resurs när nätverksregler gäller kräver auktorisering. Auktorisering stöds med [Azure Active Directory(Azure](../active-directory/fundamentals/active-directory-whatis.md) AD) autentiseringsuppgifter eller med en giltig API-nyckel.

> [!IMPORTANT]
> Om du aktiverar brandväggsregler för ditt Cognitive Services-konto blockeras inkommande begäranden om data som standard. För att möjliggöra förfrågningar genom, måste ett av följande villkor uppfyllas:
> * Begäran ska komma från en tjänst som arbetar inom ett Virtuellt Azure-nätverk (VNet) i listan över tillåtna undernät i kontot för mål cognitive services. Slutpunkten i begäranden som kommer från VNet måste anges som den [anpassade underdomänen för](cognitive-services-custom-subdomains.md) ditt Cognitive Services-konto.
> * Eller begäran ska komma från en tillåten lista över IP-adresser.
>
> Begäranden som blockeras inkluderar de från andra Azure-tjänster, från Azure-portalen, från loggning och måtttjänster och så vidare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenarier

Om du vill skydda cognitive services-resursen bör du först konfigurera en regel för att neka åtkomst till trafik från alla nätverk (inklusive internettrafik) som standard. Sedan bör du konfigurera regler som ger åtkomst till trafik från specifika virtuella nätverk. Med den här konfigurationen kan du skapa en säker nätverksgräns för dina program. Du kan också konfigurera regler för att bevilja åtkomst till trafik från utvalda offentliga IP-adressintervall för internet, vilket möjliggör anslutningar från specifika internet- eller lokala klienter.

Nätverksregler tillämpas på alla nätverksprotokoll till Azure Cognitive Services, inklusive REST och WebSocket. För att komma åt data med hjälp av verktyg som Azure-testkonsoler måste explicita nätverksregler konfigureras. Du kan tillämpa nätverksregler på befintliga Cognitive Services-resurser eller när du skapar nya Cognitive Services-resurser. När nätverksregler har tillämpats tillämpas de för alla begäranden.

## <a name="supported-regions-and-service-offerings"></a>Regioner och tjänsteerbjudanden som stöds

Stöd för virtuella nätverk för Cognitive Services som anges nedan är begränsat till *regionerna Centrala USA,* *Södra centrala USA,* *Östra USA,* *Västra USA 2*, *Norra Europa,* *Sydafrika,* *Västra Europa,* *Centrala Indien,* *Australien östra,* *västra USA*och USA *Gov Virginia* Azure-regioner. Om tjänsteerbjudandet inte visas här stöder det inte virtuella nätverk.

> [!div class="checklist"]
> * [Avvikelseidentifiering](./anomaly-detector/index.yml)
> * [Datorseende](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Ansikte](./face/index.yml)
> * [Formigenkänning](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personanpassning](./personalizer/index.yml)
> * [Textanalys](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)

Stöd för virtuella nätverk för Cognitive Services som anges nedan är begränsat till regionerna *Central US EUAP,* *South Central US,* *East US,* *West US 2*, *Global*och US *Gov Virginia* Azure.
> [!div class="checklist"]
> * [Översättare Text](./translator/index.yml)

## <a name="service-tags"></a>Tjänsttaggar
Förutom att stödja slutpunkter för virtuella nätverkstjänster för tjänsterna ovan stöder Cognitive Services också ett servicetag för utgående nätverksregler. Följande tjänster ingår i servicetag för CognitiveServicesManagement.
> [!div class="checklist"]
> * [Avvikelseidentifiering](./anomaly-detector/index.yml)
> * [Datorseende](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Ansikte](./face/index.yml)
> * [Formigenkänning](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personanpassning](./personalizer/index.yml)
> * [Textanalys](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Översättare Text](./translator/index.yml)
> * [Taltjänst](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Ändra standardåtkomstregeln för nätverk

Som standard accepterar Cognitive Services-resurser anslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk måste du först ändra standardåtgärden.

> [!WARNING]
> Om du gör ändringar i nätverksregler kan det påverka dina programs förmåga att ansluta till Azure Cognitive Services. Om du ställer in standardnätverksregeln så att alla åtkomster till data **nekas** blockeras om inte särskilda nätverksregler som **beviljar** åtkomst också tillämpas. Se till att bevilja åtkomst till alla tillåtna nätverk som använder nätverksregler innan du ändrar standardregeln för att neka åtkomst. Om du tillåter att du listar IP-adresser för ditt lokala nätverk måste du lägga till alla möjliga utgående offentliga IP-adresser från det lokala nätverket.

### <a name="managing-default-network-access-rules"></a>Hantera standardregler för nätverksåtkomst

Du kan hantera standardregler för nätverksåtkomst för Cognitive Services-resurser via Azure-portalen, PowerShell eller Azure CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Gå till den Cognitive Services-resurs som du vill skydda.

1. Välj **resurshanteringsmenyn** **Virtual network**.

   ![Alternativ för virtuellt nätverk](media/vnet/virtual-network-blade.png)

1. Om du vill neka åtkomst som standard väljer du att tillåta åtkomst från **valda nätverk**. Med inställningen **Valda nätverk** ensam, utan sällskap av konfigurerade **virtuella nätverk** eller **adressintervall** - all åtkomst nekas effektivt. När all åtkomst nekas tillåts inte begäranden som försöker använda Cognitive Services-resursen. Azure-portalen, Azure PowerShell eller Azure CLI kan fortfarande användas för att konfigurera Cognitive Services-resursen.
1. Om du vill tillåta trafik från alla nätverk väljer du att tillåta åtkomst från **Alla nätverk**.

   ![Virtuella nätverk förnekar](media/vnet/virtual-network-deny.png)

1. Klicka på **Spara** för att tillämpa dina ändringar.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps) och [logga in](/powershell/azure/authenticate-azureps)eller välj **Prova det**.

1. Visa status för standardregeln för Cognitive Services-resursen.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Ange att standardregeln ska neka nätverksåtkomst som standard.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Ange standardregeln så att nätverksåtkomst tillåts som standard.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli)eller välj **Prova det**.

1. Visa status för standardregeln för Cognitive Services-resursen.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Ange att standardregeln ska neka nätverksåtkomst som standard.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Ange standardregeln så att nätverksåtkomst tillåts som standard.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Bevilja åtkomst från ett virtuellt nätverk

Du kan konfigurera Cognitive Services-resurser så att åtkomst endast tillåts från specifika undernät. De tillåtna undernäten kan tillhöra ett virtuella nätverk i samma prenumeration eller i en annan prenumeration, inklusive prenumerationer som tillhör en annan Azure Active Directory-klientorganisation.

Aktivera en [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) för Azure Cognitive Services i det virtuella nätverket. Tjänstenslutpunkt dirigerar trafik från det virtuella nätverket via en optimal sökväg till Azure Cognitive Services-tjänsten. Identiteterna för undernätet och det virtuella nätverket överförs också med varje begäran. Administratörer kan sedan konfigurera nätverksregler för Cognitive Services-resursen som gör att begäranden kan tas emot från specifika undernät i ett virtuella nätverk. Klienter som beviljas åtkomst via dessa nätverksregler måste fortsätta att uppfylla auktoriseringskraven för Cognitive Services-resursen för att komma åt data.

Varje Cognitive Services-resurs stöder upp till 100 virtuella nätverksregler, vilket kan kombineras med [IP-nätverksregler](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill tillämpa en virtuell nätverksregel på en Cognitive Services-resurs måste användaren ha rätt behörighet för de undernät som läggs till. Behörigheten krävs är *standardrollen Deltagare* eller rollen *Cognitive Services Contributor.* Nödvändiga behörigheter kan också läggas till i anpassade rolldefinitioner.

Cognitive Services-resursen och de virtuella nätverk som beviljas åtkomst kan finnas i olika prenumerationer, inklusive prenumerationer som är en del av en annan Azure AD-klientorganisation.

> [!NOTE]
> Konfiguration av regler som ger åtkomst till undernät i virtuella nätverk som är en del av en annan Azure Active Directory-klient stöds för närvarande endast via Powershell-, CLI- och REST-API:er. Sådana regler kan inte konfigureras via Azure-portalen, även om de kan visas i portalen.

### <a name="managing-virtual-network-rules"></a>Hantera regler för virtuella nätverk

Du kan hantera virtuella nätverksregler för Cognitive Services-resurser via Azure-portalen, PowerShell eller Azure CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Gå till den Cognitive Services-resurs som du vill skydda.

1. Välj **resurshanteringsmenyn** **Virtual network**.

1. Kontrollera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett virtuellt nätverk med en befintlig nätverksregel väljer du **Lägg till befintligt virtuellt nätverk**under Virtuella **nätverk**.

   ![Lägga till befintligt vNet](media/vnet/virtual-network-add-existing.png)

1. Markera alternativen **för virtuella nätverk** och **undernät** och välj sedan **Aktivera**.

   ![Lägga till befintlig vNet-information](media/vnet/virtual-network-add-existing-details.png)

1. Om du vill skapa ett nytt virtuellt nätverk och bevilja det åtkomst väljer du **Lägg till nytt virtuellt nätverk**.

   ![Lägg till nytt vNet](media/vnet/virtual-network-add-new.png)

1. Ange den information som behövs för att skapa det nya virtuella nätverket och välj sedan **Skapa**.

   ![Skapa vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Om en tjänstslutpunkt för Azure Cognitive Services inte tidigare har konfigurerats för det valda virtuella nätverket och undernäten kan du konfigurera den som en del av den här åtgärden.
    >
    > För närvarande visas endast virtuella nätverk som tillhör samma Azure Active Directory-klient för val när regeln skapas. Om du vill bevilja åtkomst till ett undernät i ett virtuellt nätverk som tillhör en annan klient använder du Powershell-, CLI- eller REST-API:er.

1. Om du vill ta bort en virtuell nätverks- eller undernätsregel markerar du **...** för att öppna snabbmenyn för det virtuella nätverket eller undernätet och väljer **Ta bort**.

   ![Ta bort vNet](media/vnet/virtual-network-remove.png)

1. Klicka på **Spara** för att tillämpa dina ändringar.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps) och [logga in](/powershell/azure/authenticate-azureps)eller välj **Prova det**.

1. Lista regler för virtuella nätverk.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Aktivera tjänstslutpunkt för Azure Cognitive Services i ett befintligt virtuellt nätverk och undernät.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Lägg till en nätverksregel för ett virtuellt nätverk och ett undernät.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Om du vill lägga till en nätverksregel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du en fullt kvalificerad **VirtualNetworkResourceId-parameter** i formuläret "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnet-name/subnet-name".

1. Ta bort en nätverksregel för ett virtuellt nätverk och ett undernät.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli)eller välj **Prova det**.

1. Lista regler för virtuella nätverk.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Aktivera tjänstslutpunkt för Azure Cognitive Services i ett befintligt virtuellt nätverk och undernät.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Lägg till en nätverksregel för ett virtuellt nätverk och ett undernät.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Om du vill lägga till en regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du ett fullständigt kvalificerat undernät-ID i formuläret "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Du kan använda **prenumerationsparametern** för att hämta undernäts-ID:t för ett virtuella nätverk som tillhör en annan Azure AD-klientorganisation.

1. Ta bort en nätverksregel för ett virtuellt nätverk och ett undernät.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Var noga med att [ange standardregeln](#change-the-default-network-access-rule) att **neka**, eller nätverksregler har ingen effekt.

## <a name="grant-access-from-an-internet-ip-range"></a>Bevilja åtkomst från ett IP-intervall för internet

Du kan konfigurera Cognitive Services-resurser så att åtkomst tillåts från specifika offentliga IP-adressintervall för internet. Den här konfigurationen ger tillgång till specifika tjänster och lokala nätverk, vilket effektivt blockerar den allmänna internettrafiken.

Ange tillåtna internetadressintervall med [CIDR-notation](https://tools.ietf.org/html/rfc4632) i `16.17.18.0/24` formuläret `16.17.18.19`eller som enskilda IP-adresser som .

   > [!Tip]
   > Små adressintervall med prefixstorlekar "/31" eller "/32" stöds inte. Dessa intervall bör konfigureras med hjälp av individuella IP-adressregler.

IP-nätverksregler är endast tillåtna för **offentliga IP-adresser på Internet.** IP-adressintervall som reserverats för privata nätverk (enligt definitionen i [RFC 1918)](https://tools.ietf.org/html/rfc1918#section-3)är inte tillåtna i IP-regler. Privata nätverk innehåller adresser `10.*` `172.16.*`  -  `172.31.*`som `192.168.*`börjar med , och .

   > [!NOTE]
   > IP-nätverksregler har ingen effekt på begäranden som kommer från samma Azure-region som Cognitive Services-resursen. Använd [virtuella nätverksregler](#grant-access-from-a-virtual-network) för att tillåta begäranden i samma region.

Endast IPV4-adresser stöds för tillfället. Varje Cognitive Services-resurs stöder upp till 100 IP-nätverksregler, som kan kombineras med [virtuella nätverksregler](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk

Om du vill bevilja åtkomst från dina lokala nätverk till din Cognitive Services-resurs med en IP-nätverksregel måste du identifiera de IP-adresser som är vända mot internet som används av nätverket. Kontakta nätverksadministratören för hjälp.

Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md) lokalt för offentlig peering eller Microsoft-peering måste du identifiera NAT-IP-adresserna. För offentlig peering använder varje ExpressRoute-krets som standard två NAT-IP-adresser. Var och en tillämpas på Azure-tjänsttrafik när trafiken kommer in i Microsoft Azure-nätverkets ryggrad. För Microsoft-peering tillhandahålls NAT-IP-adresser som används antingen kunden eller tillhandahålls av tjänsteleverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Hantera IP-nätverksregler

Du kan hantera IP-nätverksregler för Cognitive Services-resurser via Azure-portalen, PowerShell eller Azure CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Gå till den Cognitive Services-resurs som du vill skydda.

1. Välj **resurshanteringsmenyn** **Virtual network**.

1. Kontrollera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett IP-intervall på Internet anger du IP-adressen eller adressintervallet (i [CIDR-format)](https://tools.ietf.org/html/rfc4632)under **Brandväggs** > **adressintervall**. Endast giltiga offentliga IP-adresser (ej reserverade) accepteras.

   ![Lägg till IP-intervall](media/vnet/virtual-network-add-ip-range.png)

1. Om du vill ta bort en <span class="docon docon-delete x-hidden-focus"></span> IP-nätverksregel markerar du papperskorgen bredvid adressintervallet.

   ![Ta bort IP-intervall](media/vnet/virtual-network-delete-ip-range.png)

1. Klicka på **Spara** för att tillämpa dina ändringar.

# <a name="powershell"></a>[Powershell](#tab/powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps) och [logga in](/powershell/azure/authenticate-azureps)eller välj **Prova det**.

1. Lista IP-nätverksregler.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Lägg till en nätverksregel för en enskild IP-adress.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Lägg till en nätverksregel för ett IP-adressintervall.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Ta bort en nätverksregel för en enskild IP-adress.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Ta bort en nätverksregel för ett IP-adressintervall.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli)eller välj **Prova det**.

1. Lista IP-nätverksregler.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Lägg till en nätverksregel för en enskild IP-adress.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Lägg till en nätverksregel för ett IP-adressintervall.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Ta bort en nätverksregel för en enskild IP-adress.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Ta bort en nätverksregel för ett IP-adressintervall.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Var noga med att [ange standardregeln](#change-the-default-network-access-rule) att **neka**, eller nätverksregler har ingen effekt.

## <a name="next-steps"></a>Nästa steg

* Utforska de olika [Azure Cognitive Services](welcome.md)
* Läs mer om [slutpunkter för Virtuella Azure-nätverkstjänst](../virtual-network/virtual-network-service-endpoints-overview.md)
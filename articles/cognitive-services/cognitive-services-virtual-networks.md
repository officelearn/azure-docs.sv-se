---
title: Virtuella nätverk
titleSuffix: Azure Cognitive Services
description: Konfigurera skiktad nätverks säkerhet för dina Cognitive Services-resurser.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 1ae3caa2d1f90bbbae1070d95d676eb206a361a0
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647356"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Konfigurera virtuella Azure Cognitive Services-nätverk

Azure Cognitive Services tillhandahåller en skiktad säkerhets modell. Med den här modellen kan du skydda dina Cognitive Services-konton till en viss del av nätverk. När nätverks regler har kon figurer ATS kan endast program som begär data i den angivna uppsättningen nätverk komma åt kontot. Du kan begränsa åtkomsten till dina resurser med filtrering av förfrågningar. Tillåter endast förfrågningar från angivna IP-adresser, IP-intervall eller från en lista över undernät i [virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md). Om du är intresse rad av det här erbjudandet måste du [begära för hands versions åtkomst](https://aka.ms/cog-svc-vnet-signup).

Ett program som har åtkomst till en Cognitive Services-resurs när nätverks regler tillämpas kräver auktorisering. Auktorisering stöds med [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (autentiseringsuppgifter för Azure AD) eller med en giltig API-nyckel.

> [!IMPORTANT]
> Om du aktiverar brand Väggs regler för ditt Cognitive Services-konto blockeras inkommande begär Anden för data som standard. För att tillåta begär Anden genom måste något av följande villkor vara uppfyllda:
> * Begäran bör härstamma från en tjänst som körs i ett Azure-Virtual Network (VNet) i listan över tillåtna undernät för mål Cognitive Services kontot. Slut punkten i begär Anden som kommer från VNet måste anges som den [anpassade under domänen](cognitive-services-custom-subdomains.md) för ditt Cognitive Services-konto.
> * Eller så måste begäran härstamma från en lista över tillåtna IP-adresser.
>
> Begär Anden som blockeras inkluderar de från andra Azure-tjänster, från Azure Portal, från loggnings-och mått tjänster och så vidare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenarier

För att skydda din Cognitive Services-resurs bör du först konfigurera en regel för att neka åtkomst till trafik från alla nätverk (inklusive Internet trafik) som standard. Sedan bör du konfigurera regler som beviljar åtkomst till trafik från vissa virtuella nätverk. Med den här konfigurationen kan du bygga en säker nätverks gränser för dina program. Du kan också konfigurera regler för att bevilja åtkomst till trafik från Välj offentliga IP-adressintervall för Internet, aktivera anslutningar från vissa Internet-eller lokala klienter.

Nätverks regler tillämpas på alla nätverks protokoll till Azure Cognitive Services, inklusive REST och WebSocket. Om du vill komma åt data med hjälp av verktyg som Azure test-konsoler måste explicita nätverks regler konfigureras. Du kan använda nätverks regler för befintliga Cognitive Services-resurser eller när du skapar nya Cognitive Services resurser. När nätverks reglerna tillämpas tillämpas de för alla begär Anden.

## <a name="supported-regions-and-service-offerings"></a>Regioner och tjänst erbjudanden som stöds

Stöd för virtuella nätverk för Cognitive Services är begränsat till *EUAP för Kanarie-centrala USA* *, Västeuropa och* *västra USA 2* Azure-regioner. Dessutom stöder inte alla kognitiva tjänst erbjudanden virtuella nätverk. Följande kognitiva tjänst erbjudanden tillåter användningen av virtuella nätverk. Om tjänst erbjudandet inte visas i listan, har det inte stöd för virtuella nätverk.

> [!div class="checklist"]
> * [Avvikelse detektor](./anomaly-detector/index.yml)
> * [Visuellt innehåll](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Vändning](./face/index.yml)
> * [Formulär igenkänning](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personanpassare](./personalizer/index.yml)
> * [Textanalys](./text-analytics/index.yml)

## <a name="change-the-default-network-access-rule"></a>Ändra standard regel för nätverks åtkomst

Som standard accepterar Cognitive Services-resurser anslutningar från klienter i ett nätverk. Om du vill begränsa åtkomsten till valda nätverk måste du först ändra standard åtgärden.

> [!WARNING]
> Om du gör ändringar i nätverks reglerna kan det påverka dina programs möjlighet att ansluta till Azure Cognitive Services. Om du anger standard nätverks regeln till **neka** blockeras alla åtkomst till data, om inte vissa nätverks regler som **beviljar** åtkomst också tillämpas. Se till att bevilja åtkomst till alla tillåtna nätverk med nätverks regler innan du ändrar standard regeln för att neka åtkomst. Om du tillåter att du listar IP-adresser för ditt lokala nätverk måste du lägga till alla möjliga offentliga IP-adresser från ditt lokala nätverk.

### <a name="managing-default-network-access-rules"></a>Hantera standard regler för nätverks åtkomst

Du kan hantera standard regler för nätverks åtkomst för Cognitive Services resurser via Azure Portal, PowerShell eller Azure CLI.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. Gå till den Cognitive Services resurs som du vill skydda.

1. Välj menyn **resurs hantering** som kallas **virtuellt nätverk**.

   ![Virtuellt nätverks alternativ](media/vnet/virtual-network-blade.png)

1. Om du vill neka åtkomst som standard väljer du att tillåta åtkomst från **valda nätverk**. Med de **valda nätverks** inställningarna fristående, som inte åtföljs av konfigurerade **virtuella nätverk** eller **adress intervall** , nekas all åtkomst effektivt. När all åtkomst nekas är begär Anden som försöker använda Cognitive Services resursen inte tillåtna. Azure Portal Azure PowerShell eller Azure CLI kan fortfarande användas för att konfigurera Cognitive Services resursen.
1. Om du vill tillåta trafik från alla nätverk väljer du att tillåta åtkomst från **alla nätverk**.

   ![Neka virtuella nätverk](media/vnet/virtual-network-deny.png)

1. Välj **Spara** för att tillämpa ändringarna.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps) och [Logga](/powershell/azure/authenticate-azureps)in eller Välj **prova**.

1. Visa status för standard regeln för Cognitive Services resursen.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Ange standard regeln för att neka nätverks åtkomst som standard.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Ange standard regeln för att tillåta nätverks åtkomst som standard.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in, eller Välj **prova**.

1. Visa status för standard regeln för Cognitive Services resursen.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Ange standard regeln för att neka nätverks åtkomst som standard.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Ange standard regeln för att tillåta nätverks åtkomst som standard.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Bevilja åtkomst från ett virtuellt nätverk

Du kan konfigurera Cognitive Services resurser så att endast åtkomst från vissa undernät tillåts. De tillåtna under näten kan tillhöra ett VNet i samma prenumeration eller i en annan prenumeration, inklusive prenumerationer som tillhör en annan Azure Active Directory klient.

Aktivera en [tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) för Azure Cognitive Services i VNet. Tjänst slut punkten dirigerar trafik från VNet via en optimal sökväg till Azure Cognitive Services-tjänsten. Identiteterna för under nätet och det virtuella nätverket överförs också med varje begäran. Administratörer kan sedan konfigurera nätverks regler för den Cognitive Services resurs som tillåter att förfrågningar tas emot från vissa undernät i ett VNet. Klienter som beviljats åtkomst via dessa nätverks regler måste fortsätta att uppfylla kraven för auktorisering av Cognitive Services resurs för att komma åt data.

Varje Cognitive Services-resurs har stöd för upp till 100 virtuella nätverks regler, som kan kombineras med [IP-nätverks-regler](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill tillämpa en virtuell nätverks regel på en Cognitive Services resurs måste användaren ha rätt behörighet för de undernät som läggs till. Den behörighet som krävs är standard *deltagar* rollen eller rollen *Cognitive Services Contributor* . Nödvändiga behörigheter kan också läggas till i anpassade roll definitioner.

Cognitive Services resurs och de virtuella nätverk som beviljats åtkomst kan finnas i olika prenumerationer, inklusive prenumerationer som ingår i en annan Azure AD-klient.

> [!NOTE]
> Konfiguration av regler som beviljar åtkomst till undernät i virtuella nätverk som ingår i en annan Azure Active Directory klient stöds för närvarande bara via PowerShell-, CLI-och REST-API: er. Sådana regler kan inte konfigureras via Azure Portal, men de kan visas i portalen.

### <a name="managing-virtual-network-rules"></a>Hantera virtuella nätverks regler

Du kan hantera virtuella nätverks regler för Cognitive Services resurser via Azure Portal, PowerShell eller Azure CLI.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. Gå till den Cognitive Services resurs som du vill skydda.

1. Välj menyn **resurs hantering** som kallas **virtuellt nätverk**.

1. Kontrol lera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett virtuellt nätverk med en befintlig nätverks regel väljer du **Lägg till befintligt virtuellt nätverk**under **virtuella nätverk**.

   ![Lägg till befintligt vNet](media/vnet/virtual-network-add-existing.png)

1. Välj alternativ för **virtuella nätverk** och **undernät** och välj sedan **Aktivera**.

   ![Lägg till befintlig vNet-information](media/vnet/virtual-network-add-existing-details.png)

1. Om du vill skapa ett nytt virtuellt nätverk och bevilja det åtkomst väljer du **Lägg till nytt virtuellt nätverk**.

   ![Lägg till nytt vNet](media/vnet/virtual-network-add-new.png)

1. Ange den information som krävs för att skapa det nya virtuella nätverket och välj sedan **skapa**.

   ![Skapa vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Om en tjänst slut punkt för Azure Cognitive Services inte tidigare har kon figurer ATS för det valda virtuella nätverket och under nätet, kan du konfigurera den som en del av den här åtgärden.
    >
    > För närvarande visas endast virtuella nätverk som hör till samma Azure Active Directory klient organisation för val av skapande av regel. Om du vill bevilja åtkomst till ett undernät i ett virtuellt nätverk som tillhör en annan klient organisation använder du PowerShell-, CLI-eller REST-API: er.

1. Om du vill ta bort ett virtuellt nätverk eller en under näts regel väljer du **...** för att öppna snabb menyn för det virtuella nätverket eller under nätet och välj **ta bort**.

   ![Ta bort vNet](media/vnet/virtual-network-remove.png)

1. Välj **Spara** för att tillämpa ändringarna.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps) och [Logga](/powershell/azure/authenticate-azureps)in eller Välj **prova**.

1. Lista över virtuella nätverks regler.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Aktivera tjänst slut punkten för Azure Cognitive Services på ett befintligt virtuellt nätverk och undernät.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Lägg till en nätverks regel för ett virtuellt nätverk och undernät.

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
    > Om du vill lägga till en nätverks regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du en fullständigt kvalificerad **VirtualNetworkResourceId** -parameter i formatet "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-Name/subnets/Subnet-Name".

1. Ta bort en nätverks regel för ett virtuellt nätverk och undernät.

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in, eller Välj **prova**.

1. Lista över virtuella nätverks regler.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Aktivera tjänst slut punkten för Azure Cognitive Services på ett befintligt virtuellt nätverk och undernät.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Lägg till en nätverks regel för ett virtuellt nätverk och undernät.

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
    > Om du vill lägga till en regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du ett fullständigt kvalificerat undernät-ID i formatet "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Du kan använda parametern **Subscription** för att hämta Undernäts-ID: t för ett VNet som tillhör en annan Azure AD-klient.

1. Ta bort en nätverks regel för ett virtuellt nätverk och undernät.

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
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka**eller att nätverks regler inte har någon påverkan.

## <a name="grant-access-from-an-internet-ip-range"></a>Bevilja åtkomst från ett Internet-IP-intervall

Du kan konfigurera Cognitive Services-resurser för att tillåta åtkomst från vissa offentliga Internet-IP-adressintervall. Den här konfigurationen beviljar åtkomst till vissa tjänster och lokala nätverk, vilket effektivt blockerar allmän Internet trafik.

Ange tillåtna Internet adress intervall med [CIDR-notering](https://tools.ietf.org/html/rfc4632) i formuläret `16.17.18.0/24` eller som enskilda IP-adresser som `16.17.18.19`.

   > [!Tip]
   > Små adress intervall som använder sig av prefixlängden "/31" eller "/32" stöds inte. Dessa intervall ska konfigureras med hjälp av enskilda IP-adressintervall.

IP-nätverks regler tillåts endast för **offentliga Internet** -IP-adresser. IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) tillåts inte i IP-regler. Privata nätverk innehåller adresser som börjar med `10.*`, `172.16.*` - `172.31.*`och `192.168.*`.

   > [!NOTE]
   > IP-nätverksanslutningar har ingen påverkan på begär Anden som kommer från samma Azure-region som den Cognitive Services resursen. Använd [regler för virtuella nätverk](#grant-access-from-a-virtual-network) för att tillåta begäran om samma region.

Endast IPV4-adresser stöds för tillfället. Varje Cognitive Services-resurs har stöd för upp till 100 IP-nätverksnummer som kan kombineras med [regler för virtuella nätverk](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk

Om du vill bevilja åtkomst från dina lokala nätverk till din Cognitive Services-resurs med en IP-nätverks regel måste du identifiera de Internet adresser som IP-adresser används i nätverket. Kontakta nätverks administratören om du behöver hjälp.

Om du använder [ExpressRoute](../expressroute/expressroute-introduction.md) lokalt för offentlig peering eller Microsoft-peering måste du identifiera NAT-IP-adresserna. För offentlig peering använder varje ExpressRoute-krets som standard två NAT-IP-adresser. Varje används för Azure-tjänstetrafik när trafiken går in Microsoft Azure nätverkets stamnät. De NAT-IP-adresser som används för Microsoft-peering är antingen kunden eller tillhandahålls av tjänst leverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du ett supportärende hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Hantera IP-nätverks regler

Du kan hantera IP-nätverks regler för Cognitive Services resurser via Azure Portal, PowerShell eller Azure CLI.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

1. Gå till den Cognitive Services resurs som du vill skydda.

1. Välj menyn **resurs hantering** som kallas **virtuellt nätverk**.

1. Kontrol lera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett Internet-IP-intervall anger du IP-adressen eller adress intervallet (i [CIDR-format](https://tools.ietf.org/html/rfc4632)) under **brand Väggs** > **adress intervall**. Endast giltiga offentliga IP-adresser (icke-reserverade) accepteras.

   ![Lägg till IP-intervall](media/vnet/virtual-network-add-ip-range.png)

1. Om du vill ta bort en IP-nätverks regel väljer <span class="docon docon-delete x-hidden-focus"></span> du pappers korgs ikonen bredvid adress intervallet.

   ![Ta bort IP-intervall](media/vnet/virtual-network-delete-ip-range.png)

1. Välj **Spara** för att tillämpa ändringarna.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

1. Installera [Azure PowerShell](/powershell/azure/install-az-ps) och [Logga](/powershell/azure/authenticate-azureps)in eller Välj **prova**.

1. Lista IP-nätverksnummer.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Lägg till en regel för en enskild IP-adress.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Lägg till en nätverks regel för ett IP-adressintervall.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Ta bort en nätverks regel för en enskild IP-adress.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Ta bort en nätverks regel för ett IP-adressintervall.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [Logga](/cli/azure/authenticate-azure-cli)in, eller Välj **prova**.

1. Lista IP-nätverksnummer.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Lägg till en regel för en enskild IP-adress.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Lägg till en nätverks regel för ett IP-adressintervall.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Ta bort en nätverks regel för en enskild IP-adress.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Ta bort en nätverks regel för ett IP-adressintervall.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Se till att [Ange standard regeln](#change-the-default-network-access-rule) för **neka**eller att nätverks regler inte har någon påverkan.

## <a name="next-steps"></a>Nästa steg

* Utforska de olika [Azure-Cognitive Services](welcome.md)
* Läs mer om [Azure Virtual Network-tjänstens slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md)
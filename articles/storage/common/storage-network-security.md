---
title: Konfigurera Azure Storage-brandväggar och virtuella nätverk | Microsoft-dokument
description: Konfigurera nätverkssäkerhet i lager för ditt lagringskonto.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 7120ba2cf71c9af5373b830d04d0b67952922887
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113510"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurera Azure Storage-brandväggar och virtuella nätverk

Azure Storage tillhandahåller en skiktbaserad säkerhetsmodell. Med den här modellen kan du skydda och kontrollera åtkomstnivån till dina lagringskonton som dina program och företagsmiljöer efterfrågar, baserat på typ och delmängd av nätverk som används. När nätverksregler har konfigurerats kan endast program som begär data via den angivna uppsättningen nätverk komma åt ett lagringskonto. Du kan begränsa åtkomsten till ditt lagringskonto till begäranden som kommer från angivna IP-adresser, IP-intervall eller från en lista över undernät i ett Virtuellt Azure-nätverk (VNet).

Lagringskonton har en offentlig slutpunkt som är tillgänglig via Internet. Du kan också skapa [privata slutpunkter för ditt lagringskonto](storage-private-endpoints.md), som tilldelar en privat IP-adress från ditt virtuella nätverk till lagringskontot och säkrar all trafik mellan ditt virtuella nätverk och lagringskontot via en privat länk. Azure-lagringsbrandväggen ger åtkomstkontrollåtkomst för den offentliga slutpunkten för ditt lagringskonto. Du kan också använda brandväggen för att blockera all åtkomst via den offentliga slutpunkten när du använder privata slutpunkter. Konfigurationen av lagringsbrandväggen gör det också möjligt för utvalda betrodda Azure-plattformstjänster att komma åt lagringskontot på ett säkert sätt.

Ett program som har åtkomst till ett lagringskonto när nätverksregler är i kraft kräver fortfarande korrekt auktorisering för begäran. Auktorisering stöds med Azure Active Directory (Azure AD) autentiseringsuppgifter för blobbar och köer, med en giltig kontoåtkomstnyckel eller med en SAS-token.

> [!IMPORTANT]
> Om du aktiverar brandväggsregler för ditt lagringskonto blockeras inkommande begäranden om data som standard, såvida inte begäranden kommer från en tjänst som arbetar inom ett Virtuellt Azure-nätverk (VNet) eller från tillåtna offentliga IP-adresser. Begäranden som blockeras inkluderar de från andra Azure-tjänster, från Azure-portalen, från loggning och måtttjänster och så vidare.
>
> Du kan bevilja åtkomst till Azure-tjänster som fungerar inifrån ett virtuella nätverk genom att tillåta trafik från undernätet som är värd för tjänstinstansen. Du kan också aktivera ett begränsat [Exceptions](#exceptions) antal scenarier via undantagsmekanismen som beskrivs nedan. Om du vill komma åt data från lagringskontot via Azure-portalen måste du vara på en dator inom den betrodda gränsen (antingen IP eller VNet) som du konfigurerar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenarier

För att skydda ditt lagringskonto bör du först konfigurera en regel för att neka åtkomst till trafik från alla nätverk (inklusive internettrafik) på den offentliga slutpunkten som standard. Sedan bör du konfigurera regler som ger åtkomst till trafik från specifika virtuella nätverk. Du kan också konfigurera regler för att bevilja åtkomst till trafik från utvalda offentliga IP-adressintervall för internet, vilket möjliggör anslutningar från specifika internet- eller lokala klienter. Med den här konfigurationen kan du skapa en säker nätverksgräns för dina program.

Du kan kombinera brandväggsregler som tillåter åtkomst från specifika virtuella nätverk och från offentliga IP-adressintervall på samma lagringskonto. Lagringsbrandväggsregler kan tillämpas på befintliga lagringskonton eller när nya lagringskonton skapas.

Lagringsbrandväggsregler gäller för den offentliga slutpunkten för ett lagringskonto. Du behöver inga regler för brandväggsåtkomst för att tillåta trafik för privata slutpunkter för ett lagringskonto. Processen att godkänna skapandet av en privat slutpunkt ger implicit åtkomst till trafik från undernätet som är värd för den privata slutpunkten.

Nätverksregler tillämpas på alla nätverksprotokoll till Azure-lagring, inklusive REST och SMB. Om du vill komma åt data med hjälp av verktyg som Azure-portalen, Storage Explorer och AZCopy måste explicita nätverksregler konfigureras.

När nätverksregler har tillämpats tillämpas de för alla begäranden. SAS-token som ger åtkomst till en specifik IP-adress tjänar till att begränsa åtkomsten för tokeninnehavaren, men beviljar inte ny åtkomst utöver konfigurerade nätverksregler.

Disktrafik på virtuella datorer (inklusive monterings- och avmonteringsåtgärder och disk-I/o) påverkas inte av nätverksregler. REST-åtkomst till sidblobar skyddas av nätverksregler.

Klassiska lagringskonton stöder inte brandväggar och virtuella nätverk.

Du kan använda ohanterade diskar i lagringskonton med nätverksregler som tillämpas på säkerhetskopiering och återställning av virtuella datorer genom att skapa ett undantag. Den här processen dokumenteras i avsnittet [Undantag](#exceptions) i den här artikeln. Brandväggsund undantag är inte tillämpliga med hanterade diskar eftersom de redan hanteras av Azure.

## <a name="change-the-default-network-access-rule"></a>Ändra standardåtkomstregeln för nätverk

Som standard godkänner lagringskonton anslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk måste du först ändra standardåtgärden.

> [!WARNING]
> Om du gör ändringar av nätverksreglerna kan det påverka programmens möjlighet att ansluta till Azure Storage. Om du ställer in standardnätverksregeln så att alla åtkomster till data **nekas** blockeras om inte särskilda nätverksregler som **beviljar** åtkomst också tillämpas. Se till att bevilja åtkomst till alla tillåtna nätverk som använder nätverksregler innan du ändrar standardregeln för att neka åtkomst.

### <a name="managing-default-network-access-rules"></a>Hantera standardregler för nätverksåtkomst

Du kan hantera standardregler för nätverksåtkomst för lagringskonton via Azure-portalen, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn som kallas **Brandväggar och virtuella nätverk**.

1. Om du vill neka åtkomst som standard väljer du att tillåta åtkomst från **valda nätverk**. Om du vill tillåta trafik från alla nätverk väljer du att tillåta åtkomst från **Alla nätverk**.

1. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Visa status för standardregeln för lagringskontot.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Ange att standardregeln ska neka nätverksåtkomst som standard.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Ange standardregeln så att nätverksåtkomst tillåts som standard.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2 (på andra sätt)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli).

1. Visa status för standardregeln för lagringskontot.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Ange att standardregeln ska neka nätverksåtkomst som standard.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Ange standardregeln så att nätverksåtkomst tillåts som standard.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Bevilja åtkomst från ett virtuellt nätverk

Du kan konfigurera lagringskonton så att åtkomst endast tillåts från specifika undernät. De tillåtna undernäten kan tillhöra ett virtuella nätverk i samma prenumeration, eller i en annan prenumeration, inklusive prenumerationer som tillhör en annan Azure Active Directory-klientorganisation.

Aktivera en [tjänstslutpunkt](/azure/virtual-network/virtual-network-service-endpoints-overview) för Azure Storage i det virtuella nätverket. Tjänstenslutpunkt dirigerar trafik från det virtuella nätverket via en optimal sökväg till Azure Storage-tjänsten. Identiteterna för undernätet och det virtuella nätverket överförs också med varje begäran. Administratörer kan sedan konfigurera nätverksregler för lagringskontot som gör att begäranden kan tas emot från specifika undernät i ett virtuella nätverk. Klienter som beviljas åtkomst via dessa nätverksregler måste fortsätta att uppfylla auktoriseringskraven för lagringskontot för att komma åt data.

Varje lagringskonto stöder upp till 100 virtuella nätverksregler, vilket kan kombineras med [IP-nätverksregler](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Tillgängliga regioner för virtuella nätverk

I allmänhet fungerar tjänstslutpunkter mellan virtuella nätverk och tjänstinstanser i samma Azure-region. När du använder tjänstslutpunkter med Azure Storage växer det här scopet till att omfatta den [parade regionen](/azure/best-practices-availability-paired-regions). Tjänstslutpunkter möjliggör kontinuitet under en regional redundans och åtkomst till skrivskyddade geouppsagbara lagringstanser (RA-GRS). Nätverksregler som beviljar åtkomst från ett virtuellt nätverk till ett lagringskonto ger också åtkomst till alla RA-GRS-instanser.

När du planerar för haveriberedskap under ett regionalt avbrott bör du skapa virtuella nätverk i den parade regionen i förväg. Aktivera tjänstslutpunkter för Azure Storage, med nätverksregler som ger åtkomst från dessa alternativa virtuella nätverk. Tillämpa sedan dessa regler på dina geo-redundanta lagringskonton.

> [!NOTE]
> Tjänstslutpunkter gäller inte för trafik utanför regionen för det virtuella nätverket och det angivna regionparet. Du kan bara tillämpa nätverksregler som beviljar åtkomst från virtuella nätverk till lagringskonton i den primära regionen i ett lagringskonto eller i den angivna parkopplade regionen.

### <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill tillämpa en virtuell nätverksregel på ett lagringskonto måste användaren ha rätt behörighet för de undernät som läggs till. Behörigheten som behövs är *Gå med tjänsten till ett undernät* och ingår i den inbyggda rollen Storage Account *Contributor.* Det kan också läggas till anpassade rolldefinitioner.

Lagringskontot och de virtuella nätverk som beviljas åtkomst kan finnas i olika prenumerationer, inklusive prenumerationer som är en del av en annan Azure AD-klientorganisation.

> [!NOTE]
> Konfiguration av regler som ger åtkomst till undernät i virtuella nätverk som är en del av en annan Azure Active Directory-klient stöds för närvarande endast via Powershell-, CLI- och REST-API:er. Sådana regler kan inte konfigureras via Azure-portalen, även om de kan visas i portalen.

### <a name="managing-virtual-network-rules"></a>Hantera regler för virtuella nätverk

Du kan hantera virtuella nätverksregler för lagringskonton via Azure-portalen, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn som kallas **Brandväggar och virtuella nätverk**.

1. Kontrollera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett virtuellt nätverk med en ny nätverksregel klickar du på **Lägg till befintligt virtuellt nätverk**under Virtuella **nätverk,** väljer **Virtuella nätverk** och **undernätsalternativ** och klickar sedan på **Lägg till**. Om du vill skapa ett nytt virtuellt nätverk och bevilja det åtkomst klickar du på **Lägg till nytt virtuellt nätverk**. Ange den information som behövs för att skapa det nya virtuella nätverket och klicka sedan på **Skapa**.

    > [!NOTE]
    > Om en tjänstslutpunkt för Azure Storage inte tidigare har konfigurerats för det valda virtuella nätverket och undernäten, kan du konfigurera den som en del av den här åtgärden.
    >
    > För närvarande visas endast virtuella nätverk som tillhör samma Azure Active Directory-klient för val när regeln skapas. Om du vill bevilja åtkomst till ett undernät i ett virtuellt nätverk som tillhör en annan klient använder du Powershell-, CLI- eller REST-API:er.

1. Om du vill ta bort en virtuell nätverks- eller undernätsregel klickar du på **...** för att öppna snabbmenyn för det virtuella nätverket eller undernätet och klickar på **Ta bort**.

1. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Lista regler för virtuella nätverk.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Aktivera tjänstslutpunkt för Azure Storage i ett befintligt virtuellt nätverk och undernät.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Lägg till en nätverksregel för ett virtuellt nätverk och ett undernät.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Om du vill lägga till en nätverksregel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du en fullt kvalificerad **VirtualNetworkResourceId-parameter** i formuläret "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnet-name/subnet-name".

1. Ta bort en nätverksregel för ett virtuellt nätverk och ett undernät.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Var noga med att [ange standardregeln](#change-the-default-network-access-rule) att **neka**, eller nätverksregler har ingen effekt.

#### <a name="cliv2"></a>CLIv2 (på andra sätt)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli).

1. Lista regler för virtuella nätverk.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Aktivera tjänstslutpunkt för Azure Storage i ett befintligt virtuellt nätverk och undernät.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Lägg till en nätverksregel för ett virtuellt nätverk och ett undernät.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Om du vill lägga till en regel för ett undernät i ett virtuellt nätverk som tillhör en\<annan Azure\>AD-klient använder\<du ett\>fullständigt kvalificerat undernät-ID\<i formuläret\>"/subscriptions/ subscription-ID /resourceGroups/ resourceGroup-Name /providers/Microsoft.Network/virtualNetworks/ vNet-name /subnets/\<subnet-name\>".
    >
    > Du kan använda **prenumerationsparametern** för att hämta undernäts-ID:t för ett virtuella nätverk som tillhör en annan Azure AD-klientorganisation.

1. Ta bort en nätverksregel för ett virtuellt nätverk och ett undernät.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Var noga med att [ange standardregeln](#change-the-default-network-access-rule) att **neka**, eller nätverksregler har ingen effekt.

## <a name="grant-access-from-an-internet-ip-range"></a>Bevilja åtkomst från ett IP-intervall för internet

Du kan konfigurera lagringskonton så att åtkomst tillåts från specifika offentliga IP-adressintervall för internet. Denna konfiguration ger tillgång till specifika internetbaserade tjänster och lokala nätverk och blockerar allmän internettrafik.

Ange tillåtna internetadressintervall med [CIDR-notation](https://tools.ietf.org/html/rfc4632) i formuläret *16.17.18.0/24* eller som enskilda IP-adresser som *16.17.18.19*.

   > [!NOTE]
   > Små adressintervall med prefixstorlekar "/31" eller "/32" stöds inte. Dessa intervall bör konfigureras med hjälp av individuella IP-adressregler.

IP-nätverksregler är endast tillåtna för **offentliga IP-adresser på Internet.** IP-adressintervall som reserverats för privata nätverk (enligt definitionen i [RFC 1918)](https://tools.ietf.org/html/rfc1918#section-3)är inte tillåtna i IP-regler. Privata nätverk omfattar adresser som börjar med _10.*_, _172.16.*_ - _172.31.*_ och _192.168.*_.

   > [!NOTE]
   > IP-nätverksregler har ingen effekt på begäranden som kommer från samma Azure-region som lagringskontot. Använd [virtuella nätverksregler](#grant-access-from-a-virtual-network) för att tillåta begäranden i samma region.

  > [!NOTE]
  > Tjänster som distribueras i samma region som lagringskontot använder privata Azure IP-adresser för kommunikation. Därför kan du inte begränsa åtkomsten till specifika Azure-tjänster baserat på deras offentliga utgående IP-adressintervall.

Endast IPV4-adresser stöds för konfiguration av lagringsbrandväggsregler.

Varje lagringskonto stöder upp till 100 IP-nätverksregler.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk

Om du vill bevilja åtkomst från dina lokala nätverk till ditt lagringskonto med en IP-nätverksregel måste du identifiera de IP-adresser som är vända mot internet som används av nätverket. Kontakta nätverksadministratören för hjälp.

Om du använder [ExpressRoute](/azure/expressroute/expressroute-introduction) lokalt för offentlig peering eller Microsoft-peering, måste du identifiera de NAT IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering är NAT-IP-adresser som används antingen kunderbjudande eller tillhandahålls av tjänsteleverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du en supportbegäran hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Hantera IP-nätverksregler

Du kan hantera IP-nätverksregler för lagringskonton via Azure-portalen, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn som kallas **Brandväggar och virtuella nätverk**.

1. Kontrollera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Om du vill bevilja åtkomst till ett IP-intervall på Internet anger du IP-adressen eller adressintervallet (i CIDR-format) under **Brandväggs** > **adressintervall**.

1. Om du vill ta bort en IP-nätverksregel klickar du på papperskorgen bredvid adressintervallet.

1. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Lista IP-nätverksregler.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Lägg till en nätverksregel för en enskild IP-adress.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Lägg till en nätverksregel för ett IP-adressintervall.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Ta bort en nätverksregel för en enskild IP-adress.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Ta bort en nätverksregel för ett IP-adressintervall.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Var noga med att [ange standardregeln](#change-the-default-network-access-rule) att **neka**, eller nätverksregler har ingen effekt.

#### <a name="cliv2"></a>CLIv2 (på andra sätt)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli).

1. Lista IP-nätverksregler.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Lägg till en nätverksregel för en enskild IP-adress.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Lägg till en nätverksregel för ett IP-adressintervall.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Ta bort en nätverksregel för en enskild IP-adress.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Ta bort en nätverksregel för ett IP-adressintervall.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Var noga med att [ange standardregeln](#change-the-default-network-access-rule) att **neka**, eller nätverksregler har ingen effekt.

## <a name="exceptions"></a>Undantag

Nätverksregler hjälper till att skapa en säker miljö för anslutningar mellan dina program och dina data för de flesta scenarier. Vissa program är dock beroende av Azure-tjänster som inte kan isoleras unikt via virtuella nätverks- eller IP-adressregler. Men sådana tjänster måste beviljas lagring för att möjliggöra fullständig programfunktionalitet. I sådana situationer kan du använda inställningen ***Tillåt betrodda Microsoft-tjänster...*** för att göra det möjligt för sådana tjänster att komma åt dina data, loggar eller analyser.

### <a name="trusted-microsoft-services"></a>Betrodda Microsoft-tjänster

Vissa Microsoft-tjänster fungerar från nätverk som inte kan inkluderas i dina nätverksregler. Du kan ge en delmängd av sådana betrodda Microsoft-tjänster åtkomst till lagringskontot, samtidigt som du behåller nätverksregler för andra appar. Dessa betrodda tjänster använder sedan stark autentisering för att ansluta till ditt lagringskonto på ett säkert sätt. Vi har aktiverat två lägen för betrodd åtkomst för Microsoft-tjänster.

- Resurser för vissa tjänster, **när de är registrerade i din prenumeration,** kan komma åt ditt lagringskonto i samma **prenumeration** för utvalda åtgärder, till exempel skriva loggar eller säkerhetskopiera.
- Resurser för vissa tjänster kan beviljas explicit åtkomst till ditt lagringskonto genom **att tilldela en RBAC-roll** till dess systemtilldelade hanterade identitet.


När du aktiverar inställningen **Tillåt betrodda Microsoft-tjänster...** beviljas resurser för följande tjänster som är registrerade i samma prenumeration som ditt lagringskonto åtkomst för en begränsad uppsättning åtgärder enligt beskrivningen:

| Tjänst                  | Namn på resursprovider     | Tillåtna åtgärder                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Kör säkerhetskopior och återställningar av ohanterade diskar i virtuella IAAS-datorer. (krävs inte för hanterade diskar). [Läs mer](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Aktiverar import av data till Azure med databoxen. [Läs mer](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Anpassad avbildningsgenerering och artefaktinstallation. [Läs mer](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Aktivera Blob Storage-händelsepublicering och tillåt Event Grid att publicera till lagringsköer. Lär dig mer om [blob-lagringshändelser](/azure/event-grid/event-sources) och [publicering till köer](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft.EventHub         | Arkivera data med insamling av händelsehubbar. [Läs mer](/azure/event-hubs/event-hubs-capture-overview). |
| Azure File Sync          | Microsoft.StorageSync      | Gör att du kan omvandla din on-prem-filserver till en cache för Azure File-resurser. Möjliggör synkronisering på flera webbplatser, snabb katastrofåterställning och säkerhetskopiering på molnsidan. [Läs mer](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Etablera det ursprungliga innehållet i standardfilsystemet för ett nytt HDInsight-kluster. [Läs mer](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Exportera Azure-import      | Microsoft.ImportExport     | Möjliggör import av data till Azure och export av data från Azure med hjälp av import/export-tjänsten. [Läs mer](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | Gör det möjligt att skriva övervakningsdata till ett skyddat lagringskonto, inklusive resursdiagnostiska loggar, Azure Active Directory-inloggnings- och granskningsloggar och Microsoft Intune-loggar. [Läs mer](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure-nätverk         | Microsoft.Network          | Lagra och analysera nätverkstrafikloggar. [Läs mer](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Aktivera replikering för haveriberedskap av virtuella Azure IaaS-datorer när du använder brandväggsaktiverade cache-, käll- eller mållagringskonton.  [Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

Inställningen **Tillåt betrodda Microsoft-tjänster...** gör det också möjligt för en viss instans av nedanstående tjänster att komma åt lagringskontot, om du uttryckligen [tilldelar en RBAC-roll](storage-auth-aad.md#assign-rbac-roles-for-access-rights) till den [systemtilldelade hanterade identiteten](../../active-directory/managed-identities-azure-resources/overview.md) för den resursinstansen. I det här fallet motsvarar åtkomstomfånget för instansen den RBAC-roll som tilldelats den hanterade identiteten.

| Tjänst                        | Namn på resursprovider                 | Syfte            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Gör det möjligt för Cognitive Search-tjänster att komma åt lagringskonton för indexering, bearbetning och frågor. |
| Azure Container Registry Tasks | Microsoft.Container-register/register | ACR-uppgifter kan komma åt lagringskonton när behållaravbildningar skapas. |
| Azure Data Factory             | Microsoft.DataFactory/fabriker        | Tillåter åtkomst till lagringskonton via ADF-körningen. |
| Azure Data Share               | Microsoft.DataShare/konton           | Ger åtkomst till lagringskonton via Datadelning. |
| Azure Logic Apps               | Microsoft.Logic/arbetsflöden              | Gör det möjligt för logikappar att komma åt lagringskonton. [Läs mer](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Azure Machine Learning-tjänsten | Microsoft.MachineLearningServices      | Auktoriserade Azure Machine Learning-arbetsytor skriver experimentutdata, modeller och loggar till Blob-lagring och läser data. [Läs mer](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Tillåter import och export av data från specifika SQL Database-instanser med PolyBase. [Läs mer](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.StreamAnalytics Microsoft.             | Gör att data från ett direktuppspelningsjobb kan skrivas till Blob-lagring. Den här funktionen är för närvarande en förhandsversion. [Läs mer](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft.Synapse/arbetsytor          | Aktiverar åtkomst till data i Azure Storage från Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Åtkomst till lagringsanalysdata

I vissa fall krävs åtkomst till läsa diagnostiska loggar och mått utanför nätverksgränsen. När du konfigurerar åtkomst till betrodda tjänster till lagringskontot kan du tillåta läsåtkomst för loggfiler, måtttabeller eller båda. [Läs mer om hur du arbetar med lagringsanalys.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Hantera undantag

Du kan hantera undantag för nätverksregeln via Azure-portalen, PowerShell eller Azure CLI v2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn som kallas **Brandväggar och virtuella nätverk**.

1. Kontrollera att du har valt att tillåta åtkomst från **valda nätverk**.

1. Under **Undantag**väljer du de undantag som du vill bevilja.

1. Klicka på **Spara** för att tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell

1. Installera [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Visa undantagen för lagringskontonätverksreglerna.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Konfigurera undantagen till lagringskontonätverksreglerna.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Ta bort undantagen från lagringskontonätverksreglerna.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Var noga med att [ställa in standardregeln](#change-the-default-network-access-rule) att **neka**, eller ta bort undantag har ingen effekt.

#### <a name="cliv2"></a>CLIv2 (på andra sätt)

1. Installera [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli).

1. Visa undantagen för lagringskontonätverksreglerna.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Konfigurera undantagen till lagringskontonätverksreglerna.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Ta bort undantagen från lagringskontonätverksreglerna.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Var noga med att [ställa in standardregeln](#change-the-default-network-access-rule) att **neka**, eller ta bort undantag har ingen effekt.

## <a name="next-steps"></a>Nästa steg

Läs mer om slutpunkter för Azure Network-tjänsten i [tjänstslutpunkter](/azure/virtual-network/virtual-network-service-endpoints-overview).

Gräva djupare i Azure Storage-säkerhet i [Azure Storage-säkerhetsguiden](../blobs/security-recommendations.md).

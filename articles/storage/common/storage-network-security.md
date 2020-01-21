---
title: Konfigurera Azure Storage-brandväggar och virtuella nätverk | Microsoft Docs
description: Konfigurera överlappande nätverkssäkerhet för ditt lagringskonto.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 28e3f276da231ef0186dba8ecd9c064cdd10a5a8
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281231"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurera Azure Storage-brandväggar och virtuella nätverk

Azure Storage tillhandahåller en skiktbaserad säkerhetsmodell. Med den här modellen kan du skydda och kontrol lera åtkomst nivån till dina lagrings konton som dina program och företags miljöer kräver, baserat på typ och delmängd av nätverk som används. När nätverks regler har kon figurer ATS kan endast program som begär data i den angivna uppsättningen nätverk komma åt ett lagrings konto. Du kan begränsa åtkomsten till ditt lagrings konto till begär Anden som kommer från angivna IP-adresser, IP-intervall eller från en lista över undernät i ett Azure-Virtual Network (VNet).

Lagrings konton har en offentlig slut punkt som kan nås via Internet. Du kan också skapa [privata slut punkter för ditt lagrings konto](storage-private-endpoints.md), som tilldelar en privat IP-adress från ditt VNet till lagrings kontot och skyddar all trafik mellan ditt VNet och lagrings kontot över en privat länk. Azure Storage-brandväggen ger åtkomst kontroll för den offentliga slut punkten för ditt lagrings konto. Du kan också använda brand väggen för att blockera all åtkomst via den offentliga slut punkten när du använder privata slut punkter. Konfigurationen av lagrings brand väggen aktiverar även de betrodda Azure Platform-tjänsterna för att få åtkomst till lagrings kontot på ett säkert sätt.

Ett program som har åtkomst till ett lagrings konto när nätverks regler tillämpas kräver fortfarande korrekt auktorisering för begäran. Auktorisering stöds med Azure Active Directory (Azure AD)-autentiseringsuppgifter för blobbar och köer, med en giltig konto åtkomst nyckel eller med en SAS-token.

> [!IMPORTANT]
> Att aktivera brand Väggs regler för ditt lagrings konto blockerar inkommande begär Anden om data som standard, om inte begär Anden kommer från en tjänst som körs i ett Azure-Virtual Network (VNet) eller från tillåtna offentliga IP-adresser. Begäranden som blockeras är de från andra Azure-tjänster från Azure-portalen från loggning och mått tjänster, och så vidare.
>
> Du kan bevilja åtkomst till Azure-tjänster som fungerar inifrån ett VNet genom att tillåta trafik från det undernät som är värd för tjänst instansen. Du kan också aktivera ett begränsat antal scenarier med hjälp av [undantags](#exceptions) mekanismen som beskrivs nedan. Om du vill komma åt data från lagrings kontot via Azure Portal måste du vara på en dator inom den betrodda gränser (antingen IP eller VNet) som du konfigurerar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenarier

Om du vill skydda ditt lagrings konto bör du först konfigurera en regel för att neka åtkomst till trafik från alla nätverk (inklusive Internet trafik) på den offentliga slut punkten som standard. Sedan bör du konfigurera regler som beviljar åtkomst till trafik från vissa virtuella nätverk. Du kan också konfigurera regler för att bevilja åtkomst till trafik från Välj offentliga IP-adressintervall för Internet, aktivera anslutningar från vissa Internet-eller lokala klienter. Den här konfigurationen kan du skapa en säker nätverksgräns för dina program.

Du kan kombinera brand Väggs regler som tillåter åtkomst från vissa virtuella nätverk och från offentliga IP-adressintervall på samma lagrings konto. Lagrings brand Väggs regler kan tillämpas på befintliga lagrings konton eller när du skapar nya lagrings konton.

Lagrings brand Väggs regler gäller för den offentliga slut punkten för ett lagrings konto. Du behöver inga brand Väggs åtkomst regler för att tillåta trafik för privata slut punkter för ett lagrings konto. Processen med att godkänna skapandet av en privat slut punkt ger implicit åtkomst till trafik från det undernät som är värd för den privata slut punkten.

Regler tillämpas på alla nätverksprotokoll till Azure storage, inklusive REST och SMB. Om du vill komma åt data med hjälp av verktyg som Azure Portal, Storage Explorer och AZCopy måste explicita nätverks regler konfigureras.

När nätverket regler, är de tillämpas för alla förfrågningar. SAS-token som ger åtkomst till en specifik IP-adress fungera att begränsa åtkomst till token innehavaren, men bevilja inte nya åtkomst utanför konfigurerade regler.

Trafik för virtuella datorer-disk (inklusive montera och demontera åtgärder och disk-i/o) påverkas inte av Nätverksregler. REST-åtkomst till sidblobar är skyddat av Nätverksregler.

Klassiska lagringskonton har inte stöd för brandväggar och virtuella nätverk.

Du kan använda ohanterade diskar i lagringskonton med network-regler som tillämpas för säkerhetskopiering och återställning av virtuella datorer genom att skapa ett undantag. Den här processen beskrivs i den [undantag](#exceptions) i den här artikeln. Brandväggsundantag är inte tillämpligt med hanterade diskar som de redan hanteras av Azure.

## <a name="change-the-default-network-access-rule"></a>Ändra standardåtkomstregel för nätverk

Som standard godkänner lagringskonton anslutningar från klienter på något nätverk. För att begränsa åtkomsten till valda nätverk, måste du först ändra standardåtgärden.

> [!WARNING]
> Gör ändringar i Nätverksregler kan påverka dina program möjlighet att ansluta till Azure Storage. Om du anger standard nätverks regeln till **neka** blockeras alla åtkomst till data, om inte vissa nätverks regler som **beviljar** åtkomst också tillämpas. Glöm inte att bevilja åtkomst till några tillåtna nätverk med hjälp av regler innan du ändrar Standardregeln för att neka åtkomst.

### <a name="managing-default-network-access-rules"></a>Hantera åtkomstreglerna för standardnätverket

Du kan hantera åtkomstreglerna för standardnätverket för storage-konton via Azure portal, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.

1. Om du vill neka åtkomst som standard välja att tillåta åtkomst från **valda nätverk**. För att tillåta trafik från alla nätverk, välja att tillåta åtkomst från **alla nätverk**.

1. Klicka på **spara** att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell

1. Installera den [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Visa status för Standardregeln för storage-kontot.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Ange Standardregeln för att neka åtkomst som standard.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Ange Standardregeln som tillåter åtkomst till nätverket som standard.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Installera den [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli).

1. Visa status för Standardregeln för storage-kontot.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Ange Standardregeln för att neka åtkomst som standard.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Ange Standardregeln som tillåter åtkomst till nätverket som standard.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Bevilja åtkomst från ett virtuellt nätverk

Du kan konfigurera lagrings konton så att endast åtkomst från vissa undernät tillåts. De tillåtna under näten kan tillhöra ett VNet i samma prenumeration eller i en annan prenumeration, inklusive prenumerationer som tillhör en annan Azure Active Directory klient.

Aktivera en [tjänstslutpunkt](/azure/virtual-network/virtual-network-service-endpoints-overview) för Azure Storage inom det virtuella nätverket. Tjänst slut punkten dirigerar trafik från VNet via en optimal sökväg till Azure Storage tjänsten. Identiteterna för under nätet och det virtuella nätverket överförs också med varje begäran. Administratörer kan sedan konfigurera nätverks regler för det lagrings konto som tillåter att förfrågningar tas emot från vissa undernät i ett VNet. Klienter som beviljas åtkomst via dessa Nätverksregler måste fortsätta att uppfylla behörighetskraven för storage-konto för att komma åt data.

Varje lagringskonto har stöd för upp till 100 virtual network-regler som kan kombineras med [IP Nätverksregler](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Tillgängligt virtuellt nätverk regioner

I allmänhet fungerar tjänstslutpunkter mellan virtuella nätverk och service-instanser i samma Azure-region. När du använder Tjänsteslutpunkter med Azure Storage, det här omfånget expanderar för att inkludera den [parad region](/azure/best-practices-availability-paired-regions). Tjänstslutpunkter kan affärskontinuitet under en regional redundans och åtkomst till skrivskyddad geo-redundant lagring (RA-GRS)-instanser. Regler för att bevilja åtkomst från ett virtuellt nätverk till ett lagringskonto kan du även tilldela åtkomst till valfri RA-GRS-instans.

När du planerar för haveriberedskap under ett regionalt strömavbrott, bör du skapa de virtuella nätverken i den parade regionen i förväg. Aktivera Tjänsteslutpunkter för Azure Storage, med Nätverksregler bevilja åtkomst från dessa alternativ virtuella nätverk. Därefter tillämpa reglerna för geo-redundant storage-konton.

> [!NOTE]
> Tjänstslutpunkter gäller inte för trafik utanför det virtuella nätverket och avsedda regionparet. Du kan bara använda Nätverksregler som beviljar åtkomst från virtuella nätverk till storage-konton i den primära regionen av ett lagringskonto eller i den avsedda parade regionen.

### <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill tillämpa en regel för virtuella nätverk till ett lagringskonto, måste användaren ha rätt behörigheter för undernäten läggas till. Behörighet som krävs är *delta Service till ett undernät* och ingår i den *Lagringskontodeltagare* inbyggd roll. Det kan också läggas till anpassade rolldefinitioner.

Lagrings kontot och de virtuella nätverk som beviljats åtkomst kan finnas i olika prenumerationer, inklusive prenumerationer som ingår i en annan Azure AD-klient.

> [!NOTE]
> Konfiguration av regler som beviljar åtkomst till undernät i virtuella nätverk som ingår i en annan Azure Active Directory klient stöds för närvarande bara via PowerShell-, CLI-och REST-API: er. Sådana regler kan inte konfigureras via Azure Portal, men de kan visas i portalen.

### <a name="managing-virtual-network-rules"></a>Hantera virtuella Nätverksregler

Du kan hantera virtuella Nätverksregler för lagringskonton via Azure-portalen, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.

1. Kontrollera att du har valt för att tillåta åtkomst från **valda nätverk**.

1. Att bevilja åtkomst till ett virtuellt nätverk med en ny nätverksregel under **virtuella nätverk**, klickar du på **Lägg till befintligt virtuellt nätverk**väljer **virtuella nätverk** och **Undernät** alternativ och klickar sedan på **Lägg till**. Om du vill skapa ett nytt virtuellt nätverk och ge det åtkomst, klickar du på **Lägg till nytt virtuellt nätverk**. Ange informationen som behövs för att skapa det nya virtuella nätverket och klickar sedan på **skapa**.

    > [!NOTE]
    > Om en tjänstslutpunkt för Azure Storage inte tidigare har konfigurerats för det valda virtuella nätverk och undernät, kan du konfigurera den som en del av den här åtgärden.
    >
    > För närvarande visas endast virtuella nätverk som hör till samma Azure Active Directory klient organisation för val av skapande av regel. Om du vill bevilja åtkomst till ett undernät i ett virtuellt nätverk som tillhör en annan klient organisation använder du PowerShell-, CLI-eller REST-API: er.

1. Ta bort ett virtuellt nätverk eller undernät regeln genom att klicka på **...**  öppna snabbmenyn för det virtuella nätverket eller undernätet och klickar på **ta bort**.

1. Klicka på **spara** att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell

1. Installera den [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Lista över virtuella Nätverksregler.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Aktivera tjänstslutpunkt för Azure Storage på ett befintligt virtuellt nätverk och undernät.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Lägg till en regel för ett virtuellt nätverk och undernät.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Om du vill lägga till en nätverks regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du en fullständigt kvalificerad **VirtualNetworkResourceId** -parameter i formatet "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-Name/subnets/Subnet-Name".

1. Ta bort en regel för ett virtuellt nätverk och undernät.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) till **neka**, eller Nätverksregler har ingen effekt.

#### <a name="cliv2"></a>CLIv2

1. Installera den [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli).

1. Lista över virtuella Nätverksregler.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Aktivera tjänstslutpunkt för Azure Storage på ett befintligt virtuellt nätverk och undernät.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Lägg till en regel för ett virtuellt nätverk och undernät.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Om du vill lägga till en regel för ett undernät i ett virtuellt nätverk som tillhör en annan Azure AD-klient använder du ett fullständigt kvalificerat undernät-ID i formatet "/Subscriptions/\<Subscription-ID\>/resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-Name\>/subnets/\<Subnet-Name\>".
    >
    > Du kan använda parametern **Subscription** för att hämta Undernäts-ID: t för ett VNet som tillhör en annan Azure AD-klient.

1. Ta bort en regel för ett virtuellt nätverk och undernät.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) till **neka**, eller Nätverksregler har ingen effekt.

## <a name="grant-access-from-an-internet-ip-range"></a>Bevilja åtkomst från ett internet IP-intervall

Du kan konfigurera lagringskonton för att tillåta åtkomst från internet för specifika offentliga IP-adressintervall. Den här konfigurationen ger åtkomst till specifika Internetbaserade tjänster och lokala nätverk och blockerar Allmänt Internettrafik.

Ange tillåtna internet-adressintervall med hjälp av [CIDR-notation](https://tools.ietf.org/html/rfc4632) i formuläret *16.17.18.0/24* eller som enskilda IP adresser som *16.17.18.19*.

   > [!NOTE]
   > Små adressintervall med hjälp av ”/ 31” eller ”/ 32” prefix storlekar inte stöds. Dessa områden ska konfigureras med regler för enskilda IP-adress.

IP-Nätverksregler är bara tillåtna för **offentliga internet** IP-adresser. IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) tillåts inte i IP-regler. Privata nätverk innehåller adresser som börjar med _10.*_ , _172.16. *_  - _172.31. *_ , och _192.168. *_ .

   > [!NOTE]
   > IP-Nätverksregler har ingen effekt på förfrågningar som kommer från samma Azure-region som lagringskontot. Använd [virtuella Nätverksregler](#grant-access-from-a-virtual-network) att tillåta begäranden för samma region.

  > [!NOTE]
  > Tjänster som distribueras i samma region som lagrings kontot använder privata Azure IP-adresser för kommunikation. Därför kan du inte begränsa åtkomsten till vissa Azure-tjänster baserat på deras offentliga inkommande IP-adressintervall.

Endast IPV4-adresser stöds för konfiguration av lagrings brand Väggs regler.

Varje lagrings konto har stöd för upp till 100 IP-nätverksanslutningar.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk

Om du vill bevilja åtkomst från ditt lokala nätverk till ditt lagringskonto med en regel för IP-nätverk, måste du identifiera den internetuppkopplade IP-adresser som används av nätverket. Kontakta nätverksadministratören om du behöver hjälp.

Om du använder [ExpressRoute](/azure/expressroute/expressroute-introduction) lokalt för offentlig peering eller Microsoft-peering, måste du identifiera de NAT IP-adresser som används. För offentlig peering, använder varje ExpressRoute-krets som standard två NAT IP-adresser, som används för Azure-tjänsttrafik när trafiken kommer till Microsoft Azure-stamnätverket. För Microsoft-peering används de NAT-IP-adresser som används antingen för kunden eller tillhandahålls av tjänst leverantören. Om du vill tillåta åtkomst till dina tjänstresurser måste du tillåta dessa offentliga IP-adresser i resursens IP-brandväggsinställning. För att kunna hitta ExpressRoute-kretsens IP-adresser för offentlig peering [öppnar du ett supportärende hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure-portalen. Lär dig mer om [NAT för ExpressRoute offentliga peering och Microsoft-peering.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Hantera regler för IP-nätverk

Du kan hantera IP-Nätverksregler för lagringskonton via Azure-portalen, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.

1. Kontrollera att du har valt för att tillåta åtkomst från **valda nätverk**.

1. Att bevilja åtkomst till ett internet IP-adressintervall, ange IP-adressen eller adressintervallet (i CIDR-format) under **brandväggen** > **adressintervall**.

1. Ta bort en regel för IP-, klicka på papperskorgsikonen bredvid adressintervallet.

1. Klicka på **spara** att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell

1. Installera den [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Regler för lista över IP-nätverk.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Lägg till en regel för en enskild IP-adress.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Lägg till en regel för ett IP-adressintervall.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Ta bort en regel för en enskild IP-adress.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Ta bort en regel för ett IP-adressintervall.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) till **neka**, eller Nätverksregler har ingen effekt.

#### <a name="cliv2"></a>CLIv2

1. Installera den [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli).

1. Regler för lista över IP-nätverk.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Lägg till en regel för en enskild IP-adress.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Lägg till en regel för ett IP-adressintervall.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Ta bort en regel för en enskild IP-adress.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Ta bort en regel för ett IP-adressintervall.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) till **neka**, eller Nätverksregler har ingen effekt.

## <a name="exceptions"></a>Undantag

Nätverks regler hjälper till att skapa en säker miljö för anslutningar mellan dina program och dina data för de flesta scenarier. Vissa program är dock beroende av Azure-tjänster som inte kan isoleras unikt genom virtuella nätverks-eller IP-adress regler. Men sådana tjänster måste beviljas till lagring för att möjliggöra fullständig program funktion. I sådana fall kan du använda inställningen ***Tillåt betrodda Microsoft-tjänster...*** för att aktivera sådana tjänster för att få åtkomst till dina data, loggar eller analyser.

### <a name="trusted-microsoft-services"></a>Betrodda Microsoft-tjänster

Vissa Microsoft-tjänster körs från nätverk som inte kan ingå i dina nätverks regler. Du kan ge en delmängd av dessa betrodda Microsoft-tjänster åtkomst till lagrings kontot, samtidigt som nätverks reglerna för andra appar upprätthålls. Dessa betrodda tjänster kommer sedan att använda stark autentisering för att ansluta till ditt lagrings konto på ett säkert sätt. Vi har aktiverat två lägen för betrodd åtkomst för Microsoft-tjänster.

- Resurser för vissa tjänster, **när de registreras i din prenumeration**, kan komma åt ditt lagrings konto **i samma prenumeration** för Select-åtgärder, till exempel skriva loggar eller säkerhets kopiering.
- Resurser i vissa tjänster kan beviljas uttrycklig åtkomst till ditt lagrings konto genom att **tilldela en RBAC-roll** till den systemtilldelade hanterade identiteten.


När du aktiverar inställningen **Tillåt betrodda Microsoft-tjänster...** , beviljas resurser för följande tjänster som är registrerade i samma prenumeration som ditt lagrings konto åtkomst för en begränsad uppsättning åtgärder enligt beskrivningen:

| Tjänst                  | Providernamn för resursen     | Tillåtna åtgärder                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Köra säkerhetskopieringar och återställningar av ohanterade diskar i virtuella IAAS-datorer. (krävs inte för hanterade diskar.) [Läs mer](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Gör det möjligt att importera data till Azure med hjälp av Data Box-enhet. [Läs mer](/azure/databox/data-box-overview). |
| Labb för utveckling och testning i Azure       | Microsoft.DevTestLab       | Anpassad avbildning skapande och artefakt installation. [Läs mer](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Aktivera publicering av Blob Storage-händelser och låt Event Grid att publicera till storage-köer. Lär dig mer om [blob storage-händelser](/azure/event-grid/event-sources) och [publicering till köer](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft.EventHub         | Arkivera data med Event Hubs Capture. [Läs mer](/azure/event-hubs/event-hubs-capture-overview). |
| Azure File Sync          | Microsoft.StorageSync      | Gör att du kan omvandla din lokal fil server till ett cacheminne för Azure-filresurser. Tillåter synkronisering av flera platser, snabb katastrof återställning och säkerhets kopiering på moln sidan. [Läs mer](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Etablera det inledande innehållet i standard fil systemet för ett nytt HDInsight-kluster. [Läs mer](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Azure import-export      | Microsoft.ImportExport     | Möjliggör import av data till Azure och export av data från Azure med hjälp av import/export-tjänsten. [Läs mer](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | Tillåter skrivning av övervakning av data till en säker lagringskonto [mer](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure-nätverk         | Microsoft.Network          | Store och analysera loggar med webbtrafik. [Läs mer](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Aktivera replikering för haveri beredskap för virtuella Azure IaaS-datorer när du använder brand Väggs-aktiverade cache-, käll-eller mål lagrings konton.  [Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

Inställningen **Tillåt betrodda Microsoft-tjänster...** tillåter också att en viss instans av nedanstående tjänster får åtkomst till lagrings kontot, om du uttryckligen [tilldelar en RBAC-roll](storage-auth-aad.md#assign-rbac-roles-for-access-rights) till den [systemtilldelade hanterade identiteten](../../active-directory/managed-identities-azure-resources/overview.md) för den resurs instansen. I det här fallet motsvarar åtkomst omfånget för instansen den RBAC-roll som tilldelats den hanterade identiteten.

| Tjänst                        | Providernamn för resursen          | Syfte            |
| :----------------------------- | :------------------------------------- | :---------- |
| Azure Container Registry Tasks | Microsoft. ContainerRegistry/register | ACR-aktiviteter kan komma åt lagrings konton när du skapar behållar avbildningar. |
| Azure Data Factory             | Microsoft. DataFactory/fabriker        | Ger åtkomst till lagrings konton via ADF-körningen. |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Gör att Logic Apps kan komma åt lagrings konton. [Läs mer](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning | Microsoft.MachineLearningServices      | Auktoriserade Azure Machine Learning arbets ytor Skriv experiment, modeller och loggar till Blob Storage. [Läs mer](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). |
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Tillåter import och export av data från vissa SQL Database instanser med PolyBase. [Läs mer](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Tillåter att data från ett strömmande jobb skrivs till Blob Storage. Den här funktionen är för närvarande en förhandsversion. [Läs mer](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft. Synapse/arbets ytor          | Ger åtkomst till data i Azure Storage från Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Dataåtkomst för Storage analytics

I vissa fall kan krävs åtkomst till Läs diagnostikloggar och mått från utanför nätverksgränsen för. När du konfigurerar betrodda tjänster till lagrings kontot kan du tillåta Läs åtkomst för loggfiler, mått tabeller eller både och. [Läs mer om hur du arbetar med storage analytics.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Hantering av undantag

Du kan hantera undantag nätverk via Azure-portalen, PowerShell eller Azure CLI v2.

#### <a name="azure-portal"></a>Azure portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.

1. Kontrollera att du har valt för att tillåta åtkomst från **valda nätverk**.

1. Under **undantag**, Välj de undantag som du vill tilldela.

1. Klicka på **spara** att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell

1. Installera den [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Visa undantag för Nätverksregler för storage-konto.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Konfigurera undantag för Nätverksregler för storage-konto.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Ta bort undantagen Nätverksregler för storage-konto.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) till **neka**, eller ta bort undantag har ingen effekt.

#### <a name="cliv2"></a>CLIv2

1. Installera den [Azure CLI](/cli/azure/install-azure-cli) och [logga in](/cli/azure/authenticate-azure-cli).

1. Visa undantag för Nätverksregler för storage-konto.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Konfigurera undantag för Nätverksregler för storage-konto.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Ta bort undantagen Nätverksregler för storage-konto.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) till **neka**, eller ta bort undantag har ingen effekt.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Network-tjänstslutpunkter i [tjänstslutpunkter](/azure/virtual-network/virtual-network-service-endpoints-overview).

Fördjupa dig i Azure Storage-säkerhet i [säkerhetsguiden för Azure Storage](../blobs/security-recommendations.md).

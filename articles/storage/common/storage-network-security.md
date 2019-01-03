---
title: Konfigurera Azure Storage-brandväggar och virtuella nätverk | Microsoft Docs
description: Konfigurera överlappande nätverkssäkerhet för ditt lagringskonto.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 10/30/2018
ms.author: cbrooks
ms.component: common
ms.openlocfilehash: e8e81ab81e33302b9a0da3e0230d1366cc90d208
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635518"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurera Azure Storage-brandväggar och virtuella nätverk

Azure Storage tillhandahåller en skiktbaserad säkerhetsmodell. Den här modellen kan du skydda dina storage-konton till en specifik uppsättning stöds nätverk. Om Nätverksregler har konfigurerats endast program som begär data via den angivna uppsättningen nätverk kan komma åt ett storage-konto.

Ett program som ansluter till ett lagringskonto när Nätverksregler tillämpas kräver rätt behörighet på begäran. Auktorisering stöds med Azure Active Directory (AD)-autentiseringsuppgifter (för blobbar och köer) (förhandsversion), en giltig åtkomstnyckel eller en SAS-token.

> [!IMPORTANT]
> Aktivera brandväggsregler för ditt lagringskonto blockerar inkommande begäranden om data som standard inte begäranden som kommer från en tjänst som körs i Azure Virtual Network (VNet). Begäranden som blockeras är de från andra Azure-tjänster från Azure-portalen från loggning och mått tjänster, och så vidare.
>
> Du kan ge åtkomst till Azure-tjänster som fungerar från inom ett virtuellt nätverk genom att tillåta att undernätet för tjänstinstansen. Aktivera ett begränsat antal scenarier via den [undantag](#exceptions) mekanism som beskrivs i följande avsnitt. För att komma åt Azure-portalen, skulle du behöva finnas på en dator i den betrodda gränsen (IP eller virtuellt nätverk) som du har konfigurerat.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenarier

Konfigurera lagringskonton för att neka åtkomst för trafik från alla nätverk (inklusive internet-trafik) som standard. Tilldela sedan åtkomst för trafik från specifika virtuella nätverk. Den här konfigurationen kan du skapa en säker nätverksgräns för dina program. Du kan också ge åtkomst till offentliga internet IP-adressintervall, aktivera anslutningar från specifika klienter på internet eller lokalt.

Regler tillämpas på alla nätverksprotokoll till Azure storage, inklusive REST och SMB. Om du vill komma åt data med verktyg som Azure-portalen, Storage Explorer och AZCopy, krävs explicita Nätverksregler.

Du kan använda Nätverksregler till befintliga lagringskonton eller när du skapar nya storage-konton.

När nätverket regler, är de tillämpas för alla förfrågningar. SAS-token som ger åtkomst till en specifik IP-adress fungera att begränsa åtkomst till token innehavaren, men bevilja inte nya åtkomst utanför konfigurerade regler.

Trafik för virtuella datorer-disk (inklusive montera och demontera åtgärder och disk-i/o) påverkas inte av Nätverksregler. REST-åtkomst till sidblobar är skyddat av Nätverksregler.

Klassiska lagringskonton har inte stöd för brandväggar och virtuella nätverk.

Du kan använda ohanterade diskar i lagringskonton med network-regler som tillämpas för säkerhetskopiering och återställning av virtuella datorer genom att skapa ett undantag. Den här processen beskrivs i den [undantag](#exceptions) i den här artikeln. Brandväggsundantag är inte tillämpligt med hanterade diskar som de redan hanteras av Azure.

## <a name="change-the-default-network-access-rule"></a>Ändra standardåtkomstregel för nätverk

Som standard godkänner lagringskonton anslutningar från klienter på något nätverk. För att begränsa åtkomsten till valda nätverk, måste du först ändra standardåtgärden.

> [!WARNING]
> Gör ändringar i Nätverksregler kan påverka dina program möjlighet att ansluta till Azure Storage. Ställa in Standardregeln för nätverket **neka** blockerar alla åtkomst till data, såvida inte specifika regler till **bevilja** åtkomst tillämpas också. Glöm inte att bevilja åtkomst till några tillåtna nätverk med hjälp av regler innan du ändrar Standardregeln för att neka åtkomst.

### <a name="managing-default-network-access-rules"></a>Hantera åtkomstreglerna för standardnätverket

Du kan hantera åtkomstreglerna för standardnätverket för storage-konton via Azure portal, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.

1. Om du vill neka åtkomst som standard välja att tillåta åtkomst från **valda nätverk**. För att tillåta trafik från alla nätverk, välja att tillåta åtkomst från **alla nätverk**.

1. Klicka på **spara** att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell

1. Installera den [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Visa status för Standardregeln för storage-kontot.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Ange Standardregeln för att neka åtkomst som standard.

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Ange Standardregeln som tillåter åtkomst till nätverket som standard.

    ```PowerShell
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

Du kan konfigurera lagringskonton för att tillåta åtkomst från specifika virtuella nätverk.

Aktivera en [tjänstslutpunkt](/azure/virtual-network/virtual-network-service-endpoints-overview) för Azure Storage inom det virtuella nätverket. Den här slutpunkten får trafik en optimal väg till Azure Storage-tjänsten. Identiteten för det virtuella nätverket och undernätet överförs även med varje begäran. Administratörer kan sedan konfigurera Nätverksregler för lagringskontot som tillåter begäranden tas emot från specifika undernät i det virtuella nätverket. Klienter som beviljas åtkomst via dessa Nätverksregler måste fortsätta att uppfylla behörighetskraven för storage-konto för att komma åt data.

Varje lagringskonto har stöd för upp till 100 virtual network-regler som kan kombineras med [IP Nätverksregler](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Tillgängligt virtuellt nätverk regioner

I allmänhet fungerar tjänstslutpunkter mellan virtuella nätverk och service-instanser i samma Azure-region. När du använder Tjänsteslutpunkter med Azure Storage, det här omfånget expanderar för att inkludera den [parad region](/azure/best-practices-availability-paired-regions). Tjänstslutpunkter kan affärskontinuitet under en regional redundans och åtkomst till skrivskyddad geo-redundant lagring (RA-GRS)-instanser. Regler för att bevilja åtkomst från ett virtuellt nätverk till ett lagringskonto kan du även tilldela åtkomst till valfri RA-GRS-instans.

När du planerar för haveriberedskap under ett regionalt strömavbrott, bör du skapa de virtuella nätverken i den parade regionen i förväg. Aktivera Tjänsteslutpunkter för Azure Storage, med Nätverksregler bevilja åtkomst från dessa alternativ virtuella nätverk. Därefter tillämpa reglerna för geo-redundant storage-konton.

> [!NOTE]
> Tjänstslutpunkter gäller inte för trafik utanför det virtuella nätverket och avsedda regionparet. Du kan bara använda Nätverksregler som beviljar åtkomst från virtuella nätverk till storage-konton i den primära regionen av ett lagringskonto eller i den avsedda parade regionen.

### <a name="required-permissions"></a>Nödvändiga behörigheter

Om du vill tillämpa en regel för virtuella nätverk till ett lagringskonto, måste användaren ha rätt behörigheter för undernäten läggas till. Behörighet som krävs är *delta Service till ett undernät* och ingår i den *Lagringskontodeltagare* inbyggd roll. Det kan också läggas till anpassade rolldefinitioner.

Storage-konto och de virtuella nätverken beviljats åtkomst kan vara i olika prenumerationer, men dessa prenumerationer måste ingå i samma Azure AD-klienten.

### <a name="managing-virtual-network-rules"></a>Hantera virtuella Nätverksregler

Du kan hantera virtuella Nätverksregler för lagringskonton via Azure-portalen, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.

1. Kontrollera att du har valt för att tillåta åtkomst från **valda nätverk**.

1. Att bevilja åtkomst till ett virtuellt nätverk med en ny nätverksregel under **virtuella nätverk**, klickar du på **Lägg till befintligt virtuellt nätverk**väljer **virtuella nätverk** och **Undernät** alternativ och klickar sedan på **Lägg till**. Om du vill skapa ett nytt virtuellt nätverk och ge det åtkomst, klickar du på **Lägg till nytt virtuellt nätverk**. Ange informationen som behövs för att skapa det nya virtuella nätverket och klickar sedan på **skapa**.

    > [!NOTE]
    > Om en tjänstslutpunkt för Azure Storage inte tidigare har konfigurerats för det valda virtuella nätverk och undernät, kan du konfigurera den som en del av den här åtgärden.

1. Ta bort ett virtuellt nätverk eller undernät regeln genom att klicka på **...**  öppna snabbmenyn för det virtuella nätverket eller undernätet och klickar på **ta bort**.

1. Klicka på **spara** att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell

1. Installera den [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Lista över virtuella Nätverksregler.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Aktivera tjänstslutpunkt för Azure Storage på ett befintligt virtuellt nätverk och undernät.

    ```PowerShell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Lägg till en regel för ett virtuellt nätverk och undernät.

    ```PowerShell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

1. Ta bort en regel för ett virtuellt nätverk och undernät.

    ```PowerShell
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

IP-Nätverksregler är bara tillåtna för **offentliga internet** IP-adresser. IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) tillåts inte i IP-regler. Privata nätverk innehåller adresser som börjar med _10.*_, _172.16. *_ - _172.31. *_, och _192.168. *_.

   > [!NOTE]
   > IP-Nätverksregler har ingen effekt på förfrågningar som kommer från samma Azure-region som lagringskontot. Använd [virtuella Nätverksregler](#grant-access-from-a-virtual-network) att tillåta begäranden för samma region.

Endast IPV4-adresser stöds just nu.

Varje lagringskonto har stöd för upp till 100 IP-nätverk (MPR), som kan kombineras med [virtuella Nätverksregler](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk

Om du vill bevilja åtkomst från ditt lokala nätverk till ditt lagringskonto med en regel för IP-nätverk, måste du identifiera den internetuppkopplade IP-adresser som används av nätverket. Kontakta nätverksadministratören om du behöver hjälp.

Du kan använda [ExpressRoute](/azure/expressroute/expressroute-introduction) att ansluta ditt nätverk till Azure-nätverket. Varje krets konfigureras här, med två offentliga IP-adresser. De finns i Microsoft Edge och använda [Azure offentlig Peering](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) att ansluta till Microsoft Services som Azure Storage. Skapa IP-Nätverksregler för offentliga IP-adresserna för dina kretsar för att tillåta kommunikation med Azure Storage. Du hittar din ExpressRoute-krets offentliga IP-adresser, [öppnar ett supportärende hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure portal.

### <a name="managing-ip-network-rules"></a>Hantera regler för IP-nätverk

Du kan hantera IP-Nätverksregler för lagringskonton via Azure-portalen, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.

1. Kontrollera att du har valt för att tillåta åtkomst från **valda nätverk**.

1. Att bevilja åtkomst till ett internet IP-adressintervall, ange IP-adressen eller adressintervallet (i CIDR-format) under **brandväggen** > **adressintervall**.

1. Ta bort en regel för IP-, klicka på papperskorgsikonen bredvid adressintervallet.

1. Klicka på **spara** att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell

1. Installera den [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Regler för lista över IP-nätverk.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Lägg till en regel för en enskild IP-adress.

    ```PowerShell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Lägg till en regel för ett IP-adressintervall.

    ```PowerShell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Ta bort en regel för en enskild IP-adress.

    ```PowerShell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Ta bort en regel för ett IP-adressintervall.

    ```PowerShell
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

Regler kan aktivera en säker konfiguration för de flesta scenarier. Det finns dock tillfällen där undantag måste beviljas för att aktivera fullständiga funktioner. Du kan konfigurera storage-konton med undantag för betrodda Microsoft-tjänster och för åtkomst till storage analytics-data.

### <a name="trusted-microsoft-services"></a>Betrodda Microsoft-tjänster

Vissa Microsoft-tjänster som interagerar med storage-konton fungerar från nätverk som inte kan beviljas åtkomst via Nätverksregler.

För att den här typen av tjänsten fungerar som avsett, Tillåt att uppsättningen med betrodda Microsoft-tjänster för att kringgå reglerna nätverk. De här tjänsterna använder sedan stark autentisering för att få åtkomst till lagringskontot.

Om du aktiverar den **Tillåt att betrodda Microsoft-tjänster...**  undantag, följande tjänster (när registrerad för din prenumeration), beviljas åtkomst till lagringskontot:

|Tjänst|Providernamn för resursen|Syfte|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Köra säkerhetskopieringar och återställningar av ohanterade diskar i virtuella IAAS-datorer. (krävs inte för hanterade diskar.) [Läs mer](/azure/backup/backup-introduction-to-azure-backup).|
|Azure Site Recovery|Microsoft.SiteRecovery |Konfigurera haveriberedskap genom att aktivera replikering för Azure IaaS-datorer. Detta krävs om du använder brandvägg är aktiverad cachelagringskontot eller källagringskontot eller mållagringskontot.  [Läs mer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication).|
|Azure DevTest Labs|Microsoft.DevTestLab|Anpassad avbildning skapande och artefakt installation. [Läs mer](/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Aktivera publicering av Blob Storage-händelser och låt Event Grid att publicera till storage-köer. Lär dig mer om [blob storage-händelser](/azure/event-grid/event-sources) och [publicering till köer](/azure/event-grid/event-handlers).|
|Azure Event Hubs|Microsoft.EventHub|Arkivera data med Event Hubs Capture. [Läs mer](/azure/event-hubs/event-hubs-capture-overview).|
|Azure-nätverk|Microsoft.Networking|Store och analysera loggar med webbtrafik. [Läs mer](/azure/network-watcher/network-watcher-packet-capture-overview).|
|Azure Monitor|Microsoft.Insights|Tillåter skrivning av övervakning av data till en säker lagringskonto [mer](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security#monitoring-and-secured-Azure-storage-and-networks).|
|Azure SQL Data Warehouse|Microsoft.Sql|Gör att importera och exportera scenarier med PolyBase. [Läs mer](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).|

### <a name="storage-analytics-data-access"></a>Dataåtkomst för Storage analytics

I vissa fall kan krävs åtkomst till Läs diagnostikloggar och mått från utanför nätverksgränsen för. Du kan bevilja undantag från reglerna för nätverket att tillåta läsåtkomst till loggfiler för storage-konto, mått tabeller eller båda. [Läs mer om hur du arbetar med storage analytics.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Hantering av undantag

Du kan hantera undantag nätverk via Azure-portalen, PowerShell eller Azure CLI v2.

#### <a name="azure-portal"></a>Azure Portal

1. Gå till det lagringskonto som du vill skydda.

1. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.

1. Kontrollera att du har valt för att tillåta åtkomst från **valda nätverk**.

1. Under **undantag**, Välj de undantag som du vill tilldela.

1. Klicka på **spara** att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell

1. Installera den [Azure PowerShell](/powershell/azure/install-Az-ps) och [logga in](/powershell/azure/authenticate-azureps).

1. Visa undantag för Nätverksregler för storage-konto.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Konfigurera undantag för Nätverksregler för storage-konto.

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Ta bort undantagen Nätverksregler för storage-konto.

    ```PowerShell
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

Fördjupa dig i Azure Storage-säkerhet i [säkerhetsguiden för Azure Storage](storage-security-guide.md).

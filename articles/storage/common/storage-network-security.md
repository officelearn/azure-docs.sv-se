---
title: Konfigurera Azure Storage-brandväggar och virtuella nätverk | Microsoft Docs
description: Konfigurera överlappande nätverkssäkerhet för ditt lagringskonto.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 10/25/2017
ms.author: cbrooks
ms.component: common
ms.openlocfilehash: 98972b0c52470e6a404090d993c21a47b11cd660
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427153"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurera Azure Storage-brandväggar och virtuella nätverk
Azure Storage tillhandahåller en skiktbaserad säkerhetsmodell som gör att du kan skydda dina storage-konton till en specifik uppsättning tillåtna nätverk.  Om Nätverksregler har konfigurerats endast program från tillåtna nätverk kan komma åt ett storage-konto.  När du anropar från ett tillåtna nätverk programmen kräver rätt auktorisering (en giltig åtkomstnyckel eller SAS-token) att komma åt lagringskontot.

> [!IMPORTANT]
> Aktivera brandväggsregler för ditt lagringskonto ska blockera inkommande begäranden för data, inklusive från andra Azure-tjänster.  Detta inkluderar med hjälp av portalen skriver loggar osv.  Azure-tjänster som fungerar från inom ett virtuellt nätverk kan beviljas åtkomst genom att tillåta att undernätet för tjänstinstansen.  Azure-tjänster som inte följer riktlinjerna från inom ett virtuellt nätverk kommer att blockeras av brandväggen.  Ett begränsat antal scenarier kan aktiveras via den [undantag](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) mekanism som beskrivs nedan.  Om du vill få åtkomst till portalen skulle du behöva göra det från en dator i den betrodda gränsen (IP eller virtuellt nätverk) som du har ställt in.
>

## <a name="scenarios"></a>Scenarier
Storage-konton kan konfigureras för att neka åtkomst för trafik från alla nätverk (inklusive internet-trafik) som standard.  Åtkomst kan beviljas till trafik från specifika virtuella Azure-nätverk, så att du kan skapa en säker nätverksgräns för dina program.  Kan också få åtkomst till offentliga internet IP-adressintervall, aktivera anslutningar från specifika klienter på internet eller lokalt.

Regler tillämpas på alla nätverksprotokoll till Azure storage, inklusive REST och SMB.  Åtkomst till dina data från verktyg som Azure-portalen, Storage Explorer och AZCopy kräver explicit Nätverksregler som beviljar åtkomst när Nätverksregler är i kraft.

Regler kan tillämpas på befintliga lagringskonton eller kan användas när du skapar nya Storage-konton.

När Nätverksregler tillämpas upprätthålls de för alla begäranden.  SAS-token som beviljar åtkomst till en specifik IP-adress tjänst fungerar till **gränsen** åtkomst till token innehavaren, men de stöder inte bevilja nya åtkomst utöver konfigurerade regler. 

Virtuell datordisktrafik (inklusive montera och demontera operations och disk-i/o) är **inte** påverkas av Nätverksregler.  REST-åtkomst till sidblobar är skyddat av Nätverksregler.

Klassiska lagringskonton **inte** stöd för brandväggar och virtuella nätverk.

Säkerhetskopiering och återställning av virtuella datorer med ohanterade diskar i storage-konton med Nätverksregler som tillämpas stöds via skapar ett undantag som beskrivs i den [undantag](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) i den här artikeln.  Brandväggsundantag kan inte användas med Managed Disks eftersom de hanteras redan av Azure.

## <a name="change-the-default-network-access-rule"></a>Ändra standardåtkomstregel för nätverk
Som standard godkänner lagringskonton anslutningar från klienter på något nätverk.  För att begränsa åtkomsten till valda nätverk, måste du först ändra standardåtgärden.

> [!WARNING]
> Gör ändringar i Nätverksregler kan påverka dina program möjlighet att ansluta till Azure Storage.  Ställa in Standardregeln för nätverket **neka** blockerar alla åtkomst till data, såvida inte specifika Nätverksregler *beviljar* åtkomst tillämpas också.  Glöm inte att bevilja åtkomst till några tillåtna nätverk med hjälp av regler innan du ändrar Standardregeln för att neka åtkomst.
>

#### <a name="azure-portal"></a>Azure Portal
1. Navigera till det lagringskonto som du vill skydda.  

2. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.
3. Om du vill neka åtkomst som standard välja att tillåta åtkomst från valda-nätverk.  Välja att tillåta åtkomst från ”alla nätverk” för att tillåta trafik från alla nätverk.
4. Klicka på *spara* att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell
1. Installera senast [Azure PowerShell](/powershell/azure/install-azurerm-ps) och [inloggning](/powershell/azure/authenticate-azureps).

2. Visa status för Standardregeln för storage-kontot.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Ange Standardregeln för att neka åtkomst som standard.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Ange Standardregeln som tillåter åtkomst till nätverket som standard.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Installera Azure CLI](/cli/azure/install-azure-cli) och [inloggning](/cli/azure/authenticate-azure-cli).
2. Visa status för Standardregeln för storage-kontot.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Ange Standardregeln för att neka åtkomst som standard.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Ange Standardregeln som tillåter åtkomst till nätverket som standard.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Bevilja åtkomst från ett virtuellt nätverk
Storage-konton kan konfigureras för att tillåta åtkomst från specifika Azure-nätverk. 

Genom att aktivera en [tjänstslutpunkt](/azure/virtual-network/virtual-network-service-endpoints-overview) för Azure Storage i det virtuella nätverket, trafik säkerställs en optimal väg till Azure Storage-tjänst. Identiteten för det virtuella nätverket och undernätet överförs även med varje begäran.  Administratörer kan därefter konfigurera Nätverksregler för lagringskontot som tillåter begäranden tas emot från specifika undernät i det virtuella nätverket.  Klienter som beviljas åtkomst via dessa Nätverksregler måste fortsätta att uppfylla behörighetskraven för Storage-konto för att komma åt data.

Varje lagringskonto har stöd för upp till 100 virtuella Nätverksregler som kan kombineras med [IP Nätverksregler](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Tillgängliga regioner för virtuella nätverk
I allmänhet fungerar tjänstslutpunkter mellan virtuella nätverk och service-instanser i samma Azure-region.  När tjänstslutpunkter används med Azure Storage måste det här omfånget utökas för att inkludera den [parad region](/azure/best-practices-availability-paired-regions).  På så sätt kan affärskontinuitet under en regional redundans samt sömlös åtkomst till skrivskyddad geo-redundant lagring (RA-GRS)-instanser.  Regler för att bevilja åtkomst från ett virtuellt nätverk till ett lagringskonto kan du även tilldela åtkomst till valfri RA-GRS-instans.

När du planerar för haveriberedskap under ett regionalt strömavbrott, ska du etablera de virtuella nätverken i den parade regionen i förväg. Måste vara aktiverat Tjänsteslutpunkter för Azure Storage och Nätverksregler bevilja åtkomst från dessa alternativ virtuella nätverk som ska användas för geo-redundant storage-konton.

> [!NOTE]
> Tjänstslutpunkter gäller inte för trafik utanför det virtuella nätverket och avsedda regionparet.  Nätverksregler som beviljar åtkomst från virtuella nätverk till Storage-konton kan endast användas för virtuella nätverk i den primära regionen av ett lagringskonto eller i den avsedda parade regionen.
>

### <a name="required-permissions"></a>Nödvändiga behörigheter
För att kunna tillämpa en regel för virtuella nätverk till ett lagringskonto, måste användaren ha behörighet att *delta Service till ett undernät* för undernäten läggas till.  Den här behörigheten ingår i den *Lagringskontodeltagare* inbyggd roll och du kan lägga till anpassade rolldefinitioner.

Storage-konto och de virtuella nätverken beviljas åtkomst **kan** finnas i olika prenumerationer, men dessa prenumerationer måste ingå i samma Azure Active Directory-klient.

### <a name="managing-virtual-network-rules"></a>Hantera virtuella Nätverksregler
Virtuella Nätverksregler för lagringskonton kan hanteras via Azure portal, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Navigera till det lagringskonto som du vill skydda.  
2. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.
3. Se till att du har valt att tillåta åtkomst från valda-nätverk.
4. Om du vill bevilja åtkomst till ett virtuellt nätverk med en ny regel, under ”virtuella nätverk” klickar du på ”Lägg till befintliga” för att välja ett befintligt virtuellt nätverk och undernät, klicka sedan på *Lägg till*.  Om du vill skapa ett nytt virtuellt nätverk och ge det åtkomst, klickar du på *Lägg till ny*, ange informationen som behövs för att skapa det nya virtuella nätverket och klickar sedan på *skapa*.

> [!NOTE]
> Om en tjänstslutpunkt för Azure Storage inte har konfigurerats tidigare för det valda virtuella nätverk och undernät, kan den konfigureras som en del av den här åtgärden.
>

5. Klicka på ”...” för att öppna snabbmenyn för det virtuella nätverket eller undernätet för att ta bort en regel för virtuella nätverk eller undernät, och klicka på ”Ta bort”.
6. Klicka på *spara* att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell
1. Installera senast [Azure PowerShell](/powershell/azure/install-azurerm-ps) och [inloggning](/powershell/azure/authenticate-azureps).
2. Lista över virtuella Nätverksregler
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Aktivera tjänstslutpunkt för Azure Storage på ett befintligt virtuellt nätverk och undernät
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Lägga till en regel för ett virtuellt nätverk och undernät.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Ta bort en regel för ett virtuellt nätverk och undernät.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) neka, eller Nätverksregler har ingen effekt.
>

#### <a name="cliv2"></a>CLIv2
1. [Installera Azure CLI](/cli/azure/install-azure-cli) och [inloggning](/cli/azure/authenticate-azure-cli).
2. Lista över virtuella Nätverksregler
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Aktivera tjänstslutpunkt för Azure Storage på ett befintligt virtuellt nätverk och undernät
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Lägga till en regel för ett virtuellt nätverk och undernät.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Ta bort en regel för ett virtuellt nätverk och undernät. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) neka, eller Nätverksregler har ingen effekt.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Bevilja åtkomst från ett internet IP-intervall
Storage-konton kan konfigureras för att tillåta åtkomst från internet för specifika offentliga IP-adressintervall.  Den här konfigurationen gör det möjligt för specifika Internetbaserade tjänster och lokala nätverk för att beviljas åtkomst medan Allmänt internet-trafik blockeras.

Tillåtna internet-adressintervall kan anges med hjälp av [CIDR-notation](https://tools.ietf.org/html/rfc4632) i formuläret *16.17.18.0/24* eller som enskilda IP adresser som *16.17.18.19* .

> [!NOTE]
> Små adressintervall med hjälp av ”/ 31” eller ”/ 32” prefix storlekar inte stöds.  Dessa områden ska konfigureras med regler för enskilda IP-adress.
>

IP-Nätverksregler är bara tillåtna för **offentliga internet** IP-adresser.  IP-adressintervall som är reserverade för privata nätverk (enligt definitionen i [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) är inte tillåtna i IP-regler.  Privata nätverk innehåller adresser som börjar med *10.\** , *172.16.\**   -  *172.31.\**, och *192.168.\** .

Observera att IP-Nätverksregler har ingen effekt på förfrågningar som kommer från samma Azure-region som lagringskontot.  Använd virtual network-regler för att tillåta begäranden för samma region.

Endast IPV4-adresser stöds just nu.

Varje lagringskonto har stöd för upp till 100 IP-nätverk (MPR) som kan kombineras med [virtuella Nätverksregler](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk
För att bevilja åtkomst från ditt lokala nätverk till ditt lagringskonto med en regel för IP-nätverk, måste du identifiera den internetuppkopplade IP-adresser som används av nätverket.  Kontakta nätverksadministratören om du behöver hjälp.

Om nätverket är anslutet till en Azure-nätverk med hjälp av [ExpressRoute](/azure/expressroute/expressroute-introduction), varje krets har konfigurerats med två offentliga IP-adresser i Microsoft Edge som används för att ansluta till Microsoft Services som Azure Storage med [Azures offentliga Peering](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Om du vill tillåta kommunikation från din krets till Azure Storage, måste du skapa IP-Nätverksregler för offentliga IP-adresserna för dina kretsar.  För att kunna hitta ExpressRoute-kretsen offentliga IP-adresser, [öppnar ett supportärende hos ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure portal.


### <a name="managing-ip-network-rules"></a>Hantera regler för IP-nätverk
IP-Nätverksregler för lagringskonton kan hanteras via Azure-portalen, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Navigera till det lagringskonto som du vill skydda.  
2. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.
3. Se till att du har valt att tillåta åtkomst från valda-nätverk.
4. Att bevilja åtkomst till ett internet IP-adressintervall, ange IP-adressen eller adressintervallet (i CIDR-format) under brandvägg adressintervall.
5. Klicka på Papperskorgen bredvid nätverksregel för att ta bort en regel för IP.
6. Klicka på *spara* att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell
1. Installera senast [Azure PowerShell](/powershell/azure/install-azurerm-ps) och [inloggning](/powershell/azure/authenticate-azureps).
2. Regler för lista över IP-nätverk.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Lägg till en regel för en enskild IP-adress.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Lägg till en regel för ett IP-adressintervall.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Ta bort en regel för en enskild IP-adress. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Ta bort en regel för ett IP-adressintervall.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) neka, eller Nätverksregler har ingen effekt.
>

#### <a name="cliv2"></a>CLIv2
1. [Installera Azure CLI](/cli/azure/install-azure-cli) och [inloggning](/cli/azure/authenticate-azure-cli).
2. Lista över IP-Nätverksregler
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Lägg till en regel för en enskild IP-adress.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Lägg till en regel för ett IP-adressintervall.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Ta bort en regel för en enskild IP-adress.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Ta bort en regel för ett IP-adressintervall.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) neka, eller Nätverksregler har ingen effekt.
>

## <a name="exceptions"></a>Undantag
Regler kan aktivera en säker konfiguration för de flesta fall, finns men det tillfällen där undantag måste beviljas för att aktivera fullständiga funktioner.  Storage-konton kan konfigureras med undantag för betrodda Microsoft-tjänster och för åtkomst till Storage analytics-data.

### <a name="trusted-microsoft-services"></a>Betrodda Microsoft-tjänster
Vissa Microsoft-tjänster som interagerar med Storage-konton fungerar från nätverk som inte kan beviljas åtkomst via Nätverksregler. 

Du kan tillåta uppsättningen med betrodda Microsoft-tjänster för att kringgå reglerna nätverk för att tillåta den här typen av tjänsten för att fungera som avsett. De här tjänsterna använder sedan stark autentisering för att få åtkomst till lagringskontot.

När undantaget ”betrodda Microsoft-tjänster” är aktiverad, beviljas åtkomst till lagringskontot med följande tjänster (när registrerad för din prenumeration):

|Tjänst|Providernamn för resursen|Syfte|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Utför säkerhetskopior och återställningar av ohanterade diskar i virtuella IAAS-datorer. (krävs inte för hanterade diskar.) [Läs mer](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup).|
|Azure DevTest Labs|Microsoft.DevTestLab|Anpassad avbildning skapande och artefakt installation.  [Läs mer](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Aktivera publicering av Blob Storage-händelser.  [Läs mer](https://docs.microsoft.com/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Arkivera data med Event Hubs Capture.  [Läs mer](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview).|
|Azure-nätverk|Microsoft.Networking|Store och analysera loggar med webbtrafik.  [Läs mer](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
|Azure Monitor|Microsoft.Insights| Tillåter skrivning av övervakning av data till ett konto för säker storaage [mer](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security#monitoring-and-secured-Azure-storage-and-networks).|
|


### <a name="storage-analytics-data-access"></a>Dataåtkomst för Storage analytics
I vissa fall kan krävs åtkomst till Läs diagnostikloggar och mått från utanför nätverksgränsen för.  Undantag från reglerna för nätverk kan beviljas för att tillåta läsåtkomst till Storage konto loggfiler, mått tabeller eller båda. [Läs mer om hur du arbetar med storage analytics.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Hantering av undantag
Nätverket undantag kan hanteras via Azure-portalen, PowerShell eller Azure CLI v2.

#### <a name="azure-portal"></a>Azure Portal
1. Navigera till det lagringskonto som du vill skydda.  
2. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.
3. Se till att du har valt att tillåta åtkomst från valda-nätverk.
4. Välj de undantag som du vill tilldela under undantag.
5. Klicka på *spara* att tillämpa ändringarna.

#### <a name="powershell"></a>PowerShell
1. Installera senast [Azure PowerShell](/powershell/azure/install-azurerm-ps) och [inloggning](/powershell/azure/authenticate-azureps).
2. Visa undantag för Nätverksregler för storage-konto.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Konfigurera undantag för Nätverksregler för storage-konto.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Ta bort undantagen Nätverksregler för storage-konto.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) neka, eller ta bort undantag har ingen effekt.
>

#### <a name="cliv2"></a>CLIv2
1. [Installera Azure CLI](/cli/azure/install-azure-cli) och [inloggning](/cli/azure/authenticate-azure-cli).
2. Visa undantag för Nätverksregler för storage-konto.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. Konfigurera undantag för Nätverksregler för storage-konto.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Ta bort undantagen Nätverksregler för storage-konto.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) neka, eller ta bort undantag har ingen effekt.
>

## <a name="next-steps"></a>Nästa steg
Läs mer om tjänstslutpunkter för Azure-nätverk i [tjänstslutpunkter](/azure/virtual-network/virtual-network-service-endpoints-overview).

Fördjupa dig i Azure Storage-säkerhet i [säkerhetsguiden för Azure Storage](storage-security-guide.md).

---
title: "Konfigurera brandväggar för Azure-lagring och virtuella nätverk (förhandsversion) | Microsoft Docs"
description: "Konfigurera överlappande nätverkssäkerhet för ditt lagringskonto."
services: storage
documentationcenter: 
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/25/2017
ms.author: cbrooks
ms.openlocfilehash: 9b00faa06684be353cfcf5f67f182a56511210c5
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks-preview"></a>Konfigurera brandväggar för Azure-lagring och virtuella nätverk (förhandsgranskning)
Azure Storage tillhandahåller en skiktbaserad säkerhetsmodell som gör att du kan skydda dina lagringskonton för en specifik uppsättning tillåtna nätverk.  Om Nätverksregler är konfigurerade bara program från tillåtna nätverk kan komma åt ett lagringskonto.  Vid anrop från ett nätverk med tillåtna fortfarande program kräver att rätt behörighet (en giltig snabbtangent eller SAS-token) att komma åt lagringskontot.

## <a name="preview-availability-and-support"></a>Förhandsgranska tillgänglighet och support
Brandväggar för lagring och virtuella nätverk finns i förhandsgranskningen.  Den här funktionen är tillgänglig för nya eller befintliga lagringskonton i alla regioner för offentliga Azure-molnet.

> [!NOTE]
> Produktionsarbetsbelastningar stöds inte under förhandsgranskningen.
>

## <a name="scenarios"></a>Scenarier
Storage-konton kan konfigureras för att neka åtkomst till trafik från alla nätverk (inklusive internet-trafik) som standard.  Kan bevilja åtkomst till trafik från specifika Azure-virtuella nätverk, så att du kan skapa en säker nätverksgräns för dina program.  Kan också att bevilja åtkomst till offentliga internet IP-adressintervall, aktivera anslutningar från specifika klienter på internet eller lokalt.

Nätverksregler tillämpas på alla nätverksprotokoll till Azure-lagring, inklusive REST och SMB.  Åtkomst till dina data från verktyg som Azure-portalen Lagringsutforskaren, och AZCopy kräver explicit Nätverksregler som beviljar åtkomst när Nätverksregler är i kraft.

Nätverksregler kan tillämpas på befintliga lagringskonton och kan tillämpas under genereringen av nya Storage-konton.

När Nätverksregler tillämpas tillämpas de för alla begäranden.  SAS-token som ger åtkomst till en specifik IP-adress tjänst ut till **gränsen** åtkomst innehavarens token, men de stöder inte bevilja nya åtkomst utanför konfigurerade regler. 

Disk för virtuell dator-trafik (inklusive montera och demontera åtgärder och disk-i/o) **inte** påverkas av Nätverksregler.  REST-åtkomst till sidblobbar skyddas av Nätverksregler.

> [!NOTE]
> Säkerhetskopiering och återställning av virtuella datorer med hjälp av ohanterade diskar i storage-konton med reglerna för nätverk stöds inte för närvarande.  Mer information finns i [begränsningar när du säkerhetskopierar och återställer en virtuell dator](/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm)
>

Klassiska lagringskonton **inte** stöd för brandväggar och virtuella nätverk.

## <a name="change-the-default-network-access-rule"></a>Ändra standardåtkomstregel för nätverk
Som standard godkänna lagringskonton anslutningar från klienter på ett nätverk.  Du måste ändra standardåtgärden som begränsar åtkomsten till valda nätverken.

> [!WARNING]
> Gör ändringar i Nätverksregler kan påverka dina program möjlighet att ansluta till Azure Storage.  Ställa in Standardregeln för nätverk till **neka** blockerar all åtkomst till data om specifika regler *bevilja* åtkomst används också.  Glöm inte att bevilja åtkomst till några tillåtna nätverk med hjälp av Nätverksregler innan du ändrar Standardregeln för att neka åtkomst.
>

#### <a name="azure-portal"></a>Azure Portal
1. Navigera till storage-konto som du vill skydda.  
> [!NOTE]
> Kontrollera att ditt lagringskonto är i något av regionerna som stöds för förhandsversion.
>

2. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.
3. Välja att tillåta åtkomst från valda-nätverk för att neka åtkomst som standard.  Välja att tillåta åtkomst från ”alla nätverk” för att tillåta trafik från alla nätverk.
4. Klicka på *spara* tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell
1. Installera senaste [Azure PowerShell](/powershell/azure/install-azurerm-ps) och [inloggning](/powershell/azure/authenticate-azureps).

2. Visa status för Standardregeln för lagringskontot.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Ange Standardregeln för att neka åtkomst till nätverket som standard.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Ange Standardregeln som tillåter åtkomst till nätverket som standard.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Installera Azure CLI 2.0](/cli/azure/install-azure-cli) och [inloggning](/cli/azure/authenticate-azure-cli).
2. Visa status för Standardregeln för lagringskontot.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Ange Standardregeln för att neka åtkomst till nätverket som standard.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Ange Standardregeln som tillåter åtkomst till nätverket som standard.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Bevilja åtkomst från ett virtuellt nätverk
Storage-konton kan konfigureras för att tillåta åtkomst från specifika virtuella Azure-nätverk. 

Genom att aktivera en [tjänstslutpunkten](/azure/virtual-network/virtual-network-service-endpoints-overview) för Azure Storage i det virtuella nätverket trafik säkerställs en optimal väg till Azure Storage-tjänsten. Identiteten för det virtuella nätverket och undernätet skickas också med varje begäran.  Administratörer kan därefter konfigurera Nätverksregler för lagringskontot som tillåter begäranden tas emot från specifika undernät i det virtuella nätverket.  Klienter som beviljas åtkomst via reglerna nätverk måste fortsätta att uppfylla krav auktorisering på lagringskontot för att komma åt data.

Varje lagringskonto har stöd för upp till 100 virtuella nätverk (MPR) som kan kombineras med [IP Nätverksregler](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Tillgängliga regioner för virtuellt nätverk
I allmänhet fungerar Tjänsteslutpunkter mellan virtuella nätverk och instanser av tjänsten i samma Azure-region.  När Tjänsteslutpunkter används med Azure Storage, detta scope utökas för att inkludera den [parad region](/azure/best-practices-availability-paired-regions).  På så sätt kan kontinuitet under en regional växling vid fel, samt seemless åtkomst till skrivskyddad geo-reduntant lagring (RA-GRS) instanser.  Nätverksregler som ger åtkomst från ett virtuellt nätverk till ett lagringskonto kan också ge åtkomst till alla RA-GRS-instans.

När du planerar för katastrofåterställning under ett regionalt strömavbrott, ska du etablera virtuella nätverk i parad region i förväg. Slutpunkter för Azure Storage ska aktiveras och Nätverksregler som beviljar åtkomst från dessa alternativa virtuella nätverk som ska användas på dina geo-redundant lagring.

> [!NOTE]
> Slutpunkter gäller inte för trafik utanför det virtuella nätverket och avsedda region-par.  Nätverksregler som beviljar åtkomst från virtuella nätverk till Storage-konton kan endast användas för virtuella nätverk i den primära regionen för ett lagringskonto eller avsedda parad region.
>

### <a name="required-permissions"></a>Nödvändiga behörigheter
För att kunna tillämpa en regel för virtuellt nätverk till ett lagringskonto, måste användaren ha behörighet att *delta Service till ett undernät* för de undernätverk som läggs till.  Den här behörigheten ingår i den *Storage-konto deltagare* inbyggd roll och du kan lägga till anpassade rolldefinitioner.

Storage-konto och de virtuella nätverken åtkomst **kan** vara för olika prenumerationer, men dessa prenumerationer måste vara en del av samma Azure Active Directory-klienten.

### <a name="managing-virtual-network-rules"></a>Hantera regler för virtuellt nätverk
Virtuella Nätverksregler för storage-konton kan hanteras via Azure portal, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Navigera till storage-konto som du vill skydda.  
2. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.
3. Se till att du har valt att tillåta åtkomst från valda-nätverk.
4. Klicka på ”Lägg till befintliga” om du vill bevilja åtkomst till ett virtuellt nätverk med en ny regel i nätverket, under ”virtuella nätverk” för att välja ett befintligt virtuellt nätverk och undernät, klicka på *Lägg till*.  Om du vill skapa ett nytt virtuellt nätverk och ge det åtkomst, klickar du på *Lägg till ny*, anger du informationen som behövs för att skapa ett nytt virtuellt nätverk och klicka sedan på *skapa*.

> [!NOTE]
> Om en tjänstslutpunkt för Azure Storage inte har konfigurerats tidigare för den valda virtuella nätverk och undernät, kan den konfigureras som en del av den här åtgärden.
>

5. Klicka på ”...” för att öppna snabbmenyn för det virtuella nätverket eller undernätet för att ta bort en regel för virtuella nätverk eller undernät, och klicka på ”Ta bort”.
6. Klicka på *spara* tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell
1. Installera senaste [Azure PowerShell](/powershell/azure/install-azurerm-ps) och [inloggning](/powershell/azure/authenticate-azureps).
2. Virtuellt nätverk regler
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Aktivera tjänstslutpunkten för Azure Storage på ett befintligt virtuellt nätverk och undernät
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
1. [Installera Azure CLI 2.0](/cli/azure/install-azure-cli) och [inloggning](/cli/azure/authenticate-azure-cli).
2. Virtuellt nätverk regler
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Aktivera tjänstslutpunkten för Azure Storage på ett befintligt virtuellt nätverk och undernät
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

## <a name="grant-access-from-an-internet-ip-range"></a>Bevilja åtkomst från en internet IP-intervall
Storage-konton kan konfigureras för att tillåta åtkomst från specifika offentliga internet IP-adressintervall.  Den här konfigurationen kan specifika Internetbaserade tjänster och lokala nätverk för att beviljas åtkomst medan allmänna internet-trafiken blockeras.

Tillåtna internet-adressintervall kan anges med hjälp av [CIDR-notering](https://tools.ietf.org/html/rfc4632) i formuläret *16.17.18.0/24* eller som enskilda IP adresser som *16.17.18.19* .

> [!NOTE]
> Liten adressintervall med ”/ 31” eller ”/ 32” prefix storlekar inte stöds.  Dessa områden ska konfigureras med regler för enskilda IP-adress.
>

Regler för IP-nätverk är bara tillåtna för **offentliga internet** IP-adresser.  IP-adressintervall som reserverats för privata nätverk (som definieras i RFC 1918) tillåts inte i IP-regler.  Privata nätverk innehåller adresser som börjar med *10.\** , *172.16.\** , och *192.168.\** .

Endast IPV4-adresser stöds just nu.

Varje lagringskonto har stöd för upp till 100 IP-Nätverksregler som kan kombineras med [regler för virtuellt nätverk](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurera åtkomst från lokala nätverk
För att bevilja åtkomst från ditt lokala nätverk till ditt lagringskonto med en regel för IP-nätverk, måste du identifiera internetuppkopplad IP-adresser som används i nätverket.  Kontakta nätverksadministratören för hjälp.

Om nätverket är anslutet till en Azure-nätverk med hjälp av [ExpressRoute](/azure/expressroute/expressroute-introduction), varje kretsen har konfigurerats med två offentliga IP-adresser i Microsoft Edge som används för att ansluta till Microsoft Services som Azure Storage med hjälp av [Offentlig azure-Peering](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Om du vill tillåta kommunikation från kretsen till Azure Storage, måste du skapa IP-Nätverksregler för offentliga IP-adresserna för dina kretsar.  För att hitta ExpressRoute-krets offentliga IP-adresser, [öppna ett supportärende med ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via Azure portal.


### <a name="managing-ip-network-rules"></a>Hantera regler för IP-nätverk
IP-Nätverksregler för storage-konton kan hanteras via Azure-portalen, PowerShell eller CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Navigera till storage-konto som du vill skydda.  
2. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.
3. Se till att du har valt att tillåta åtkomst från valda-nätverk.
4. Att bevilja åtkomst till ett internet IP-intervall, ange IP-adressen eller adressintervallet (i CIDR-format) under brandvägg adressintervall.
5. Om du vill ta bort en regel för IP-nätverket, klicka på ”...” för att öppna snabbmenyn för regeln och klicka på ”Ta bort”.
6. Klicka på *spara* tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell
1. Installera senaste [Azure PowerShell](/powershell/azure/install-azurerm-ps) och [inloggning](/powershell/azure/authenticate-azureps).
2. Visa en lista med regler för IP-nätverk.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Lägg till en regel för en enstaka IP-adress.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Lägg till en regel för ett IP-adressintervall.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Ta bort en regel för en enstaka IP-adress. 
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
1. [Installera Azure CLI 2.0](/cli/azure/install-azure-cli) och [inloggning](/cli/azure/authenticate-azure-cli).
2. IP-Nätverksregler
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Lägg till en regel för en enstaka IP-adress.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Lägg till en regel för ett IP-adressintervall.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Ta bort en regel för en enstaka IP-adress.  
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
Nätverksregler kan aktivera en säker konfiguration för de flesta scenarier, finns men det tillfällen där undantag måste beviljas för att möjliggöra fullständig funktionalitet.  Storage-konton kan konfigureras med undantag för betrodda Microsoft-tjänster och för åtkomst till Storage analytics-data.

### <a name="trusted-microsoft-services"></a>Betrodda Microsoft-tjänster
Vissa Microsoft-tjänster som samverkar med Storage-konton fungerar mellan nätverk som inte kan beviljas åtkomst via Nätverksregler. 

Du kan tillåta uppsättning betrodda Microsoft services för att kringgå reglerna nätverket så att den här typen av tjänsten för att fungera som avsett. Tjänsterna använder sedan stark autentisering för att komma åt lagringskontot.

När ”betrodda Microsoft Services” undantaget aktiveras har följande tjänster (när registrerad i din prenumeration) beviljats åtkomst till lagringskontot:

|Tjänst|Providernamn för resurs|Syfte|
|:------|:---------------------|:------|
|Azure DevTest Labs|Microsoft.DevTestLab|Anpassad bild skapas och artefakt installation.  [Läs mer](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Aktivera publicering för Blob Storage-händelse.  [Läs mer](https://docs.microsoft.com/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Arkivering av data med Event Hubs avbilda.  [Lär dig mer](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview).|
|Azure HDInsight|Microsoft.HDInsight|Klusteretablering och installation.  [Läs mer](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage).|
|Azure-nätverk|Microsoft.Networking|Lagra och analysera loggar med webbtrafik.  [Läs mer](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
||||

### <a name="storage-analytics-data-access"></a>Storage analytics dataåtkomst
I vissa fall krävs behörighet att läsa diagnostikloggar och mått som från utanför gränsen för nätverket.  Undantag från reglerna i nätverket kan beviljas för att tillåta läsåtkomst till lagring konto loggfiler, mätvärden tabeller, eller båda. [Mer information om hur du arbetar med storage analytics.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Hantering av undantag
Nätverket undantag kan hanteras via Azure-portalen, PowerShell eller Azure CLI v2.

#### <a name="azure-portal"></a>Azure Portal
1. Navigera till storage-konto som du vill skydda.  
2. Klicka på inställningsmenyn kallas **brandväggar och virtuella nätverk**.
3. Se till att du har valt att tillåta åtkomst från valda-nätverk.
4. Välj de undantag som du vill bevilja under undantag.
5. Klicka på *spara* tillämpa dina ändringar.

#### <a name="powershell"></a>PowerShell
1. Installera senaste [Azure PowerShell](/powershell/azure/install-azurerm-ps) och [inloggning](/powershell/azure/authenticate-azureps).
2. Visa undantag för Nätverksregler för storage-konto.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Konfigurera undantag för Nätverksregler för storage-konto.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Ta bort undantag till Nätverksregler för storage-konto.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) neka, eller ta bort undantag har ingen effekt.
>

#### <a name="cliv2"></a>CLIv2
1. [Installera Azure CLI 2.0](/cli/azure/install-azure-cli) och [inloggning](/cli/azure/authenticate-azure-cli).
2. Visa undantag för Nätverksregler för storage-konto.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. Konfigurera undantag för Nätverksregler för storage-konto.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Ta bort undantag till Nätverksregler för storage-konto.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Se till att [ange Standardregeln](#change-the-default-network-access-rule) neka, eller ta bort undantag har ingen effekt.
>

## <a name="next-steps"></a>Nästa steg
Lär dig mer om Azure Service nätverksslutpunkter i [Tjänsteslutpunkter](/azure/virtual-network/virtual-network-service-endpoints-overview).

Gräva djupare i Azure Storage-säkerhet i [säkerhetsguiden för Azure Storage](storage-security-guide.md).

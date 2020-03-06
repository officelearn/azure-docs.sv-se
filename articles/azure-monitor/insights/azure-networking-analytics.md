---
title: Azure Networking Analytics-lösning i Azure Monitor | Microsoft Docs
description: Du kan använda Azure Networking Analytics-lösningen i Azure Monitor för att granska Azure Network Security Group-loggar och Azure Application Gateway-loggar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 1045f86db5e1a9ed1979a266937974045e401e27
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360890"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Lösningar för övervakning av Azure-nätverk i Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor erbjuder följande lösningar för att övervaka dina nätverk:
* Övervakare av nätverksprestanda (NPM) till
    * Övervaka nätverkets hälso tillstånd
* Azure Application Gateway-analys för att granska
    * Azure Application Gateway-loggar
    * Azure Application Gateway-mått
* Lösningar för att övervaka och granska nätverks aktivitet i moln nätverket
    * [Trafikanalys](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Azure-nätverkssäkerhetsgruppanalys

## <a name="network-performance-monitor-npm"></a>Övervakare av nätverksprestanda (NPM)

Den [övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview) hanterings lösningen är en lösning för nätverks övervakning som övervakar hälsa, tillgänglighet och nåbarhet för nätverk.  Den används för att övervaka anslutningen mellan:

* Offentligt moln och lokalt
* Data Center och användar platser (avdelnings kontor)
* Undernät är värdar för olika nivåer av ett program med flera nivåer.

Mer information finns i [övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway och nätverks säkerhets grupp analys
Använda lösningarna:
1. Lägg till hanterings lösningen i Azure Monitor och
2. Aktivera diagnostik för att dirigera diagnostiken till en Log Analytics-arbetsyta i Azure Monitor. Du behöver inte skriva loggarna till Azure Blob Storage.

Du kan aktivera diagnostik och motsvarande lösning för antingen en eller båda av Application Gateway-och nätverks säkerhets grupper.

Om du inte aktiverar diagnostisk resurs loggning för en viss resurs typ, men installerar lösningen, är instrument panels bladet för den resursen tomma och visar ett fel meddelande.

> [!NOTE]
> I januari 2017 ändrades det stödda sättet att skicka loggar från programgatewayer och nätverks säkerhets grupper till en Log Analytics-arbetsyta. Om du ser lösningen för **Azure Networking Analytics (inaktuell)** kan du läsa mer i [Migrera från den gamla lösningen för nätverks analys](#migrating-from-the-old-networking-analytics-solution) för steg som du måste följa.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Granska informationen om Azure Networking-datainsamling
Hanterings lösningarna Azure Application Gateway Analytics och nätverks säkerhets grupp analys samlar in diagnostikloggar direkt från Azure Application gatewayer och nätverks säkerhets grupper. Du behöver inte skriva loggarna till Azure Blob Storage och ingen agent krävs för data insamling.

I följande tabell visas metoder för data insamling och annan information om hur data samlas in för Azure Application Gateway Analytics och nätverks säkerhets grupps analys.

| Plattform | Direkt agent | System Center Operations Manager agent | Azure | Operations Manager krävs. | Operations Manager agent data som skickas via hanterings gruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |När du loggar |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Azure Application Gateway Analytics-lösning i Azure Monitor

![Azure Application Gateway-analys symbol](media/azure-networking-analytics/azure-analytics-symbol.png)

Följande loggar stöds för Application Gateway:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Följande mått stöds för Application Gateway: igen


* 5 minuters data flöde

### <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure Application Gateway Analytics-lösningen:

1. Aktivera lösningen Azure Application Gateway Analytics från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](../../azure-monitor/insights/solutions.md).
2. Aktivera diagnostikloggning för de [programgatewayer](../../application-gateway/application-gateway-diagnostics.md) som du vill övervaka.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Aktivera Azure Application Gateway-diagnostik i portalen

1. I Azure Portal navigerar du till den Application Gateway resurs som ska övervakas.
2. Välj *diagnostikloggar* för att öppna följande sida.

   ![bild av Azure Application Gateway-resurs](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Klicka på *Aktivera diagnostik* för att öppna följande sida.

   ![bild av Azure Application Gateway-resurs](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Aktivera diagnostik genom att klicka *på* under *status*.
5. Klicka i kryss rutan för *att skicka till Log Analytics*.
6. Välj en befintlig Log Analytics arbets yta eller skapa en arbets yta.
7. Klicka på kryss rutan under **loggen** för varje logg typ som ska samlas in.
8. Klicka på *Spara* för att aktivera loggning av diagnostik till Azure Monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Aktivera Azure-Nätverksdiagnostik med PowerShell

Följande PowerShell-skript innehåller ett exempel på hur du aktiverar resurs loggning för Application Gateway.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Använd Azure Application Gateway Analytics
![bild av Azure Application Gateway Analytics-panel](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

När du klickar på panelen **Azure Application Gateway Analytics** i översikten kan du Visa sammanfattningar av loggarna och sedan gå vidare till information för följande kategorier:

* Application Gateway åtkomst loggar
  * Klient-och Server fel för Application Gateway åtkomst loggar
  * Begär Anden per timme för varje Application Gateway
  * Misslyckade förfrågningar per timme för varje Application Gateway
  * Fel per användar agent för Application Gateway
* Application Gateway prestanda
  * Värd hälsa för Application Gateway
  * Högsta och 95 percentil för Application Gateway misslyckade förfrågningar

![bild av Azure Application Gateway Analytics-instrumentpanelen](media/azure-networking-analytics/log-analytics-appgateway01.png)

![bild av Azure Application Gateway Analytics-instrumentpanelen](media/azure-networking-analytics/log-analytics-appgateway02.png)

På instrument panelen för **Azure Application Gateway Analytics** granskar du sammanfattnings informationen på ett av bladet och klickar sedan på en för att visa detaljerad information på sidan för loggs ökning.

På någon av logg Sök sidorna kan du Visa resultat efter tid, detaljerade resultat och logg Sök historik. Du kan också filtrera efter FACET för att begränsa resultaten.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Azure Network Security Group Analytics-lösning i Azure Monitor

![Azure-nätverkssäkerhetsgruppanalys symbol](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Nätverks säkerhets gruppens analys lösning flyttas till community-support eftersom dess funktioner har ersatts av [trafikanalys](../../network-watcher/traffic-analytics.md).
> - Lösningen är nu tillgänglig i [Azures snabb starts mallar](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) och kommer snart inte längre vara tillgänglig på Azure Marketplace.
> - För befintliga kunder som redan har lagt till lösningen i sin arbets yta kommer den att fortsätta fungera utan ändringar.
> - Microsoft fortsätter att stödja sändning av NSG-resurs loggar till arbets ytan med hjälp av diagnostikinställningar.

Följande loggar stöds för nätverks säkerhets grupper:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure Networking Analytics-lösningen:

1. Aktivera Azure Network Security Group Analytics-lösningen från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i [Lägg till Azure Monitor lösningar från Lösningsgalleriet](../../azure-monitor/insights/solutions.md).
2. Aktivera diagnostikloggning för de [nätverks säkerhets grupps](../../virtual-network/virtual-network-nsg-manage-log.md) resurser som du vill övervaka.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Aktivera diagnostik för Azure Network Security-gruppen i portalen

1. I Azure Portal navigerar du till den nätverks säkerhets grupp resurs som ska övervakas
2. Välj *diagnostikloggar* för att öppna följande sida

   ![bild av Azure nätverks säkerhets grupp resurs](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Klicka på *Aktivera diagnostik* för att öppna följande sida

   ![bild av Azure nätverks säkerhets grupp resurs](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Aktivera diagnostik genom att klicka *på* under *status*
5. Klicka i kryss rutan för *att skicka till Log Analytics*
6. Välj en befintlig Log Analytics arbets yta eller skapa en arbets yta
7. Klicka på kryss rutan under **loggen** för varje logg typ som ska samlas in
8. Klicka på *Spara* för att aktivera loggning av diagnostik till Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Aktivera Azure-Nätverksdiagnostik med PowerShell

Följande PowerShell-skript innehåller ett exempel på hur du aktiverar resurs loggning för nätverks säkerhets grupper
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Använd Azure Network Security Group Analytics
När du klickar på panelen för **Azure Network Security Group Analytics** i översikten kan du Visa sammanfattningar av loggarna och sedan gå vidare till information för följande kategorier:

* Blockerade flöden i nätverks säkerhets grupp
  * Regler för nätverks säkerhets grupper med blockerade flöden
  * MAC-adresser med blockerade flöden
* Tillåtna flöden för nätverks säkerhets grupp
  * Regler för nätverks säkerhets grupper med tillåtna flöden
  * MAC-adresser med tillåtna flöden

![bild av Azure Network Security Group Analytics-instrumentpanelen](media/azure-networking-analytics/log-analytics-nsg01.png)

![bild av Azure Network Security Group Analytics-instrumentpanelen](media/azure-networking-analytics/log-analytics-nsg02.png)

På instrument panelen för **Azure Network Security Group Analytics** granskar du sammanfattnings informationen på ett av bladet och klickar sedan på en för att visa detaljerad information på sidan för loggs ökning.

På någon av logg Sök sidorna kan du Visa resultat efter tid, detaljerade resultat och logg Sök historik. Du kan också filtrera efter FACET för att begränsa resultaten.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrera från den gamla lösningen för nätverks analys
I januari 2017 ändrades det stödda sättet att skicka loggar från Azure Application gatewayer och Azure-nätverks säkerhets grupper till en Log Analytics arbets yta. Dessa ändringar ger följande fördelar:
+ Loggarna skrivs direkt till Azure Monitor utan att du behöver använda ett lagrings konto
+ Mindre latens från den tid då loggar genereras till dem som är tillgängliga i Azure Monitor
+ Färre konfigurations steg
+ Ett vanligt format för alla typer av Azure Diagnostics

Använda de uppdaterade lösningarna:

1. [Konfigurera diagnostik så att den skickas direkt till Azure Monitor från Azure Application-gatewayer](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfigurera diagnostik så att den skickas direkt till Azure Monitor från Azure nätverks säkerhets grupper](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Aktivera *Azure Application Gateway-analys* och *Azure-nätverkssäkerhetsgruppanalys* -lösningen med hjälp av processen som beskrivs i [lägg till Azure Monitor lösningar från Lösningsgalleriet](solutions.md)
3. Uppdatera eventuella sparade frågor, instrument paneler eller aviseringar för att använda den nya data typen
   + Skriv är till AzureDiagnostics. Du kan använda ResourceType för att filtrera till Azures nätverks loggar.

     | Istället för: | Använd |
     | --- | --- |
     | NetworkApplicationgateways &#124; där OperationName = = "ApplicationGatewayAccess" | AzureDiagnostics &#124; där resourcetype = = "APPLICATIONGATEWAYS" och OperationName = = "ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124; där OperationName = = "ApplicationGatewayPerformance" | AzureDiagnostics &#124; där resourcetype = = "APPLICATIONGATEWAYS" och OperationName = = "ApplicationGatewayPerformance" |
     | NetworkSecuritygroups | AzureDiagnostics &#124; where ResourceType=="NETWORKSECURITYGROUPS" |

   + För alla fält som har suffixet \_s, \_d eller \_g i namnet, ändrar du det första symbolen till gemener
   + För fält som har suffixet \_o i namn delas data upp i enskilda fält baserat på de kapslade fält namnen.
4. Ta bort *Azure Networking Analytics-lösningen (inaktuell)* .
   + Om du använder PowerShell använder du `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Data som samlas in innan ändringen visas inte i den nya lösningen. Du kan fortsätta att fråga efter dessa data med hjälp av de gamla typ-och fält namnen.

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Nästa steg
* Använd [logg frågor i Azure Monitor](../log-query/log-query-overview.md) om du vill visa detaljerade Azure Diagnostics-data.

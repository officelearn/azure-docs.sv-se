---
title: Azure Networking Analytics-lösning i Azure Monitor | Microsoft-dokument
description: Du kan använda Azure Networking Analytics-lösningen i Azure Monitor för att granska Azure-nätverkssäkerhetsgrupploggar och Azure Application Gateway-loggar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 1045f86db5e1a9ed1979a266937974045e401e27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275573"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Övervakningslösningar för Azure-nätverk i Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor erbjuder följande lösningar för övervakning av dina nätverk:
* Övervakare av nätverksprestanda (NPM) till
    * Övervaka nätverkets hälsa
* Azure Application Gateway-analys för granskning
    * Azure Application Gateway-loggar
    * Azure Application Gateway-mått
* Lösningar för att övervaka och granska nätverksaktivitet i ditt molnnätverk
    * [Trafikanalys](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Analys av Azure Network Security Group

## <a name="network-performance-monitor-npm"></a>Övervakare av nätverksprestanda (NPM)

Hanteringslösningen [för network performance monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) är en nätverksövervakningslösning som övervakar nätverkens hälsa, tillgänglighet och räckvidd.  Det används för att övervaka anslutningen mellan:

* Offentligt moln och lokalt
* Datacenter och användarplatser (filialkontor)
* Undernät som är värdar för olika nivåer i ett program med flera nivåer.

Mer information finns i [Övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway och nätverkssäkerhetsgruppanalys
Så här använder du lösningarna:
1. Lägg till hanteringslösningen i Azure Monitor och
2. Aktivera diagnostik för att dirigera diagnostiken till en Log Analytics-arbetsyta i Azure Monitor. Det är inte nödvändigt att skriva loggarna till Azure Blob-lagring.

Du kan aktivera diagnostik och motsvarande lösning för antingen en eller båda av säkerhetsgrupper för Programgateway och nätverk.

Om du inte aktiverar diagnostikresursloggning för en viss resurstyp, men installerar lösningen, är instrumentpanelsbladen för den resursen tomma och visar ett felmeddelande.

> [!NOTE]
> I januari 2017 ändrades sättet att skicka loggar från Programgateways och nätverkssäkerhetsgrupper till en Log Analytics-arbetsyta. Om du ser Azure **Networking Analytics-lösningen (föråldrad)** läser du [migrera från den gamla Networking Analytics-lösningen](#migrating-from-the-old-networking-analytics-solution) för steg som du behöver följa.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Granska information om datainsamling i Azure-nätverk
Azure Application Gateway-analys och hanteringslösningar för nätverkssäkerhetsgruppanalys samlar diagnostikloggar direkt från Azure Application Gateways och Network Security Groups. Det är inte nödvändigt att skriva loggarna till Azure Blob-lagring och ingen agent krävs för datainsamling.

I följande tabell visas datainsamlingsmetoder och annan information om hur data samlas in för Azure Application Gateway-analys och analytics för nätverkssäkerhetsgrupper.

| Plattform | Direkt agent | System Center Operations Manager-agent | Azure | Krävs Operations Manager? | Operations Manager-agentdata som skickas via hanteringsgrupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |när du loggas |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Analyslösning för Azure Application Gateway i Azure Monitor

![Azure Application Gateway Analytics-symbol](media/azure-networking-analytics/azure-analytics-symbol.png)

Följande loggar stöds för Programgateways:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* AnsökanGatewayFirewallLog

Följande mått stöds för Programgateways:igen


* 5 minuters dataflöde

### <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera analytics-lösningen för Azure Application Gateway:

1. Aktivera Azure Application Gateway-analyslösningen från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i [Lägg till Azure Monitor-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).
2. Aktivera diagnostikloggning för de [programgateways](../../application-gateway/application-gateway-diagnostics.md) som du vill övervaka.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Aktivera Diagnostik för Azure Application Gateway i portalen

1. I Azure-portalen navigerar du till application gateway-resursen för att övervaka.
2. Välj *Diagnostikloggar* för att öppna följande sida.

   ![avbildning av Azure Application Gateway-resurs](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Klicka *på Aktivera diagnostik* för att öppna följande sida.

   ![avbildning av Azure Application Gateway-resurs](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Om du vill aktivera diagnostik *klickar* du på under *Status*.
5. Klicka på kryssrutan för *Skicka till Logganalys*.
6. Välj en befintlig Log Analytics-arbetsyta eller skapa en arbetsyta.
7. Klicka på kryssrutan under **Logg** för var och en av de loggtyper som ska samlas in.
8. Klicka på *Spara* om du vill aktivera loggning av diagnostik till Azure Monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Aktivera Azure-nätverksdiagnostik med PowerShell

Följande PowerShell-skript är ett exempel på hur du aktiverar resursloggning för programgateways.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Använda Azure Application Gateway-analys
![bild av analytics-panel för Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

När du har klickat på panelen **Azure Application Gateway-analys** i översikten kan du visa sammanfattningar av dina loggar och sedan gå in på information om följande kategorier:

* Åtkomstloggar för programgateway
  * Klient- och serverfel för åtkomstloggar för Application Gateway
  * Begäranden per timme för varje programgateway
  * Misslyckade begäranden per timme för varje programgateway
  * Fel av användaragent för application gateways
* Prestanda för programgateway
  * Värdhälsa för programgateway
  * Högsta och 95:e percentilen för misslyckade begäranden i Application Gateway

![bild av instrumentpanelen för Azure Application Gateway-analys](media/azure-networking-analytics/log-analytics-appgateway01.png)

![bild av instrumentpanelen för Azure Application Gateway-analys](media/azure-networking-analytics/log-analytics-appgateway02.png)

På instrumentpanelen **för Azure Application Gateway analytics** granskar du sammanfattningsinformationen i ett av bladen och klickar sedan på ett för att visa detaljerad information på loggsöksidan.

På någon av loggsöksidorna kan du visa resultat efter tid, detaljerade resultat och loggsökhistorik. Du kan också filtrera efter fasor för att begränsa resultaten.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Analyslösning för Azure Network Security Group i Azure Monitor

![Azure Network Security Group Analytics-symbol](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Analytics-lösningen för Nätverkssäkerhetsgruppen går över till community-support eftersom dess funktionalitet har ersatts av [Traffic Analytics](../../network-watcher/traffic-analytics.md).
> - Lösningen är nu tillgänglig i [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) och kommer snart inte längre att vara tillgänglig på Azure Marketplace.
> - För befintliga kunder som redan har lagt till lösningen på sin arbetsyta fortsätter den att fungera utan ändringar.
> - Microsoft kommer att fortsätta att stödja att skicka NSG-resursloggar till din arbetsyta med hjälp av diagnostikinställningar.

Följande loggar stöds för nätverkssäkerhetsgrupper:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure Networking Analytics-lösningen:

1. Aktivera Analytics-lösningen för Azure Network Security Group från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i Lägg till [Azure Monitor-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).
2. Aktivera diagnostikloggning för de [nätverkssäkerhetsgruppsresurser](../../virtual-network/virtual-network-nsg-manage-log.md) som du vill övervaka.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Aktivera diagnostik för Azure-nätverkssäkerhetsgrupper i portalen

1. I Azure-portalen navigerar du till nätverkssäkerhetsgruppens resurs för att övervaka
2. Välj *Diagnostikloggar* för att öppna följande sida

   ![bild av Azure Network Security Group-resurs](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Klicka *på Aktivera diagnostik* för att öppna följande sida

   ![bild av Azure Network Security Group-resurs](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Om du vill aktivera diagnostik *klickar* du på under *Status*
5. Klicka på kryssrutan för *Skicka till logganalys*
6. Välj en befintlig Log Analytics-arbetsyta eller skapa en arbetsyta
7. Klicka på kryssrutan under **Logg** för var och en av loggtyperna som ska samlas in
8. Klicka på *Spara* för att aktivera loggning av diagnostik till Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Aktivera Azure-nätverksdiagnostik med PowerShell

Följande PowerShell-skript är ett exempel på hur du aktiverar resursloggning för nätverkssäkerhetsgrupper
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Använda Analys av Azure Network Security Group
När du har klickat på **analytics-panelen för Azure Network Security Group** i översikten kan du visa sammanfattningar av dina loggar och sedan gå in på information om följande kategorier:

* Nätverkssäkerhetsgrupp blockerade flöden
  * Regler för nätverkssäkerhetsgrupp med blockerade flöden
  * MAC-adresser med blockerade flöden
* Nätverkssäkerhetsgrupp tillät flöden
  * Regler för nätverkssäkerhetsgrupp med tillåtna flöden
  * MAC-adresser med tillåtna flöden

![bild av analytics instrumentpanelen för Azure Network Security Group](media/azure-networking-analytics/log-analytics-nsg01.png)

![bild av analytics instrumentpanelen för Azure Network Security Group](media/azure-networking-analytics/log-analytics-nsg02.png)

På **analytics** instrumentpanelen för Azure Network Security Group granskar du sammanfattningsinformationen i ett av bladen och klickar sedan på en för att visa detaljerad information på loggsöksidan.

På någon av loggsöksidorna kan du visa resultat efter tid, detaljerade resultat och loggsökhistorik. Du kan också filtrera efter fasor för att begränsa resultaten.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrera från den gamla Analytics-lösningen för nätverk
I januari 2017 ändrades sättet att skicka loggar från Azure Application Gateways och Azure Network Security Groups till en Log Analytics-arbetsyta. Dessa ändringar ger följande fördelar:
+ Loggar skrivs direkt till Azure Monitor utan att behöva använda ett lagringskonto
+ Mindre svarstid från den tidpunkt då loggar genereras till dem är tillgängliga i Azure Monitor
+ Färre konfigurationssteg
+ Ett vanligt format för alla typer av Azure-diagnostik

Så här använder du de uppdaterade lösningarna:

1. [Konfigurera diagnostik som ska skickas direkt till Azure Monitor från Azure Application Gateways](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfigurera diagnostik som ska skickas direkt till Azure Monitor från Azure Network Security Groups](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Aktivera *Azure Application Gateway Analytics* och *Azure Network Security Group Analytics-lösningen* med hjälp av processen som beskrivs i Lägg till [Azure Monitor-lösningar från lösningsgalleriet](solutions.md)
3. Uppdatera sparade frågor, instrumentpaneler eller aviseringar för att använda den nya datatypen
   + Typ är till AzureDiagnostics. Du kan använda ResourceType för att filtrera till Azure-nätverksloggar.

     | Istället för: | Använda: |
     | --- | --- |
     | NetworkApplicationgateways &#124; där OperationName=="ApplicationGatewayAccess" | AzureDiagnostics &#124; där ResourceType=="APPLICATIONGATEWAYS" och OperationName=="ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124; där OperationName=="ApplicationGatewayPerformance" | AzureDiagnostics &#124; där ResourceType=="APPLICATIONGATEWAYS" och OperationName=="ApplicationGatewayPerformance" |
     | Nätverkssäkerhetsgrupper | AzureDiagnostics &#124; där ResourceType=="NETWORKSECURITYGROUPS" |

   + För alla fält som har \_ett \_suffix \_med s, d eller g i namnet ändrar du det första tecknet till gemener
   + För alla fält som har \_ett suffix med o i namn delas data upp i enskilda fält baserat på de kapslade fältnamnen.
4. Ta bort *Azure Networking Analytics-lösningen (föråldrad).*
   + Om du använder PowerShell använder du`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Data som samlas in före ändringen visas inte i den nya lösningen. Du kan fortsätta att fråga efter dessa data med hjälp av gamla typ- och fältnamn.

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Nästa steg
* Använd [loggfrågor i Azure Monitor](../log-query/log-query-overview.md) för att visa detaljerade Azure-diagnostikdata.

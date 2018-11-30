---
title: Azure Networking Analytics-lösningen i Log Analytics | Microsoft Docs
description: Du kan använda Azure Networking Analytics-lösningen i Log Analytics för att granska Azure loggar för nätverkssäkerhetsgrupper och Azure Application Gateway-loggar.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: ewinner
editor: ''
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: richrund
ms.component: ''
ms.openlocfilehash: 061ad1cf8cf99d8100163bd6b7c9d72377075108
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428769"
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Azure-nätverk övervakningslösningar i Log Analytics

Log Analytics erbjuder följande lösningar för att övervaka dina nätverk:
* Övervakare av nätverksprestanda (NPM) till
 * Övervaka hälsotillståndet för nätverket
* Azure Application Gateway analytics att granska
 * Azure Application Gateway-loggar
 * Azure Application Gateway-mått
* Lösningar för att övervaka och granska nätverksaktivitet i nätverket molnet
* [Trafikanalys](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
* Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Övervakare av nätverksprestanda (NPM)

Den [Övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview) lösning är en lösning som övervakar hälsa, tillgänglighet och Åtkomstmöjligheten i nätverk för nätverksövervakning.  Används för att övervaka anslutningen mellan:

* Offentliga molnet och lokalt
* Datacenter och platser för användare (avdelningskontor)
* Undernät som är värd för olika nivåer av ett program med flera nivåer.

Mer information finns i [Övervakare av nätverksprestanda](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway och Nätverkssäkerhetsgruppen analytics
Använda lösningarna:
1. Lägg till hanteringslösning för i Log Analytics, och
2. Aktivera diagnostik att dirigera diagnostik till en Log Analytics-arbetsyta. Du behöver inte skriva loggarna till Azure Blob storage.

Du kan aktivera diagnostik- och motsvarande lösningen för en eller båda av Application Gateway och nätverk säkerhetsgrupper.

Om du inte aktiverar diagnostikloggning för en viss resurstyp och installera lösningen för instrumentpanelen bladen för den här resursen är tomma och visa ett felmeddelande.

> [!NOTE]
> I januari 2017 ändras sättet som stöds för att skicka loggar från Application Gateways och Nätverkssäkerhetsgrupper till Log Analytics. Om du ser den **Azure Networking Analytics (inaktuell)** lösning, referera till [migrera från den gamla nätverk analyslösning](#migrating-from-the-old-networking-analytics-solution) anvisningar om hur du ska följa.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Granska Azure networking information om insamling av data
Azure Application Gateway-analys och hanteringslösningar för Nätverkssäkerhetsgruppen analytics kan du samla in diagnostikloggar direkt från Azure Application Gateway och Nätverkssäkerhetsgrupper. Det är inte nödvändigt att skriva loggar till Azure Blob storage och ingen agent krävs för insamling av data.

I följande tabell visar data samlingsmetoder och annan information om hur data samlas in för Azure Application Gateway-analys och analys för Nätverkssäkerhetsgruppen.

| Plattform | Direktagent | System Center Operations Manager-agenten | Azure | Operations Manager som krävs? | Operations Manager agent-data skickas via hanteringsgruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |När du har loggat |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Azure Application Gateway analytics-lösningen i Log Analytics

![Azure Application Gateway Analytics symbol](media/azure-networking-analytics/azure-analytics-symbol.png)

Följande loggar kan användas för Application Gateway:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Följande mått stöds för Programgatewayer: igen


* 5 minuter dataflöde

### <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure Application Gateway analytics-lösningen:

1. Aktivera Azure Application Gateway analytics-lösningen från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till Log Analytics-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).
2. Aktivera diagnostik loggning för den [Programgatewayer](../../application-gateway/application-gateway-diagnostics.md) du vill övervaka.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Aktivera Azure Application Gateway-diagnostik i portalen

1. I Azure-portalen går du till Application Gateway-resursen för att övervaka
2. Välj *diagnostikloggar* att öppna sidan

   ![Bild av Azure Application Gateway-resursen](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Klicka på *slå på diagnostik* att öppna sidan

   ![Bild av Azure Application Gateway-resursen](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Aktivera diagnostik, klicka på *på* under *Status*
5. Klicka på kryssrutan för *skicka till Log Analytics*
6. Välj en befintlig Log Analytics-arbetsyta eller skapa en arbetsyta
7. Klicka på kryssrutan under **Log** för var och en av loggtyper att samla in
8. Klicka på *spara* att aktivera loggning av diagnostik till Log Analytics

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Aktivera diagnostik för Azure-nätverk med hjälp av PowerShell

Följande PowerShell-skript innehåller ett exempel på hur du aktiverar loggning för programgatewayer.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Använd Azure Application Gateway analytics
![Bild av Azure Application Gateway analytics panelen](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

När du klickar på den **Azure Application Gateway analytics** panelen i översikten, kan du visa sammanfattningar av dina loggar och sedan kan du läsa informationen i följande kategorier:

* Programåtkomst för Gateway-loggar
  * Klient- och fel för åtkomstloggar för Application Gateway
  * Förfrågningar per timme för varje Application Gateway
  * Misslyckade förfrågningar per timme för varje Application Gateway
  * Fel per användaragent för Programgatewayer
* Application Gateway-prestanda
  * Värdens hälsotillstånd för Application Gateway
  * Högsta och den 95: e percentilen för Application Gateway misslyckade begäranden

![Bild av Azure Application Gateway analytics-instrumentpanelen](media/azure-networking-analytics/log-analytics-appgateway01.png)

![Bild av Azure Application Gateway analytics-instrumentpanelen](media/azure-networking-analytics/log-analytics-appgateway02.png)

På den **Azure Application Gateway analytics** instrumentpanelen läser du sammanfattningsinformationen på något av bladen och klicka sedan på en om du vill visa detaljerad information på sidan log search.

På någon av sidorna log search, kan du visa resultat genom tid, detaljerade resultat och sökhistoriken log. Du kan också filtrera efter fasetter att begränsa resultaten.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Azure Network Security Group analytics-lösningen i Log Analytics

![Azure Network Security Group Analytics symbol](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Nätverkssäkerhetsgrupp analyslösning flyttas till community-support, eftersom dess funktionalitet har ersatts av [trafikanalys](../../network-watcher/traffic-analytics.md).
> - Lösningen är nu tillgängligt i [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) och snart inte längre är tillgänglig på Azure Marketplace.
> - För befintliga kunder som redan har lagts till i lösningen till sin arbetsyta, fortsätter att fungera utan ändringar.
> - Microsoft fortsätter att stödja NSG skicka diagnostikloggar till din arbetsyta med hjälp av inställningarna för Startdiagnostik.

Följande loggar har stöd för nätverkssäkerhetsgrupper:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure Networking Analytics-lösningen:

1. Aktivera Azure Network Security Group analytics-lösningen från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till Log Analytics-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).
2. Aktivera diagnostik loggning för den [Nätverkssäkerhetsgrupp](../../virtual-network/virtual-network-nsg-manage-log.md) resurser som du vill övervaka.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Aktivera Azure network security group diagnostik i portalen

1. I Azure-portalen går du till resursen som Nätverkssäkerhetsgruppen ska övervaka
2. Välj *diagnostikloggar* att öppna sidan

   ![Bild av Azure Network Security Group-resurs](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Klicka på *slå på diagnostik* att öppna sidan

   ![Bild av Azure Network Security Group-resurs](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Aktivera diagnostik, klicka på *på* under *Status*
5. Klicka på kryssrutan för *skicka till Log Analytics*
6. Välj en befintlig Log Analytics-arbetsyta eller skapa en arbetsyta
7. Klicka på kryssrutan under **Log** för var och en av loggtyper att samla in
8. Klicka på *spara* att aktivera loggning av diagnostik till Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Aktivera diagnostik för Azure-nätverk med hjälp av PowerShell

Följande PowerShell-skript innehåller ett exempel på hur du aktiverar loggning för nätverkssäkerhetsgrupper
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Använd Azure Network Security Group analytics
När du klickar på den **Azure Network Security Group analytics** panelen i översikten, kan du visa sammanfattningar av dina loggar och sedan kan du läsa informationen i följande kategorier:

* Nätverkssäkerhetsgrupp blockerade flöden
  * Regler för nätverkssäkerhetsgrupper med blockerade flöden
  * MAC-adresser med blockerade flöden
* Nätverkssäkerhetsgrupp som är tillåtna flöden
  * Regler för nätverkssäkerhetsgrupper med tillåtna flöden
  * MAC-adresser med tillåtna flöden

![Bild av Azure Network Security Group analytics-instrumentpanelen](media/azure-networking-analytics/log-analytics-nsg01.png)

![Bild av Azure Network Security Group analytics-instrumentpanelen](media/azure-networking-analytics/log-analytics-nsg02.png)

På den **Azure Network Security Group analytics** instrumentpanelen läser du sammanfattningsinformationen på något av bladen och klicka sedan på en om du vill visa detaljerad information på sidan log search.

På någon av sidorna log search, kan du visa resultat genom tid, detaljerade resultat och sökhistoriken log. Du kan också filtrera efter fasetter att begränsa resultaten.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrera från den gamla Networking Analytics-lösningen
I januari 2017 ändras sättet som stöds för att skicka loggar från Azure Application Gateway och Azure-Nätverkssäkerhetsgrupper till Log Analytics. Dessa ändringar ger följande fördelar:
+ Loggarna skrivs direkt till Log Analytics utan att behöva använda ett lagringskonto
+ Mindre fördröjning från den tidpunkt när loggarna genereras till dem som det är tillgängligt i Log Analytics
+ Färre konfigurationssteg
+ Ett vanligt format för alla typer av Azure-diagnostik

Att använda de uppdaterade lösningarna:

1. [Konfigurera diagnostik skickas direkt till Log Analytics från Azure Application Gateway](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfigurera diagnostik skickas direkt till Log Analytics från Azure-Nätverkssäkerhetsgrupper](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Aktivera den *Azure Application Gateway Analytics* och *Azure Network Security Group Analytics* lösning med hjälp av metoden som beskrivs i [lägga till Log Analytics-lösningar från den Lösningsgalleriet](../../azure-monitor/insights/solutions.md)
3. Uppdatera alla sparade frågor, instrumentpaneler eller aviseringar att använda den nya datatypen
  + Typen är att AzureDiagnostics. Du kan använda resurstypens för att filtrera till Azures nätverksloggar.

    | Istället för: | Användning: |
    | --- | --- |
    | NetworkApplicationgateways &#124; där OperationName == ”ApplicationGatewayAccess” | AzureDiagnostics &#124; där ResourceType = ”APPLICATIONGATEWAYS” och OperationName == ”ApplicationGatewayAccess” |
    | NetworkApplicationgateways &#124; där OperationName == ”ApplicationGatewayPerformance” | AzureDiagnostics &#124; där ResourceType == ”APPLICATIONGATEWAYS” och OperationName = ApplicationGatewayPerformance |
    | NetworkSecuritygroups | AzureDiagnostics &#124; där ResourceType == ”NETWORKSECURITYGROUPS” |

   + För alla fält som har suffixet \_s, \_d, eller \_g i namnet, ändra det första tecknet till gemener
   + För alla fält som har suffixet \_o i namn data delas upp i enskilda fält baserat på de kapslade fältnamn.
4. Ta bort den *Azure Networking Analytics (inaktuell)* lösning.
  + Om du använder PowerShell använder du `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Data som samlas in innan ändringen inte visas i den nya lösningen. Du kan fortsätta att fråga efter dessa data med hjälp av äldre typ och fältnamn.

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Nästa steg
* Använd [Loggsökningar i Log Analytics](../../log-analytics/log-analytics-queries.md) att visa detaljerad Azure diagnostics-data.

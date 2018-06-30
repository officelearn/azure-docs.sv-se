---
title: Azure nätverk Analytics lösning i Log Analytics | Microsoft Docs
description: Du kan använda Azure-nätverk Analytics-lösning i logganalys för att granska grupp säkerhetsloggar av Azure-nätverk och Azure Programgateway loggar.
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
ms.component: na
ms.openlocfilehash: 8a92bf7b031899ee75fbf2bb2fdfd7dced3bc1ad
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127900"
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Azure nätverk övervakning lösningar i logganalys

Logganalys erbjuder följande lösningar för att övervaka dina nätverk:
* Network Performance Monitor (NPM) till
 * Övervaka hälsotillståndet hos ditt nätverk
* Azure Application Gateway analyser för att granska
 * Azure Application Gateway-loggar
 * Azure Application Gateway-mått
* Lösningar för att övervaka och granska nätverksaktivitet i nätverket moln
* [Trafik Analytics](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
* Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

Den [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) hanteringslösning är ett nätverk övervakningslösning som övervakar hälsa, tillgänglighet och tillgänglighet av nätverk.  Används för att övervaka anslutningen mellan:

* offentliga molnet och lokalt
* datacenter och användarplatser (avdelningskontor)
* undernät som är värd för olika nivåer av ett program med flera nivåer.

Mer information finns i [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway och Nätverkssäkerhetsgruppen analytics
Använda lösningarna:
1. Lägg till hanteringslösningen Log Analytics och
2. Aktivera diagnostik för att dirigera diagnostik till logganalys-arbetsytan. Du behöver inte skriva loggarna till Azure Blob storage.

Du kan aktivera diagnostik- och motsvarande lösningen för en eller båda av Programgateway och nätverk säkerhetsgrupper.

Om du inte aktivera diagnostikloggning för en viss resurstyp, men installerar lösningen, instrumentpanelen blad för den här resursen är tomma och ett felmeddelande visas.

> [!NOTE]
> Sättet att skicka loggar från Programgatewayer och Nätverkssäkerhetsgrupper till logganalys ändras i januari 2017. Om du ser den **Azure-nätverk Analytics (föråldrad)** lösning, referera till [migrera från den gamla nätverk Analytics lösningen](#migrating-from-the-old-networking-analytics-solution) anvisningar för hur du ska följa.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Granska Azure nätverk information för samlingen
Samla in diagnostik loggarna direkt från Azure Programgatewayer och Nätverkssäkerhetsgrupper Azure Programgateway analyser och Nätverkssäkerhetsgruppen management Analyslösningar. Det är inte nödvändigt att skriva loggarna till Azure Blob storage och ingen agent krävs för datainsamling.

I följande tabell visar metoder för insamling av data och annan information om hur data samlas in för Azure Programgateway analyser och Nätverkssäkerhetsgruppen analytics.

| Plattform | Styr agent | System Center Operations Manager-agenten | Azure | Operations Manager som krävs? | Operations Manager agent-data som skickas via management-grupp | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |När du har loggat |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Azure Application Gateway analytics lösning i logganalys

![Azure Application Gateway Analytics symbol](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Följande loggar stöds för Programgatewayer:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Följande mått stöds för Programgatewayer: igen


* 5 minut genomflöde

### <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure Programgateway analytics lösningen:

1. Aktivera Azure Programgateway analytics-lösning från [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).
2. Aktivera diagnostik loggning för den [Programgatewayer](../application-gateway/application-gateway-diagnostics.md) du vill övervaka.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Aktivera Azure Programgateway diagnostik i portalen

1. Navigera till resursen Programgateway att övervaka i Azure-portalen
2. Välj *diagnostik loggar* att öppna följande sida

   ![Bild av Azure Programgateway resurs](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. Klicka på *aktivera diagnostiken* att öppna följande sida

   ![Bild av Azure Programgateway resurs](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. Aktivera diagnostik, klicka på *på* under *Status*
5. Klicka på kryssrutan för *skicka till logganalys*
6. Välj en befintlig logganalys-arbetsyta eller skapa en arbetsyta
7. Klickar du på kryssrutan under **loggen** för varje logg att samla in
8. Klicka på *spara* att aktivera loggning av diagnostik till logganalys

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Aktivera Azure Nätverksdiagnostik med PowerShell

Följande PowerShell-skript innehåller ett exempel på hur du aktiverar loggning för programgatewayer.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Använd Azure Programgateway analytics
![Bild av Azure Programgateway analytics panelen](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

När du klickar på den **Azure Programgateway analytics** panelen på Översikt, kan du visa sammanfattningar av loggar och visa sedan detaljnivåerna till information i följande kategorier:

* Programåtkomst för Gateway-loggar
  * Klient- och fel för åtkomstloggar för Programgateway
  * Förfrågningar per timme för varje Programgateway
  * Misslyckade förfrågningar per timme för varje Programgateway
  * Fel per användaragent för Programgatewayer
* Programprestanda för Gateway
  * Värden hälsa för Programgateway
  * Maximal och 95 percentil för Programgateway misslyckade begäranden

![Bild av instrumentpanelen för Azure Programgateway](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![Bild av instrumentpanelen för Azure Programgateway](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

På den **Azure Programgateway analytics** instrumentpanel, Granska sammanfattningen i ett av bladen och klicka sedan på en om du vill visa detaljerad information på sidan logga.

Du kan visa resultaten av tid, detaljerade resultat och Logghistoriken på någon av sidorna loggen sökning. Du kan också filtrera efter aspekter att begränsa resultaten.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Azure Network Security Group analytics lösning i logganalys

![Azure Network Security Group Analytics symbol](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

> [!NOTE]
> Nätverkssäkerhetsgruppen analytics lösningen flyttas till community-support, eftersom dess funktioner har ersatts av [trafik Analytics](../network-watcher/traffic-analytics.md).
> - Lösningen är nu tillgängligt i [Azure Quickstart mallar](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) och snart inte längre att vara tillgänglig i Azure Marketplace.
> - För befintliga kunder som redan lagts till lösningen till deras arbetsyta, fortsätter den att fungera utan ändringar.
> - Microsoft fortsätter att stödja NSG skicka diagnostikloggar till din arbetsyta med hjälp av inställningarna för Webbprogramdiagnostik.

Följande loggar stöds för nätverkssäkerhetsgrupper:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure-nätverk Analytics-lösningen:

1. Aktivera Azure Network Security Group analytics-lösning från [Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) eller genom att använda processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).
2. Aktivera diagnostik loggning för den [Nätverkssäkerhetsgruppen](../virtual-network/virtual-network-nsg-manage-log.md) resurser som du vill övervaka.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Aktivera diagnostik av gruppen för Azure-nätverk i portalen

1. Navigera till Nätverkssäkerhetsgruppen resursen ska övervaka i Azure-portalen
2. Välj *diagnostik loggar* att öppna följande sida

   ![Bild av Azure Network Security Group resurs](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. Klicka på *aktivera diagnostiken* att öppna följande sida

   ![Bild av Azure Network Security Group resurs](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. Aktivera diagnostik, klicka på *på* under *Status*
5. Klicka på kryssrutan för *skicka till logganalys*
6. Välj en befintlig logganalys-arbetsyta eller skapa en arbetsyta
7. Klickar du på kryssrutan under **loggen** för varje logg att samla in
8. Klicka på *spara* att aktivera loggning av diagnostik till logganalys

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Aktivera Azure Nätverksdiagnostik med PowerShell

Följande PowerShell-skript innehåller ett exempel på hur du aktiverar loggning för nätverkssäkerhetsgrupper
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Använd Azure Network Security Group analytics
När du klickar på den **Azure Network Security Group analytics** panelen på Översikt, kan du visa sammanfattningar av loggar och visa sedan detaljnivåerna till information i följande kategorier:

* Nätverkssäkerhetsgruppen blockeras flöden
  * Regler för nätverkssäkerhetsgrupper med blockerade flöden
  * MAC-adresser med blockerade flöden
* Nätverkssäkerhetsgruppen tillåtna flöden
  * Regler för nätverkssäkerhetsgrupper med tillåtna flöden
  * MAC-adresser med tillåtna flöden

![Bild av instrumentpanelen för Azure Nätverkssäkerhetsgrupp](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![Bild av instrumentpanelen för Azure Nätverkssäkerhetsgrupp](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

På den **Azure Network Security Group analytics** instrumentpanel, Granska sammanfattningen i ett av bladen och klicka sedan på en om du vill visa detaljerad information på sidan logga.

Du kan visa resultaten av tid, detaljerade resultat och Logghistoriken på någon av sidorna loggen sökning. Du kan också filtrera efter aspekter att begränsa resultaten.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrera från den gamla nätverk Analytics-lösningen
Sättet att skicka loggar från Azure Programgatewayer och säkerhetsgrupper för Azure-nätverket till logganalys ändras i januari 2017. Ändringarna ger följande fördelar:
+ Loggarna skrivs direkt till Log Analytics utan att behöva använda ett lagringskonto
+ Mindre fördröjning från när loggar genereras till dem som finns i logganalys
+ Färre konfigurationssteg
+ Ett vanligt format för alla typer av Azure-diagnostik

Använda de uppdaterade lösningarna:

1. [Konfigurera diagnostik skickas direkt till Log Analytics från Azure Programgatewayer](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfigurera diagnostik skickas direkt till Log Analytics från Azure Nätverkssäkerhetsgrupper](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Aktivera den *Azure Application Gateway Analytics* och *Azure Network Security Group Analytics* lösning med hjälp av den process som beskrivs i [lägga till logganalys lösningar från den Lösningar galleri](log-analytics-add-solutions.md)
3. Uppdatera alla sparade frågor, instrumentpaneler eller aviseringar för att använda den nya datatypen
  + Typen är att AzureDiagnostics. Du kan använda resurstypens för att filtrera till Azure-nätverk loggarna.

    | Istället för: | Användning: |
    | --- | --- |
    | NetworkApplicationgateways &#124; där OperationName == ”ApplicationGatewayAccess” | AzureDiagnostics &#124; där ResourceType = ”APPLICATIONGATEWAYS” och OperationName == ”ApplicationGatewayAccess” |
    | NetworkApplicationgateways &#124; där OperationName == ”ApplicationGatewayPerformance” | AzureDiagnostics &#124; där ResourceType == ”APPLICATIONGATEWAYS” och OperationName = ApplicationGatewayPerformance |
    | NetworkSecuritygroups | AzureDiagnostics &#124; där ResourceType == ”NETWORKSECURITYGROUPS” |

   + För alla fält som har suffixet \_s, \_d, eller \_g i namnet, ändra det första tecknet till gemener
   + För alla fält som har suffixet \_o i namn data delas upp i enskilda fält baserat på de kapslade fältnamn.
4. Ta bort den *Azure nätverk Analytics (inaktuell)* lösning.
  + Om du använder PowerShell använder du `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Data som samlas in innan ändringen inte visas i den nya lösningen. Du kan fortsätta att fråga efter data med hjälp av den gamla typen och fältnamn.

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Nästa steg
* Använd [logga sökningar i logganalys](log-analytics-log-searches.md) att visa detaljerad Azure-diagnostikdata.

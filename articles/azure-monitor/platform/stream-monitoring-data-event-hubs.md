---
title: Strömma Azure-övervaknings data till händelsehubben
description: Lär dig hur du direktuppspelar Azures övervaknings data till en Event Hub för att hämta data till en partner SIEM eller ett analys verktyg.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 915df5d6356e2246c8937cb167c8068b00e0917b
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854615"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Strömma Azure-övervakningsdata till en händelsehubb
Azure Monitor ger en fullständig lösning för stack övervakning för program och tjänster i Azure, i andra moln och lokalt. Förutom att använda Azure Monitor för att analysera data och använda dem för olika övervaknings scenarier, kan du behöva skicka dem till andra övervaknings verktyg i din miljö. Den mest effektiva metoden att strömma övervaknings data till externa verktyg i de flesta fall är att använda [Azure Event Hubs](/azure/event-hubs/). Den här artikeln innehåller en kort beskrivning av hur du kan strömma övervaknings data från olika källor till en Event Hub och länkar till detaljerad vägledning.


## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Innan du konfigurerar strömning för en data källa måste du [skapa ett Event Hubs-namnområde och händelsehubben](../../event-hubs/event-hubs-create.md). Det här namn området och händelsehubben är målet för alla dina övervaknings data. Ett Event Hubs-namnområde är en logisk gruppering av händelse hubbar som delar samma åtkomst princip, ungefär som ett lagrings konto har enskilda blobbar inom det lagrings kontot. Tänk på följande om Event Hub-namnområdet och händelse nav som du använder för att strömma övervaknings data:

* Antalet data flödes enheter gör att du kan öka data flödets skala för dina Event Hub. Det krävs vanligt vis en data flödes enhet. Om du behöver skala upp när logg användningen ökar kan du manuellt öka antalet data flödes enheter för namn området eller aktivera automatisk inflation.
* Med antalet partitioner kan du parallellisera förbrukningen för många konsumenter. En enda partition har stöd för upp till 20MBps eller cirka 20 000 meddelanden per sekund. Beroende på vilket verktyg som utnyttjar data kan det hända att det inte går att använda från flera partitioner. Fyra partitioner är rimliga för att börja med om du inte är säker på hur många partitioner som ska ställas in.
* Du ställer in meddelande kvarhållning på händelsehubben till minst 7 dagar. Om ditt förbrukare verktyg går ned i mer än en dag, ser detta till att verktyget kan fortsätta där det slutade för händelser upp till 7 dagar gammal.
* Använd standard konsument gruppen för händelsehubben. Du behöver inte skapa andra konsument grupper eller använda en separat konsument grupp om du inte planerar att ha två olika verktyg som använder samma data från samma Event Hub.
* För Azure aktivitets loggen väljer du ett Event Hubs namn område och Azure Monitor skapar en händelsehubben i det namn området som kallas _Insights-loggar-operativa loggar_. För andra typer av loggar kan du antingen välja en befintlig händelsehubben eller låta Azure Monitor skapa en Event Hub per logg kategori.
* Utgående port 5671 och 5672 måste vanligt vis öppnas på datorn eller VNET som använder data från händelsehubben.

## <a name="monitoring-data-available"></a>Tillgängliga övervaknings data
[Källor för övervaknings data för Azure Monitor](data-sources.md) beskriver olika data nivåer för Azure-program och vilka typer av övervaknings data som är tillgängliga för var och en. I följande tabell visas var och en av dessa nivåer och en beskrivning av hur data kan strömmas till en händelsehubben. Följ länkarna som finns för ytterligare information.

| Nivå | Data | Metod |
|:---|:---|:---|
| [Azure-klientorganisation](data-sources.md#azure-tenant) | Azure Active Directory gransknings loggar | Konfigurera en inställning för klientens diagnostik på din AAD-klient. Mer information finns i [Självstudier: strömma Azure Active Directory loggar till en Azure Event Hub](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) . |
| [Azure-prenumeration](data-sources.md#azure-subscription) | Azure-aktivitetslogg | Skapa en logg profil för att exportera aktivitets logg händelser till Event Hubs.  Mer information finns i [strömma Azure-plattforms loggar till azure Event Hubs](resource-logs-stream-event-hubs.md) . |
| [Azure-resurser](data-sources.md#azure-resources) | Plattforms mått<br> Resursloggar |Båda typerna av data skickas till en Event Hub med en inställning för resurs diagnostik. Mer information finns i [strömma Azure-resurstilldelningar till en Event Hub](resource-logs-stream-event-hubs.md) . |
| [Operativ system (gäst)](data-sources.md#operating-system-guest) | Virtuella Azure-datorer | Installera [Azure-diagnostik-tillägget](diagnostics-extension-overview.md) på virtuella Windows-och Linux-datorer i Azure. Se [strömmande Azure-diagnostik data i den aktiva sökvägen med hjälp av Event Hubs](diagnostics-extension-stream-event-hubs.md) för information om virtuella Windows-datorer och [Använd Linux Diagnostic-tillägg för att övervaka statistik och loggar](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) för information om virtuella Linux-datorer. |
| [Program kod](data-sources.md#application-code) | Application Insights | Application Insights tillhandahåller inte någon direkt metod för att strömma data till händelse nav. Du kan [Konfigurera kontinuerlig export](../../azure-monitor/app/export-telemetry.md) av Application Insights data till ett lagrings konto och sedan använda en Logi Kap par för att skicka data till en Event Hub enligt beskrivningen i [manuell strömning med Logic app](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Manuell strömning med Logic app
För data som du inte kan strömma direkt till en Event Hub kan du skriva till Azure Storage och sedan använda en tidsinitierad Logic-app som [hämtar data från Blob Storage](../../connectors/connectors-create-api-azureblobstorage.md#add-action) och skickar [dem som ett meddelande till händelsehubben](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partner verktyg med Azure Monitor-integrering

Genom att vidarebefordra dina övervaknings data till en Event Hub med Azure Monitor kan du enkelt integrera med externa SIEM och övervaknings verktyg. Exempel på verktyg med Azure Monitor-integrering är följande:

| Verktyg | Värd i Azure | Beskrivning |
|:---|:---| :---|
|  IBM QRadar | Nej | Microsoft Azure DSM-och Microsoft Azure Event Hub-protokollet kan hämtas från [IBM support-webbplatsen](https://www.ibm.com/support). Du kan lära dig mer om integrering med Azure på [QRADAR DSM-konfiguration](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | Nej | [Azure Monitor-tillägget för Splunk](https://splunkbase.splunk.com/app/3534/) är ett projekt med öppen källkod som är tillgängligt i Splunkbase. Dokumentationen finns på [Azure Monitor addon för Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Om du inte kan installera ett tillägg i din Splunk-instans, om du t. ex. använder en proxy eller som körs på Splunk-molnet, kan du vidarebefordra händelserna till insamlaren för HTTP-Splunk med hjälp av [Azure Function för Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), som utlöses av nya meddelanden i händelsehubben. |
| SumoLogic | Nej | Anvisningar för att konfigurera SumoLogic för att använda data från en händelsehubben är tillgängliga vid [insamling av loggar för Azure audit-appen från Event Hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Nej | Smart Connector för ArcSight Azure Event Hub är tillgängligt som en del av [ArcSight Smart Connector-samlingen](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Syslog-server | Nej | Om du vill strömma Azure Monitor data direkt till en Syslog-server kan du använda en [lösning som baseras på en Azure-funktion](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | Nej| Anvisningar för att konfigurera LogRhythm för att samla in loggar från en Event Hub finns [här](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Ja | Mer information finns i [komma igång med övervakning och loggning med Logz.io för Java-appar som körs på Azure](https://docs.microsoft.com/azure/developer/java/fundamentals/java-get-started-with-logzio)


## <a name="next-steps"></a>Nästa steg
* [Arkivera aktivitets loggen till ett lagrings konto](../../azure-monitor/platform/archive-activity-log.md)
* [Läs översikten över Azures aktivitets logg](../../azure-monitor/platform/platform-logs-overview.md)
* [Konfigurera en avisering baserat på en aktivitets logg händelse](../../azure-monitor/platform/alerts-log-webhook.md)



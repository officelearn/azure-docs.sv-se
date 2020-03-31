---
title: Strömma Azure-övervakningsdata till händelsehubb
description: Lär dig hur du streamar dina Azure-övervakningsdata till en händelsehubb för att få in data i en partner-SIEM- eller analysverktyg.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 08177165439ff7d3205e31757e5d1e28759a9836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274195"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>Strömma Azure-övervakningsdata till en händelsehubb
Azure Monitor tillhandahåller en komplett fullständig stackövervakningslösning för program och tjänster i Azure, i andra moln och lokalt. Förutom att använda Azure Monitor för att analysera dessa data och utnyttja dem för olika övervakningsscenarier, kan du behöva skicka dem till andra övervakningsverktyg i din miljö. Den mest effektiva metoden för att strömma övervakningsdata till externa verktyg i de flesta fall är att använda [Azure Event Hubs](/azure/event-hubs/). Den här artikeln innehåller en kort beskrivning av hur du kan strömma övervakningsdata från olika källor till en händelsehubb och länkar till detaljerad vägledning.


## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Innan du konfigurerar direktuppspelning för en datakälla måste du [skapa ett namnområde för eventhubbar och händelsenav](../../event-hubs/event-hubs-create.md). Den här namnområdes- och händelsehubben är målet för alla dina övervakningsdata. Ett namnområde för eventhubbar är en logisk gruppering av händelsehubbar som delar samma åtkomstprincip, ungefär som ett lagringskonto har enskilda blobbar i det lagringskontot. Tänk på följande information om namnområdet för händelsehubbar och händelsehubbar som du använder för strömmande övervakningsdata:

* Med antalet dataflödesenheter kan du öka dataflödesskalan för dina händelsehubbar. Endast en dataflödesenhet är vanligtvis nödvändig. Om du behöver skala upp när logganvändningen ökar kan du manuellt öka antalet dataflödesenheter för namnområdet eller aktivera automatisk inflation.
* Med antalet partitioner kan du parallellisera konsumtionen mellan många konsumenter. En enda partition kan stödja upp till 20 MBps eller cirka 20 000 meddelanden per sekund. Beroende på vilket verktyg som förbrukar data kan det vara bra att använda från flera partitioner. Fyra partitioner är rimligt att starta om du är osäker på om du inte är säker på hur många partitioner som ska ställas in.
* Du kan ange bevarande av meddelanden på händelsehubben till minst 7 dagar. Om ditt tidskrävande verktyg går ner i mer än en dag, säkerställer detta att verktyget kan fortsätta där det slutade för händelser upp till 7 dagar gamla.
* Du bör använda standardkonsumentgruppen för din händelsehubb. Det finns ingen anledning att skapa andra konsumentgrupper eller använda en separat konsumentgrupp om du inte planerar att ha två olika verktyg som använder samma data från samma händelsehubb.
* För Azure Activity-loggen väljer du ett namnområde för eventhubbar och Azure Monitor skapar en händelsenav inom namnområdet som kallas _insights-logs-operational-logs_. För andra loggtyper kan du antingen välja en befintlig händelsenav eller låta Azure Monitor skapa en händelsehubb per loggkategori.
* Utgående port 5671 och 5672 måste vanligtvis öppnas på datorn eller VNET som förbrukar data från händelsehubben.

## <a name="monitoring-data-available"></a>Tillgängliga övervakningsdata
[Källor till övervakningsdata för Azure Monitor](data-sources.md) beskriver de olika nivåerna av data för Azure-program och vilka typer av övervakningsdata som är tillgängliga för varje. I följande tabell visas var och en av dessa nivåer och en beskrivning av hur dessa data kan strömmas till en händelsehubb. Följ länkarna för mer information.

| Nivå | Data | Metod |
|:---|:---|:---|
| [Azure-klientorganisation](data-sources.md#azure-tenant) | Granskningsloggar för Azure Active Directory | Konfigurera en klientdiagnostikinställning på din AAD-klient. Mer information finns i [självstudiekurs: Strömma Azure Active Directory-loggar till en Azure-händelsehubb.](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |
| [Azure-prenumeration](data-sources.md#azure-subscription) | Azure-aktivitetslogg | Skapa en loggprofil för att exportera aktivitetslogghändelser till eventhubbar.  Mer information finns [i Strömma Azure-plattformsloggar till Azure Event Hubs.](resource-logs-stream-event-hubs.md) |
| [Azure-resurser](data-sources.md#azure-resources) | Plattformsmått<br> Resursloggar |Båda typerna av data skickas till en händelsehubb med hjälp av en resursdiagnostikinställning. Mer information finns i [Strömma Azure-resursloggar till en händelsehubb.](resource-logs-stream-event-hubs.md) |
| [Operativsystem (gäst)](data-sources.md#operating-system-guest) | Virtuella Azure-datorer | Installera [Azure Diagnostics Extension](diagnostics-extension-overview.md) på windows och linux virtuella datorer i Azure. Se [Strömmande Azure Diagnostics-data i den aktiva sökvägen med hjälp av eventhubbar](diagnostics-extension-stream-event-hubs.md) för information om Virtuella datorer i Windows och Använd Linux Diagnostic Extension för [att övervaka mått och loggar](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) för information om virtuella Linux-datorer. |
| [Programkod](data-sources.md#application-code) | Application Insights | Application Insights tillhandahåller inte en direkt metod för att strömma data till händelsehubbar. Du kan [ställa in kontinuerlig export](../../azure-monitor/app/export-telemetry.md) av Application Insights-data till ett lagringskonto och sedan använda en Logic App för att skicka data till en händelsenav enligt beskrivningen i Manuell [direktuppspelning med Logic App](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Manuell direktuppspelning med Logic App
För data som du inte direkt kan strömma till en händelsehubb kan du skriva till Azure-lagring och sedan använda en tidsutlöst Logic App som [hämtar data från blob-lagring](../../connectors/connectors-create-api-azureblobstorage.md#add-action) och [skickar den som ett meddelande till händelsehubben](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Partnerverktyg med Azure Monitor-integrering

Genom att dirigera dina övervakningsdata till en händelsehubb med Azure Monitor kan du enkelt integrera med externa SIEM- och övervakningsverktyg. Exempel på verktyg med Azure Monitor-integrering är följande:

| Verktyg | Värd i Azure | Beskrivning |
|:---|:---| :---|
|  IBM QRadar | Inga | Microsoft Azure DSM och Microsoft Azure Event Hub Protocol är tillgängliga för nedladdning från [IBM:s supportwebbplats](https://www.ibm.com/support). Du kan läsa mer om integreringen med Azure vid [QRadar DSM-konfiguration](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | Inga | [Azure Monitor Add-On for Splunk](https://splunkbase.splunk.com/app/3534/) är ett projekt med öppen källkod som är tillgängligt i Splunkbase. Dokumentationen finns på [Azure Monitor Addon For Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).<br><br> Om du inte kan installera ett tillägg i din Splunk-förekomst, om du till exempel använder en proxy eller körs på Splunk Cloud, kan du vidarebefordra dessa händelser till splunk HTTP Event Collector med [Azure-funktion för Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), som utlöses av nya meddelanden i händelsehubben. |
| SumoLogic | Inga | Instruktioner för hur du konfigurerar SumoLogic för att använda data från en händelsehubb finns på [Collect Logs for the Azure Audit App from Event Hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Inga | Den smarta ArcSight Azure Event Hub-anslutningen är tillgänglig som en del av [ArcSight smart connector collection](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Syslog-server | Inga | Om du vill strömma Azure Monitor-data direkt till en syslog-server kan du använda en [lösning baserad på en Azure-funktion](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRytm | Inga| Instruktioner för att ställa in LogRhythm för att samla in loggar från en händelsehubb finns [här](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Ja | Mer information finns i [Komma igång med övervakning och loggning med Logz.io för Java-appar som körs på Azure](https://docs.microsoft.com/azure/java/java-get-started-with-logzio)


## <a name="next-steps"></a>Efterföljande moment
* [Arkivera aktivitetsloggen till ett lagringskonto](../../azure-monitor/platform/archive-activity-log.md)
* [Läs översikten över Azure Activity-loggen](../../azure-monitor/platform/platform-logs-overview.md)
* [Ställ in en avisering baserat på en aktivitetslogghändelse](../../azure-monitor/platform/alerts-log-webhook.md)



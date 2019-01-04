---
title: Stream Azure-övervakningsdata till Event Hubs
description: Lär dig mer om att strömma alla dina Azure övervakningsdata till en händelsehubb för att hämta data till en partner SIEM eller analysverktyg.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: a39d497c90f49f8699b9d27be175e501973804c5
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811519"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Stream Azure-övervakningsdata till en händelsehubb för användning av något externt verktyg

Azure Monitor innehåller en enda pipeline för att få åtkomst till alla övervakningsdata från Azure-miljön, så att du kan enkelt konfigurera partner SIEM och övervakningsverktyg för att använda dessa data. Den här artikeln beskriver steg för steg genom att ställa in olika nivåer av data från Azure-miljön som ska skickas till en enda Event Hubs-namnområde eller händelse hubb, där den kan samlas in av något externt verktyg.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

## <a name="what-data-can-i-send-into-an-event-hub"></a>Vilka data som kan skicka till en event hub?

Det finns flera ”nivåer” för övervakning av data inom Azure-miljön, och metoden för att komma åt data från varje nivå varierar något. Dessa nivåer kan normalt beskrivas som:

- **Program övervakade data:** Data om prestanda och funktioner i koden som du har skrivit och körs på Azure. Exempel på program övervakningsdata är prestandaspårningar, programloggar och telemetri för användaren. Programövervakning data samlas vanligtvis på något av följande sätt:
  - Genom att instrumentera din kod med ett SDK som den [Application Insights SDK](../../application-insights/app-insights-overview.md).
  - Genom att köra en övervakningsagent som lyssnar efter nya program loggar in på datorn köra ditt program, till exempel den [Windows Azure-Diagnostikagenten](./../../azure-monitor/platform/diagnostics-extension-overview.md) eller [Linux Azure-Diagnostikagenten](../../virtual-machines/extensions/diagnostics-linux.md).
- **Övervakningsdata för Gästoperativsystem:** Information om operativsystemet där programmet körs. Exempel på övervakningsdata för gäst-OS är Linux syslog eller Windows-systemhändelser. Om du vill samla in den här typen av data, måste du installera en agent som den [Windows Azure-Diagnostikagenten](./../../azure-monitor/platform/diagnostics-extension-overview.md) eller [Linux Azure-Diagnostikagenten](../../virtual-machines/extensions/diagnostics-linux.md).
- **Azure-resurs övervakning av data:** Information om driften av en Azure-resurs. För vissa typer av Azure-resurser, till exempel virtuella datorer, finns ett gäst-OS och programmen för att övervaka inuti den Azure-tjänsten. För andra Azure-resurser, till exempel Nätverkssäkerhetsgrupperna, är resurs-övervakningsdata den högsta nivån av tillgängliga data (eftersom det finns ingen gäst-OS eller program som körs i dessa resurser). Dessa data kan samlas in med [resursdiagnostikinställningar](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
- **Azure-prenumeration övervakning av data:** Data om driften och hanteringen av en Azure-prenumeration, samt data om klientens hälsotillstånd och driften av Azure själva. Den [aktivitetsloggen](./../../azure-monitor/platform/activity-logs-overview.md) innehåller de flesta prenumerationen övervakning av data, till exempel service health incidenter och Azure Resource Manager granskningar. Du kan samla in dessa data med en profil för loggen.
- **Azure-klient övervakning av data:** Information om driften av Azure på klientnivå-tjänster, till exempel Azure Active Directory. Azure Active Directory revisioner och inloggningar är exempel på klient övervakningsdata. Dessa data kan samlas in med hjälp av en diagnostikinställning för klienten.

Data från valfri nivå kan skickas till en händelsehubb där det kan användas i ett partner-verktyg. I nästa avsnitt beskrivs hur du kan konfigurera data från varje nivå strömmas till en händelsehubb. Förutsätter vi att du redan har tillgångar på den nivån som ska övervakas.

## <a name="set-up-an-event-hubs-namespace"></a>Konfigurera ett namnområde för Event Hubs

Innan du börjar måste du [skapa ett Event Hubs-namnområde och en händelsehubb](../../event-hubs/event-hubs-create.md). Den här namnområde och en händelsehubb är målet för alla dina övervakningsdata. Ett namnområde för Event Hubs är en logisk gruppering av händelsehubbar som delar samma åtkomstprincip, mycket som en storage-konto har enskilda blobbar i det lagringskontot. Lägg märke till några få information om event hubs-namnområde och händelsehubbar som du skapar:
* Vi rekommenderar att du använder en Standard Event Hubs-namnområdet.
* Vanligtvis krävs bara en genomflödesenhet. Om du vill skala upp när dina log användning ökar du alltid manuellt öka antalet dataflödesenheter för namnområdet senare eller aktivera automatisk inflationen.
* Antal throughput units kan du öka skala dataflöde för event hubs. Antalet partitioner kan du parallellisera förbrukning bland många konsumenter. En partition kan göra upp till 20MBps eller cirka 20 000 meddelanden per sekund. Beroende på vilket verktyg som använder data kanske eller kanske inte stöder förbrukar från flera partitioner. Om du inte vet om antalet partitioner för att ange rekommenderar vi att börja med fyra partitioner.
* Vi rekommenderar att du ställer in kvarhållning av meddelanden på din event hub till 7 dagar. Om ditt verktyg för konsumerande stängs av i mer än en dag, Detta säkerställer att verktyget kan ta vid där den slutade (för händelser upp till 7 dagar).
* Vi rekommenderar att du använder förinställd konsumentgrupp för din händelsehubb. Det finns inget behov att skapa andra konsumentgrupper eller använda en separat konsumentgrupp om du planerar att ha två olika verktyg som använder samma data från samma event hub.
* För Azure-aktivitetsloggen kan du välja ett namnområde för Event Hubs och Azure Monitor skapar en händelsehubb i namnområdet kallas ”insights-logs-operationallogs”. För andra loggtyper av kan du antingen välja en befintlig händelsehubb (så att du kan återanvända samma insights-logs-operationallogs event hub) eller så har Azure Monitor skapar en event hub per loggkategori.
* Vanligtvis måste du öppna port 5671 och 5672 på den dator som använder data från händelsehubben.

Se även de [Azure Event Hubs vanliga frågor och svar](../../event-hubs/event-hubs-faq.md).

## <a name="azure-tenant-monitoring-data"></a>Azure-klient övervakningsdata

Azure-klient övervakningsdata är för närvarande endast tillgängliga för Azure Active Directory. Du kan använda data från [Azure Active Directory-rapportering](../../active-directory/reports-monitoring/overview-reports.md), som innehåller historik över inloggning aktivitet och granska spårning av ändringar som gjorts i en viss klient.

### <a name="azure-active-directory-data"></a>Azure Active Directory-data

Om du vill skicka data från Azure Active Directory-loggen i ett Event Hubs-namnområde måste konfigurera du en diagnostikinställning för klient på din AAD-klient. [Den här guiden](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) att ställa in en diagnostikinställning för klienten.

## <a name="azure-subscription-monitoring-data"></a>Azure-prenumeration övervakningsdata

Azure-prenumeration övervakningsdata är tillgängliga i den [Azure-aktivitetsloggen](./../../azure-monitor/platform/activity-logs-overview.md). Innehåller skapa, uppdatera och ta bort från Resource Manager, ändringar i [Azure tjänstehälsa](../../service-health/service-health-overview.md) som kan påverka resurser i din prenumeration, de [resurshälsa](../../service-health/resource-health-overview.md) tillstånd övergångar och flera andra typer av händelser på prenumerationsnivå. [Den här artikeln beskriver alla typer av händelser som visas i Azure-aktivitetsloggen](./../../azure-monitor/platform/activity-log-schema.md).

### <a name="activity-log-data"></a>Aktivitetsloggdata

Om du vill skicka data från Azure-aktivitetsloggen till Event Hubs-namnområdet, ställer du in en logg för din prenumeration. [Den här guiden](./activity-logs-stream-event-hubs.md) att ställa in en logg-profil på din prenumeration. Göra detta en gång per prenumeration som du vill övervaka.

> [!TIP]
> En logg-profil kan för närvarande du bara välja ett namnområde för Event Hubs, där en händelsehubb skapas med de namnet ”insights-operational-logs”. Det går inte ännu att ange ditt eget namn på händelsehubb i en logg-profil.

## <a name="azure-resource-metrics-and-diagnostics-logs"></a>Azure-resurs mått och diagnostik för loggar

Azure-resurser genererar två typer av övervakning av data:
1. [Resursdiagnostikloggar](./../../azure-monitor/platform/diagnostic-logs-overview.md)
2. [Mått](../../azure-monitor/platform/data-collection.md)

Båda typerna av data skickas till en händelsehubb med en resursdiagnostikinställning. [Den här guiden](./../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) att ställa in en resursdiagnostikinställning på en viss resurs. Ange en resursdiagnostikinställning för varje resurs som du vill samla in loggar.

> [!TIP]
> Du kan använda Azure Policy för att säkerställa att alla resurser inom ett visst omfång alltid är konfigurerad med en diagnostikinställning [med effekten DeployIfNotExists i principregeln](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="guest-os-data"></a>Gäst-OS-data

Du måste installera en agent för att skicka guest OS övervakningsdata i en händelsehubb. För Windows eller Linux anger du de data som du vill ska skickas till event hub och event hub som data ska skickas i en konfigurationsfil och skicka konfigurationsfilen till agenten som körs på den virtuella datorn.

### <a name="linux-data"></a>Linux-data

Den [Linux Azure diagnostikagenten](../../virtual-machines/extensions/diagnostics-linux.md) kan användas för att skicka övervakningsdata från en Linux-dator till en händelsehubb. Gör detta genom att lägga till event hub som en mottagare i din LAD skyddade inställningar för konfigurationsfiler JSON. [Se den här artikeln om du vill veta mer om att lägga till event hub-mottagare i din Linux Azure diagnostikagenten](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Du kan inte konfigurera strömning av gäst-OS övervakade data till en händelsehubb i portalen. I stället måste du manuellt redigera konfigurationsfilen.

### <a name="windows-data"></a>Windows-data

Den [Windows Azure Diagnostics-agenten](./../../azure-monitor/platform/diagnostics-extension-overview.md) kan användas för att skicka övervakningsdata från en Windows-dator till en händelsehubb. Gör detta genom att lägga till event hub som en mottagare i din privateConfig-avsnittet i konfigurationsfilen WAD. [Se den här artikeln om du vill veta mer om att lägga till event hub-mottagare i din Windows Azure Diagnostics-agenten](./../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

> [!NOTE]
> Du kan inte konfigurera strömning av gäst-OS övervakade data till en händelsehubb i portalen. I stället måste du manuellt redigera konfigurationsfilen.

## <a name="application-monitoring-data"></a>Program övervakade data

Programmet övervakningsdata kräver att koden är utrustade med ett SDK, så det finns en allmänna lösning till routning program övervakning av data till en händelsehubb i Azure. Dock [Azure Application Insights](../../application-insights/app-insights-overview.md) är en tjänst som kan användas för att samla in data för Azure på programnivå. Om du använder Application Insights kan du strömma övervakningsdata till en händelsehubb genom att göra följande:

1. [Konfigurera löpande export](../../azure-monitor/app/export-telemetry.md) av Application Insights-data till ett lagringskonto.

2. Konfigurera en timerutlöst Logikapp som [hämtar data från blob storage](../../connectors/connectors-create-api-azureblobstorage.md#add-action) och [skickas som ett meddelande till händelsehubben](../../connectors/connectors-create-api-azure-event-hubs.md#add-action).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Vad kan jag göra med övervakningsdata som skickas till min event hub?

Routning övervakningsdata till en event hub med Azure Monitor kan du lätt kan integrera med partner SIEM och övervakningsverktyg. De flesta verktygen kräver event hub-anslutningssträngen och vissa behörigheter för din Azure-prenumeration för att läsa data från händelsehubben. Här är en ofullständig lista över verktyg med Azure Monitor-integrering:

* **IBM QRadar** – The DSM för Microsoft Azure och Microsoft Azure Event Hub-protokollet är tillgängliga för nedladdning från [supportwebbplatsen IBM](http://www.ibm.com/support). Du kan [lära dig mer om integrering med Azure här](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** -det finns två sätt beroende på din Splunk-konfiguration:
    1. [Azure Monitor-tillägget för Splunk](https://splunkbase.splunk.com/app/3534/) är tillgängligt i Splunkbase och ett projekt med öppen källkod. [Dokumentationen är här](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Om du inte kan installera ett tillägg i din instans av Splunk (t.ex.) Om en proxy eller som körs på Splunk molnet), du kan vidarebefordra dessa händelser till Splunk HTTP Event Collector med [den här funktionen som utlöses av nya meddelanden i event hub](https://github.com/Microsoft/AzureFunctionforSplunkVS).
* **SumoLogic** -instruktioner för att konfigurera SumoLogic för att använda data från en händelsehubb är [tillgänglig här](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)
* **ArcSight** -smart ArcSight Azure Event Hub-anslutningsappen är tillgänglig som en del av [ArcSight smart connector samlingen här](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852).
* **Syslog-servern** – om du vill strömma Azure Monitor-data direkt till en syslog-server, du kan ta en titt [denna GitHub-lagringsplats](https://github.com/miguelangelopereira/azuremonitor2syslog/).

## <a name="next-steps"></a>Nästa steg
* [Arkivera aktivitetsloggen till ett lagringskonto](../../azure-monitor/platform/archive-activity-log.md)
* [Läs en översikt över Azure-aktivitetsloggen](../../azure-monitor/platform/activity-logs-overview.md)
* [Konfigurera en avisering baserat på en händelse i aktivitetsloggen](../../azure-monitor/platform/alerts-log-webhook.md)


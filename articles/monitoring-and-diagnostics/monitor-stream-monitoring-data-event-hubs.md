---
title: "Strömma Azure övervakningsdata i Händelsehubbar | Microsoft Docs"
description: "Lär dig mer om att strömma alla Azure övervakningsdata till en händelsehubb för att hämta data till en partner SIEM eller analytics-verktyget."
author: johnkemnetz
manager: robb
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/05/2018
ms.author: johnkem
ms.openlocfilehash: 1b1c50f106be8848fb1f32deefa6cb9acb7a298a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Dataströmmen Azure övervakningsdata till en händelsehubb för användning av ett externt verktyg

Övervakare för Azure tillhandahåller en enkel rörledning för att få åtkomst till alla övervakningsdata från Azure-miljön, vilket gör att du enkelt konfigurera partner SIEM och övervakningsverktyg för att använda data. Den här artikeln vägleder genom att ställa in olika nivåer av data från Azure-miljön för att skickas till en enda Händelsehubbar namnområde eller event hub där den kan samlas in av ett externt verktyg.

## <a name="what-data-can-i-send-into-an-event-hub"></a>Vilka data som kan skicka till en händelsehubb? 

Det finns flera 'nivåer' övervakningsdata i Azure-miljön, och metoden att komma åt data från varje nivå varierar något. Dessa nivåer kan normalt beskrivas som:

- **Programmet övervakningsdata:** Data om prestanda och funktionerna i den kod som du har skrivit och körs på Azure. Exempel på program övervakningsdata är prestanda spårningar och programloggarna telemetri för användaren. Programövervakning data samlas vanligtvis på något av följande sätt:
  - Av instrumentering koden med en SDK som den [Application Insights SDK](../application-insights/app-insights-overview.md).
  - Genom att köra en övervakningsagenten som lyssnar efter nya program som loggar in på datorn kör ditt program som den [Windows Azure-Diagnostikagenten](./azure-diagnostics.md) eller [Linux Azure Diagnostikagenten](../virtual-machines/linux/diagnostic-extension.md).
- **Gästoperativsystem övervakningsdata:** information om operativsystem som programmet körs. Exempel på övervakningsdata för gäst-OS är Linux syslog eller Windows-systemhändelser. Om du vill samla in den här typen av data, måste du installera en agent som den [Windows Azure-Diagnostikagenten](./azure-diagnostics.md) eller [Linux Azure Diagnostikagenten](../virtual-machines/linux/diagnostic-extension.md).
- **Azure-resurs övervakningsdata:** Data om handhavandet av en Azure-resurs. För vissa typer av Azure-resurs, till exempel virtuella datorer, finns ett operativsystem och program för att övervaka inuti den Azure-tjänsten. För andra Azure-resurser, till exempel Nätverkssäkerhetsgrupper, är resursen övervakningsdata den högsta nivån i data är tillgängliga (eftersom det finns ingen operativsystem eller program som körs i resurserna). Dessa data kan samlas in med hjälp av [diagnostiska resursinställningar](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
- **Azure-plattformen övervakningsdata:** Data om drift och hantering av en Azure-prenumeration eller klient, samt data om klientens hälsotillstånd och drift av Azure sig själv. Den [aktivitetsloggen](./monitoring-overview-activity-logs.md), inklusive data i hälsa och Active Directory granskningar är exempel på plattformen övervakningsdata. Dessa data kan samlas in med hjälp av diagnostikinställningar samt.

Data från alla skikt kan skickas till en händelsehubb, där den kan användas i en partner-verktyget. I nästa avsnitt beskrivs hur du konfigurerar data från varje nivå strömmas till en händelsehubb. Anvisningarna förutsätter att du redan har tillgångar på nivån som ska övervakas.

## <a name="set-up-an-event-hubs-namespace"></a>Skapa ett namnområde för Händelsehubbar

Innan du börjar måste du [skapar en Händelsehubbar namnområde och händelsen hubb](../event-hubs/event-hubs-create.md). Det här namnområdet och händelsen navet är målet för alla övervakningsdata. Ett namnområde för Händelsehubbar är en logisk gruppering av händelsehubbar som delar samma åtkomstprincip, mycket har kontot som en lagring enskilda blobbar i detta lagringskonto. Observera några information om event hubs namnområde och händelsehubbar som du skapar:
* Vi rekommenderar att du använder en Standard Händelsehubbar namnrymd.
* Vanligtvis krävs endast en genomflödesenhet. Om du behöver skala upp som din logg Minnesanvändningen ökar du alltid manuellt öka antalet genomflödesenheter för namnområdet senare eller aktivera automatisk inflation.
* Antalet genomflödesenheter kan du öka genomflödet skala för din händelsehubbar. Antalet partitioner kan du parallelize förbrukning över många konsumenter. En partition kan du göra upp till 20MBps eller cirka 20 000 meddelanden per sekund. Beroende på verktyget förbrukar data kan eller stöder inte förbrukar från flera partitioner. Om du inte vet om antalet partitioner för att ange rekommenderar vi börjar med fyra partitioner.
* Vi rekommenderar att du ställer in meddelandet kvarhållning på din händelsehubb till 7 dagar. Om din användning verktyget kraschar för mer än en dag, Detta säkerställer att verktyget kan ta vid där den slutade (för händelser upp till 7 dagar).
* Vi rekommenderar att du använder förinställd konsumentgrupp för din händelsehubb. Det finns inget behov av att skapa andra konsumentgrupper eller använda en separat konsumentgrupp om du planerar att ha två olika verktyg som använder samma data från samma event hub.
* För Azure-aktivitetsloggen du väljer ett namnområde för Event Hubs och Azure övervakaren skapar en händelsehubb inom det namnområde som kallas ”insikter-loggar-operationallogs”. För andra typer av loggen kan du antingen välja en befintlig händelsehubb (så att du kan återanvända samma insikter-loggar-operationallogs händelsehubben) eller Azure övervakaren skapar en händelsehubb per loggen kategori.
* Vanligtvis måste du öppna port 5671 och 5672 på datorn som använder data från event hub.

Se även information om den [Azure Event Hubs vanliga frågor och svar](../event-hubs/event-hubs-faq.md).

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>Hur ställer jag in Azure-plattformen övervakningsdata strömmas till en händelsehubb?

Azure-plattformen övervakningsdata kommer från två huvudsakliga källor:
1. Den [Azure aktivitetsloggen](./monitoring-overview-activity-logs.md), som innehåller skapa, uppdatera och ta bort från hanteraren för filserverresurser, ändringar i [Azure tjänstens hälsa](../service-health/service-health-overview.md) som kan påverka resurser i din prenumeration, [resurshälsa](../service-health/resource-health-overview.md) tillståndsövergångar och flera andra typer av prenumerationsnivån händelser. [Den här artikeln beskrivs alla typer av händelser som visas i Azure-aktivitetsloggen](./monitoring-activity-log-schema.md).
2. [Azure Active Directory reporting](../active-directory/active-directory-reporting-azure-portal.md), som innehåller en historik över inloggning aktivitet och granska spår efter ändringar som görs i en viss klient. Det går inte ännu till dataströmmen Azure Active Directory-data i en händelsehubb.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Loggdata för dataströmmen Azure aktivitet i en händelsehubb

För att skicka data från Azure-aktivitetsloggen i ett namnområde för Händelsehubbar, ställer du in en loggen på din prenumeration. [Följ den här guiden](./monitoring-stream-activity-logs-event-hubs.md) att ställa in en logg profil på din prenumeration. Gör en gång per prenumeration som du vill övervaka.

> [!TIP]
> En logg-profil kan för närvarande du bara välja ett namnområde som Händelsehubbar som en händelsehubb skapas med namnet 'insikter-operativa-loggar.' Det ännu inte möjligt att ange egna händelsehubbens namn i en profil för loggen.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Hur ställer jag in Azure-resurs övervakningsdata strömmas till en händelsehubb?

Azure-resurser Generera två typer av övervakningsdata:
1. [Resursen diagnostikloggar](./monitoring-overview-of-diagnostic-logs.md)
2. [Mått](monitoring-overview-metrics.md)

Båda typerna av data skickas till en händelsehubb med hjälp av en diagnostikinställningen för resursen. [Följ den här guiden](./monitoring-stream-diagnostic-logs-to-event-hubs.md) att ställa in en diagnostikinställningen för resursen på en viss resurs. Ange en resurs diagnostikinställningen för varje resurs som du vill samla in loggar.

> [!TIP]
> Du kan använda Azure för att se till att alla resurser inom ett visst omfång alltid är konfigurerad med en diagnostikinställningen [med DeployIfNotExists effekt i principregeln](../azure-policy/policy-definition.md#policy-rule). Idag stöds DeployIfNotExists bara på inbyggda principer.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>Hur ställer jag in övervakningsdata för gäst-OS strömmas till en händelsehubb?

Du måste installera en agent för att skicka gäst OS övervakningsdata i en händelsehubb. Ange de data som ska skickas till händelsehubben samt händelsehubben som data ska skickas i en konfigurationsfil och skicka konfigurationsfilen till agenten som körs på den virtuella datorn för Windows eller Linux.

### <a name="stream-linux-data-to-an-event-hub"></a>Dataströmmen Linux data till en händelsehubb

Den [Linux Azure diagnostikagenten](../virtual-machines/linux/diagnostic-extension.md) kan användas för att skicka övervakningsdata från en Linux-dator till en händelsehubb. Gör detta genom att lägga till händelsehubben som en mottagare i din LAD konfigurationsinställningar filen skyddas JSON. [Finns den här artikeln om du vill veta mer om att lägga till hubben händelsemottagaren till din Linux Azure diagnostikagenten](../virtual-machines/linux/diagnostic-extension.md#protected-settings).

> [!NOTE]
> Du kan inte konfigurera strömning av gäst OS övervakningsdata till en händelsehubb i portalen. I stället måste du manuellt redigera konfigurationsfilen.

### <a name="stream-windows-data-to-an-event-hub"></a>Dataströmmen Windows data till en händelsehubb

Den [Windows Azure diagnostikagenten](./azure-diagnostics.md) kan användas för att skicka övervakningsdata från en Windows-dator till en händelsehubb. Gör detta genom att lägga till händelsehubben som en mottagare i din privateConfig-avsnittet i konfigurationsfilen BOMULLSTUSS. [Finns den här artikeln om du vill veta mer om att lägga till hubben händelsemottagaren din Windows Azure diagnostikagenten](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> Du kan inte konfigurera strömning av gäst OS övervakningsdata till en händelsehubb i portalen. I stället måste du manuellt redigera konfigurationsfilen.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>Hur ställer jag in programmet övervakningsdata strömmas till händelsehubb?

Programmet övervakningsdata kräver att din kod är försett med en SDK, så att det inte finns en allmänna lösning till routning program övervakningsdata till en händelsehubb i Azure. Dock [Azure Application Insights](../application-insights/app-insights-overview.md) är en tjänst som kan användas för att samla in data om Azure på programnivå. Om du använder Application Insights strömma du övervakningsdata till en händelsehubb genom att göra följande:

1. [Ställ in löpande export](../application-insights/app-insights-export-telemetry.md) Application Insights-data till ett lagringskonto.

2. Ställ in en timer utlöst Logikapp som [hämtar data från blob storage](../connectors/connectors-create-api-azureblobstorage.md#use-an-action) och [skickar det som ett meddelande till händelsehubben](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Vad kan jag göra med övervakningsdata som skickas till min händelsehubb?

Routning övervakningsdata till en händelsehubb med Azure-Monitor kan du lätt kan integrera med partner SIEM och övervakningsverktyg. De flesta verktygen kräver event hub anslutningssträngen och vissa behörighet till din Azure-prenumeration för att läsa data från event hub. Här är en ofullständig lista över verktyg med Azure-Monitor-integrering:

* **IBM QRadar** -på Microsoft Azure DSM och Microsoft Azure Event Hub Protocol är tillgängliga för nedladdning från [supportwebbplatsen IBM](http://www.ibm.com/support). Du kan [mer information om integreringen med Azure här](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** -beroende på inställningarna Splunk det finns två sätt:
    1. [Azure-Monitor-tillägget för Splunk](https://splunkbase.splunk.com/app/3534/) finns i Splunkbase och ett projekt med öppen källkod. [Dokumentation är här](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Om du inte installerar ett tillägg i din instans av Splunk (t.ex. Om en proxy eller körs på Splunk molnet), du kan vidarebefordra dessa händelser till Splunk HTTP Event Collector med [denna funktion som utlöses av nya meddelanden i händelsehubben](https://github.com/sebastus/AzureFunctionForSplunkVS).
* **SumoLogic** -instruktioner för att ställa in SumoLogic för att använda data från en händelsehubb [tillgänglig här](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)

## <a name="next-steps"></a>Nästa steg
* [Arkivera aktivitetsloggen till ett lagringskonto](monitoring-archive-activity-log.md)
* [Läs översikt över Azure-aktivitetsloggen](monitoring-overview-activity-logs.md)
* [Konfigurera en avisering baserat på en aktivitet händelselogg](insights-auditlog-to-webhook-email.md)


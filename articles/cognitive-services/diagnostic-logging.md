---
title: Diagnostikloggning
titleSuffix: Azure Cognitive Services
description: Den här guiden innehåller steg-för-steg-instruktioner för att aktivera diagnostikloggning för en Azure Cognitive Service. Dessa loggar ger omfattande, frekventa data om driften av en resurs som används för problemidentifiering och felsökning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 539a35f170b2ee0c94762a30ed9376ca4a416210
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71827902"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Aktivera diagnostikloggning för Azure Cognitive Services

Den här guiden innehåller steg-för-steg-instruktioner för att aktivera diagnostikloggning för en Azure Cognitive Service. Dessa loggar ger omfattande, frekventa data om driften av en resurs som används för problemidentifiering och felsökning. Innan du fortsätter måste du ha ett Azure-konto med en prenumeration på minst en Cognitive Service, till exempel [Bing Web Search,](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview) [Speech Services](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)eller [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Krav

Om du vill aktivera diagnostikloggning måste du lagra loggdata någonstans. Den här självstudien använder Azure Storage och Log Analytics.

* [Azure storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) - Behåller diagnostikloggar för principgranskning, statisk analys eller säkerhetskopiering. Lagringskontot behöver inte vara i samma prenumeration som resursen som avger loggar så länge som användaren som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) - Ett flexibelt loggsöknings- och analysverktyg som möjliggör analys av råloggar som genereras av en Azure-resurs.

> [!NOTE]
> Ytterligare konfigurationsalternativ finns tillgängliga. Mer information finns i [Samla in och använda loggdata från dina Azure-resurser](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Aktivera insamling av diagnostikloggar  

Låt oss börja med att aktivera diagnostikloggning med Azure-portalen.

> [!NOTE]
> Om du vill aktivera den här funktionen med PowerShell eller Azure CLI använder du instruktionerna i [Samla in och använda loggdata från dina Azure-resurser](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

1. Navigera till Azure Portal. Leta sedan reda på och välj en Cognitive Services-resurs. Prenumeration på Bing Web Search.   
2. Leta sedan upp **övervakning** på menyn till vänster och välj **Diagnostikinställningar**. Den här skärmen innehåller alla tidigare skapade diagnostikinställningar för den här resursen.
3. Om det finns en tidigare skapad resurs som du vill använda kan du markera den nu. Annars väljer du **+ Lägg till diagnostikinställning**.
4. Ange ett namn för inställningen. Välj sedan **Arkiv till ett lagringskonto** och **Skicka för att logga Analytics**.
5. När du uppmanas att konfigurera väljer du det lagringskonto och OMS-arbetsyta som du vill använda för att lagra diagnostikloggar för dig. **Om**du inte har ett lagringskonto eller ENS-arbetsyta följer du anvisningarna för att skapa ett.
6. Välj **Granskning,** **RequestResponse**och **AllMetrics**. Ange sedan kvarhållningsperioden för dina diagnostikloggdata. Om en bevarandeprincip är noll lagras händelser för den loggkategorin på obestämd tid.
7. Klicka på **Spara**.

Det kan ta upp till två timmar innan loggningsdata är tillgängliga för att fråga och analysera. Så oroa dig inte om du inte ser något direkt.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Visa och exportera diagnostikdata från Azure Storage

Azure Storage är en robust objektlagringslösning som är optimerad för lagring av stora mängder ostrukturerade data. I det här avsnittet får du lära dig att fråga ditt lagringskonto efter totala transaktioner under en 30-dagarsperiod och exportera data till excel.

1. Leta reda på den Azure Storage-resurs som du skapade i det sista avsnittet från Azure-portalen.
2. Leta reda på **Övervakning** på menyn för vänster navigering och välj **Mått**.
3. Använd de tillgängliga listrã¤ringar på ã¤5 ringar fÃ¥ringar fÃ¥er till att konfigurera frågan. I det här exemplet ska vi ange tidsintervallet till **Senaste 30 dagarna** och måttet till **Transaktion**.
4. När frågan är klar visas en visualisering av transaktionen under de senaste 30 dagarna. Om du vill exportera dessa data använder du knappen **Exportera till Excel** högst upp på sidan.

Läs mer om vad du kan göra med diagnostikdata i [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Visa loggar i Log Analytics

Följ dessa instruktioner för att utforska logganalysdata för din resurs.

1. Leta reda på och välj **Logganalys** på menyn för vänster navigering på Azure-portalen.
2. Leta reda på och välj den resurs som du skapade när diagnostik aktiveras.
3. Leta upp och välj **Loggar**under **Allmänt**. Från den här sidan kan du köra frågor mot dina loggar.

### <a name="sample-queries"></a>Exempelfrågor

Här är några grundläggande Kusto frågor som du kan använda för att utforska dina loggdata.

Kör den här frågan för alla diagnostikloggar från Azure Cognitive Services under en angiven tidsperiod:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Kör den här frågan om du vill visa de 10 senaste loggarna:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Kör den här frågan om du vill gruppera åtgärder efter **resurs:**

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Kör den här frågan för att hitta den genomsnittliga tid det tar att utföra en åtgärd:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Kör den här frågan om du vill visa volymen av åtgärder över tid som delas av OperationName med antalet som är lagerplats för varje 10:e plats.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Nästa steg

* Om du vill förstå hur du aktiverar loggning och även de mått och loggkategorier som stöds av de olika Azure-tjänsterna läser du både [översikten över mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) i Microsoft Azure och [översikt över azure-diagnostikloggar.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview)
* Läs de här artiklarna om händelsehubbar:
  * [Vad är Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Kom igång med Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Läs [hämta mått och diagnostikloggar från Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Läs [Förstå loggsökningar i Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).

---
title: Diagnostikloggning
titleSuffix: Azure Cognitive Services
description: Den här guiden innehåller stegvisa instruktioner för att aktivera Diagnostisk loggning för en Azure Cognitive Services. De här loggarna ger omfattande, frekventa data om användningen av en resurs som används för problemidentifiering och felsökning.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: 3be912f053bf206999546678e1e407548af181bf
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657685"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Aktivera Diagnostisk loggning för Azure Cognitive Services

Den här guiden innehåller stegvisa instruktioner för att aktivera Diagnostisk loggning för en Azure Cognitive Services. De här loggarna ger omfattande, frekventa data om användningen av en resurs som används för problemidentifiering och felsökning. Innan du fortsätter måste du ha ett Azure-konto med en prenumeration på minst en Cognitive Service, till exempel [webbsökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [Taltjänster](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview), eller [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Förutsättningar

Om du vill aktivera Diagnostisk loggning, behöver du någonstans att lagra dina loggdata. Den här självstudien används Azure Storage och Log Analytics.

* [Azure storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) -behåller diagnostikloggar för principgranskning, statiska analys eller säkerhetskopiering. Storage-kontot behöver inte finnas i samma prenumeration som resursen loggarna så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics) -flexibla log sökning och analys med verktyget för analys av loggarna som genereras av en Azure-resurs.

> [!NOTE]
> Ytterligare konfigurationsalternativ är tillgängliga. Mer information finns i [samla in och använda loggdata från resurserna i Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Aktivera insamling av diagnostiklogg  

Låt oss börja med att aktivera loggning med Azure portal.

> [!NOTE]
> Om du vill aktivera den här funktionen med hjälp av PowerShell eller Azure CLI, använder du instruktionerna i [samla in och använda loggdata från resurserna i Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#diagnostic-settings).

1. Gå till Azure-portalen. Sedan leta upp och välj en resurs för Cognitive Services. Exempelvis kan din prenumeration på webbsökning i Bing.   
2. Sedan från den vänstra navigeringsmenyn fram **övervakning** och välj **diagnostikinställningar**. Den här sidan innehåller alla tidigare skapade diagnostikinställningar för den här resursen.
3. Om det finns en tidigare skapad resurs som du vill använda, kan du välja den nu. Annars väljer **+ Lägg till diagnostikinställning**.
4. Ange ett namn för inställningen. Välj sedan **arkivet till ett lagringskonto** och **skicka till log Analytics**.
5. När du uppmanas att konfigurera, välja storage-konto och OMS-arbetsyta som du vill använda för att lagra du diagnostikloggar. **Obs!** Om du inte har ett lagringskonto eller OMS-arbetsyta, följer du anvisningarna för att skapa en.
6. Välj **Audit**, **RequestResponse**, och **AllMetrics**. Sedan ställer du in kvarhållningsperioden för din diagnostiklogg data. Om en kvarhållningsprincip har angetts till noll lagras händelser för den log-kategorin på obestämd tid.
7. Klicka på **Spara**.

Det kan ta upp till två timmar innan loggningsdata kan fråga och analysera. Så oroa dig inte om du inte ser något direkt.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Visa och exportera diagnostikdata från Azure Storage

Azure Storage är en robust objektet lagringslösning som är optimerad för att lagra stora mängder Ostrukturerade data. I det här avsnittet får du lära dig att fråga efter ditt storage-konto för totalt antal transaktioner under en 30 dagars period och exportera data till excel.

1. Leta upp den Azure Storage-resurs som du skapade i det sista avsnittet från Azure-portalen.
2. Leta upp från den vänstra navigeringsmenyn **övervakning** och välj **mått**.
3. Använd tillgängliga listrutorna för att konfigurera din fråga. I det här exemplet ska vi ange tidsintervall **de senaste 30 dagarna** och mått som ska **transaktion**.
4. När frågan har slutförts, visas en visualisering av transaktionen under de senaste 30 dagarna. Om du vill exportera dessa data använder den **exportera till Excel** knappen högst upp på sidan.

Mer information om vad du kan göra med diagnostikdata i [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Visa loggar i Log Analytics

Följ dessa instruktioner för att utforska log analytics-data för din resurs.

1. Leta upp från Azure-portalen och välj **Log Analytics** från den vänstra navigeringsmenyn.
2. Leta upp och välj den resurs du skapade när du aktiverar diagnostik.
3. Under **Allmänt**letar du upp och välj **loggar**. Du kan köra frågor mot dina loggar från den här sidan.

### <a name="sample-queries"></a>Exempelfrågor

Här följer några grundläggande Kusto-frågor du kan använda för att utforska dina loggdata.

Kör den här frågan för alla diagnostikloggar från Azure Cognitive Services för en angiven tidsperiod:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Kör den här frågan om du vill se de 10 senaste loggarna:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Kör den här frågan till grupp-aktiviteter med **Resource**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Kör den här frågan för att hitta den genomsnittliga tid det tar för att utföra en åtgärd:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Kör den här frågan om du vill visa mängden åtgärder över tid dela efter OperationName med antal binned för varje 10-tal.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Nästa steg

* Om du vill lära dig mer om att aktivera loggning och mått och loggfiler kategorier som stöds av olika Azure-tjänster kan läsa både den [översikt över mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) i Microsoft Azure och [översikt över Azure diagnostikloggar ](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview) artiklar.
* Läs de här artiklarna om du vill veta mer om event hubs:
  * [Vad är Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Kom igång med Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Läs [hämta mått och diagnostikloggar från Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Läs [förstå loggsökningar i Azure Monitor-loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).

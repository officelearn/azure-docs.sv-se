---
title: Analysera loggar och mått i Azure våren-molnet | Microsoft Docs
description: Lär dig hur du analyserar diagnostikdata i Azure våren Cloud
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038891"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>Analysera loggar och mått med diagnostikinställningar

Med hjälp av diagnostiska funktioner i Azure våren Cloud kan du analysera loggar och mät värden med någon av följande tjänster:

* Analysera dem med Azure Log Analytics, där data skrivs direkt till Azure Log Analytics utan att först behöva skriva data till lagringen.
* Spara dem till ett lagrings konto för granskning eller manuell kontroll. Du kan ange Retentions tid (i dagar).
* Strömma dem till Event Hubs för inmatning av en tjänst från tredje part eller en anpassad analys lösning.

För att komma igång måste du aktivera någon av dessa tjänster för att ta emot data.  Läs [den här självstudien](../azure-monitor/log-query/get-started-portal.md)om du vill veta mer om hur du konfigurerar Log Analytics.  

## <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar

1. Gå till Azure våren Cloud-instansen i Azure Portal.
1. Välj meny alternativet **diagnostikinställningar** .
1. Välj knappen **Lägg till diagnostisk inställning** .
1. Ange ett namn för inställningen och välj var du vill skicka loggarna. Du kan välja en valfri kombination av följande tre alternativ:
    * Arkivera till ett lagringskonto
    * Strömma till en händelsehubb
    * Skicka till Log Analytics

1. Välj vilken logg kategori och vilken mått kategori du vill övervaka och ange kvarhållningsperiod (i dagar). Retentions tiden gäller endast för lagrings kontot.
1. Välj **Spara** för att tillämpa inställningen.

> [!NOTE]
> Det kan finnas upp till 15 minuter mellan när loggar eller mått genereras och när de visas i lagrings konto/Event Hub/Log Analytics.

## <a name="viewing-logs"></a>Visa loggar

### <a name="using-log-analytics"></a>Använda Log Analytics

1. Från Azure Portal väljer du Log Analytics i navigerings menyn till vänster.
1. Välj arbets ytan Log Analytics som du valde när du lägger till diagnostikinställningar.
1. Välj `Logs` för att öppna bladet loggs ökning.
1. Ange en enkel fråga i logg Sök rutan.  Exempel:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Välj `Run` om du vill visa Sök resultatet.
1. Du kan söka i loggarna för det specifika programmet eller instansen genom att ange ett filter villkor:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Läs mer om frågespråket som används i Log Analytics [i den här artikeln](../azure-monitor/log-query/query-language.md)

### <a name="using-logs-and-metrics-in-storage-account"></a>Använda loggar och mått i lagrings kontot

1. Från Azure Portal väljer du lagrings konton i den vänstra navigerings menyn.
1. Välj det lagrings konto som du valde när du lägger till diagnostikinställningar.
1. Välj `Blobs`-post för att öppna bladet BLOB-behållare.
1. Sök efter en behållare med namnet `insights-logs-applicationconsole` för att granska program loggarna.
1. Hitta en behållare med namnet `insights-metrics-pt1m` om du vill granska program mått.

[Läs mer om att skicka diagnostikinformation till ett lagrings konto.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Använda Event Hubs

1. Från Azure Portal väljer du Event Hubs i navigerings menyn till vänster.
1. Leta upp och välj Event Hubs som du valde när du lade till diagnostikinställningar.
1. Välj `Event Hubs` för att öppna bladet Event Hub-lista.
1. Hitta en händelsehubben som kallas `insights-logs-applicationconsole` för att granska program loggarna.
1. Hitta en händelsehubben som kallas `insights-metrics-pt1m` för att granska program mått.

[Läs mer om att skicka diagnostikinformation till en Event Hub.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyzing-logs"></a>Analysera loggar

Azure Log Analytics tillhandahåller Kusto så att du kan skicka frågor till dina loggar för analys.  Läs [Log Analytics själv studie kursen](../azure-monitor/log-query/get-started-portal.md) för en snabb introduktion till att skicka frågor till loggar med Kusto.

Program loggar innehåller viktig information om programmets hälso tillstånd, prestanda och mycket annat.  Nedan visas några enkla frågor som hjälper dig att förstå programmets aktuella och tidigare tillstånd.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Visa program loggar från Azure våren Cloud

Om du vill granska en lista över program loggar från Azure våren-molnet, sorterat efter tid med de senaste loggarna som visas först:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Visa loggar poster som innehåller fel eller undantag

Med den här frågan kan du granska logg poster som nämner ett fel eller undantag.  Resultaten sorteras inte.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Använd den här frågan för att hitta fel eller ändra sökorden för att hitta vissa felkoder eller undantag.  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Visa antalet fel och undantag som rapporter ATS av ditt program under den senaste timmen

Den här frågan skapar ett cirkel diagram som visar antalet fel och undantag som loggats av ditt program under den senaste timmen:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Läs mer om att skicka frågor till program loggar

Azure Monitor ger omfattande stöd för att skicka frågor till program loggar med hjälp av Log Analytics.  Läs mer om den här tjänsten i självstudien om [logg frågor](../azure-monitor/log-query/get-started-queries.md) med hjälp av Azure Monitor. [Översikten över logg frågor i Azure Monitor](../azure-monitor/log-query/log-query-overview.md) ger mer information om hur du skapar frågor för att analysera program loggarna.

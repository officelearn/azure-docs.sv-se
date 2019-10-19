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
ms.openlocfilehash: 955641f3511989baa5bfc3c0fa4d7df7ccbf9bfa
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554578"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analysera loggar och mått med diagnostikinställningar

Med hjälp av diagnostikprogrammet i Azure våren Cloud kan du analysera loggar och mät värden med någon av följande tjänster:

* Använd Azure Log Analytics, där data skrivs direkt utan att behöva skrivas till lagring först.
* Spara dem till ett lagrings konto för granskning eller manuell kontroll. Du kan ange Retentions tid (i dagar).
* Strömma dem till händelsehubben för inmatning av en tjänst från tredje part eller en anpassad analys lösning.

Kom igång genom att aktivera någon av dessa tjänster för att ta emot data. Läs [komma igång med Log Analytics i Azure Monitor](../azure-monitor/log-query/get-started-portal.md)om du vill veta mer om hur du konfigurerar Log Analytics. 

## <a name="configure-diagnostics-settings"></a>Konfigurera diagnostikinställningar

1. I Azure Portal går du till din Azure våren Cloud-instans.
1. Välj alternativet **diagnostikinställningar** och välj sedan **Lägg till diagnostikinställningar**.
1. Ange ett namn för inställningen och välj sedan var du vill skicka loggarna. Du kan välja en valfri kombination av följande tre alternativ:
    * **Arkivera till ett lagrings konto**
    * **Strömma till en Event Hub**
    * **Skicka till Log Analytics**

1. Välj vilken logg kategori och vilken mått kategori du vill övervaka och ange sedan kvarhållningsperiod (i dagar). Retentions tiden gäller endast för lagrings kontot.
1. Välj **Spara**.

> [!NOTE]
> Det kan finnas en lucka i upp till 15 minuter mellan när loggarna eller måtten genereras och när de visas i ditt lagrings konto, Event Hub eller Log Analytics.

## <a name="view-the-logs"></a>Visa loggarna

### <a name="use-log-analytics"></a>Använda Log Analytics

1. I Azure Portal väljer du **Log Analytics**i det vänstra fönstret.
1. Välj den Log Analytics arbets yta som du valde när du lade till diagnostikinställningar.
1. Öppna fönstret **loggs ökning** genom att välja **loggar**.
1. I rutan **loggs** ökning anger du en enkel fråga som:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Om du vill visa Sök resultatet väljer du **Kör**.
1. Du kan söka i loggarna för det specifika programmet eller instansen genom att ange ett filter villkor:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Om du vill veta mer om frågespråket som används i Log Analytics, se [Azure Monitor logg frågor](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Använd ditt lagrings konto 

1. I Azure Portal i det vänstra fönstret väljer du **lagrings konton**.

1. Välj det lagrings konto som du valde när du lade till diagnostikinställningar.
1. Öppna fönstret **BLOB container** genom att välja **blobbar**.
1. Om du vill granska program loggarna söker du efter en behållare med namnet **Insights-logs-applicationconsole**.
1. Om du vill granska program mått söker du efter en behållare med namnet **Insights-Metrics-pt1m**.

Mer information om hur du skickar diagnostikinformation till ett lagrings konto finns i [lagra och Visa diagnostikdata i Azure Storage](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage).

### <a name="use-your-event-hub"></a>Använda händelsehubben

1. I Azure Portal väljer du **Event Hubs**i det vänstra fönstret.

1. Sök efter och välj den händelsehubben som du valde när du lade till diagnostikinställningar.
1. Öppna fönstret **Event Hub List** genom att välja **Event Hubs**.
1. Om du vill granska program loggar kan du söka efter en Event Hub med namnet **Insights-logs-applicationconsole**.
1. Om du vill granska program mått söker du efter ett händelsehubben som kallas **Insights-Metrics-pt1m**.

Mer information om hur du skickar diagnostikinformation till en Event Hub finns i [strömma Azure-diagnostik data i den aktiva sökvägen med Event Hubs](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analysera loggarna

Azure Log Analytics tillhandahåller Kusto så att du kan skicka frågor till dina loggar för analys. En snabb introduktion till att köra frågor mot loggar med hjälp av Kusto finns i [själv studie kursen om Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Program loggar innehåller viktig information om programmets hälso tillstånd, prestanda och mycket annat. I nästa avsnitt finns några enkla frågor som hjälper dig att förstå programmets aktuella och tidigare tillstånd.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Visa program loggar från Azure våren Cloud

Om du vill granska en lista över program loggar från Azure våren Cloud, sorterat efter tid med de senaste loggarna som visas först, kör du följande fråga:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Visa loggar poster som innehåller fel eller undantag

Om du vill granska osorterade logg poster som nämner ett fel eller undantag kör du följande fråga:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Använd den här frågan för att hitta fel eller ändra sökorden för att hitta vissa felkoder eller undantag. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Visa antalet fel och undantag som rapporter ATS av ditt program under den senaste timmen

Kör följande fråga för att skapa ett cirkel diagram som visar antalet fel och undantag som loggats av programmet den senaste timmen:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Läs mer om att skicka frågor till program loggar

Azure Monitor ger omfattande stöd för att skicka frågor till program loggar med hjälp av Log Analytics. Mer information om den här tjänsten finns [i kom igång med logg frågor i Azure Monitor](../azure-monitor/log-query/get-started-queries.md). Mer information om hur du skapar frågor för att analysera dina program loggar finns i [Översikt över logg frågor i Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

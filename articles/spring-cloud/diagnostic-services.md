---
title: Analysera loggar och mått i Azure Spring Cloud | Microsoft-dokument
description: Lär dig hur du analyserar diagnostikdata i Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.openlocfilehash: 544de1b4ac46a58d533f71a46266807a3b93820a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920050"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Analysera loggar och mått med diagnostikinställningar

Med hjälp av diagnostikfunktionen i Azure Spring Cloud kan du analysera loggar och mått med någon av följande tjänster:

* Använd Azure Log Analytics, där data skrivs till Azure Storage. Det finns en fördröjning när du exporterar loggar till Log Analytics.
* Spara loggar till ett lagringskonto för granskning eller manuell inspektion. Du kan ange kvarhållningstiden (i dagar).
* Strömma loggar till din händelsehubb för inmatning av en tjänst från tredje part eller anpassad analyslösning.

Välj den loggkategori och måttkategori som du vill övervaka.

## <a name="logs"></a>Loggar

|Logga | Beskrivning |
|----|----|
| **AnsökanConsole** | Konsollogg över alla kundprogram. | 
| **SystemLoggar** | För närvarande loggar endast [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) i den här kategorin. |

## <a name="metrics"></a>Mått

En fullständig lista över mått finns i [Mått för Fjädra moln](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-concept-metrics#user-metrics-options).

För att komma igång kan du aktivera en av dessa tjänster för att ta emot data. Mer information om hur du konfigurerar Logganalys finns [i Komma igång med Logganalys i Azure Monitor](../azure-monitor/log-query/get-started-portal.md). 

## <a name="configure-diagnostics-settings"></a>Konfigurera diagnostikinställningar

1. Gå till din Azure Spring Cloud-instans i Azure-portalen.
1. Välj alternativet **Inställningar för diagnostik** och välj sedan Inställningen Lägg till **diagnostik**.
1. Ange ett namn för inställningen och välj sedan var du vill skicka loggarna. Du kan välja valfri kombination av följande tre alternativ:
    * **Arkivera till ett lagringskonto**
    * **Strömma till ett händelsenav**
    * **Skicka till Log Analytics**

1. Välj vilken loggkategori och måttkategori du vill övervaka och ange sedan kvarhållningstiden (i dagar). Kvarhållningstiden gäller endast för lagringskontot.
1. Välj **Spara**.

> [!NOTE]
> Det kan finnas en lucka på upp till 15 minuter mellan när loggar eller mått släpps ut och när de visas i ditt lagringskonto, din händelsehubb eller Logganalys.

## <a name="view-the-logs-and-metrics"></a>Visa loggar och mått
Det finns olika metoder för att visa loggar och mått enligt beskrivningen under följande rubriker.

### <a name="use-logs-blade"></a>Använd bladet Loggar

1. Gå till din Azure Spring Cloud-instans i Azure-portalen.
1. Om du vill öppna fönstret **Logga sökning** väljer du **Loggar**.
1. I sökrutan **Logga**
   * Om du vill visa loggar anger du en enkel fråga som:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Om du vill visa mått anger du en enkel fråga som:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Om du vill visa sökresultatet väljer du **Kör**.

### <a name="use-log-analytics"></a>Använda Log Analytics

1. Välj **Logganalys**i den vänstra rutan i Azure-portalen .
1. Välj arbetsytan Log Analytics som du valde när du lade till diagnostikinställningarna.
1. Om du vill öppna fönstret **Logga sökning** väljer du **Loggar**.
1. I sökrutan **Logga**
   * om du vill visa loggar anger du en enkel fråga som:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * om du vill visa mått anger du en enkel fråga som:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Om du vill visa sökresultatet väljer du **Kör**.
1. Du kan söka i loggarna för det specifika programmet eller instansen genom att ange ett filtervillkor:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]  
> `==`är skiftlägeskänslig, men `=~` är det inte.

Mer information om frågespråket som används i Logganalys finns i [Azure Monitor-loggfrågor](../azure-monitor/log-query/query-language.md).

### <a name="use-your-storage-account"></a>Använda ditt lagringskonto 

1. Välj **Lagringskonton**i den vänstra rutan i Azure-portalen .

1. Välj det lagringskonto som du valde när du lade till diagnostikinställningarna.
1. Om du vill öppna fönstret **Blob Container** väljer du **Blobbar**.
1. Om du vill granska programloggar söker du efter en behållare som kallas **insights-logs-applicationconsole**.
1. Om du vill granska programmått söker du efter en behållare som kallas **insights-metrics-pt1m**.

Mer information om hur du skickar diagnostikinformation till ett [lagringskonto](../storage/common/storage-introduction.md)finns i Lagra och visa diagnostikdata i Azure Storage .

### <a name="use-your-event-hub"></a>Använda händelsehubben

1. Välj **Event Hubs**i den vänstra rutan i Azure-portalen .

1. Sök efter och välj den händelsehubb som du valde när du lade till diagnostikinställningarna.
1. Om du vill öppna fönstret **Lista händelsehubben** väljer du **Händelsehubbar**.
1. Om du vill granska programloggar söker du efter en händelsenav som kallas **insights-logs-applicationconsole**.
1. Om du vill granska programmått söker du efter en händelsenav som kallas **insights-metrics-pt1m**.

Mer information om hur du skickar diagnostikinformation till en händelsehubb finns [i Strömmande Azure Diagnostics-data i den aktiva sökvägen med hjälp av Event Hubs](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs).

## <a name="analyze-the-logs"></a>Analysera loggarna

Azure Log Analytics körs med en Kusto-motor så att du kan fråga dina loggar för analys. En snabb introduktion till att fråga loggar med Kusto kan du läsa [log analytics-självstudien](../azure-monitor/log-query/get-started-portal.md).

Programloggar ger viktig information och utförliga loggar om programmets hälsa, prestanda med mera. I nästa avsnitt finns några enkla frågor som hjälper dig att förstå programmets aktuella och tidigare tillstånd.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Visa programloggar från Azure Spring Cloud

Om du vill granska en lista över programloggar från Azure Spring Cloud, sorterade efter tid med de senaste loggarna som visas först, kör du följande fråga:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Visa loggar poster som innehåller fel eller undantag

Om du vill granska osorterade loggposter som nämner ett fel eller ett undantag kör du följande fråga:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Använd den här frågan om du vill hitta fel eller ändra frågetermerna för att hitta specifika felkoder eller undantag. 

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Visa antalet fel och undantag som rapporterats av ditt program under den senaste timmen

Om du vill skapa ett cirkeldiagram som visar antalet fel och undantag som loggats av ditt program under den senaste timmen kör du följande fråga:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Läs mer om att fråga programloggar

Azure Monitor ger omfattande stöd för att fråga programloggar med hjälp av Log Analytics. Mer information om den här tjänsten finns [i Komma igång med loggfrågor i Azure Monitor](../azure-monitor/log-query/get-started-queries.md). Mer information om hur du skapar frågor för att analysera dina programloggar finns [i Översikt över loggfrågor i Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

---
title: Långsamma Query-loggar – Azure Database for MariaDB
description: Beskriver de loggar som är tillgängliga i Azure Database for MariaDB och de tillgängliga parametrarna för att aktivera olika loggnings nivåer.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: b38838c20e4ab18b64cabcb2749ec39163f1b52d
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76515062"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Långsamma Query-loggar i Azure Database for MariaDB
I Azure Database for MariaDB är den långsamma fråge loggen tillgänglig för användare. Åtkomst till transaktions loggen stöds inte. Den långsamma frågans logg kan användas för att identifiera Flask halsar i prestanda för fel sökning.

Mer information om den långsamma frågans logg finns i MariaDB-dokumentationen för [långsam frågekörning](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-slow-query-logs"></a>Komma åt långsamma Query-loggar
Du kan visa och hämta Azure Database for MariaDB långsamma fråge loggar med hjälp av Azure Portal och Azure CLI.

I Azure Portal väljer du Azure Database for MariaDB-servern. Under **övervaknings** rubriken väljer du sidan **Server loggar** .

Mer information om Azure CLI finns i [Konfigurera och komma åt Server loggar med Azure CLI](howto-configure-server-logs-cli.md).

På samma sätt kan du skicka loggfiler till Azure Monitor med hjälp av diagnostikloggar. Se [nedan](concepts-server-logs.md#diagnostic-logs) för mer information.

## <a name="log-retention"></a>Logg kvarhållning
Loggar är tillgängliga i upp till sju dagar från deras skapande. Om den totala storleken på de tillgängliga loggarna överstiger 7 GB, tas de äldsta filerna bort tills utrymmet är tillgängligt.

Loggarna roteras var 24: e timme eller 7 GB, beroende på vilket som kommer först.

## <a name="configure-slow-query-logging"></a>Konfigurera loggning av långsam fråga
Som standard är den långsamma frågans logg inaktive rad. Ange slow_query_log till på för att aktivera det.

Andra parametrar som du kan justera är:

- **long_query_time**: om en fråga tar längre tid än long_query_time (i sekunder) som frågan loggas. Standardvärdet är 10 sekunder.
- **log_slow_admin_statements**: om on innehåller administrativa uttryck som ALTER_TABLE och ANALYZE_TABLE i de instruktioner som skrivs till slow_query_log.
- **log_queries_not_using_indexes**: bestämmer om frågor som inte använder index ska loggas i slow_query_log
- **log_throttle_queries_not_using_indexes**: den här parametern begränsar antalet icke-indexfrågor som kan skrivas till den långsamma fråge loggen. Den här parametern börjar gälla när log_queries_not_using_indexes är inställt på på.
- **log_output**: om "File", tillåter att den långsamma fråge loggen skrivs till både den lokala serverns lagrings plats och för att Azure Monitor diagnostikloggar. Om du använder "ingen" skrivs den långsamma fråge loggen bara till Azure Monitor-diagnostikloggar. 

> [!IMPORTANT]
> Om dina tabeller inte är indexerade kan inställning av `log_queries_not_using_indexes` och `log_throttle_queries_not_using_indexes` parametrar till på påverka MariaDB prestanda eftersom alla frågor som körs mot dessa icke-indexerade tabeller skrivs till den långsamma frågans logg.<br><br>
> Om du planerar att logga långsamma förfrågningar under en längre tid rekommenderar vi att du anger `log_output` till "ingen". Om detta är inställt på "File" skrivs dessa loggar till den lokala serverns lagring och kan påverka MariaDB-prestanda. 

En fullständig beskrivning av logg parametrarna för långsamma frågor finns i dokumentationen om MariaDB [långsam logg dokumentation](https://mariadb.com/kb/en/library/slow-query-log-overview/) .

## <a name="diagnostic-logs"></a>Diagnostikloggar
Azure Database for MariaDB är integrerat med Azure Monitor diagnostikloggar. När du har aktiverat långsamma Query-loggar på MariaDB-servern kan du välja att de ska skickas till Azure Monitor loggar, Event Hubs eller Azure Storage. Om du vill veta mer om hur du aktiverar diagnostikloggar kan du läsa avsnittet så här i [dokumentationen för diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md).

> [!IMPORTANT]
> Den här Diagnostic-funktionen för Server loggar är bara tillgänglig i Generell användning och minnesoptimerade [pris nivåer](concepts-pricing-tiers.md).

I följande tabell beskrivs vad som finns i varje logg. Beroende på utmatnings metoden kan de fält som ingår och i vilken ordning de visas variera.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Namnet på servern |
| `start_time_t` [UTC] | Tiden då frågan började |
| `query_time_s` | Total tid som frågan tog att köra |
| `lock_time_s` | Total tid då frågan låstes |
| `user_host_s` | Användarnamn |
| `rows_sent_s` | Antal rader som skickats |
| `rows_examined_s` | Antal rader som granskas |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Infoga ID |
| `sql_text_s` | Fullständig fråga |
| `server_id_s` | Server-ID |
| `thread_id_s` | Tråd-id |
| `\_ResourceId` | Resurs-URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analysera loggar i Azure Monitor loggar

När dina långsamma fråge loggar är skickas för att Azure Monitor loggar via diagnostikloggar kan du utföra ytterligare analyser av dina långsamma frågor. Nedan visas några exempel frågor som hjälper dig att komma igång. Se till att uppdatera följande med Server namnet.

- Frågor som är längre än 10 sekunder på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Lista de 5 vanligaste frågorna på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Sammanfatta långsamma frågor efter minsta, högsta, genomsnittliga och standard avvikelse på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Grafa den långsamma frågans distribution på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Visa frågor som är längre än 10 sekunder på alla MariaDB-servrar med diagnostiska loggar aktiverade

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Efterföljande moment
- [Så här konfigurerar du långsamma Query-loggar från Azure Portal](howto-configure-server-logs-portal.md)
- [Så här konfigurerar du långsamma Query-loggar från Azure CLI](howto-configure-server-logs-cli.md)

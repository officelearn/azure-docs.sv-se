---
title: Långsamma frågeloggar - Azure Database för MySQL
description: Beskriver de långsamma frågeloggarna som är tillgängliga i Azure Database for MySQL och de tillgängliga parametrarna för att aktivera olika loggningsnivåer.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 9a3a58cab2d9673a4660967e3a11d7f88900e718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269437"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Långsamma frågeloggar i Azure Database för MySQL
I Azure Database for MySQL är den långsamma frågeloggen tillgänglig för användare. Åtkomst till transaktionsloggen stöds inte. Den långsamma frågeloggen kan användas för att identifiera flaskhalsar för prestanda för felsökning.

Mer information om den långsamma frågeloggen mysQL finns i avsnittet Om den [långsamma frågeloggen](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)för MySQL.

## <a name="access-slow-query-logs"></a>Komma åt långsamma frågeloggar
Du kan lista och ladda ned Azure Database for MySQL långsam frågeloggar med Azure-portalen och Azure CLI.

Välj din Azure-databas för MySQL-server i Azure-portalen. Välj sidan **Serverloggar under** rubriken **Övervakning.**

Mer information om Azure CLI finns i [Konfigurera och komma åt långsamma frågeloggar med Azure CLI](howto-configure-server-logs-in-cli.md).

På samma sätt kan du leda loggarna till Azure Monitor med hjälp av diagnostikloggar. Se [nedan](concepts-server-logs.md#diagnostic-logs) för mer information.

## <a name="log-retention"></a>Loggkvarhållning
Loggar är tillgängliga i upp till sju dagar från deras skapelse. Om den totala storleken på de tillgängliga loggarna överstiger 7 GB tas de äldsta filerna bort tills det finns ledigt. 

Loggarna roteras var 24:e timme eller 7 GB, beroende på vilket som inträffar först.

## <a name="configure-slow-query-logging"></a>Konfigurera långsam frågeloggning 
Som standard är den långsamma frågeloggen inaktiverad. Om du vill aktivera den ställer du in slow_query_log på PÅ.

Andra parametrar som du kan justera är:

- **long_query_time:** om en fråga tar längre tid än long_query_time (i sekunder) loggas frågan. Standardvärdet är 10 sekunder.
- **log_slow_admin_statements**: om ON innehåller administrativa uttalanden som ALTER_TABLE och ANALYZE_TABLE i de uttalanden som skrivits till slow_query_log.
- **log_queries_not_using_indexes**: avgör om frågor som inte använder index loggas till slow_query_log
- **log_throttle_queries_not_using_indexes**: Den här parametern begränsar antalet icke-indexfrågor som kan skrivas till den långsamma frågeloggen. Den här parametern börjar gälla när log_queries_not_using_indexes är inställt på PÅ.
- **log_output:** om "Arkiv", gör att den långsamma frågeloggen kan skrivas till både den lokala serverlagringen och till Azure Monitor Diagnostic Logs. Om "Ingen" skrivs den långsamma frågeloggen endast till Azure Monitor Diagnostics Logs. 

> [!IMPORTANT]
> Om tabellerna inte indexeras `log_queries_not_using_indexes` kan `log_throttle_queries_not_using_indexes` inställningen och parametrarna till PÅ påverka MySQL-prestanda eftersom alla frågor som körs mot dessa icke-indexerade tabeller skrivs till den långsamma frågeloggen.<br><br>
> Om du planerar att logga långsamma frågor under en längre tid `log_output` rekommenderar vi att du ställer in "Ingen". Om den är inställd på "Arkiv" skrivs dessa loggar till den lokala serverlagringen och kan påverka MySQL-prestanda. 

Se dokumentationen för långsam [frågelogg](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) för MySQL för fullständiga beskrivningar av parametrarna för långsam frågelogg.

## <a name="diagnostic-logs"></a>Diagnostikloggar
Azure Database for MySQL är integrerad med Azure Monitor Diagnostic Logs. När du har aktiverat långsamma frågeloggar på mySQL-servern kan du välja att låta dem släppas ut till Azure Monitor-loggar, eventhubbar eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar finns i avsnittet om [diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md).

I följande tabell beskrivs vad som finns i varje logg. Beroende på utdatametoden kan de inkluderade fälten och i vilken ordning de visas variera.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated`- Jag vet inte vad du är på. | Tidsstämpel när loggen spelades in i UTC |
| `Type` | Typ av logg. Alltid`AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Resursleverantörens namn. Alltid`MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs URI |
| `Resource` | Serverns namn |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Serverns namn |
| `start_time_t`- Jag vet inte vad du är på. | När frågan började |
| `query_time_s` | Total tid i sekunder som frågan tog att köra |
| `lock_time_s` | Total tid i sekunder som frågan låstes |
| `user_host_s` | Användarnamn |
| `rows_sent_s` | Antal skickade rader |
| `rows_examined_s` | Antal undersökta rader |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Infoga ID |
| `sql_text_s` | Fullständig fråga |
| `server_id_s` | Serverns ID |
| `thread_id_s` | Tråd-ID |
| `\_ResourceId` | Resurs URI |

> [!Note]
> För `sql_text`kommer loggen att trunkeras om den överskrider 2048 tecken.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analysera loggar i Azure Monitor Logs

När dina långsamma frågeloggar har skickas till Azure Monitor Logs via diagnostikloggar kan du utföra ytterligare analyser av dina långsamma frågor. Nedan följer några exempelfrågor som hjälper dig att komma igång. Se till att uppdatera nedanstående med ditt servernamn.

- Frågor som är längre än 10 sekunder på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Lista de 5 längsta frågorna på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Summera långsamma frågor efter minsta, högsta, genomsnittliga och standardavvikelsefrågetid på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Diagram över den långsamma frågedistributionen på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Visa frågor som är längre än 10 sekunder på alla MySQL-servrar med diagnostikloggar aktiverade

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Efterföljande moment
- [Konfigurera långsamma frågeloggar från Azure-portalen](howto-configure-server-logs-in-portal.md)
- [Konfigurera långsamma frågeloggar från Azure CLI](howto-configure-server-logs-in-cli.md).

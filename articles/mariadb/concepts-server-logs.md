---
title: Långsamma frågeloggar - Azure Database för MariaDB
description: Beskriver loggarna som är tillgängliga i Azure Database för MariaDB och de tillgängliga parametrarna för att aktivera olika loggningsnivåer.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/13/2020
ms.openlocfilehash: ffd4ab463080001dbab5b0ed9ece69c4b5f91382
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272091"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Långsamma frågeloggar i Azure Database för MariaDB
I Azure Database för MariaDB är den långsamma frågeloggen tillgänglig för användare. Åtkomst till transaktionsloggen stöds inte. Den långsamma frågeloggen kan användas för att identifiera flaskhalsar för prestanda för felsökning.

Mer information om den långsamma frågeloggen finns i MariaDB-dokumentationen för [långsam frågelogg](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="configure-slow-query-logging"></a>Konfigurera långsam frågeloggning
Som standard är den långsamma frågeloggen inaktiverad. Om du vill `slow_query_log` aktivera den ställer du in på ON. Detta kan aktiveras med hjälp av Azure-portalen eller Azure CLI. 

Andra parametrar som du kan justera är:

- **long_query_time:** om en fråga tar längre tid än long_query_time (i sekunder) loggas frågan. Standardvärdet är 10 sekunder.
- **log_slow_admin_statements**: om ON innehåller administrativa uttalanden som ALTER_TABLE och ANALYZE_TABLE i de uttalanden som skrivits till slow_query_log.
- **log_queries_not_using_indexes**: avgör om frågor som inte använder index loggas till slow_query_log
- **log_throttle_queries_not_using_indexes**: Den här parametern begränsar antalet icke-indexfrågor som kan skrivas till den långsamma frågeloggen. Den här parametern börjar gälla när log_queries_not_using_indexes är inställt på PÅ.
- **log_output:** om "Arkiv", gör att den långsamma frågeloggen kan skrivas till både den lokala serverlagringen och till Azure Monitor Diagnostic Logs. Om "Ingen" skrivs den långsamma frågeloggen endast till Azure Monitor Diagnostics Logs. 

> [!IMPORTANT]
> Om tabellerna inte är indexerade `log_throttle_queries_not_using_indexes` kan inställningen och parametrarna `log_queries_not_using_indexes` till PÅ påverka MariaDB-prestanda eftersom alla frågor som körs mot dessa icke-indexerade tabeller skrivs till den långsamma frågeloggen.<br><br>
> Om du planerar att logga långsamma frågor under en längre tid `log_output` rekommenderar vi att du ställer in "Ingen". Om den är inställd på "Arkiv" skrivs dessa loggar till den lokala serverlagringen och kan påverka MariaDB-prestanda. 

Se dokumentationen för den [långsamma frågeloggen](https://mariadb.com/kb/en/library/slow-query-log-overview/) för MariaDB för fullständiga beskrivningar av parametrarna för långsam frågelogg.

## <a name="access-slow-query-logs"></a>Komma åt långsamma frågeloggar
Det finns två alternativ för åtkomst till långsamma frågeloggar i Azure Database för MariaDB: lokal serverlagring eller Azure Monitor Diagnostic Logs. Detta är inställt `log_output` med parametern.

För lokal serverlagring kan du lista och hämta långsamma frågeloggar med Azure-portalen eller Azure CLI. Navigera till servern i Azure-portalen i Azure-portalen. Välj sidan **Serverloggar under** rubriken **Övervakning.** Mer information om Azure CLI finns i [Konfigurera och komma åt serverloggar med Azure CLI](howto-configure-server-logs-cli.md). 

Med Azure Monitor Diagnostic Logs kan du leda långsamma frågeloggar till Azure Monitor Logs (Log Analytics), Azure Storage eller Event Hubs. Se [nedan](concepts-server-logs.md#diagnostic-logs) för mer information.

## <a name="local-server-storage-log-retention"></a>Lokal lagringsloggkvarhållning
När du loggar till serverns lokala lagring är loggar tillgängliga i upp till sju dagar från det att de har skapats. Om den totala storleken på de tillgängliga loggarna överstiger 7 GB tas de äldsta filerna bort tills det finns ledigt.

Loggarna roteras var 24:e timme eller 7 GB, beroende på vilket som inträffar först.

> [!Note]
> Ovanstående loggkvarhållning gäller inte för loggar som är piped med Hjälp av Azure Monitor Diagnostic Logs. Du kan ändra kvarhållningsperioden för de datamottagare som skickas ut till (t.ex. Azure Storage).

## <a name="diagnostic-logs"></a>Diagnostikloggar
Azure Database för MariaDB är integrerad med Azure Monitor Diagnostic Logs. När du har aktiverat långsamma frågeloggar på din MariaDB-server kan du välja att låta dem släppas ut till Azure Monitor-loggar, eventhubbar eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar finns i avsnittet om [diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md).

I följande tabell beskrivs vad som finns i varje logg. Beroende på utdatametoden kan de inkluderade fälten och i vilken ordning de visas variera.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated`- Jag vet inte vad du är på. | Tidsstämpel när loggen spelades in i UTC |
| `Type` | Typ av logg. Alltid`AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Resursleverantörens namn. Alltid`MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs URI |
| `Resource` | Serverns namn |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Serverns namn |
| `start_time_t`- Jag vet inte vad du är på. | När frågan började |
| `query_time_s` | Total tid för frågan tog att köra |
| `lock_time_s` | Total tid för frågan var låst |
| `user_host_s` | Användarnamn |
| `rows_sent_s` | Antal skickade rader |
| `rows_examined_s` | Antal undersökta rader |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Infoga ID |
| `sql_text_s` | Fullständig fråga |
| `server_id_s` | Server-ID |
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

- Visa frågor som är längre än 10 sekunder på alla MariaDB-servrar med diagnostikloggar aktiverade

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Efterföljande moment
- [Konfigurera långsamma frågeloggar från Azure-portalen](howto-configure-server-logs-portal.md)
- [Konfigurera långsamma frågeloggar från Azure CLI](howto-configure-server-logs-cli.md)

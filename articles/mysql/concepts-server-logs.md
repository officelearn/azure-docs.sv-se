---
title: Serverloggar för Azure Database for MySQL
description: Beskriver loggarna som är tillgängliga i Azure Database för MySQL och tillgängliga parametrar för att aktivera olika loggningsnivåer.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: c5087a038e31c4819ef1ef173bb32faa41e04c97
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417779"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Serverloggar i Azure Database for MySQL
Långsam frågelogg är tillgänglig för användare i Azure Database för MySQL. Åtkomst till transaktionsloggen stöds inte. Långsam frågelogg kan användas för att identifiera flaskhalsar i prestanda för felsökning. 

Mer information om den långsam frågeloggen för MySQL finns i referenshandboken för MySQL [långsam logg frågeavsnitt](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Åtkomst till serverloggar
Du kan visa och ladda ned Azure Database för MySQL server-loggar med hjälp av Azure-portalen och Azure CLI.

Välj din Azure Database for MySQL-server i Azure-portalen. Under den **övervakning** väljer du den **serverloggar** sidan.

Läs mer om Azure CLI, [konfigurera och åtkomst till server-loggar med Azure CLI](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Kvarhållning av logg
Loggarna är tillgängliga i upp till sju dagar från de har skapats. Om den totala storleken på tillgängliga loggar överskrider 7 GB, raderas de äldsta filerna tills utrymme är tillgängligt. 

Loggar roteras var 24 timmar eller 7 GB, beroende på vilket som kommer först.


## <a name="configure-logging"></a>Konfigurera loggning 
Långsam frågelogg är inaktiverad som standard. Aktivera det genom att ange slow_query_log on.

Andra parametrar som du kan justera är:

- **long_query_time**: om en fråga som tar längre tid än long_query_time (i sekunder) som frågan loggas. Standardvärdet är 10 sekunder.
- **log_slow_admin_statements**: om vidare innehåller administrativa instruktioner som ALTER_TABLE och ANALYZE_TABLE i instruktionerna som skrivs till slow_query_log.
- **log_queries_not_using_indexes**: Anger om frågor som inte använder index loggas i slow_query_log
- **log_throttle_queries_not_using_indexes**: Den här parametern begränsar antalet icke-index frågor som kan skrivas till långsam frågelogg. Den här parametern träder i kraft när log_queries_not_using_indexes är inställt på på.

Se MySQL [långsam fråga log dokumentation](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) fullständiga beskrivningar av långsam logg Frågeparametrar.

## <a name="diagnostic-logs"></a>Diagnostikloggar
Azure Database för MySQL är integrerat med Azure Monitor-diagnostikloggar. När du har aktiverat långsamma frågeloggar på MySQL-servern, kan du låta dem som Azure Monitor-loggar, Event Hubs eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar finns hur till avsnittet i den [diagnostikloggar dokumentation](../azure-monitor/platform/diagnostic-logs-overview.md).

> [!IMPORTANT]
> Den här diagnostiska funktionen för serverloggar är endast tillgänglig i generell användning och Minnesoptimerad [prisnivåer](concepts-pricing-tiers.md).

I följande tabell beskrivs vad som finns i varje logg. De fält som ingår och i vilken ordning som de visas kan variera beroende på utdata-metod.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Tidsstämpel när loggen registrerades i UTC |
| `Type` | Typ av loggen. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resursgrupp som servern tillhör |
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Namnet på servern |
| `start_time_t` [UTC] | Tid som frågan började |
| `query_time_s` | Total tid som frågan tog att utföra |
| `lock_time_s` | Total tid som frågan var låst |
| `user_host_s` | Användarnamn |
| `rows_sent_s` | Antalet rader som har skickats |
| `rows_examined_s` | Antalet rader som undersöks |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Infoga id |
| `sql_text_s` | Fullständig fråga |
| `server_id_s` | Serverns id |
| `thread_id_s` | Tråd-id |
| `\_ResourceId` | Resurs-URI |

## <a name="next-steps"></a>Nästa steg
- [Hur du konfigurerar och få åtkomst till serverloggar från Azure CLI](howto-configure-server-logs-in-cli.md).

---
title: Serverloggar för Azure Database for MariaDB
description: Beskriver loggarna som är tillgängliga i Azure-databas för MariaDB, och de tillgängliga parametrarna för att aktivera olika loggningsnivåer.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 8a78a9b8f0772a83e45ac2b926878e61e6ee2e61
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926339"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Serverloggar i Azure Database for MariaDB
I Azure Database for MariaDB är långsam frågelogg tillgängliga för användare. Åtkomst till transaktionsloggen stöds inte. Långsam frågelogg kan användas för att identifiera flaskhalsar i prestanda för felsökning.

Mer information om långsam frågelogg finns MariaDB-dokumentationen för [långsam frågelogg](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Åtkomst till serverloggar
Du kan visa och ladda ned Azure-databas för MariaDB loggar med hjälp av Azure-portalen och Azure CLI.

Välj din Azure Database for MariaDB-server i Azure-portalen. Under den **övervakning** väljer du den **serverloggar** sidan.

Läs mer om Azure CLI, [konfigurera och åtkomst till server-loggar med Azure CLI](howto-configure-server-logs-cli.md).

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

Se MariaDB [långsam fråga log dokumentation](https://mariadb.com/kb/en/library/slow-query-log-overview/) fullständiga beskrivningar av långsam logg Frågeparametrar.

## <a name="diagnostic-logs"></a>Diagnostikloggar
Azure Database for MariaDB är integrerad med Azure Monitor-diagnostikloggar. När du har aktiverat långsamma frågeloggar på MariaDB-servern, kan du låta dem som Azure Monitor-loggar, Event Hubs eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar finns hur till avsnittet i den [diagnostikloggar dokumentation](../azure-monitor/platform/diagnostic-logs-overview.md).

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
| `ResourceProvider` | Namnet på resursprovidern. Alltid `MICROSOFT.DBFORMARIADB` |
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
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Infoga ID |
| `sql_text_s` | Fullständig fråga |
| `server_id_s` | Server-ID |
| `thread_id_s` | Tråd-ID |
| `\_ResourceId` | Resurs-URI |

## <a name="next-steps"></a>Nästa steg
- [Hur du konfigurerar och få åtkomst till serverloggar i Azure Portal](howto-configure-server-logs-portal.md).

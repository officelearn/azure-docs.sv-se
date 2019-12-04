---
title: Långsamma Query-loggar – Azure Database for MySQL
description: Beskriver långsamma frågemeddelanden som är tillgängliga i Azure Database for MySQL och de tillgängliga parametrarna för att aktivera olika loggnings nivåer.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: cd0d09e4d46747b7f3f8e6fb714dd711beef9484
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770855"
---
# <a name="slow-query-logs-in-azure-database-for-mysql"></a>Långsamma Query-loggar i Azure Database for MySQL
I Azure Database for MySQL är den långsamma fråge loggen tillgänglig för användare. Åtkomst till transaktions loggen stöds inte. Den långsamma frågans logg kan användas för att identifiera Flask halsar i prestanda för fel sökning.

Mer information om den långsamma loggen MySQL finns i [avsnittet om långsam fråga](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)i MySQL-referensen.

## <a name="access-slow-query-logs"></a>Komma åt långsamma Query-loggar
Du kan visa och hämta Azure Database for MySQL långsamma fråge loggar med hjälp av Azure Portal och Azure CLI.

I Azure Portal väljer du Azure Database for MySQL-servern. Under **övervaknings** rubriken väljer du sidan **Server loggar** .

Mer information om Azure CLI finns i [Konfigurera och komma åt långsamma Query-loggar med Azure CLI](howto-configure-server-logs-in-cli.md).

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

> [!Note]
> För `sql_text`kommer loggen att trunkeras om den överskrider 2048 tecken.

Se [logg dokumentationen för en långsam fråga](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) i MySQL för att få en fullständig beskrivning av logg parametrarna för långsamma frågor.

## <a name="diagnostic-logs"></a>Diagnostikloggar
Azure Database for MySQL är integrerat med Azure Monitor diagnostikloggar. När du har aktiverat långsamma Query-loggar på MySQL-servern kan du välja att de ska skickas till Azure Monitor loggar, Event Hubs eller Azure Storage. Om du vill veta mer om hur du aktiverar diagnostikloggar kan du läsa avsnittet så här i [dokumentationen för diagnostikloggar](../azure-monitor/platform/resource-logs-overview.md).

> [!IMPORTANT]
> Den här Diagnostic-funktionen för Server loggar är bara tillgänglig i Generell användning och minnesoptimerade [pris nivåer](concepts-pricing-tiers.md).

I följande tabell beskrivs vad som finns i varje logg. Beroende på utmatnings metoden kan de fält som ingår och i vilken ordning de visas variera.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. `AzureDiagnostics` alltid |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. `MICROSOFT.DBFORMYSQL` alltid |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Namnet på servern |
| `start_time_t` [UTC] | Tiden då frågan började |
| `query_time_s` | Total tid i sekunder som frågan tog för att köras |
| `lock_time_s` | Total tid i sekunder som frågan låstes |
| `user_host_s` | Användarnamn |
| `rows_sent_s` | Antal rader som skickats |
| `rows_examined_s` | Antal rader som granskas |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Infoga ID |
| `sql_text_s` | Fullständig fråga |
| `server_id_s` | Serverns ID |
| `thread_id_s` | Tråd-ID |
| `\_ResourceId` | Resurs-URI |

## <a name="next-steps"></a>Nästa steg
- [Konfigurera och få åtkomst till Server loggar från Azure CLI](howto-configure-server-logs-in-cli.md).

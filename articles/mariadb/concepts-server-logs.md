---
title: Serverloggar för Azure Database for MariaDB
description: Beskriver loggarna som är tillgängliga i Azure-databas för MariaDB, och de tillgängliga parametrarna för att aktivera olika loggningsnivåer.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a26f61eb199d8f370e1a9dd010932dc868b74ae4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61041266"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Serverloggar i Azure Database for MariaDB
I Azure Database for MariaDB är långsam frågelogg tillgängliga för användare. Åtkomst till transaktionsloggen stöds inte. Långsam frågelogg kan användas för att identifiera flaskhalsar i prestanda för felsökning.

Mer information om långsam frågelogg finns MariaDB-dokumentationen för [långsam frågelogg](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Åtkomst till serverloggar
Du kan visa och ladda ned Azure-databas för MariaDB loggar med hjälp av Azure-portalen och Azure CLI.

Välj din Azure Database for MariaDB-server i Azure-portalen. Under den **övervakning** väljer du den **serverloggar** sidan.

<!-- For more information on Azure CLI, see [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md).-->

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

## <a name="next-steps"></a>Nästa steg
- [Hur du konfigurerar och få åtkomst till serverloggar i Azure Portal](howto-configure-server-logs-portal.md).

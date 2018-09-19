---
title: Serverloggar för Azure Database for MySQL
description: Beskriver loggarna som är tillgängliga i Azure Database för MySQL och tillgängliga parametrar för att aktivera olika loggningsnivåer.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 09/17/2018
ms.openlocfilehash: ac5be20815b552c08e5cd1054bf24d7a10b56498
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124277"
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
- **log_throttle_queries_not_using_indexes**: den här parametern begränsar antalet icke-index frågor som kan skrivas till långsam frågelogg. Den här parametern träder i kraft när log_queries_not_using_indexes är inställt på på.

Se MySQL [långsam fråga log dokumentation](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) fullständiga beskrivningar av långsam logg Frågeparametrar.

## <a name="next-steps"></a>Nästa steg
- [Hur du konfigurerar och få åtkomst till serverloggar från Azure CLI](howto-configure-server-logs-in-cli.md).

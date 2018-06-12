---
title: Server-loggar för Azure-databas för MySQL
description: Beskriver tillgängliga i Azure-databasen loggar för MySQL och tillgängliga parametrar för att aktivera olika loggningsnivåer.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 50e4b9b8b8f9433ec725aaa982e969cec7afb91c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265793"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Loggas i Azure-databas för MySQL
Långsam frågeloggen är tillgänglig för användare i Azure-databas för MySQL. Åtkomst till transaktionsloggen stöds inte. Långsam frågeloggen kan användas för att identifiera prestandaflaskhalsar för felsökning. 

Mer information om MySQL långsam frågeloggen finns MySQL handledningen [långsamma fråga loggen avsnittet](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html).

## <a name="access-server-logs"></a>Åtkomst till serverloggar
Du kan visa och hämta Azure-databas för MySQL server-loggar med hjälp av Azure-portalen och Azure CLI.

Välj din Azure-databas för MySQL-server i Azure-portalen. Under den **övervakning** rubrik, Välj den **serverloggen** sidan.

Mer information om Azure CLI finns [konfigurera och access server-loggar med hjälp av Azure CLI](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Kvarhållning av logg
Loggarna är tillgängliga i upp till sju dagar från skapas. Om den totala storleken på de tillgängliga loggarna överskrider 7.5 GB, är de äldsta filerna bort förrän utrymme. 

Loggar roteras var 24 timmar eller 7.5 GB, beroende på vilket som kommer först.


## <a name="configure-logging"></a>Konfigurera loggning 
Långsam frågeloggen är inaktiverat som standard. Ange slow_query_log på ON för att aktivera den.

Andra parametrar som du kan justera inkluderar:

- **long_query_time**: om en fråga som tar längre tid än long_query_time (i sekunder) som frågan loggas. Standardvärdet är 10 sekunder.
- **log_slow_admin_statements**: om ON innehåller administrativa rapporter som ALTER_TABLE och ANALYZE_TABLE i instruktionerna som skrivs till slow_query_log.
- **log_queries_not_using_indexes**: Anger om frågor som inte använder index loggas i slow_query_log
- **log_throttle_queries_not_using_indexes**: den här parametern begränsar antalet icke-index frågor som kan skrivas till loggfilen för långsam frågor. Den här parametern börjar gälla när log_queries_not_using_indexes är inställt på ON.

Finns MySQL [långsamma fråga loggen dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) fullständiga beskrivningar av loggen för långsam Frågeparametrar.

## <a name="next-steps"></a>Nästa steg
- [Konfigurera och komma åt server-loggar från Azure CLI](howto-configure-server-logs-in-cli.md).

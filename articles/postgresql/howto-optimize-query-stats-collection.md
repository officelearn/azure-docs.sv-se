---
title: Optimera fråga stats samling i Azure Database för PostgreSQL server fråga stats samling
description: Den här artikeln beskrivs hur du kan optimera frågan stats samlingen på en Azure Database for PostgreSQL-server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: df54693aee9a9a23b8202c90a6c23008ff7a7cb6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548723"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>Optimera fråga statistiksamling i Azure Database for PostgreSQL-server 
Den här artikeln beskrivs för att optimera insamling av frågekörningsstatistik frågan i en Azure Database for PostgreSQL-server.

## <a name="using-pgstatsstatements"></a>Med hjälp av pg_stats_statements
**Pg_stat_statements** är en PostgreSQL-tillägg som är aktiverad som standard i Azure Database för PostgreSQL. Tillägget ger dig möjlighet att spåra körning statistik för alla SQL-instruktioner som körs av en server. Den här modulen webhook i varje frågekörning och levereras med en icke-trivialt prestandakostnad. Aktivera **pg_stat_statements** framtvingar fråga text skrivningar till filer på disk.

För kunder som har unika frågor med lång frågetexten eller inte övervakar aktivt **pg_stat_statements**, rekommenderar vi att inaktiverar **pg_stat_statements** för bästa prestanda genom att ange `pg_stat_statements.track = NONE`.

På vissa slutkundsarbetsbelastningar som vi har sett upp till en 50 procent prestandaförbättringar genom att inaktivera **pg_stat_statements**. Men är förhållandet som är genom att inaktivera pg_stat_statements det inte går att felsöka prestandaproblem.

Ange `pg_stat_statements.track = NONE`:

- I Azure-portalen går du till den [PostgreSQL resurshantering och välj parametrar serverbladet](howto-configure-server-parameters-using-portal.md).

![PostgreSQL server parametern-bladet](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Med hjälp av [Azure CLI](howto-configure-server-parameters-using-cli.md), az postgres server configuration set `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="using-query-store"></a>Med hjälp av Query Store 
Den [Query Store](concepts-query-store.md) funktion i Azure Database för PostgreSQL är en mer högpresterande metod för att spåra frågestatistik och rekommenderas som ett alternativ till *pg_stats_statements*. 

## <a name="next-steps"></a>Nästa steg
Överväg att ställa in `pg_stat_statements.track = NONE` i den [Azure-portalen](howto-configure-server-parameters-using-portal.md) eller med hjälp av [Azure CLI](howto-configure-server-parameters-using-cli.md).

Se den [Användningsscenarier för Query Store](concepts-query-store-scenarios.md) och [Query Store metodtips](concepts-query-store-best-practices.md) för mer information. 

---
title: Optimera fråga stats samlingen på en Azure Database for PostgreSQL-server
description: Den här artikeln beskrivs hur du kan optimera frågan stats samlingen på en Azure Database for PostgreSQL-server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 428a22f79008130448bef65f14322d6880b1b367
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814375"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql-server"></a>Optimera insamling av frågekörningsstatistik fråga på en Azure Database for PostgreSQL-server 
Den här artikeln beskriver hur du optimerar insamling av frågekörningsstatistik fråga på en Azure Database for PostgreSQL-server.

## <a name="use-pgstatsstatements"></a>Använda pg_stats_statements
**Pg_stat_statements** är en PostgreSQL-tillägg som är aktiverad som standard i Azure Database för PostgreSQL. Tillägget ger dig möjlighet att spåra körning statistik för alla SQL-instruktioner som körs av en server. Den här modulen hookar i varje frågekörning och levereras med en icke-trivialt prestandakostnad. Aktivera **pg_stat_statements** framtvingar fråga text skrivningar till filer på disk.

Om du har unika frågor med lång frågetexten eller inte övervakar aktivt **pg_stat_statements**, inaktivera **pg_stat_statements** för bästa prestanda. Du gör detta genom att ändra inställningen till `pg_stat_statements.track = NONE`.

Vissa slutkundsarbetsbelastningar har sett upp till en 50 procent prestandaförbättring när **pg_stat_statements** är inaktiverad. Du måste du göra när du inaktiverar pg_stat_statements är det inte går att felsöka prestandaproblem.

Ange `pg_stat_statements.track = NONE`:

- I Azure-portalen går du till den [PostgreSQL resurshantering och välj parametrar serverbladet](howto-configure-server-parameters-using-portal.md).

  ![PostgreSQL server parametern-bladet](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Använd den [Azure CLI](howto-configure-server-parameters-using-cli.md) az postgres server configuration inställd `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Använda Query Store 
Den [Query Store](concepts-query-store.md) funktion i Azure Database för PostgreSQL erbjuder en mer effektiv metod för att spåra frågestatistik. Vi rekommenderar den här funktionen som ett alternativ till *pg_stats_statements*. 

## <a name="next-steps"></a>Nästa steg
Överväg att ställa in `pg_stat_statements.track = NONE` i den [Azure-portalen](howto-configure-server-parameters-using-portal.md) eller genom att använda den [Azure CLI](howto-configure-server-parameters-using-cli.md).

Mer information finns i: 
- [Query Store-användningsscenarier](concepts-query-store-scenarios.md) 
- [Metodtips för Query Store](concepts-query-store-best-practices.md) 

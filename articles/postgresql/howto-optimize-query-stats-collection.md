---
title: Optimera insamling av frågestatistik - Azure Database for PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du kan optimera frågestatistiksamlingen i en Azure-databas för PostgreSQL - Single Server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f467f01118470eb51f7decf3bd6457917c566723
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770177"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Optimera frågestatistiksamlingen i en Azure-databas för PostgreSQL - Single Server
I den här artikeln beskrivs hur du optimerar frågestatistiksamlingen på en Azure-databas för PostgreSQL-server.

## <a name="use-pg_stats_statements"></a>Använd pg_stats_statements
**Pg_stat_statements** är ett PostgreSQL-tillägg som är aktiverat som standard i Azure Database for PostgreSQL. Tillägget är ett sätt att spåra körningsstatistik för alla SQL-uttryck som körs av en server. Denna modul krokar i varje fråga körning och levereras med en icke-trivial prestanda kostnad. Om **du** aktiverar pg_stat_statements skrivningar av frågetext till filer på disken.

Om du har unika frågor med lång frågetext eller om du inte aktivt övervakar **pg_stat_statements**inaktiverar **du pg_stat_statements** för bästa prestanda. Det gör du genom `pg_stat_statements.track = NONE`att ändra inställningen till .

Vissa kundarbetsbelastningar har sett upp till 50 procent prestandaförbättring när **pg_stat_statements** är inaktiverad. Den kompromiss du gör när du inaktiverar pg_stat_statements är oförmågan att felsöka prestandaproblem.

Så `pg_stat_statements.track = NONE`här ställer du in:

- Gå till [sidan PostgreSQL-resurshantering](howto-configure-server-parameters-using-portal.md)i Azure-portalen och välj bladet serverparametrar .

  ![Parameterblad för PostgreSQL-server](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Använd [azure CLI](howto-configure-server-parameters-using-cli.md) az postgres-serverkonfigurationen inställd `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`på .

## <a name="use-the-query-store"></a>Använda Frågearkivet 
[Funktionen Query Store](concepts-query-store.md) i Azure Database for PostgreSQL är en mer effektiv metod för att spåra frågestatistik. Vi rekommenderar den här funktionen som ett alternativ till att använda *pg_stats_statements*. 

## <a name="next-steps"></a>Nästa steg
Överväg `pg_stat_statements.track = NONE` att ange i [Azure-portalen](howto-configure-server-parameters-using-portal.md) eller med hjälp av [Azure CLI](howto-configure-server-parameters-using-cli.md).

Mer information finns i: 
- [Användningsscenarier för Query Store](concepts-query-store-scenarios.md) 
- [Metodtips för Query Store](concepts-query-store-best-practices.md) 

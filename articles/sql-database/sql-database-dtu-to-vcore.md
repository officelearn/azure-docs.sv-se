---
title: Migrera från DTU till virtuell kärna
description: Migrera från DTU-modellen till vCore-modellen. Att migrera till vCore liknar uppgradering eller nedgradering mellan standard- och premiumnivåerna.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945407"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrera från den DTU-baserade modellen till den vCore-baserade modellen

## <a name="migrate-a-database"></a>Migrera en databas

Att migrera en databas från den DTU-baserade inköpsmodellen till den vCore-baserade inköpsmodellen liknar uppgradering eller nedgradering mellan standard- och premiumtjänstnivåerna i den DTU-baserade inköpsmodellen.

## <a name="migrate-databases-that-use-geo-replication"></a>Migrera databaser som använder georeplikering

Att migrera från den DTU-baserade modellen till den vCore-baserade inköpsmodellen liknar uppgradering eller nedgradering av geo-replikeringsrelationer mellan databaser i standard- och premiumtjänstnivåerna. Under migreringen behöver du inte stoppa geo-replikering, men du måste följa dessa sekvenseringsregler:

- När du uppgraderar måste du först uppgradera den sekundära databasen och sedan uppgradera den primära.
- När du nedgraderar, återför ordningen: du måste nedgradera den primära databasen först och sedan nedgradera den sekundära.

När du använder georeplikering mellan två elastiska pooler rekommenderar vi att du anger den ena poolen som primär och den andra som sekundär. I så fall bör du använda samma sekvenseringsvägledning när du migrerar elastiska pooler. Om du har elastiska pooler som innehåller både primära och sekundära databaser behandlar du poolen med det högre utnyttjandet som primärt och följer sekvenseringsreglerna därefter.  

I följande tabell finns vägledning för specifika migreringsscenarier:

|Aktuell tjänstnivå|Måltjänstnivå|Typ av migrering|Användaråtgärder|
|---|---|---|---|
|Standard|Generellt syfte|Laterala|Kan migrera i valfri ordning, men måste säkerställa lämplig vCore-storlek*|
|Premium|Affärskritiskt|Laterala|Kan migrera i valfri ordning, men måste säkerställa lämplig vCore-storlek*|
|Standard|Affärskritiskt|Uppgradera|Måste migrera sekundärt först|
|Affärskritiskt|Standard|Nedgradera|Måste migrera primär först|
|Premium|Generellt syfte|Nedgradera|Måste migrera primär först|
|Generellt syfte|Premium|Uppgradera|Måste migrera sekundärt först|
|Affärskritiskt|Generellt syfte|Nedgradera|Måste migrera primär först|
|Generellt syfte|Affärskritiskt|Uppgradera|Måste migrera sekundärt först|
||||

\*Som en tumregel kräver varje 100 DU:er på standardnivån minst 1 vCore, och var 125 DU:er i premiumnivån kräver minst 1 vCore. Mer information finns i [vCore-baserad inköpsmodell](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model).

## <a name="migrate-failover-groups"></a>Migrera redundansgrupper

Migrering av redundansklar med flera databaser kräver individuell migrering av de primära och sekundära databaserna. Under den processen gäller samma överväganden och sekvenseringsregler. När databaserna har konverterats till den vCore-baserade inköpsmodellen fortsätter redundansgruppen att gälla med samma principinställningar.

### <a name="create-a-geo-replication-secondary-database"></a>Skapa en sekundär georeplikeringsdatabas

Du kan bara skapa en sekundär georeplikeringsdatabas (en geografisk sekundär) genom att använda samma tjänstnivå som du använde för den primära databasen. För databaser med hög logggenereringshastighet rekommenderar vi att du skapar den geo-sekundära med samma beräkningsstorlek som den primära.

Om du skapar en geografisk sekundär i den elastiska poolen för `maxVCore` en enda primär databas kontrollerar du att inställningen för poolen matchar den primära databasens beräkningsstorlek. Om du skapar en geografisk sekundär för en primär i en annan elastisk `maxVCore` pool rekommenderar vi att poolerna har samma inställningar.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Använda databaskopia för att konvertera en DTU-baserad databas till en vCore-baserad databas

Du kan kopiera en databas med en DTU-baserad beräkningsstorlek till en databas med en vCore-baserad beräkningsstorlek utan begränsningar eller särskild sekvensering så länge målberäkningsstorleken stöder källdatabasens maximala databasstorlek. Databaskopian skapar en ögonblicksbild av data från och med kopieringens starttid och synkroniserar inte data mellan källan och målet.

## <a name="next-steps"></a>Nästa steg

- De specifika beräkningsstorlekar och lagringsstorleksalternativ som är tillgängliga för enskilda databaser finns i [SQL Database vCore-baserade resursgränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md).
- För de specifika beräkningsstorlekar och lagringsstorleksalternativ som är tillgängliga för elastiska pooler finns i [SQL Database vCore-baserade resursgränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

---
title: Migrera från DTU till vCore
description: Migrera från DTU-modellen till vCore-modellen. Migrering till vCore liknar uppgradering eller nedgradering mellan standard-och Premium nivåerna.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/08/2019
ms.openlocfilehash: f34439b7750ca1858e71d4a36121eb65001fff50
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811259"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrera från den DTU-baserade modellen till den vCore-baserade modellen

## <a name="migrate-a-database"></a>Migrera en databas

Migrering av en databas från den DTU-baserade inköps modellen till den vCore-baserade inköps modellen liknar uppgradering eller nedgradering mellan standard-och premium tjänst nivåerna i den DTU-baserade inköps modellen.

## <a name="migrate-databases-that-use-geo-replication"></a>Migrera databaser som använder geo-replikering

Migrering från den DTU-baserade modellen till den vCore-baserade inköps modellen liknar att uppgradera eller nedgradera geo-replikeringsrelationen mellan databaser på standard-och premium-tjänst nivåerna. Under migreringen behöver du inte stoppa geo-replikering, men du måste följa dessa ordningsföljds regler:

- När du uppgraderar måste du uppgradera den sekundära databasen först och sedan uppgradera den primära databasen.
- Ändra ordning när du nedgraderar ordningen: du måste nedgradera den primära databasen först och sedan nedgradera den sekundära.

När du använder geo-replikering mellan två elastiska pooler rekommenderar vi att du anger en pool som primär och den andra som den sekundära. I så fall bör du använda samma rikt linjer när du migrerar elastiska pooler. Men om du har elastiska pooler som innehåller både primära och sekundära databaser, behandlar poolen med den högre användningen som primär och följer ordningsföljden för ordningsföljd.  

Följande tabell innehåller vägledning för olika scenarier för migrering:

|Aktuell tjänst nivå|Mål tjänst nivå|Typ av migrering|Användar åtgärder|
|---|---|---|---|
|Standard|Generellt syfte|Lateral|Kan migrera i vilken ordning som helst, men måste säkerställa lämplig vCore storlek *|
|Premium|Affärskritisk|Lateral|Kan migrera i vilken ordning som helst, men måste säkerställa lämplig vCore storlek *|
|Standard|Affärskritisk|Uppgradera|Måste migrera sekundär första|
|Affärskritisk|Standard|Nedgradera|Måste migrera primär första|
|Premium|Generellt syfte|Nedgradera|Måste migrera primär första|
|Generellt syfte|Premium|Uppgradera|Måste migrera sekundär första|
|Affärskritisk|Generellt syfte|Nedgradera|Måste migrera primär första|
|Generellt syfte|Affärskritisk|Uppgradera|Måste migrera sekundär första|
||||

\* varje 100-DTU: er på standard nivån kräver minst 1 vCore och varje 125-DTU: er på Premium-nivån kräver minst 1 vCore.

## <a name="migrate-failover-groups"></a>Migrera redundansväxla grupper

Migrering av failover-grupper med flera databaser kräver en individuell migrering av de primära och sekundära databaserna. Under den processen gäller samma överväganden och ordningsföljd regler. När databaserna har konverterats till den vCore-baserade inköps modellen, kommer gruppen för redundans att gälla med samma princip inställningar.

### <a name="create-a-geo-replication-secondary-database"></a>Skapa en sekundär databas för geo-replikering

Du kan skapa en sekundär databas för geo-replikering (endast en geo-sekundär) med samma tjänst nivå som du använde för den primära databasen. För databaser med hög logg skapande frekvens rekommenderar vi att du skapar geo-Secondary med samma beräknings storlek som den primära.

Om du skapar en geo-sekundär i den elastiska poolen för en enda primär databas kontrollerar du att den `maxVCore`-inställningen för poolen stämmer överens med den primära databasens beräknings storlek. Om du skapar en geo-Secondary för en primär i en annan elastisk pool, rekommenderar vi att poolerna har samma `maxVCore`-inställningar.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Använd databas kopiering för att konvertera en DTU-baserad databas till en vCore-baserad databas

Du kan kopiera alla databaser med en DTU-baserad beräknings storlek till en databas med en vCore beräknings storlek utan begränsningar eller särskilda sekvenser så länge mål beräknings storleken har stöd för den maximala databas storleken för käll databasen. Databas kopian skapar en ögonblicks bild av data från den tidpunkt då kopieringen startar och synkroniserar inte data mellan källan och målet.

## <a name="next-steps"></a>Nästa steg

- De angivna beräknings storlekarna och lagrings storleks alternativen för enskilda databaser finns i [SQL Database vCore resurs gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md).
- De angivna beräknings storlekarna och lagrings storleks alternativen för elastiska pooler finns i [SQL Database vCore resurs gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

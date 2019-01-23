---
title: Skrivskyddade repliker i Azure Database for PostgreSQL
description: Den här artikeln beskrivs skrivskyddade repliker i Azure Database för PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: cb02f0b786ff6f1c7dbef5471fb95ce6516f824c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54466082"
---
# <a name="read-replicas-in-azure-database-for-postgresql"></a>Läs repliker i Azure Database för PostgreSQL
Läs replik-funktionen kan du replikera data från en Azure Database for PostgreSQL-server (master) upp till fem skrivskyddade servrar (läsa repliker) inom samma Azure-region. Läs repliker uppdateras asynkront med inbyggda replikeringsteknik för PostgreSQL-motorn.

Repliker är nya servrar som kan hanteras på liknande sätt som normala fristående Azure Database for PostgreSQL-servrar. Du debiteras för varje Läs replik för etablerad beräkning i vCores och allokerat lagringsutrymme i GB/månad.

## <a name="when-to-use-read-replicas"></a>När du ska använda skrivskyddade repliker
Läs replica-funktionen är avsett för att förbättra prestanda och skalning för läsintensiva arbetsbelastningar. Skrivskyddade arbetsbelastningar kan exempelvis vara isolerade på replikerna medan skrivning arbetsbelastningar kan dirigeras till huvudservern.

Ett vanligt scenario är att ha BI och analytiska arbetsbelastningar använder den skrivskyddade repliken som datakälla för rapportering.

Eftersom repliker är skrivskyddade kan de direkt minska inte behovet av write-kapacitet på huvudmålservern och så den här funktionen inte är riktad mot write-intensiva arbetsbelastningar.

Läs replica-funktionen använder Postgresqls asynkron replikering och så är inte avsedd för synkron replikeringsscenarier. Det blir en mätbara fördröjning mellan huvudservern och repliken. Data på repliken blir konsekvent med data i bakgrunden. Använd denna funktion för arbetsbelastningar som kan hantera den här fördröjningen.

## <a name="creating-a-replica"></a>Skapa en replik
Huvudservern måste ha den **azure.replication_support** inställd på REPLIKEN. Ändra den här parametern kräver en omstart av servern ska börja gälla. (**Azure.replication_support** parametern gäller för generell användning och Minnesoptimerad nivåer).

När du har initierat skapa replica arbetsflöde kan skapas en tom Azure Database for PostgreSQL-server. Den nya servern är fylld med de data som fanns på huvudservern. Den tid det tar för att skapa den nya repliken beror på mängden data på huvudservern och hur lång tid sedan den senaste veckovisa fullständiga säkerhetskopieringen. Det kan röra sig om ett par minuter till några timmar.

Läs replica-funktionen använder Postgresqls fysiska replikering (inte logiska replikering). Strömning replikering med replikering fack är standardläget för åtgärden. Vid behov, används loggöverföring för att snabba upp processen.

> [!NOTE]
> Om du inte redan har en avisering uppsättning lagring på dina servrar som vi rekommenderar att du gör detta för att informera dig när en server närmar sig lagringsgränsen, eftersom detta påverkar replikeringen.

[Lär dig hur du skapar en skrivskyddad replik i Azure-portalen](howto-read-replicas-portal.md).

## <a name="connecting-to-a-replica"></a>Ansluta till en replik
När du skapar en replik, ärver inte brandväggsregler eller VNet-tjänstslutpunkt för domänens huvudserver. Dessa regler måste konfigureras separat för repliken.

Repliken ärver dess administratörskonto från huvudservern. Alla användarkonton på huvudservern replikeras till de skrivskyddade replikerna. Du kan bara ansluta till en skrivskyddad replik med hjälp av användarkontona som är tillgängliga på huvudservern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användarkonto, precis som på en vanlig Azure-databas för PostgreSQL-server. Exempelvis om serverns namn är myreplica och administratörsanvändarnamnet är myadmin kan du ansluta till den från psql på följande sätt:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```
och i Kommandotolken anger du lösenordet för användarkontot.

## <a name="monitoring-replication"></a>Övervakning av replikeringen
Det finns en **Max Lag över repliker** mått som är tillgängliga i Azure Monitor. Detta mått är tillgängliga på den överordnade servern. Mätvärdet visar fördröjning mellan huvudservern och de flesta släpar repliken. 

Vi ger även en **repliken fördröjning** mått i Azure Monitor. Det här måttet är tillgängligt för repliker endast. 

Måttet beräknas från vyn pg_stat_wal_receiver:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp())
```
Kom ihåg att repliken fördröjning mått visar tiden sedan senast återupprepas transaktion. Om det finns inga transaktioner som sker i bakgrunden, visar den här tidsförskjutningen i måttet.

Vi rekommenderar att du ställer in en avisering som meddelar dig när repliken fördröjning når ett värde som inte är acceptabel för din arbetsbelastning. 

Mer information finns i fråga huvudservern direkt för att få replikeringsfördröjning i byte på alla repliker.
On PG 10:
```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

På PG 9.6 och nedan:
```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Om en master eller repliken servern har startats om återspeglas den tid det tar att starta om och sedan komma ifatt i repliken Lag-mått.

## <a name="stopping-replication-to-a-replica"></a>Stoppa replikering till en replik
Du kan välja att stoppa replikering mellan en och en replik. Detta innebär att repliken för att starta om för att ta bort dess replikeringsinställningar. När replikeringen har stoppats mellan en och en replikserver, blir replikservern en fristående server. Data i den fristående servern är de data som fanns på repliken när kommandot Stoppa replikering har påbörjats. Det här fristående server fånga inte med huvudservern.

Den här servern kan inte göras i en replik igen.

Kontrollera att repliken har alla data som du behöver innan du stoppa replikeringen.

Du kan [Lär dig hur du stoppar en replik i dokumentationen för how-to](howto-read-replicas-portal.md).


## <a name="considerations"></a>Överväganden

### <a name="preparing-for-replica"></a>Förbereda för replikering
**Azure.replication_support** måste anges till REPLIKEN på huvudservern innan du kan skapa en replik. Ändra den här parametern kräver en omstart av servern ska börja gälla. Den här parametern gäller för generell användning och Minnesoptimerad nivåer.

### <a name="stopped-replicas"></a>Stoppad repliker
När du väljer att stoppa replikering mellan huvud- och repliken startas repliken för att tillämpa ändringarna. Därefter kan kan den inte fås till en replik igen.

### <a name="replicas-are-new-servers"></a>Repliker är nya servrar
Repliker skapas som ny Azure Database for PostgreSQL-servrar. Befintliga servrar kan inte göras i repliker.

### <a name="replica-server-configuration"></a>Repliken serverkonfiguration
Replikservrar skapas med hjälp av samma server-konfigurationer som huvudserver, vilket innefattar följande konfigurationer:
- Prisnivå
- Compute-generering
- vCores
- Storage
- Kvarhållningsperiod för säkerhetskopiering
- Redundansalternativ för säkerhetskopiering
- Motorversion för PostgreSQL

När en replik har skapats, prisnivån (förutom till och från Basic), compute-generering, vCores, lagring och kvarhållningsperiod för säkerhetskopiering kan ändras oberoende från huvudservern.

> [!IMPORTANT]
> Innan en master-serverkonfigurationen har uppdaterats till nya värden, bör de replikerna konfiguration uppdateras till samma eller högre värden. Detta säkerställer att repliker är kan hålla jämna steg med ändringar som gjorts i huvuddatabasen.

Postgres kräver särskilt replik server värdet för parametern max_connections ska vara större än eller lika med värdet i master annars inte kommer att starta replikeringen. Azure Database för PostgreSQL har max_connections värdet beroende på vilken sku. Mer information finns i [gränser doc](concepts-limits.md). 

Försök att göra en uppdatering som bryter mot detta leder till ett fel.


### <a name="deleting-the-master"></a>Tar bort huvudservern
När en huvudserver har tagits bort, blir alla skrivskyddade repliker fristående servrar. Replikerna kommer att startas om för den här ändringen.

### <a name="other"></a>Annat
- Läs repliker kan bara skapas i samma Azure-region som huvudserver.
- Det går inte att skapa en replik av en replik.

## <a name="next-steps"></a>Nästa steg
- [Hur du skapar och hanterar läsa repliker i Azure portal](howto-read-replicas-portal.md).

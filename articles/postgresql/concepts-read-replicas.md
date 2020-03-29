---
title: Läsa repliker – Azure-databas för PostgreSQL - Single Server
description: I den här artikeln beskrivs funktionen läsreplik i Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: fd6d3e24adfc22d2f6ea17f09b8dea4638a054b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76769046"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Läsa repliker i Azure Database för PostgreSQL - Single Server

Med funktionen läsreplik kan du replikera data från en Azure-databas för PostgreSQL-server till en skrivskyddad server. Du kan replikera från huvudservern till upp till fem repliker. Repliker uppdateras asynkront med PostgreSQL-motorns inbyggda replikeringsteknik.

Repliker är nya servrar som du hanterar liknar vanliga Azure-databas för PostgreSQL-servrar. För varje läsreplik faktureras du för den etablerade beräkningen i virtuella kärnor och lagring i GB/månad.

Lär dig hur du [skapar och hanterar repliker](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>När ska en läsreplik användas
Läsreplikfunktionen hjälper till att förbättra prestanda och skala för läsintensiva arbetsbelastningar. Läsarbetsbelastningar kan isoleras till replikerna, medan skrivarbetsbelastningar kan dirigeras till huvudhanteraren.

Ett vanligt scenario är att bi- och analysarbetsbelastningar ska använda läsrepliken som datakälla för rapportering.

Eftersom repliker är skrivskyddade minskar de inte direkt skrivkapacitetsbördorna på bakgrunden. Den här funktionen är inte inriktad på skrivintensiva arbetsbelastningar.

Funktionen läsreplik använder PostgreSQL asynkron replikering. Funktionen är inte avsedd för synkrona replikeringsscenarier. Det kommer att finnas en mätbar fördröjning mellan huvud- och repliken. Data på repliken blir så småningom konsekventa med data på huvudhanteraren. Använd den här funktionen för arbetsbelastningar som kan hantera den här fördröjningen.

## <a name="cross-region-replication"></a>Replikering mellan regioner
Du kan skapa en läsreplik i en annan region än huvudservern. Replikering mellan regioner kan vara till hjälp för scenarier som planering av haveriberedskap eller föra data närmare användarna.

Du kan ha en huvudserver i valfri [Azure-databas för PostgreSQL-region](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). En huvudserver kan ha en replik i den parade regionen eller de universella replikregionerna. Bilden nedan visar vilka replikområden som är tillgängliga beroende på huvudregionen.

[![Läsa replikområden](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universella replikregioner
Du kan alltid skapa en läsreplik i något av följande områden, oavsett var huvudservern finns. Dessa är de universella replikregionerna:

Australien Östra, Australien Sydost, Centrala USA, Östasien, Östra USA, Östra USA 2, Östra Japan, Japan Väst, Korea Central, Korea Syd, Norra centrala USA, Norra Europa, Södra centrala USA, Sydostasien, Storbritannien Syd, Storbritannien Väst, Västeuropa, Västra USA.

*Västra US 2 är inte tillgänglig för tillfället som en replikplats för flera regioner.


### <a name="paired-regions"></a>Länkade regioner
Förutom de universella replikregionerna kan du skapa en läsreplik i azure-parade regionen på huvudservern. Om du inte känner till regionens par kan du läsa mer av [artikeln Azure Paired Regions](../best-practices-availability-paired-regions.md).

Om du använder repliker över flera regioner för planering av haveriberedskap rekommenderar vi att du skapar repliken i den parade regionen i stället för en av de andra regionerna. Parade regioner undviker samtidiga uppdateringar och prioriterar fysisk isolering och datahemvist.  

Det finns begränsningar att tänka på: 

* Regional tillgänglighet: Azure Database för PostgreSQL är tillgängligt i västra USA 2, France Central, UAE North och Germany Central. Deras parade regioner är dock inte tillgängliga.
    
* Uni-directional par: Vissa Azure-regioner är bara ihopkopplade i en riktning. Dessa regioner inkluderar Västra Indien, Södra Brasilien. 
   Det innebär att en huvudserver i västra Indien kan skapa en replik i södra Indien. En huvudserver i södra Indien kan dock inte skapa en replik i västra Indien. Detta beror på att Västra Indiens sekundära region är södra Indien, men södra Indiens sekundära region är inte västra Indien.


## <a name="create-a-replica"></a>Skapa en replik
När du startar arbetsflödet för att skapa replik skapas en tom Azure-databas för PostgreSQL-server. Den nya servern är fylld med data som fanns på huvudservern. Skapandetiden beror på mängden data på huvudhanteraren och tiden sedan den senaste veckovisa fullständiga säkerhetskopieringen. Tiden kan variera från några minuter till flera timmar.

Varje replik är aktiverad för [automatisk lagring.](concepts-pricing-tiers.md#storage-auto-grow) Med funktionen för automatisk tillväxt kan repliken hålla jämna steg med de data som replikeras till den och förhindra avbrott i replikeringen som orsakas av lagringsfel.

Läsreplikfunktionen använder PostgreSQL fysisk replikering, inte logisk replikering. Direktuppspelningsreplikering med replikeringsplatser är standardåtgärdsläget. Vid behov används loggfrakt för att komma ikapp.

Lär dig hur du [skapar en läsreplik i Azure-portalen](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Ansluta till en replik
När du skapar en replik ärver den inte brandväggsreglerna eller huvudserverns slutpunkt för VNet-tjänsten. Dessa regler måste ställas in oberoende av dem för repliken.

Repliken ärver administratörskontot från huvudservern. Alla användarkonton på huvudservern replikeras till läsreplikerna. Du kan bara ansluta till en läsreplik med hjälp av de användarkonton som är tillgängliga på huvudservern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användarkonto, som du skulle på en vanlig Azure-databas för PostgreSQL-server. För en server som heter **min replik** med admin användarnamn **myadmin**, kan du ansluta till repliken med hjälp av psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Ange lösenordet för användarkontot vid prompten.

## <a name="monitor-replication"></a>Övervaka replikering
Azure Database for PostgreSQL innehåller två mått för övervakning av replikering. De två måtten är **Max Lag Över repliker** och **Repliklagning**. Mer information om hur du visar dessa mått finns i **avsnittet Övervaka en replik** i [den lästa replikens instruktioner](howto-read-replicas-portal.md).

Måttet **Max fördröjning över repliker** visar fördröjningen i byte mellan huvud- och den mest eftersläpande repliken. Det här måttet är endast tillgängligt på huvudservern.

Måttet **Repliklagning** visar tiden sedan den senast återspelade transaktionen. Om det inte sker några transaktioner på huvudservern återspeglar måttet den här tidsfördröjningen. Det här måttet är endast tillgängligt för replikservrar. Replikfördröjning `pg_stat_wal_receiver` beräknas från vyn:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Ange en avisering för att informera dig när fördröjningen når ett värde som inte är acceptabelt för din arbetsbelastning. 

Om du vill ha ytterligare insikt kan du fråga huvudservern direkt för att hämta replikeringsfördröjningen i byte på alla repliker.

I PostgreSQL version 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

I PostgreSQL version 9.6 och tidigare:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Om en huvudserver eller en läsad replik startas om återspeglas den tid det tar att starta om och komma ikapp i måttet Repliklagning.

## <a name="stop-replication"></a>Stoppa replikering
Du kan stoppa replikeringen mellan en huvudsida och en replik. Stoppåtgärden gör att repliken startar om och tar bort replikeringsinställningarna. När repliken har stoppats mellan en huvudserver och en läsreplik blir repliken en fristående server. Data i den fristående servern är de data som var tillgängliga på repliken när kommandot stop replication startades. Den fristående servern hinner inte ikapp huvudservern.

> [!IMPORTANT]
> Den fristående servern kan inte göras till en replik igen.
> Innan du stoppar replikeringen på en läsreplik kontrollerar du att repliken har alla data som du behöver.

När du stoppar replikeringen förlorar repliken alla länkar till tidigare huvudrepliker och andra repliker.

Lär dig hur du [stoppar replikeringen till en replik](howto-read-replicas-portal.md).

## <a name="failover"></a>Redundans
Det finns ingen automatisk redundans mellan huvud- och replikservrar. 

Eftersom replikering är asynkront finns det fördröjning mellan huvud- och repliken. Mängden fördröjning kan påverkas av ett antal faktorer som hur tung arbetsbelastningen som körs på huvudservern är och svarstiden mellan datacenter. I de flesta fall sträcker sig replikfördröjning mellan några sekunder till ett par minuter. Du kan spåra din faktiska replikeringsfördröjning med hjälp av den metriska *repliklagningen*, som är tillgänglig för varje replik. Det här måttet visar tiden sedan den senast återspelade transaktionen. Vi rekommenderar att du identifierar vad din genomsnittliga fördröjning är genom att observera din replik fördröjning under en tidsperiod. Du kan ställa in en avisering på replikfördröjning, så att om det går utanför det förväntade intervallet kan du vidta åtgärder.

> [!Tip]
> Om du redundans till repliken anger fördröjningen när du länkar av repliken från bakgrunden hur mycket data som går förlorade.

När du har bestämt dig för att du vill växla över till en replik 

1. Stoppa replikeringen till repliken<br/>
   Det här steget är nödvändigt för att replikservern ska kunna acceptera skrivningar. Som en del av den här processen startas replikservern om och kopplas bort från bakgrunden. När du startar stoppreplikering tar backend-processen vanligtvis cirka 2 minuter att slutföra. Se [avsnittet stoppa replikering](#stop-replication) i den här artikeln för att förstå konsekvenserna av den här åtgärden.
    
2. Peka programmet på den (tidigare) repliken<br/>
   Varje server har en unik anslutningssträng. Uppdatera programmet så att det pekar på (tidigare) repliken i stället för huvudprogrammet.
    
När programmet har bearbetat läsningar och skrivningar har du slutfört redundansen. Hur många driftstopp dina programupplevelser beror på när du upptäcker ett problem och slutför steg 1 och 2 ovan.


## <a name="considerations"></a>Överväganden

I det här avsnittet sammanfattas överväganden om funktionen läsreplik.

### <a name="prerequisites"></a>Krav
Innan du skapar en `azure.replication_support` läsreplik måste parametern anges till **REPLIK** på huvudservern. När den här parametern ändras krävs en omstart av servern för att ändringen ska börja gälla. Parametern `azure.replication_support` gäller endast för nivåerna Allmänt ändamål och Minnesoptimerade.

### <a name="new-replicas"></a>Nya repliker
En läsreplik skapas som en ny Azure-databas för PostgreSQL-server. Det går inte att skapa en befintlig server till en replik. Du kan inte skapa en replik av en annan läsreplik.

### <a name="replica-configuration"></a>Konfiguration av replik
En replik skapas med samma beräknings- och lagringsinställningar som huvudsã¤nde. När en replik har skapats kan flera inställningar ändras oberoende av huvudservern: beräkningsgenerering, virtuella kärnor, lagring och lagringsperiod för säkerhetskopiering. Prisnivån kan också ändras oberoende av dem, förutom till eller från basic-nivån.

> [!IMPORTANT]
> Innan en huvudinställning uppdateras till ett nytt värde uppdaterar du replikkonfigurationen till ett lika stort eller större värde. På så sätt säkerställer du att repliken klarar alla ändringar som görs på huvudservern.

PostgreSQL kräver att `max_connections` värdet för parametern på läsrepliken är större än eller lika med huvudvärdet. Annars startar inte repliken. I Azure Database for PostgreSQL baseras `max_connections` parametervärdet på SKU. Mer information finns [i Gränser i Azure Database för PostgreSQL](concepts-limits.md). 

Om du försöker uppdatera servervärdena som beskrivs ovan, men inte följer gränserna, visas ett felmeddelande.

Brandväggsregler, virtuella nätverksregler och parameterinställningar ärvs inte från huvudservern till repliken när repliken skapas eller efteråt.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL kräver](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) att `max_prepared_transactions` värdet för parametern på läsrepliken är större än eller lika med huvudvärdet. Annars startar inte repliken. Om du vill `max_prepared_transactions` ändra på bakgrunden ändrar du den först på replikerna.

### <a name="stopped-replicas"></a>Stoppade repliker
Om du stoppar replikeringen mellan en huvudserver och en läsreplik startas repliken om för att tillämpa ändringen. Den stoppade repliken blir en fristående server som accepterar både läsningar och skrivningar. Den fristående servern kan inte göras till en replik igen.

### <a name="deleted-master-and-standalone-servers"></a>Borttagna huvudservrar och fristående servrar
När en huvudserver tas bort blir alla dess läsrepliker fristående servrar. Replikerna startas om för att återspegla den här ändringen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar och hanterar läsande repliker i Azure-portalen](howto-read-replicas-portal.md).
* Lär dig hur du [skapar och hanterar läsande repliker i Azure CLI och REST API](howto-read-replicas-cli.md).

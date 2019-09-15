---
title: Läsa repliker i Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs funktionen Läs replik i Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: e276340041e69101190645caad9dbf6de57abd95
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996502"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Läsa repliker i Azure Database for PostgreSQL-enskild server

Med funktionen Läs replik kan du replikera data från en Azure Database for PostgreSQL-server till en skrivskyddad Server. Du kan replikera från huvudservern till upp till fem repliker. Repliker uppdateras asynkront med PostgreSQL-motorn för intern replikering.

Repliker är nya servrar som du hanterar precis som vanliga Azure Database for PostgreSQL-servrar. För varje Läs replik debiteras du för den etablerade beräkningen i virtuella kärnor och lagring i GB/månad.

Lär dig hur du [skapar och hanterar repliker](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>När du ska använda en Läs replik
Funktionen Läs replik hjälper till att förbättra prestanda och skalning för Läs intensiva arbets belastningar. Läs arbets belastningar kan isoleras till replikerna, medan Skriv arbets belastningar kan dirigeras till huvud servern.

Ett vanligt scenario är att låta BI och analytiska arbets belastningar använda Läs repliken som data källa för rapportering.

Eftersom repliker är skrivskyddade kan de inte direkt minska Skriv kapacitets bördan på huvud servern. Den här funktionen är inte riktad mot Skriv intensiva arbets belastningar.

Funktionen Läs replik använder PostgreSQL asynkron replikering. Funktionen är inte avsedd för synkrona scenarier för replikering. Det kommer att bli en mätbar fördröjning mellan huvud servern och repliken. Data på repliken kommer slutligen att bli konsekventa med data i huvud servern. Använd den här funktionen för arbets belastningar som kan hantera denna fördröjning.

## <a name="cross-region-replication"></a>Replikering mellan regioner
Du kan skapa en Läs replik i en annan region än huvud servern. Replikering mellan regioner kan vara användbart för scenarier som haveri beredskap planering eller för att hämta data närmare dina användare.

Du kan ha en huvud server i valfri [Azure Database for PostgreSQL region](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). En huvud server kan ha en replik i dess kopplade region eller Universal Replica-regioner. I bilden nedan visas vilka replik regioner som är tillgängliga beroende på din huvud region.

[![Läs replik regioner](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universal Replica-regioner
Du kan alltid skapa en Läs replik i någon av följande regioner, oavsett var huvud servern finns. Det här är Universal Replica-regionerna:

Östra Australien, sydöstra Australien, centrala USA, Asien, östra, östra USA, östra USA 2, Japan, östra, västra Japan, centrala Korea, centrala, norra centrala USA, norra Europa, södra centrala USA, Sydostasien, Storbritannien, södra, Storbritannien, västra, Västeuropa, västra USA, västra USA 2.


### <a name="paired-regions"></a>Länkade regioner
Förutom Universal Replica-regioner kan du skapa en Läs replik i den Azure-kopplade regionen på huvud servern. Om du inte känner till din regions par kan du läsa mer i [artikeln Azure-kopplade regioner](../best-practices-availability-paired-regions.md).

Om du använder repliker över flera regioner för att planera haveri beredskap rekommenderar vi att du skapar repliken i den kopplade regionen i stället för någon av de andra regionerna. Kopplade regioner förhindrar samtidiga uppdateringar och prioriterar fysisk isolering och data placering.  

Det finns begränsningar att tänka på: 

* Regional tillgänglighet: Azure Database for PostgreSQL är tillgängligt i USA, västra 2, Frankrike, centrala, Förenade Arabemiraten nord och Tyskland, centrala. De kopplade regionerna är dock inte tillgängliga.
    
* Enkelriktade par: Vissa Azure-regioner är bara kopplade i en riktning. I dessa regioner ingår västra Indien, södra Brasilien. 
   Det innebär att en huvud server i västra Indien kan skapa en replik i södra Indien. En huvud server i södra Indien kan dock inte skapa en replik i västra Indien. Detta beror på att den sekundära regionen västra Indien är södra Indien, men den sekundära regionen i södra Indien är inte västra Indien.


## <a name="create-a-replica"></a>Skapa en replik
Huvud servern måste ha `azure.replication_support` parametern inställd på **replik**. När den här parametern ändras krävs en omstart av servern för att ändringen ska börja gälla. `azure.replication_support` (Parametern gäller endast för generell användning och minnesoptimerade nivåer).

När du startar arbets flödet skapa replik skapas en tom Azure Database for PostgreSQL-Server. Den nya servern fylls med de data som fanns på huvud servern. Skapande tiden beror på mängden data i huvud servern och tiden sedan den senaste veckovis fullständiga säkerhets kopieringen. Tiden kan vara från några minuter till flera timmar.

Varje replik är aktive rad för att utöka lagringen [automatiskt](concepts-pricing-tiers.md#storage-auto-grow). Funktionen för automatisk storleks ökning gör det möjligt för repliken att hålla sig uppdaterad med de data som replikeras till den och förhindrar en paus i replikeringen som orsakas av fel i lagrings utrymmet.

Funktionen Läs replik använder PostgreSQL fysisk replikering, inte logisk replikering. Att strömma replikering med hjälp av Replikerings-platser är standard åtgärds läget. Vid behov används logg överföring för att komma igång.

Lär dig hur du [skapar en Läs replik i Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Anslut till en replik
När du skapar en replik ärver den inte brand Väggs reglerna eller slut punkten för VNet-tjänsten på huvud servern. Dessa regler måste konfigureras separat för repliken.

Repliken ärver administratörs kontot från huvud servern. Alla användar konton på huvud servern replikeras till läsa repliker. Du kan bara ansluta till en Läs replik med hjälp av de användar konton som är tillgängliga på huvud servern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användar konto, precis som på en vanlig Azure Database for PostgreSQL Server. För en server med namnet **min replik** med admin username- **administratören**kan du ansluta till repliken med hjälp av psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Ange lösen ordet för användar kontot vid prompten.

## <a name="monitor-replication"></a>Övervaka replikering
Azure Database for PostgreSQL tillhandahåller två mått för övervakning av replikering. De två måtten är **maximal fördröjning mellan repliker** och **replik fördröjning**. Information om hur du visar dessa mått finns i avsnittet **övervaka en replik** i [artikeln Läs mer om att läsa replikering](howto-read-replicas-portal.md).

Måttet **Max fördröjning över repliker** visar fördröjningen i byte mellan huvud servern och den mest isolerings repliken. Detta mått är bara tillgängligt på huvud servern.

Värdet för **replik fördröjningen** visar tiden sedan den senaste återspelade transaktionen. Om det inte finns några transaktioner på huvud servern, motsvarar måttet denna tids fördröjning. Det här måttet är endast tillgängligt för replik servrar. Replik fördröjningen beräknas från `pg_stat_wal_receiver` vyn:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Ange en avisering som meddelar dig när replik fördröjningen når ett värde som inte är acceptabelt för din arbets belastning. 

Om du vill ha mer information kan du fråga huvud servern direkt för att hämta replikeringens fördröjning i byte på alla repliker.

I PostgreSQL version 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

I PostgreSQL version 9,6 och tidigare:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Om en huvud server eller en Läs replik startas om återspeglas den tid det tar att starta om och fånga upp i replikens fördröjnings mått.

## <a name="stop-replication"></a>Stoppa replikering
Du kan stoppa replikering mellan en huvud server och en replik. Åtgärden stoppa gör att repliken startas om och tar bort dess replikeringsinställningar. När replikeringen har stoppats mellan en huvud server och en Läs replik blir repliken en fristående server. Data i den fristående servern är de data som var tillgängliga på repliken när kommandot stoppa replikering startades. Den fristående servern är inte uppfångad med huvud servern.

> [!IMPORTANT]
> Den fristående servern kan inte göras till en replik igen.
> Innan du stoppar replikeringen på en Läs replik måste du se till att repliken har alla data som du behöver.

När du stoppar replikering förlorar repliken alla länkar till sin tidigare huvud replik och andra repliker.

Lär dig hur du [stoppar replikering till en replik](howto-read-replicas-portal.md).

## <a name="failover"></a>Redundans
Det finns ingen automatisk redundans mellan huvud-och replik servrar. 

Eftersom replikeringen är asynkron finns det en fördröjning mellan huvud servern och repliken. Mängden fördröjning kan påverkas av ett antal faktorer, t. ex. hur mycket hög belastningen på huvud servern som körs på huvud servern och fördröjningen mellan data Center. I de flesta fall är replik fördröjningen mellan några sekunder till några minuter. Du kan spåra den faktiska replikeringens fördröjning med hjälp av mått *replik fördröjningen*, som är tillgänglig för varje replik. Det här måttet visar tiden sedan den senaste återspelade transaktionen. Vi rekommenderar att du identifierar den genomsnittliga fördröjningen genom att iaktta din replik fördröjning under en viss tids period. Du kan ställa in en avisering på replik fördröjningen, så att om den går utanför det förväntade intervallet kan du vidta åtgärder.

> [!Tip]
> Om du redundansväxlas till repliken kommer fördröjningen vid den tidpunkt då du avlänkar repliken från huvud servern att indikera hur mycket data som förloras.

När du har valt att du vill redundansväxla till en replik, 

1. Stoppa replikering till repliken<br/>
   Det här steget är nödvändigt för att göra replik servern tillgänglig för skrivningar. Som en del av den här processen kommer replik servern att startas om och kopplas bort från huvud servern. När du har initierat stoppa replikeringen tar det vanligt vis ungefär 2 minuter att slutföra backend-processen. Se avsnittet [stoppa replikering](#stop-replication) i den här artikeln för att förstå konsekvenserna av den här åtgärden.
    
2. Peka ditt program till den (tidigare) repliken<br/>
   Varje server har en unik anslutnings sträng. Uppdatera programmet så att det pekar på den (tidigare) repliken i stället för huvud servern.
    
När ditt program har bearbetat läsningar och skrivningar har du slutfört redundansväxlingen. Hur lång tid det tar för program upplevelser att vara beroende av när du upptäcker ett problem och Slutför steg 1 och 2 ovan.


## <a name="considerations"></a>Överväganden

I det här avsnittet sammanfattas överväganden om funktionen Läs replik.

### <a name="prerequisites"></a>Förutsättningar
Innan du skapar en Läs replik `azure.replication_support` måste parametern ställas in på **replik** på huvud servern. När den här parametern ändras krävs en omstart av servern för att ändringen ska börja gälla. `azure.replication_support` Parametern gäller endast för generell användning-och minnesoptimerade nivåer.

### <a name="new-replicas"></a>Nya repliker
En Läs replik skapas som en ny Azure Database for PostgreSQL Server. Det går inte att göra en befintlig server till en replik. Du kan inte skapa en replik av en annan Läs replik.

### <a name="replica-configuration"></a>Replik konfiguration
En replik skapas med samma beräknings-och lagrings inställningar som huvud servern. När en replik har skapats kan flera inställningar ändras oberoende från huvud servern: beräknings generering, virtuella kärnor, lagring och kvarhållning av säkerhets kopior. Pris nivån kan också ändras oberoende, förutom till eller från Basic-nivån.

> [!IMPORTANT]
> Innan en huvud inställning uppdateras till ett nytt värde uppdaterar du replik konfigurationen till ett lika eller högre värde. På så sätt säkerställer du att repliken klarar alla ändringar som görs på huvudservern.

PostgreSQL kräver att värdet för `max_connections` parametern på Läs repliken är större än eller lika med huvudets värde, annars startar inte repliken. I Azure Database for PostgreSQL `max_connections` baseras parametervärdet på SKU: n. Mer information finns i [gränser i Azure Database for PostgreSQL](concepts-limits.md). 

Om du försöker uppdatera Server värden, men inte följer gränserna, visas ett fel meddelande.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[Postgresql kräver](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) att värdet för `max_prepared_transactions` parametern på Läs repliken är större än eller lika med huvudets värde, annars startar inte repliken. Om du vill ändra `max_prepared_transactions` i huvud repliken måste du först ändra den på replikerna.

### <a name="stopped-replicas"></a>Stoppade repliker
Om du stoppar replikeringen mellan en huvud server och en Läs replik, startar repliken om för att tillämpa ändringen. Den stoppade repliken blir en fristående server som accepterar både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

### <a name="deleted-master-and-standalone-servers"></a>Borttagna huvud servrar och fristående servrar
När en huvud server tas bort blir alla dess läsnings repliker fristående servrar. Replikerna har startats om för att återspegla den här ändringen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar och hanterar Läs repliker i Azure Portal](howto-read-replicas-portal.md).
* Lär dig hur du [skapar och hanterar Läs repliker i Azure CLI och REST API](howto-read-replicas-cli.md).

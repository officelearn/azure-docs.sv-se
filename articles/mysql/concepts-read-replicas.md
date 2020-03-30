---
title: Läs repliker - Azure Database för MySQL.
description: 'Lär dig mer om att läsa repliker i Azure Database för MySQL: välja regioner, skapa repliker, ansluta till repliker, övervaka replikering och stoppa replikering.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 98461928e465a103f73761afce5270234224fbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167353"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Skrivskyddad replik i Azure Database for MySQL

Med funktionen för skrivskyddade repliker kan du replikera data från en Azure Database for MySQL-server till en skrivskyddad server. Du kan replikera från huvudservern till upp till fem repliker. Replikerna uppdateras asynkront med MySQL-motorns interna replikeringsteknik som utgår från replikernas position i en binär loggfil (binlog). Mer information om binlog-replikering finns i [översikten över MySQL-binlogreplikering](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliker är nya servrar som du hanterar liknar vanliga Azure-databas för MySQL-servrar. För varje läsreplik faktureras du för den etablerade beräkningen i virtuella kärnor och lagring i GB/månad.

Mer information om MySQL-replikeringsfunktioner och problem finns i [Dokumentationen för MySQL-replikering](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>När ska en läsreplik användas

Läsreplikfunktionen hjälper till att förbättra prestanda och skala för läsintensiva arbetsbelastningar. Läsarbetsbelastningar kan isoleras till replikerna, medan skrivarbetsbelastningar kan dirigeras till huvudhanteraren.

Ett vanligt scenario är att bi- och analysarbetsbelastningar ska använda läsrepliken som datakälla för rapportering.

Eftersom repliker är skrivskyddade minskar de inte direkt skrivkapacitetsbördorna på bakgrunden. Den här funktionen är inte inriktad på skrivintensiva arbetsbelastningar.

Funktionen läsreplik använder MySQL asynkron replikering. Funktionen är inte avsedd för synkrona replikeringsscenarier. Det kommer att finnas en mätbar fördröjning mellan huvud- och repliken. Data på repliken blir så småningom konsekventa med data på huvudhanteraren. Använd den här funktionen för arbetsbelastningar som kan hantera den här fördröjningen.

## <a name="cross-region-replication"></a>Replikering mellan regioner
Du kan skapa en läsreplik i en annan region än huvudservern. Replikering mellan regioner kan vara till hjälp för scenarier som planering av haveriberedskap eller föra data närmare användarna.

Du kan ha en huvudserver i valfri [Azure-databas för MySQL-region](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  En huvudserver kan ha en replik i den parade regionen eller de universella replikregionerna. Bilden nedan visar vilka replikområden som är tillgängliga beroende på huvudregionen.

[![Läsa replikområden](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universella replikregioner
Du kan skapa en läsreplik i något av följande områden, oavsett var huvudservern finns. De universella replikregioner som stöds är:

Australien Östra, Australien Sydost, Centrala USA, Östasien, Östra USA, Östra USA 2, Östra Japan, Japan Väst, Korea Central, Korea Syd, Norra centrala USA, Norra Europa, Södra centrala USA, Sydostasien, Storbritannien Syd, Storbritannien Väst, Västeuropa, Västra USA.

*Västra US 2 är inte tillgänglig för tillfället som en replikplats för flera regioner.


### <a name="paired-regions"></a>Länkade regioner
Förutom de universella replikregionerna kan du skapa en läsreplik i azure-parade regionen på huvudservern. Om du inte känner till regionens par kan du läsa mer av [artikeln Azure Paired Regions](../best-practices-availability-paired-regions.md).

Om du använder repliker över flera regioner för planering av haveriberedskap rekommenderar vi att du skapar repliken i den parade regionen i stället för en av de andra regionerna. Parade regioner undviker samtidiga uppdateringar och prioriterar fysisk isolering och datahemvist.  

Det finns dock begränsningar att tänka på: 

* Regional tillgänglighet: Azure Database for MySQL är tillgänglig i Västra USA 2, France Central, UAE North och Germany Central. Deras parade regioner är dock inte tillgängliga.
    
* Uni-directional par: Vissa Azure-regioner är bara ihopkopplade i en riktning. Dessa regioner inkluderar Västra Indien, Södra Brasilien och US Gov Virginia. 
   Det innebär att en huvudserver i västra Indien kan skapa en replik i södra Indien. En huvudserver i södra Indien kan dock inte skapa en replik i västra Indien. Detta beror på att Västra Indiens sekundära region är södra Indien, men södra Indiens sekundära region är inte västra Indien.


## <a name="create-a-replica"></a>Skapa en replik

Om en huvudserver inte har några befintliga replikservrar startas befälhavaren först om för att förbereda sig för replikering.

När du startar arbetsflödet för att skapa replik skapas en tom Azure-databas för MySQL-server. Den nya servern är fylld med data som fanns på huvudservern. Skapandetiden beror på mängden data på huvudhanteraren och tiden sedan den senaste veckovisa fullständiga säkerhetskopieringen. Tiden kan variera från några minuter till flera timmar.

Varje replik är aktiverad för [automatisk lagring.](concepts-pricing-tiers.md#storage-auto-grow) Med funktionen för automatisk tillväxt kan repliken hålla jämna steg med de data som replikeras till den och förhindra ett avbrott i replikeringen som orsakas av fel utanför lagring.

Lär dig hur du [skapar en läsreplik i Azure-portalen](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Ansluta till en replik

När en replik skapas ärver brandväggsreglerna eller slutpunkten för VNet-tjänsten för huvudservern. Därefter är dessa regler oberoende av huvudservern.

Repliken ärver administratörskontot från huvudservern. Alla användarkonton på huvudservern replikeras till läsreplikerna. Du kan bara ansluta till en läsreplik med hjälp av de användarkonton som är tillgängliga på huvudservern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användarkonto, som du skulle på en vanlig Azure-databas för MySQL-server. För en server som heter **myreplica** med admin användarnamn **myadmin**, kan du ansluta till repliken med hjälp av mysql CLI:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Ange lösenordet för användarkontot vid prompten.

## <a name="monitor-replication"></a>Övervaka replikering

Azure Database for MySQL tillhandahåller **fördröjningen för replikering i sekunder** i Azure Monitor. Det här måttet är endast tillgängligt för repliker.

Det här måttet `seconds_behind_master` beräknas med hjälp av `SHOW SLAVE STATUS` det mått som är tillgängligt i MySQL:s kommando.

Ställ in en avisering för att informera dig när replikeringsfördröjningen når ett värde som inte är acceptabelt för din arbetsbelastning.

## <a name="stop-replication"></a>Stoppa replikering

Du kan stoppa replikeringen mellan en huvudsida och en replik. När repliken har stoppats mellan en huvudserver och en läsreplik blir repliken en fristående server. Data i den fristående servern är de data som var tillgängliga på repliken när kommandot stop replication startades. Den fristående servern hinner inte ikapp huvudservern.

När du väljer att stoppa replikeringen till en replik förlorar den alla länkar till dess tidigare huvud och andra repliker. Det finns ingen automatisk redundans mellan en huvudhanterare och dess replik.

> [!IMPORTANT]
> Den fristående servern kan inte göras till en replik igen.
> Innan du stoppar replikeringen på en läsreplik kontrollerar du att repliken har alla data som du behöver.

Lär dig hur du [stoppar replikeringen till en replik](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

### <a name="pricing-tiers"></a>Prisnivåer

Läsrepliker är för närvarande endast tillgängliga på prisnivåerna Allmänt ändamål och Minne optimerad.

### <a name="master-server-restart"></a>Omstart av huvudservern

När du skapar en replik för en huvudhanterare som inte har några befintliga repliker startar huvuduppsättningen först om för att förbereda sig för replikering. Ta hänsyn till detta och utföra dessa operationer under en lågtrafikperiod.

### <a name="new-replicas"></a>Nya repliker

En läsreplik skapas som en ny Azure-databas för MySQL-server. Det går inte att skapa en befintlig server till en replik. Du kan inte skapa en replik av en annan läsreplik.

### <a name="replica-configuration"></a>Konfiguration av replik

En replik skapas med samma serverkonfiguration som huvudprogrammet. När en replik har skapats kan flera inställningar ändras oberoende av huvudservern: beräkningsgenerering, virtuella kärnor, lagring och lagringsperiod för säkerhetskopiering. Prisnivån kan också ändras oberoende av dem, förutom till eller från basic-nivån.

> [!IMPORTANT]
> Uppdatera replikkonfigurationen till samma eller högre värden innan en huvudserverkonfiguration uppdateras till nya värden. På så sätt säkerställer du att repliken klarar alla ändringar som görs på huvudservern.

Brandväggsregler, virtuella nätverksregler och parameterinställningar ärvs från huvudservern till repliken när repliken skapas. Efteråt är replikens regler oberoende.

### <a name="stopped-replicas"></a>Stoppade repliker

Om du stoppar replikeringen mellan en huvudserver och en läsreplik blir den stoppade repliken en fristående server som accepterar både läsningar och skrivningar. Den fristående servern kan inte göras till en replik igen.

### <a name="deleted-master-and-standalone-servers"></a>Borttagna huvudservrar och fristående servrar

När en huvudserver tas bort stoppas replikeringen till alla lästa repliker. Dessa repliker blir automatiskt fristående servrar och kan acceptera både läsningar och skrivningar. Själva huvudservern tas bort.

### <a name="user-accounts"></a>Användarkonton

Användare på huvudservern replikeras till läsreplikerna. Du kan bara ansluta till en läsreplik med hjälp av de användarkonton som är tillgängliga på huvudservern.

### <a name="server-parameters"></a>Serverparametrar

I syfte att förhindra att data blir osynkroniserade samt att undvika potentiell dataförlust eller skadade data är vissa serverparametrar låsta från att uppdateras vid användning av skrivskyddade repliker.

Följande serverparametrar är låsta på både huvud- och replikservrarna:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

Parametern [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) är låst på replikservrarna. 

### <a name="other"></a>Annat

- Globala transaktionsidentifierare (GTID) stöds inte.
- Det går inte att skapa en replik av en replik.
- Minnestabeller kan leda till att repliker inte synkroniseras. Detta är en begränsning av MySQL-replikeringstekniken. Läs mer i [MySQL-referensdokumentationen](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) för mer information.
- Kontrollera att huvudservertabellerna har primära nycklar. Brist på primärnycklar kan resultera i replikeringsfördröjning mellan huvud- och replikerna.
- Granska den fullständiga listan över MySQL-replikeringsbegränsningar i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar och hanterar läsande repliker med Azure-portalen](howto-read-replicas-portal.md)
- Lär dig hur du [skapar och hanterar läsande repliker med Hjälp av Azure CLI- och REST API](howto-read-replicas-cli.md)

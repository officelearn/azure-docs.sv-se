---
title: Läs repliker i Azure Database for MariaDB
description: Den här artikeln beskriver skrivskyddade repliker för Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 8abe257090b5159053a37350c9e24cc27073679b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079371"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Läs repliker i Azure Database for MariaDB

Läs replica-funktionen kan du replikera data från en Azure Database for MariaDB-server till en skrivskyddad server. Du kan replikera från huvudservern till upp till fem repliker inom samma region som huvudserver. Repliker uppdateras asynkront med MariaDB-motorn binär logg (binlog) fil baserat på positionen replikeringsteknik med globala transaktions-ID (GTID). Mer information om binlog replikering finns i [binlog replikering: översikt](https://mariadb.com/kb/en/library/replication-overview/).

> [!IMPORTANT]
> Samma region läsa repliker är för närvarande i offentlig förhandsversion.

Repliker är nya servrar som du hanterar liknar vanliga Azure Database for MariaDB-servrar. Var finns replik du faktureras för den etablerade beräkningen i virtuella kärnor och lagring i GB / månad.

Läs mer om GTID replikering i den [MariaDB replikering dokumentation](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>När du ska använda en skrivskyddad replik

Läs replica-funktionen hjälper till att förbättra prestanda och skalning för läsintensiva arbetsbelastningar. Läs arbetsbelastningar kan isoleras repliker, medan skrivning arbetsbelastningar kan dirigeras till huvudservern.

Ett vanligt scenario är att ha BI och analytiska arbetsbelastningar använder den skrivskyddade repliken som datakälla för rapportering.

Eftersom repliker är skrivskyddade, minska inte de direkt behovet av write-kapacitet på huvudmålservern. Den här funktionen är inte riktad mot write-intensiva arbetsbelastningar.

Läs replica-funktionen använder asynkron replikering. Funktionen är inte avsedd för synkron replikeringsscenarier. Det blir en mätbara fördröjning mellan huvudservern och repliken. Data på repliken blir så småningom konsekventa med data i bakgrunden. Använd denna funktion för arbetsbelastningar som kan hantera den här fördröjningen.

Läs repliker kan förbättra din haveriberedskapsplan. Om det finns ett regionalt haveri och din huvudservern är tillgänglig, kan du dirigera din arbetsbelastning till en replik i en annan region. Då kan du först repliken acceptera skrivningar med hjälp av funktionen Stoppa replikering. Du kan sedan omdirigera ditt program genom att uppdatera anslutningssträngen. Läs mer i den [replikeringsstopp](#stop-replication) avsnittet.

## <a name="create-a-replica"></a>Skapa en replik

Om en huvudserver har inga befintliga replikservern, startas först huvudservern för att förbereda själva för replikering.

När du börjar skapa replica arbetsflöde, skapas en tom Azure Database for MariaDB-server. Den nya servern är fylld med de data som fanns på huvudservern. Tiden för skapandet beror på mängden data på huvudservern och hur lång tid sedan den senaste veckovisa fullständiga säkerhetskopieringen. Tiden kan variera mellan några minuter till flera timmar.

> [!NOTE]
> Om du inte har en avisering uppsättning lagring på dina servrar, rekommenderar vi att du gör det. Aviseringen informerar dig när en server närmar sig lagringsgränsen, vilket påverkar replikeringen.

Lär dig hur du [skapar en skrivskyddad replik i Azure-portalen](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Ansluta till en replik

När du skapar en replik, ärver den inte brandväggsregler eller VNet-tjänstslutpunkt för domänens huvudserver. Dessa regler måste konfigureras separat för repliken.

Repliken ärver administratörskontot som från huvudservern. Alla användarkonton på huvudservern replikeras till de skrivskyddade replikerna. Du kan bara ansluta till en skrivskyddad replik med hjälp av användarkonton som är tillgängliga på huvudservern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användarkonto, precis som på en vanlig Azure-databas för MariaDB-server. För en server med namnet **myreplica** med administratörsanvändarnamnet **myadmin**, du kan ansluta till repliken med hjälp av mysql CLI:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Ange lösenordet för användarkontot i Kommandotolken.

## <a name="monitor-replication"></a>Övervakare för replikering

Azure Database for MariaDB tillhandahåller den **replikeringsfördröjning i sekunder** mått i Azure Monitor. Det här måttet är tillgängligt för repliker endast.

Det här måttet beräknas med hjälp av den `seconds_behind_master` mått som är tillgängliga i MariaDB `SHOW SLAVE STATUS` kommando.

Ställa in en avisering om när replikeringsfördröjning når ett värde som inte är acceptabel för din arbetsbelastning.

## <a name="stop-replication"></a>Stoppa replikering

Du kan stoppa replikering mellan en och en replik. När replikeringen har stoppats mellan en huvudserver och en skrivskyddad replik, blir repliken en fristående server. Data i den fristående servern är de data som fanns på repliken när kommandot Stoppa replikering har startats. Den fristående servern ifatt inte med huvudservern.

När du väljer att stoppa replikering till en replik, förlorar alla länkar till dess tidigare huvud- och andra repliker. Det finns ingen automatisk växling mellan en huvudserver och dess replik.

> [!IMPORTANT]
> Den fristående servern kan inte göras i en replik igen.
> Kontrollera att repliken har alla data som du behöver innan du stoppa replikering på en skrivskyddad replik.

Lär dig hur du [Stoppa replikering till en replik](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

### <a name="pricing-tiers"></a>Prisnivåer

Läs repliker är för närvarande endast tillgängliga i prisnivåer för generell användning och Minnesoptimerad.

### <a name="master-server-restart"></a>Huvudservern omstart

När du skapar en replik för en huvudserver som har inga befintliga replikeringar startas först huvudservern för att förbereda själva för replikering. Ha i åtanke och utföra dessa åtgärder under en period som med låg belastning.

### <a name="new-replicas"></a>Nya repliker

En skrivskyddad replik skapas som en ny Azure Database for MariaDB-server. En befintlig server kan inte göras i en replik. Du kan inte skapa en replik av en annan skrivskyddade replik.

### <a name="replica-configuration"></a>Repliken konfiguration

En replik skapas med hjälp av samma serverkonfiguration som huvudserver. När en replik skapas flera inställningar kan ändras oberoende från huvudservern: compute-generering, vCores, lagring, kvarhållningsperiod för säkerhetskopiering och MariaDB Motorversion. Prisnivån kan också ändras oberoende av varandra, förutom till eller från Basic-nivån.

> [!IMPORTANT]
> Innan en huvudserver-konfiguration har uppdaterats till nya värden, uppdatera konfigurationen för repliken till samma eller högre värden. Den här åtgärden säkerställer att replikeringen kan hålla jämna steg med ändringar som görs till huvudservern.

### <a name="stopped-replicas"></a>Stoppad repliker

Om du stoppar replikering mellan en huvudserver och en skrivskyddad replik blir stoppad repliken en fristående server som accepterar både läsningar och skrivningar. Den fristående servern kan inte göras i en replik igen.

### <a name="deleted-master-and-standalone-servers"></a>Borttagna huvud- och fristående servrar

När en huvudserver tas bort så stoppas replikeringen till alla skrivskyddade repliker. De här replikeringarna blir fristående servrar. Huvudservern själva tas bort.

### <a name="user-accounts"></a>Användarkonton

Användare på huvudservern replikeras till de skrivskyddade replikerna. Du kan bara ansluta till en skrivskyddad replik med hjälp av användarkontona som är tillgängliga på huvudservern.

### <a name="server-parameters"></a>Serverparametrar

Att förhindra att data blir osynkroniserad och för att undvika potentiell dataförlust eller skadade data låses vissa serverparametrar uppdateras när med läser repliker.

Följande serverparametrar är låsta på både master och repliken:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

Den [ `event_scheduler` ](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) parametern är låst på replikservrarna.

### <a name="other"></a>Annat

- Det går inte att skapa en replik av en replik.
- InMemory-tabeller kan medföra att replikerna blir osynkroniserad. Det här är en begränsning i MariaDB-replikeringsteknik.
- Se till att huvudservern tabellerna har primärnycklar. Brist på primärnycklar kan resultera i replikeringsfördröjning mellan huvud- och repliker.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa och hantera skrivskyddade repliker med Azure portal](howto-read-replicas-portal.md)

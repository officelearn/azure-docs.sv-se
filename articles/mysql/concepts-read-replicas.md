---
title: Läs repliker i Azure Database för MySQL.
description: Den här artikeln beskrivs skrivskyddade repliker för Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/30/2019
ms.openlocfilehash: be592cb6bb7c041fab0a2f96a338f4f4bb0ff00a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510928"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Läs repliker i Azure Database for MySQL

Läs replica-funktionen kan du replikera data från en Azure Database for MySQL-server till en skrivskyddad server. Du kan replikera från huvudservern till upp till fem repliker. Repliker uppdateras asynkront med MySQL-motorn interna binär logg (binlog) fil baserat på positionen replikeringsteknik. Mer information om binlog replikering finns i [översikt över replikering av MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

> [!IMPORTANT]
> Du kan skapa en skrivskyddad replik i samma region som din huvudservern eller i alla andra Azure-regioner valfri. Replikering över flera regioner är för närvarande i offentlig förhandsversion.

Repliker är nya servrar som du hanterar liknar vanliga Azure Database for MySQL-servrar. Var finns replik du faktureras för den etablerade beräkningen i virtuella kärnor och lagring i GB / månad.

Läs mer om funktioner för replikering av MySQL och problem i den [dokumentation för replikering av MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>När du ska använda en skrivskyddad replik

Läs replica-funktionen hjälper till att förbättra prestanda och skalning för läsintensiva arbetsbelastningar. Läs arbetsbelastningar kan isoleras repliker, medan skrivning arbetsbelastningar kan dirigeras till huvudservern.

Ett vanligt scenario är att ha BI och analytiska arbetsbelastningar använder den skrivskyddade repliken som datakälla för rapportering.

Eftersom repliker är skrivskyddade, minska inte de direkt behovet av write-kapacitet på huvudmålservern. Den här funktionen är inte riktad mot write-intensiva arbetsbelastningar.

Läs replica-funktionen använder asynkron replikering av MySQL. Funktionen är inte avsedd för synkron replikeringsscenarier. Det blir en mätbara fördröjning mellan huvudservern och repliken. Data på repliken blir så småningom konsekventa med data i bakgrunden. Använd denna funktion för arbetsbelastningar som kan hantera den här fördröjningen.

Läs repliker kan förbättra din haveriberedskapsplan. Om det finns ett regionalt haveri och din huvudservern är tillgänglig, kan du dirigera din arbetsbelastning till en replik i en annan region. Då kan du först repliken acceptera skrivningar med hjälp av funktionen Stoppa replikering. Du kan sedan omdirigera ditt program genom att uppdatera anslutningssträngen. Läs mer i den [replikeringsstopp](#stop-replication) avsnittet.

## <a name="create-a-replica"></a>Skapa en replik

Om en huvudserver har inga befintliga replikservern, startas först huvudservern för att förbereda själva för replikering.

När du börjar skapa replica arbetsflöde, skapas en tom Azure Database for MySQL-server. Den nya servern är fylld med de data som fanns på huvudservern. Tiden för skapandet beror på mängden data på huvudservern och hur lång tid sedan den senaste veckovisa fullständiga säkerhetskopieringen. Tiden kan variera mellan några minuter till flera timmar.

> [!NOTE]
> Om du inte har en avisering uppsättning lagring på dina servrar, rekommenderar vi att du gör det. Aviseringen informerar dig när en server närmar sig lagringsgränsen, vilket påverkar replikeringen.

Lär dig hur du [skapar en skrivskyddad replik i Azure-portalen](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Ansluta till en replik

När du skapar en replik, ärver den inte brandväggsregler eller VNet-tjänstslutpunkt för domänens huvudserver. Dessa regler måste konfigureras separat för repliken.

Repliken ärver administratörskontot som från huvudservern. Alla användarkonton på huvudservern replikeras till de skrivskyddade replikerna. Du kan bara ansluta till en skrivskyddad replik med hjälp av användarkonton som är tillgängliga på huvudservern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användarkonto, precis som på en vanlig Azure-databas för MySQL-server. För en server med namnet **myreplica** med administratörsanvändarnamnet **myadmin**, du kan ansluta till repliken med hjälp av mysql CLI:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Ange lösenordet för användarkontot i Kommandotolken.

## <a name="monitor-replication"></a>Övervakare för replikering

Azure Database for MySQL tillhandahåller de **replikeringsfördröjning i sekunder** mått i Azure Monitor. Det här måttet är tillgängligt för repliker endast.

Det här måttet beräknas med hjälp av den `seconds_behind_master` mått som är tillgängliga i MySQL: s `SHOW SLAVE STATUS` kommando.

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

En skrivskyddad replik skapas som en ny Azure Database for MySQL-server. En befintlig server kan inte göras i en replik. Du kan inte skapa en replik av en annan skrivskyddade replik.

### <a name="replica-configuration"></a>Repliken konfiguration

En replik skapas med hjälp av samma serverkonfiguration som huvudserver. När en replik skapas flera inställningar kan ändras oberoende från huvudservern: compute-generering, vCores, lagring, kvarhållningsperiod för säkerhetskopiering och MySQL-databasmotorn version. Prisnivån kan också ändras oberoende av varandra, förutom till eller från Basic-nivån.

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
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

Den [ `event_scheduler` ](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parametern är låst på replikservrarna. 

### <a name="other"></a>Annat 

- Globala transaktions-ID: n (GTID) stöds inte.
- Det går inte att skapa en replik av en replik.
- InMemory-tabeller kan medföra att replikerna blir osynkroniserad. Detta är en begränsning av MySQL-replikeringsteknik. Läs mer i den [MySQL referensdokumentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) för mer information.
- Se till att huvudservern tabellerna har primärnycklar. Brist på primärnycklar kan resultera i replikeringsfördröjning mellan huvud- och repliker.
- Läs den fullständiga listan med begränsningar för replikering av MySQL i den [MySQL-dokumentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa och hantera skrivskyddade repliker med Azure portal](howto-read-replicas-portal.md)
- Lär dig hur du [skapa och hantera skrivskyddade repliker med Azure CLI](howto-read-replicas-cli.md)

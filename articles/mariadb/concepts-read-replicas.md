---
title: Läs repliker i Azure Database for MariaDB
description: I den här artikeln beskrivs Läs repliker för Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 6c1104ba340f23a76d5b9b4167f895f5a115152c
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773294"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Läs repliker i Azure Database for MariaDB

Med funktionen Läs replik kan du replikera data från en Azure Database for MariaDB-server till en skrivskyddad Server. Du kan replikera från huvudservern till upp till fem repliker. Repliker uppdateras asynkront med MariaDB-motorns binära logg (BinLog)-baserad teknik för replikering med globalt transaktions-ID (GTID). Mer information om BinLog-replikering finns i [Översikt över BinLog-replikering](https://mariadb.com/kb/en/library/replication-overview/).

Repliker är nya servrar som du hanterar precis som vanliga Azure Database for MariaDB-servrar. För varje Läs replik debiteras du för den etablerade beräkningen i virtuella kärnor och lagring i GB/månad.

Om du vill veta mer om GTID-replikering kan du läsa [dokumentationen om MariaDB-replikering](https://mariadb.com/kb/en/library/gtid/).

## <a name="when-to-use-a-read-replica"></a>När du ska använda en Läs replik

Funktionen Läs replik hjälper till att förbättra prestanda och skalning för Läs intensiva arbets belastningar. Läs arbets belastningar kan isoleras till replikerna, medan Skriv arbets belastningar kan dirigeras till huvud servern.

Ett vanligt scenario är att låta BI och analytiska arbets belastningar använda Läs repliken som data källa för rapportering.

Eftersom repliker är skrivskyddade kan de inte direkt minska Skriv kapacitets bördan på huvud servern. Den här funktionen är inte riktad mot Skriv intensiva arbets belastningar.

Funktionen Läs replik använder asynkron replikering. Funktionen är inte avsedd för synkrona scenarier för replikering. Det kommer att bli en mätbar fördröjning mellan huvud servern och repliken. Data på repliken kommer slutligen att bli konsekventa med data i huvud servern. Använd den här funktionen för arbets belastningar som kan hantera denna fördröjning.


## <a name="cross-region-replication"></a>Replikering mellan regioner
Du kan skapa en Läs replik i en annan region än huvud servern. Replikering mellan regioner kan vara användbart för scenarier som haveri beredskap planering eller för att hämta data närmare dina användare.

Du kan ha en huvud server i valfri [Azure Database for MariaDB region](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  En huvud server kan ha en replik i dess kopplade region eller Universal Replica-regioner. I bilden nedan visas vilka replik regioner som är tillgängliga beroende på din huvud region.

[![Läs replik regioner](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universal Replica-regioner
Du kan alltid skapa en Läs replik i någon av följande regioner, oavsett var huvud servern finns. Det här är Universal Replica-regionerna:

Östra Australien, sydöstra Australien, centrala USA, Asien, östra, östra USA, östra USA 2, Japan, östra, västra Japan, centrala Korea, centrala, norra centrala USA, norra Europa, södra centrala USA, Sydostasien, Storbritannien, södra, Storbritannien, västra, Västeuropa, västra USA, västra USA 2.


### <a name="paired-regions"></a>Länkade regioner
Förutom Universal Replica-regioner kan du skapa en Läs replik i den Azure-kopplade regionen på huvud servern. Om du inte känner till din regions par kan du läsa mer i [artikeln Azure-kopplade regioner](../best-practices-availability-paired-regions.md).

Om du använder repliker över flera regioner för att planera haveri beredskap rekommenderar vi att du skapar repliken i den kopplade regionen i stället för någon av de andra regionerna. Kopplade regioner förhindrar samtidiga uppdateringar och prioriterar fysisk isolering och data placering.  

Det finns dock begränsningar att tänka på: 

* Regional tillgänglighet: Azure Database for MariaDB är tillgängligt i USA, västra 2, Frankrike, centrala, Förenade Arabemiraten nord och Tyskland, centrala. De kopplade regionerna är dock inte tillgängliga.
    
* Enkelriktade par: Vissa Azure-regioner är bara kopplade i en riktning. I dessa regioner ingår västra Indien, södra Brasilien och US Gov, Virginia. 
   Det innebär att en huvud server i västra Indien kan skapa en replik i södra Indien. En huvud server i södra Indien kan dock inte skapa en replik i västra Indien. Detta beror på att den sekundära regionen västra Indien är södra Indien, men den sekundära regionen i södra Indien är inte västra Indien.


## <a name="create-a-replica"></a>Skapa en replik

Om en huvud server inte har några befintliga replik servrar startar originalet om första gången för att förbereda sig för replikering.

När du startar arbets flödet skapa replik skapas en tom Azure Database for MariaDB-Server. Den nya servern fylls med de data som fanns på huvud servern. Skapande tiden beror på mängden data i huvud servern och tiden sedan den senaste veckovis fullständiga säkerhets kopieringen. Tiden kan vara från några minuter till flera timmar.

> [!NOTE]
> Om du inte har en lagrings avisering som kon figurer ATS på dina servrar, rekommenderar vi att du gör det. Aviseringen informerar dig när en server närmar sig lagrings gränsen, vilket kommer att påverka replikeringen.

Lär dig hur du [skapar en Läs replik i Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Anslut till en replik

När du skapar en replik ärver den inte brand Väggs reglerna eller slut punkten för VNet-tjänsten på huvud servern. Dessa regler måste konfigureras separat för repliken.

Repliken ärver administratörs kontot från huvud servern. Alla användar konton på huvud servern replikeras till läsa repliker. Du kan bara ansluta till en Läs replik med hjälp av de användar konton som är tillgängliga på huvud servern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användar konto, precis som på en vanlig Azure Database for MariaDB Server. För en server med namnet **unreplica** med administratörs **användar namnet administratör kan**du ansluta till repliken med hjälp av MySQL CLI:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Ange lösen ordet för användar kontot vid prompten.

## <a name="monitor-replication"></a>Övervaka replikering

Azure Database for MariaDB anger måttet för **replikeringsfördröjning i sekunder** i Azure Monitor. Måttet är endast tillgängligt för repliker.

Måttet beräknas med hjälp av `seconds_behind_master` måttet som är tillgängligt `SHOW SLAVE STATUS` i MariaDB-kommandot.

Ange en avisering för att meddela dig när fördröjningen för replikering når ett värde som inte är acceptabelt för din arbets belastning.

## <a name="stop-replication"></a>Stoppa replikering

Du kan stoppa replikering mellan en huvud server och en replik. När replikeringen har stoppats mellan en huvud server och en Läs replik blir repliken en fristående server. Data i den fristående servern är de data som var tillgängliga på repliken när kommandot stoppa replikering startades. Den fristående servern är inte uppfångad med huvud servern.

När du väljer att stoppa replikeringen till en replik förlorar den alla länkar till den tidigare repliken och andra repliker. Det finns ingen automatisk redundans mellan en huvud server och dess replik.

> [!IMPORTANT]
> Den fristående servern kan inte göras till en replik igen.
> Innan du stoppar replikeringen på en Läs replik måste du se till att repliken har alla data som du behöver.

Lär dig hur du [stoppar replikering till en replik](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

### <a name="pricing-tiers"></a>Prisnivåer

Läs repliker är för närvarande endast tillgängliga i Generell användning och minnesoptimerade pris nivåer.

### <a name="master-server-restart"></a>Omstart av Master Server

När du skapar en replik för en huvud server som inte har några befintliga repliker, startar originalet om först för att förbereda sig för replikering. Ta detta i beaktande och utför dessa åtgärder under en period med låg belastning.

### <a name="new-replicas"></a>Nya repliker

En Läs replik skapas som en ny Azure Database for MariaDB Server. Det går inte att göra en befintlig server till en replik. Du kan inte skapa en replik av en annan Läs replik.

### <a name="replica-configuration"></a>Replik konfiguration

En replik skapas med samma server konfiguration som huvud servern. När en replik har skapats kan flera inställningar ändras oberoende från huvud servern: beräknings generation, virtuella kärnor, lagring, kvarhållning av säkerhets kopior och MariaDB motor version. Pris nivån kan också ändras oberoende, förutom till eller från Basic-nivån.

> [!IMPORTANT]
> Uppdatera replikkonfigurationen till samma eller högre värden innan en huvudserverkonfiguration uppdateras till nya värden. På så sätt säkerställer du att repliken klarar alla ändringar som görs på huvudservern.

### <a name="stopped-replicas"></a>Stoppade repliker

Om du stoppar replikeringen mellan en huvud server och en Läs replik blir den stoppade repliken en fristående server som accepterar både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

### <a name="deleted-master-and-standalone-servers"></a>Borttagna huvud servrar och fristående servrar

När en huvud server tas bort, stoppas replikeringen till alla Läs repliker. Dessa repliker blir fristående servrar. Själva huvud servern tas bort.

### <a name="user-accounts"></a>Användarkonton

Användare på huvud servern replikeras till läsa repliker. Du kan bara ansluta till en Läs replik med de användar konton som är tillgängliga på huvud servern.

### <a name="server-parameters"></a>Serverparametrar

För att förhindra att data blir osynkroniserade och för att undvika potentiell data förlust eller skada, är vissa Server parametrar låsta från att uppdateras när du använder Läs repliker.

Följande Server parametrar är låsta på både huvud-och replik servern:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

[`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) Parametern är låst på replik servrarna.

### <a name="other"></a>Annat

- Det finns inte stöd för att skapa en replik av en replik.
- InMemory-tabeller kan orsaka att repliker blir osynkroniserade. Detta är en begränsning av MariaDB-replikerings teknik.
- Se till att huvud server tabellerna har primära nycklar. Brist på primär nycklar kan leda till replikeringsfördröjning mellan huvud-och replikerna.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar och hanterar Läs repliker med hjälp av Azure Portal](howto-read-replicas-portal.md)
- Lär dig hur du [skapar och hanterar Läs repliker med Azure CLI](howto-read-replicas-cli.md)
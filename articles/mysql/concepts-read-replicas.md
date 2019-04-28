---
title: Läs repliker i Azure Database för MySQL.
description: Den här artikeln beskrivs skrivskyddade repliker för Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6e33c7571dc735ce9984a0ce1b37275a6c4c7eca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093224"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Läs repliker i Azure Database for MySQL

Läs replica-funktionen kan du replikera data från en Azure Database for MySQL-server (master) till upp till fem skrivskyddade servrar (repliker) inom samma Azure-region. Skrivskyddade repliker uppdateras asynkront med MySQL-motorn interna binär logg (binlog) fil baserat på positionen replikeringsteknik. Mer information om binlog replikering finns i [översikt över replikering av MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliker som skapats i Azure Database for MySQL-tjänsten är nya servrar som kan hanteras på samma sätt som normal/fristående MySQL-servrar. För varje skrivskyddad replik faktureras du för den etablerade beräkningen i virtuella kärnor och den etablerade lagringen i GB/månad.

Läs mer om funktioner för replikering av MySQL och problem i den [dokumentation för replikering av MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-read-replicas"></a>När du ska använda skrivskyddade repliker

Program och arbetsbelastningar som läses beräkningsintensiva kan betjänas av skrivskyddade repliker. Läs bidrar till öka mängden Läs tillgänglig kapacitet jämfört med om du bara använda en enskild server för både läsning och skrivning. Skrivskyddade arbetsbelastningar kan isoleras repliker, medan skrivning arbetsbelastningar kan dirigeras till huvudservern.

Ett vanligt scenario är att ha BI och analytiska arbetsbelastningar använder den skrivskyddade repliken som datakälla för rapportering.

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

### <a name="pricing-tiers"></a>Prisnivåer

Läs repliker är för närvarande endast tillgängliga i prisnivåer för generell användning och Minnesoptimerad.

### <a name="master-server-restart"></a>Huvudservern omstart

När du skapar en replik för en huvudserver som har inga befintliga replikeringar startas först huvudservern för att förbereda själva för replikering. Ha i åtanke och utföra dessa åtgärder under en period som med låg belastning.

### <a name="stopping-replication"></a>Stoppa replikering

Du kan välja att stoppa replikering mellan en och en replikserver. Stoppa replikering tar du bort replikeringsrelationen mellan huvud- och replik-servern.

När replikeringen har stoppats, blir replikservern en fristående server. Data i den fristående servern är de data som fanns på repliken när kommandot ”Stoppa replikering” initierades. Den fristående servern fånga inte med huvudservern. Den här servern kan inte göras i en replik igen.

### <a name="replicas-are-new-servers"></a>Repliker är nya servrar

Repliker skapas som ny Azure Database för MySQL-servrar. Befintliga servrar kan inte göras i repliker.

### <a name="replica-server-configuration"></a>Repliken serverkonfiguration

Replikservrar skapas med hjälp av samma server-konfigurationer som huvudserver, vilket innefattar följande konfigurationer:

- Prisnivå
- Compute-generering
- vCores
- Storage
- Kvarhållningsperiod för säkerhetskopiering
- Redundansalternativ för säkerhetskopiering
- MySQL-Motorversion
- Brandväggsregler

När en replik har skapats kan du ändra prisnivån (förutom till och från Basic), compute-generering, vCores, lagring och kvarhållning av säkerhetskopior oberoende av huvudservern.

### <a name="master-server-configuration"></a>Master-serverkonfiguration

Om en master serverkonfiguration (ex.) virtuella kärnor och lagring) ska uppdateras, de replikerna konfiguration ska också uppdateras till samma eller högre värden. Utan detta blir replikservern kanske inte kan hålla jämna steg med ändringar som gjorts i huvuddatabasen och kan krascha som ett resultat.

Nya brandväggsregler som lagts till huvudservern när du har skapat en replikserver replikeras inte till repliken. Repliken ska uppdateras med den här nya brandväggsregeln samt.

### <a name="deleting-the-master-server"></a>Tar bort huvudservern

När en huvudserver tas bort så stoppas replikeringen till alla skrivskyddade repliker. De här replikeringarna blir fristående servrar. Huvudservern själva tas bort.

### <a name="user-accounts"></a>Användarkonton

Användare på huvudservern replikeras till de skrivskyddade replikerna. Du kan bara ansluta till en skrivskyddad replik med hjälp av användarkontona som är tillgängliga på huvudservern.

### <a name="other"></a>Annat

- Globala transaktions-ID: n (GTID) stöds inte.
- Det går inte att skapa en replik av en replik.
- InMemory-tabeller kan medföra att replikerna blir osynkroniserad. Detta är en begränsning av MySQL-replikeringsteknik. Läs mer i den [MySQL referensdokumentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) för mer information.
- Justera den [ `innodb_file_per_table` ](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) parametern på en huvudserver när du skapar en replikserver kan orsaka repliken för att bli osynkroniserad. Replikservern är inte medvetna om de olika registerutrymmen.
- Se till att huvudservern tabellerna har primärnycklar. Brist på primärnycklar kan resultera i replikeringsfördröjning mellan huvud- och repliker.
- Läs den fullständiga listan med begränsningar för replikering av MySQL i den [MySQL-dokumentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa och hantera skrivskyddade repliker med Azure portal](howto-read-replicas-portal.md)
- Lär dig hur du [skapa och hantera skrivskyddade repliker med Azure CLI](howto-read-replicas-cli.md)

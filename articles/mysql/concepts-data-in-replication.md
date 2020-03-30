---
title: Data-in replikering - Azure Database för MySQL
description: Lär dig mer om hur du använder datareplikering för att synkronisera från en extern server till Azure Database for MySQL-tjänsten.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20be34191355e6ade40e0f3b218818bfa5345a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533240"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikera data till Azure Database för MySQL

Med datareplikering kan du synkronisera data från en extern MySQL-server till Azure Database for MySQL-tjänsten. Den externa servern kan vara lokal, i virtuella datorer eller en databastjänst som finns hos andra molnleverantörer. Datareplikering baseras på positionsbaserad replikering med en binär loggfil (binlog) som är inbyggd i MySQL. Mer information om binlog-replikering finns i [översikten över MySQL-binlogreplikering](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>När ska datainsamling användas
De viktigaste scenarierna att överväga att använda Data-in Replication är:

- **Hybriddatasynkronisering:** Med Data-in Replication kan du synkronisera data mellan dina lokala servrar och Azure Database for MySQL. Den här synkroniseringen är användbar för att skapa hybridprogram. Den här metoden är tilltalande när du har en befintlig lokal databasserver men vill flytta data till en region närmare slutanvändarna.
- **Synkronisering i flera moln:** För komplexa molnlösningar använder du Data-in Replication för att synkronisera data mellan Azure Database för MySQL och olika molnleverantörer, inklusive virtuella datorer och databastjänster som finns i dessa moln.
 
För migreringsscenarier använder du [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Begränsningar och överväganden

### <a name="data-not-replicated"></a>Data replikeras inte
[*Mysql-systemdatabasen*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) på huvudservern replikeras inte. Ändringar av konton och behörigheter på huvudservern replikeras inte. Om du skapar ett konto på huvudservern och det här kontot måste komma åt replikservern skapar du samma konto manuellt på replikserversidan. Information om vilka tabeller som finns i systemdatabasen finns i [MySQL-handboken](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="requirements"></a>Krav
- Huvudserverversionen måste vara minst MySQL version 5.6. 
- Huvud- och replikserverversionerna måste vara desamma. Till exempel måste båda vara MySQL version 5.6 eller båda måste vara MySQL version 5.7.
- Varje tabell måste ha en primärnyckel.
- Huvudservern bör använda MySQL InnoDB-motorn.
- Användaren måste ha behörighet att konfigurera binär loggning och skapa nya användare på huvudservern.
- Om huvudservern har SSL aktiverat kontrollerar du att SSL-certifikatet `mysql.az_replication_change_master` som angetts för domänen har inkluderats i den lagrade proceduren. Se följande [exempel](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) och `master_ssl_ca` parametern.
- Se till att huvudserverns IP-adress har lagts till i Azure Database for MySQL-replikserverns brandväggsregler. Uppdatera brandväggsregler med hjälp av [Azure-portalen](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) eller [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Se till att datorn som är värd för huvudservern tillåter både inkommande och utgående trafik på port 3306.
- Kontrollera att huvudservern har en **offentlig IP-adress,** DNS är allmänt tillgänglig eller har ett fullständigt kvalificerat domännamn (FQDN).

### <a name="other"></a>Annat
- Data-in-replikering stöds endast i prisnivåer för allmänt ändamål och minnesoptimerad.
- Globala transaktionsidentifierare (GTID) stöds inte.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurerar data-in-replikering](howto-data-in-replication.md)
- Lär dig mer om [att replikera i Azure med läsrepliker](concepts-read-replicas.md)
- Lär dig mer om hur du [migrerar data med minimal stilleståndstid med DMS](howto-migrate-online.md)
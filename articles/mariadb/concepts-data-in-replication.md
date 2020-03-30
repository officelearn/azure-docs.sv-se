---
title: Data-in replikering - Azure Database för MariaDB
description: Lär dig mer om hur du använder datareplikering för att synkronisera från en extern server till Azure Database for MariaDB-tjänsten.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 1fbcc1fb27d5e6df4641f79c0d634580f74000b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532068"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikera data till Azure Database för MariaDB

Med datareplikering kan du synkronisera data från en MariaDB-server som körs lokalt, på virtuella datorer eller i databastjänster som hanteras av andra molnleverantörer i tjänsten Azure Database for MariaDB-tjänsten. Datareplikering baseras på positionsbaserad replikering med en binär loggfil (binlog) som är inbyggd i MariaDB. Mer information om binlog-replikering finns i [översikten över binlogreplikering](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>När ska datainsamling användas
De viktigaste scenarierna att överväga att använda Data-in Replication är:

- **Hybriddatasynkronisering:** Med Data-in Replication kan du synkronisera data mellan dina lokala servrar och Azure Database för MariaDB. Den här synkroniseringen är användbar för att skapa hybridprogram. Den här metoden är tilltalande när du har en befintlig lokal databasserver, men vill flytta data till en region närmare slutanvändarna.
- **Synkronisering i flera moln:** För komplexa molnlösningar använder du Data-in Replication för att synkronisera data mellan Azure Database för MariaDB och olika molnleverantörer, inklusive virtuella datorer och databastjänster som finns i dessa moln.

## <a name="limitations-and-considerations"></a>Begränsningar och överväganden

### <a name="data-not-replicated"></a>Data replikeras inte
[*Mysql-systemdatabasen*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) på huvudservern replikeras inte. Ändringar av konton och behörigheter på huvudservern replikeras inte. Om du skapar ett konto på huvudservern och det här kontot måste komma åt replikservern, skapa du samma konto manuellt på replikserversidan. Information om vilka tabeller som finns i systemdatabasen finns i [MariaDB-dokumentationen](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Krav
- Huvudserverversionen måste vara minst MariaDB version 10.2.
- Huvud- och replikserverversionerna måste vara desamma. Till exempel måste båda vara MariaDB version 10.2.
- Varje tabell måste ha en primärnyckel.
- Huvudservern bör använda InnoDB-motorn.
- Användaren måste ha behörighet att konfigurera binär loggning och skapa nya användare på huvudservern.
- Om huvudservern har SSL aktiverat kontrollerar du att SSL-certifikatet `mariadb.az_replication_change_master` som angetts för domänen har inkluderats i den lagrade proceduren. Se följande [exempel](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) och `master_ssl_ca` parametern.
- Se till att huvudserverns IP-adress har lagts till i Azure Database for MariaDB-replikserverns brandväggsregler. Uppdatera brandväggsregler med hjälp av [Azure-portalen](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) eller [Azure CLI](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Se till att datorn som är värd för huvudservern tillåter både inkommande och utgående trafik på port 3306.
- Kontrollera att huvudservern har en **offentlig IP-adress,** DNS är allmänt tillgänglig eller har ett fullständigt kvalificerat domännamn (FQDN).

### <a name="other"></a>Annat
- Data-in-replikering stöds endast i prisnivåer för allmänt ändamål och minnesoptimerad.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [ställer in data-in replikering](howto-data-in-replication.md).

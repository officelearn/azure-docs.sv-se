---
title: Replikera data till Azure Database for MySQL
description: Lär dig mer om att använda datareplikering för att synkronisera från en extern server till tjänsten Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: b501a1f1ea54aff5617932dc5085d6d19f86976c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970357"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikera data till Azure Database for MySQL

Med Datareplikering kan du synkronisera data från en extern MySQL-server till tjänsten Azure Database for MySQL. Den externa servern kan vara lokalt, i virtuella datorer eller i en databas tjänst som är värd för andra moln leverantörer. Datareplikering baseras på positionsbaserad replikering med en binär loggfil (binlog) som är inbyggd i MySQL. Mer information om BinLog-replikering finns i [Översikt över MySQL BinLog-replikering](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>När du ska använda Datareplikering
Huvud scenarierna för att överväga att använda Datareplikering är:

- **Synkronisering av hybrid data:** Med Datareplikering kan du synkronisera data mellan dina lokala servrar och Azure Database for MySQL. Den här synkroniseringen är användbar för att skapa hybrid program. Den här metoden kan överklaga när du har en befintlig lokal databas server men vill flytta data till en region närmare slutanvändarna.
- **Synkronisering med flera moln:** För komplexa moln lösningar använder du Datareplikering för att synkronisera data mellan Azure Database for MySQL och olika moln leverantörer, inklusive virtuella datorer och databas tjänster som finns i molnet.
 
Använd [Azure Database migration service](https://azure.microsoft.com/services/database-migration/)(DMS) för migrerings scenarier.

## <a name="limitations-and-considerations"></a>Begränsningar och överväganden

### <a name="data-not-replicated"></a>Data har inte repliker ATS
[*MySQL system-databasen*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) på huvud servern replikeras inte. Ändringar av konton och behörigheter på huvud servern replikeras inte. Om du skapar ett konto på huvud servern och det här kontot behöver åtkomst till replik servern, skapar du samma konto manuellt på replik Server sidan. Information om vilka tabeller som finns i system-databasen finns i [hand boken för MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Krav
- Huvud Server versionen måste vara minst MySQL version 5,6. 
- Huvud-och replik Server versionerna måste vara desamma. Till exempel måste båda vara MySQL version 5,6 eller båda måste vara MySQL version 5,7.
- Varje tabell måste ha en primärnyckel.
- Huvud servern bör använda MySQL InnoDB-motorn.
- Användaren måste ha behörighet att konfigurera binär loggning och skapa nya användare på huvud servern.
- Om huvud servern har SSL aktiverat kontrollerar du att det SSL-CA-certifikat som har angetts för domänen har inkluderats i den lagrade proceduren `mysql.az_replication_change_master`. Se följande [exempel](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) och parametern `master_ssl_ca`.
- Se till att huvudserverns IP-adress har lagts till i Azure Database for MySQL-replikserverns brandväggsregler. Uppdatera brandväggsregler med hjälp av [Azure-portalen](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) eller [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli).
- Se till att datorn som är värd för huvudservern tillåter både inkommande och utgående trafik på port 3306.
- Se till att huvud servern har en **offentlig IP-adress**, att DNS är offentligt tillgängligt eller har ett fullständigt kvalificerat domän namn (FQDN).

### <a name="other"></a>Annat
- Datareplikering stöds bara i Generell användning och minnesoptimerade pris nivåer.
- Globala transaktions-ID: n (GTID) stöds inte.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurerar replikering av data](howto-data-in-replication.md)
- Lär dig mer om att [Replikera i Azure med Läs repliker](concepts-read-replicas.md)
- Lär dig mer om hur du [migrerar data med minimal stillestånds tid med DMS](howto-migrate-online.md)
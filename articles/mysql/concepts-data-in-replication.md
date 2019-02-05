---
title: Replikera data till Azure Database för MySQL.
description: Den här artikeln beskriver data i replikering för Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: f91a6da9a305c6620e4e01ab7aa3c554374cb5d7
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691530"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikera data till Azure Database for MySQL

Data i replikering kan du synkronisera data från en extern MySQL-server till Azure Database for MySQL-tjänsten. Den externa servern kan vara lokalt, i virtuella datorer eller en databastjänst som är värd för andra molnleverantörer. Data i replikering baseras på den binära logg (binlog) baserat på positionen filreplikeringen interna till MySQL. Mer information om binlog replikering finns i [översikt över replikering av MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>När du ska använda Data i replikering
Huvudscenarierna överväga användning av Data i replikering är:

- **Hybrid datasynkronisering:** Du kan behålla data synkroniseras mellan dina lokala servrar och Azure Database för MySQL med Data i replikering. Den här synkroniseringen är användbart för att skapa hybridprogram. Den här metoden är tilltalande när du har en befintlig lokal databas-server men vill flytta data till en region närmare för slutanvändare.
- **Synkronisering av flera moln:** För komplexa molnlösningar, använder du Data i replikering för att synkronisera data mellan Azure Database for MySQL och olika molnleverantörer, inklusive virtuella datorer och databastjänster som finns i dessa moln.

## <a name="limitations-and-considerations"></a>Begränsningar och överväganden

### <a name="data-not-replicated"></a>Data som inte har replikerats
Den [ *mysql-databas* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) på huvudservern inte replikeras. Ändringar av konton och behörigheter på huvudservern replikeras inte. Om du skapar ett konto på huvudservern och det här kontot behöver åtkomst till replikservern, måste du manuellt skapa samma konto på serversidan repliken. Information om vilka tabeller finns i systemdatabasen finns i den [MySQL manuell](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Krav
- Huvudservern måste vara minst MySQL version 5.6. 
- Serverversioner huvud- och repliken måste vara samma. Till exempel måste båda vara MySQL version 5.6 eller båda måste vara MySQL version 5.7.
- Varje tabell måste ha en primärnyckel.
- Huvudservern bör använda MySQL InnoDB-motorn.
- Användaren måste ha behörighet att konfigurera binär loggning och skapa nya användare på huvudservern.

### <a name="other"></a>Annat
- Data i replikering finns endast stöd för generellt syfte och Minnesoptimerad prisnivåer.
- Globala transaktions-ID: n (GTID) stöds inte.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [ställa in data i replikering](howto-data-in-replication.md)
- Lär dig mer om [replikering i Azure med läsa repliker](concepts-read-replicas.md)

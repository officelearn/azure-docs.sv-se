---
title: Replikera data till Azure Database for MariaDB.
description: Den här artikeln beskriver data i replikering för Azure-databas för MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a6bb6ae45c4240f21b1e0baf4956b053d4b98720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948187"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikera data till Azure Database for MariaDB

Data i replikering kan du synkronisera data från en MariaDB-server som körs lokalt, i virtuella datorer eller databastjänster med andra molnleverantörer till Azure Database for MariaDB-tjänsten. Data i replikering baseras på den binära logg (binlog) baserat på positionen filreplikeringen inbyggd MariaDB. Mer information om binlog replikering finns i [binlog replikering: översikt](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>När du ska använda Data i replikering
Huvudscenarierna överväga användning av Data i replikering är:

- **Hybrid datasynkronisering:** med Data i replikering kan du behålla data synkroniseras mellan dina lokala servrar och Azure-databas för MariaDB. Den här synkroniseringen är användbart för att skapa hybridprogram. Den här metoden är tilltalande när du har en befintlig lokal databas-server, men vill flytta data till en region närmare för slutanvändare.
- **Flera Molnsynkronisering:** för komplexa molnlösningar, Använd Data i replikeringen att synkronisera data mellan Azure Database for MariaDB och olika molnleverantörer, inklusive virtuella datorer och databastjänster som finns i dessa moln.

## <a name="limitations-and-considerations"></a>Begränsningar och överväganden

### <a name="data-not-replicated"></a>Data som inte har replikerats
Den [ *mysql-databas* ](https://mariadb.com/kb/en/library/the-mysql-database-tables/) på huvudservern replikeras inte. Ändringar av konton och behörigheter på huvudservern replikeras inte. Om du skapar ett konto på huvudservern och det här kontot behöver åtkomst till replikservern, sedan manuellt skapa samma konto på serversidan repliken. Information om vilka tabeller finns i systemdatabasen finns i den [MariaDB dokumentation](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Krav
- Huvudservern måste vara minst MariaDB version 10.2.
- Serverversioner huvud- och repliken måste vara samma. Till exempel måste båda vara MariaDB version 10.2.
- Varje tabell måste ha en primärnyckel.
- Huvudservern bör använda InnoDB-motorn.
- Användaren måste ha behörighet att konfigurera binär loggning och skapa nya användare på huvudservern.

### <a name="other"></a>Annat
- Data i replikering finns endast stöd för generellt syfte och Minnesoptimerad prisnivåer.
- Globala transaktions-ID: n (GTID) stöds inte.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [ställa in data i replikering](howto-data-in-replication.md).

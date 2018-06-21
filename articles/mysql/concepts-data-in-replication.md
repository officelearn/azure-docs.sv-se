---
title: Replikera data till Azure-databas för MySQL.
description: Den här artikeln beskriver data i replikering för Azure-databas för MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: 72f8211ecc0534b15402911de8fc0ec3d541a835
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294912"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikerar data till Azure-databas för MySQL

Data i replikering kan du synkronisera data från en MySQL-server som kör lokalt, i virtuella datorer eller databastjänster hos andra molntjänstleverantörer till Azure-databas för MySQL-tjänst. Data i replikering baseras på den binära logg (binlog) position-baserade filreplikering inbyggd MySQL. Mer information om binlog replikering finns i [MySQL binlog replikering: översikt](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>När du ska använda Data i replikering
Huvudscenarier överväga att använda Data i replikeringen är:

- **Hybrid datasynkronisering:** med Data i replikeringen, kan du synkronisera data mellan din lokala servrar och Azure-databas för MySQL. Den här synkroniseringen är användbart för att skapa hybridprogram. Den här metoden är tilltalande när du har en befintlig lokal databasserver, men vill flytta data till en region som ligger närmare till slutanvändare.
- **Flera Molnsynkronisering:** för avancerade molnlösningar, Använd Data i replikeringen att synkronisera data mellan Azure-databas för MySQL och annan molntjänstleverantörer inklusive virtuella datorer och databastjänster i dessa moln.

## <a name="limitations-and-considerations"></a>Begränsningar och överväganden

### <a name="data-not-replicated"></a>Data som inte har replikerats
Den [ *mysql-databas* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) på den primära servern inte har replikerats. Ändringar av konton och behörigheter på den primära servern replikeras inte. Om du skapar ett konto på den primära servern och det här kontot behöver åtkomst till replikservern, sedan manuellt skapa samma konto på serversidan replik. För att förstå vilka tabeller finns i systemdatabasen, finns det [MySQL manuell](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Krav
- Primär server-version måste vara minst MySQL version 5.6. 
- De primära servern och repliken server-versionerna måste vara samma. Till exempel måste båda vara MySQL version 5.6 eller måste båda vara MySQL version 5.7.
- Varje tabell måste ha en primärnyckel.
- Primär server ska använda MySQL InnoDB-motorn.
- Användaren måste ha behörighet att konfigurera binär loggning och skapa nya användare på den primära servern.

### <a name="other"></a>Annat
- Globala transaktions-ID: n (GTID) stöds inte.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [konfigurerar data i replikering](howto-data-in-replication.md)

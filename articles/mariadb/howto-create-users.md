---
title: Skapa användare i Azure Database for MariaDB-server
description: Den här artikeln beskrivs hur du kan skapa nya användarkonton för att interagera med en Azure Database for MariaDB-server.
author: jasonwhowell
ms.author: jasonh
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 189e122e04d56d28c1e1e94d328569647614a124
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542135"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Skapa användare i Azure Database for MariaDB 
Den här artikeln beskrivs hur du kan skapa användare i Azure Database for MariaDB.

När du först skapade Azure Database for MariaDB, som du angav en användare för serveradministratör och ett lösenord. Mer information kan du följa den [snabbstarten](quickstart-create-mariadb-server-database-using-azure-portal.md). Du kan hitta din användarens inloggningsnamn för serveradministratören i Azure Portal.

Administratörsanvändare server hämtar viss behörighet för servern som anges: VÄLJ, INFOGA, UPPDATERA, TA BORT, TA BORT, LÄSA IN, BEARBETA, REFERENSER, INDEX, ALTER, VISA DATABASER, SKAPA TEMPORÄRA TABELLER, LÅSA TABELLER, KÖRA SKAPAR UNDERORDNAD REPLIKERING, REPLIKERINGSKLIENT, VISA, VISA, SKAPA RUTINUNDERHÅLL, ALTER RUTINEN, SKAPA ANVÄNDARE , HÄNDELSE, UTLÖSARE

När Azure Database for MariaDB-server har skapats kan använda du första användarkontot för server-administratör att skapa ytterligare användare och ge administratörsåtkomst till dem. Serveradministratörskontot kan också användas för att skapa mindre privilegierade användare som har åtkomst till enskilda databasscheman.

## <a name="create-additional-admin-users"></a>Skapa ytterligare administrativa användare
1. Hämta anslutning administration och information användarnamn.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Hittar du enkelt servernamnet och inloggningsuppgifter från servern **översikt** sidan eller **egenskaper** sidan på Azure portal. 

2. Använda administratörskonto och lösenord för att ansluta till databasservern. Använd din önskade klientverktyg, till exempel MySQL Workbench, mysql.exe, HeidiSQL eller andra. 
   Om du är osäker på hur du ansluter kan du läsa [använda MySQL Workbench för att ansluta och fråga efter data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt ditt nya användarnamn för platshållarvärdet `new_master_user`. Den här syntaxen ger de listade behörigheter på alla databasscheman (*.*) till användarnamn (new_master_user i det här exemplet). 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. Kontrollera beviljar 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="create-database-users"></a>Skapa databasanvändare

1. Hämta anslutning administration och information användarnamn.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Hittar du enkelt servernamnet och inloggningsuppgifter från servern **översikt** sidan eller **egenskaper** sidan på Azure portal. 

2. Använda administratörskonto och lösenord för att ansluta till databasservern. Använd din önskade klientverktyg, till exempel MySQL Workbench, mysql.exe, HeidiSQL eller andra. 
   Om du är osäker på hur du ansluter kan du läsa [använda MySQL Workbench för att ansluta och fråga efter data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt platshållarvärdet `db_user` med avsedda nytt användarnamn och platshållarvärdet `testdb` med databasnamn på din.

   Den här sql-syntax i koden skapar en ny databas med namnet testdb exempelsyfte. Därefter skapar en ny användare i tjänsten Azure Database for MariaDB, och beviljar alla behörigheter till det nya databasschemat (testdb.\*) för användaren. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. Kontrollera beviljar i databasen.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Logga in på servern, om du anger den avsedda databas med nytt användarnamn och lösenord. Det här exemplet visar mysql-kommandoraden. Med det här kommandot uppmanas du lösenordet för användarnamnet. Ersätt dina egna servernamnet, databasnamnet och användarnamn.

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
Mer information om hantering av användarkonton, finns i MariaDB-dokumentationen för [Användarkontohantering](https://mariadb.com/kb/en/library/user-account-management/), [BEVILJA Syntax](https://mariadb.com/kb/en/library/grant/), och [privilegier](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Nästa steg
Öppna brandväggen för IP-adresserna för de nya användarna datorer så att de kan ansluta: [Skapa och hantera Azure-databas för MariaDB brandväggsregler med hjälp av Azure portal](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->

---
title: Skapa användare i Azure Database for MySQL-server
description: Den här artikeln beskrivs hur du kan skapa nya användarkonton för att interagera med en Azure Database for MySQL-server.
author: jasonwhowell
ms.author: jasonh
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: e8714777b1f9f08de4d02fcb44c25197cdc48899
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546019"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Skapa användare i Azure Database for MySQL-server 
Den här artikeln beskrivs hur du kan skapa användare i en Azure Database for MySQL-server.

När du först skapade Azure Database för MySQL som du angav en användare för serveradministratör och ett lösenord. Mer information kan du följa den [snabbstarten](quickstart-create-mysql-server-database-using-azure-portal.md). Du kan hitta din användarens inloggningsnamn för serveradministratören i Azure Portal.

Administratörsanvändare server hämtar viss behörighet för servern som anges: VÄLJ, INFOGA, UPPDATERA, TA BORT, TA BORT, LÄSA IN, BEARBETA, REFERENSER, INDEX, ALTER, VISA DATABASER, SKAPA TEMPORÄRA TABELLER, LÅSA TABELLER, KÖRA SKAPAR UNDERORDNAD REPLIKERING, REPLIKERINGSKLIENT, VISA, VISA, SKAPA RUTINUNDERHÅLL, ALTER RUTINEN, SKAPA ANVÄNDARE , HÄNDELSE, UTLÖSARE

När Azure Database for MySQL-server har skapats kan använda du första användarkontot för server-administratör att skapa ytterligare användare och ge administratörsåtkomst till dem. Serveradministratörskontot kan också användas för att skapa mindre privilegierade användare som har åtkomst till enskilda databasscheman.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Så här skapar du ytterligare administrativa användare i Azure Database for MySQL
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

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Skapa databasanvändare i Azure Database för MySQL

1. Hämta anslutning administration och information användarnamn.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Hittar du enkelt servernamnet och inloggningsuppgifter från servern **översikt** sidan eller **egenskaper** sidan på Azure portal. 

2. Använda administratörskonto och lösenord för att ansluta till databasservern. Använd din önskade klientverktyg, till exempel MySQL Workbench, mysql.exe, HeidiSQL eller andra. 
   Om du är osäker på hur du ansluter kan du läsa [använda MySQL Workbench för att ansluta och fråga efter data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt platshållarvärdet `db_user` med avsedda nytt användarnamn och platshållarvärdet `testdb` med databasnamn på din.

   Den här sql-syntax i koden skapar en ny databas med namnet testdb exempelsyfte. Därefter skapar en ny användare i MySQL-tjänsten och beviljar alla behörigheter till det nya databasschemat (testdb.\*) för användaren. 

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

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Nästa steg
Öppna brandväggen för IP-adresserna för de nya användarna datorer så att de kan ansluta: [Skapa och hantera Azure Database för MySQL-brandväggsregler med hjälp av Azure portal](howto-manage-firewall-using-portal.md) eller [Azure CLI](howto-manage-firewall-using-cli.md).

Mer information om hantering av användarkonton, finns i dokumentationen för MySQL [Användarkontohantering](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [BEVILJA Syntax](https://dev.mysql.com/doc/refman/5.7/en/grant.html), och [privilegier](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).

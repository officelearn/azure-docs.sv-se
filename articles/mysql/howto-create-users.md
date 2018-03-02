---
title: "Skapa användare i Azure-databas för MySQL-server"
description: "Den här artikeln beskriver hur du kan skapa nya användarkonton för att interagera med en Azure-databas för MySQL-servern."
services: mysql
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9fd592efe48adefc6aca7a6caea24f546da23fa5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Skapa användare i Azure-databas för MySQL-server 
Den här artikeln beskriver hur du kan skapa användare i en Azure-databas för MySQL-servern.

När du först skapade din Azure-databas för MySQL angav du ett admin användarnamn och lösenord. Mer information kan du följa den [Quickstart](quickstart-create-mysql-server-database-using-azure-portal.md). Du kan hitta din admin användarnamn från Azure-portalen.

Administratörsanvändare server hämtar viss behörighet för servern som anges: Markera, infoga, uppdatera, ta bort, skapa, ta bort, Läs, PROCESS, referenser, INDEX, ALTER, visa databaser, skapa TEMPORÄRA tabeller, lås tabeller, kör, replikering SLAVSERVER, replikering KLIENT, SKAPA VY, VISA, RUTINUNDERHÅLL, ALTER RUTIN, SKAPA ANVÄNDARE, HÄNDELSE, UTLÖSARE

När Azure-databasen för MySQL-server har skapats kan kan du använda första användarkontot för server-administratör att skapa ytterligare användare och ger administratören tillgång till dem. Dessutom kan administratörskonto server användas för att skapa mindre privilegierade användare som har åtkomst till enskilda databasen scheman.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Så här skapar du ytterligare administrativa användare i Azure-databas för MySQL
1. Hämta anslutningsnamn för administration och information.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Hittar du servernamnet och logga in information från servern **översikt** sidan eller **egenskaper** sida i Azure-portalen. 

2. Använd administratörskonto och lösenord för att ansluta till databasservern. Använd din önskade klientverktyg, till exempel MySQL arbetsstationen mysql.exe, HeidiSQL eller andra. 
   Om du är osäker på hur du ansluter [Använd MySQL-arbetsstationen för att ansluta och fråga efter data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt ditt nya användarnamn för platshållaren värdet `new_master_user`. Den här syntaxen ger listan behörigheter på alla scheman för databasen (*.*) till användarnamnet (new_master_user i det här exemplet). 

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

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Skapa databasanvändare i Azure-databas för MySQL

1. Hämta anslutningsnamn för administration och information.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Hittar du servernamnet och logga in information från servern **översikt** sidan eller **egenskaper** sida i Azure-portalen. 

2. Använd administratörskonto och lösenord för att ansluta till databasservern. Använd din önskade klientverktyg, till exempel MySQL arbetsstationen mysql.exe, HeidiSQL eller andra. 
   Om du är osäker på hur du ansluter [Använd MySQL-arbetsstationen för att ansluta och fråga efter data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt platshållaren värdet `db_user` med rätt användarnamn och platshållare värdet `testdb` med din egen databasnamn.

   Den här syntaxen för sql-kod skapar en ny databas med namnet testdb exempel. Därefter skapar en ny användare i tjänsten MySQL och beviljar alla behörigheter i nya databasschemat (testdb.\*) för användaren. 

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

5. Logga in på servern, anger den avsedda databas med nytt användarnamn och lösenord. Det här exemplet visar mysql-kommandoraden. Med det här kommandot uppmanas du lösenordet för användarnamnet. Ersätt egna servernamnet, databasnamnet och användarnamn.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Nästa steg
Öppna brandväggen för IP-adresserna för de nya användarna datorer för att kunna ansluta: [skapa och hantera Azure-databas för MySQL brandväggsregler med hjälp av Azure portal](howto-manage-firewall-using-portal.md) eller [Azure CLI](howto-manage-firewall-using-cli.md).

Mer information om hantering av användarkonton finns i produktdokumentationen för MySQL för [Användarkontohantering](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [BEVILJA Syntax](https://dev.mysql.com/doc/refman/5.7/en/grant.html), och [privilegier](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).

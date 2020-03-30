---
title: Skapa användare - Azure Database för MySQL
description: I den här artikeln beskrivs hur du kan skapa nya användarkonton för att interagera med en Azure-databas för MySQL-server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c4e95164badaf0b255f5ee76d0fec7686c2abf8b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382876"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Skapa användare i Azure Database för MySQL-server

I den här artikeln beskrivs hur du kan skapa användare i en Azure-databas för MySQL-server.

När du först skapade din Azure-databas för MySQL angav du ett användarnamn och lösenord för serveradministratörsinloggning. Om du vill ha mer information kan du följa [Snabbstarten](quickstart-create-mysql-server-database-using-azure-portal.md). Du kan hitta ditt användarnamn för serveradministratörsinloggning från Azure-portalen.

Serveradministratörsanvändaren får vissa privilegier för servern enligt listan: VÄLJ, INFOGA, UPPDATERA, TA BORT, SKAPA, SLÄPPA, LADDA OM, BEARBETA, REFERENSER, INDEX, ÄNDRA, VISA DATABASER, SKAPA TEMPORÄRA TABELLER, LÅSTABELLER, EXECUTE, REPLIKERINGSLAV, REPLIKERING KLIENT, SKAPA VY, VISA VY, SKAPA RUTIN, ÄNDRA RUTIN, SKAPA ANVÄNDARE, HÄNDELSE, UTLÖSARE

När Azure Database for MySQL-servern har skapats kan du använda det första användarkontot för serveradministratör för att skapa ytterligare användare och ge administratörsåtkomst till dem. Serveradministratörskontot kan också användas för att skapa mindre privilegierade användare som har åtkomst till enskilda databasscheman.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Skapa ytterligare administratörsanvändare i Azure Database för MySQL

1. Hämta anslutningsinformation och administratörsanvändarnamn.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta servernamn och inloggningsinformation från sidan **Översikt** över servern eller sidan **Egenskaper** i Azure-portalen.

2. Använd administratörskontot och lösenordet för att ansluta till databasservern. Använd ditt önskade klientverktyg, till exempel MySQL Workbench, mysql.exe, HeidiSQL eller andra.
   Om du är osäker på hur du ansluter läser [du Använda MySQL Workbench för att ansluta och fråga data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt det nya användarnamnet för `new_master_user`platshållarvärdet . Den här syntaxen ger de angivna behörigheterna för alla databasscheman (*.*) till användarnamnet (new_master_user i det här exemplet).

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION;

   FLUSH PRIVILEGES;
   ```

4. Verifiera bidragen

   ```sql
   USE sys;

   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Så här skapar du databasanvändare i Azure Database for MySQL

1. Hämta anslutningsinformation och administratörsanvändarnamn.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta servernamn och inloggningsinformation från sidan **Översikt** över servern eller sidan **Egenskaper** i Azure-portalen.

2. Använd administratörskontot och lösenordet för att ansluta till databasservern. Använd ditt önskade klientverktyg, till exempel MySQL Workbench, mysql.exe, HeidiSQL eller andra.
   Om du är osäker på hur du ansluter läser [du Använda MySQL Workbench för att ansluta och fråga data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt platshållarvärdet `db_user` med ditt avsedda nya användarnamn `testdb` och platshållarvärdet med ditt eget databasnamn.

   Den här sql-kodsyntaxen skapar en ny databas med namnet testdb till exempel. Sedan skapas en ny användare i MySQL-tjänsten och alla privilegier till det\*nya databasschemat (testdb. ) för den användaren.

   ```sql
   CREATE DATABASE testdb;

   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';

   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';

   FLUSH PRIVILEGES;
   ```

4. Verifiera bidragen i databasen.

   ```sql
   USE testdb;

   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. Logga in på servern och ange den angivna databasen med det nya användarnamnet och lösenordet. I det här exemplet visas kommandoraden mysql. Med det här kommandot uppmanas du att ange lösenordet för användarnamnet. Ersätt ditt eget servernamn, databasnamn och användarnamn.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Nästa steg

Öppna brandväggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta: [Skapa och hantera Azure Database for MySQL-brandväggsregler med hjälp av Azure-portalen](howto-manage-firewall-using-portal.md) eller Azure [CLI](howto-manage-firewall-using-cli.md).

Mer information om hantering av användarkonton finns i MySQL-produktdokumentation för [hantering av användarkonton,](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html) [GRANT-syntax](https://dev.mysql.com/doc/refman/5.7/en/grant.html)och [Privilegier](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).

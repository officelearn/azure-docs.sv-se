---
title: Skapa användare - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du kan skapa nya användarkonton för att interagera med en Azure-databas för MariaDB-server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/2/2020
ms.openlocfilehash: 1b79a49b2fb87ebf180aaaa40447f40c5a982c2e
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632293"
---
# <a name="create-users-in-azure-database-for-mariadb"></a>Skapa användare i Azure Database for MariaDB 
I den här artikeln beskrivs hur du kan skapa användare i Azure Database för MariaDB.

När du först skapade din Azure-databas för MariaDB angav du ett användarnamn och lösenord för serveradministratörsinloggning. Om du vill ha mer information kan du följa [Snabbstarten](quickstart-create-mariadb-server-database-using-azure-portal.md). Du kan hitta ditt användarnamn för serveradministratörsinloggning från Azure-portalen.

Serveradministratörsanvändaren får vissa privilegier för servern enligt listan: VÄLJ, INFOGA, UPPDATERA, TA BORT, SKAPA, SLÄPP, LADDA OM, BEARBETA, REFERENSER, INDEX, ÄNDRA, VISA DATABASER, SKAPA TEMPORÄRA TABELLER, LÅSTABELLER, EXECUTE, REPLIKERINGSLAV, REPLIKERINGSKLIENT, SKAPA VY, VISA VY, SKAPA RUTIN, ÄNDRA RUTIN, SKAPA ANVÄNDARE, HÄNDELSE, UTLÖSARE

När Azure Database for MariaDB-servern har skapats kan du använda det första användarkontot för serveradministratör för att skapa ytterligare användare och ge administratörsåtkomst till dem. Serveradministratörskontot kan också användas för att skapa mindre privilegierade användare som har åtkomst till enskilda databasscheman.

> [!NOTE]
> Super-privilegiet och DBA-rollen stöds inte. Granska [privilegierna](concepts-limits.md#privilege-support) i begränsningsartikeln för att förstå vad som inte stöds i tjänsten.

## <a name="create-additional-admin-users"></a>Skapa ytterligare administratörsanvändare
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

## <a name="create-database-users"></a>Skapa databasanvändare

1. Hämta anslutningsinformation och administratörsanvändarnamn.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta servernamn och inloggningsinformation från sidan **Översikt** över servern eller sidan **Egenskaper** i Azure-portalen. 

2. Använd administratörskontot och lösenordet för att ansluta till databasservern. Använd ditt önskade klientverktyg, till exempel MySQL Workbench, mysql.exe, HeidiSQL eller andra. 
   Om du är osäker på hur du ansluter läser [du Använda MySQL Workbench för att ansluta och fråga data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt platshållarvärdet `db_user` med ditt avsedda nya användarnamn `testdb` och platshållarvärdet med ditt eget databasnamn.

   Den här sql-kodsyntaxen skapar en ny databas med namnet testdb till exempel. Sedan skapas en ny användare i Azure Database for MariaDB-tjänsten och alla privilegier\*till det nya databasschemat (testdb. ) för den användaren. 

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

   ```bash
   mysql --host mydemoserver.mariadb.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```
   Mer information om hantering av användarkonton finns i MariaDB-dokumentation för [hantering av användarkonton,](https://mariadb.com/kb/en/library/user-account-management/) [BEVILJA syntax](https://mariadb.com/kb/en/library/grant/)och [Privilegier](https://mariadb.com/kb/en/library/grant/#privilege-levels).

## <a name="next-steps"></a>Nästa steg
Öppna brandväggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta: [Skapa och hantera Azure Database för MariaDB-brandväggsregler med hjälp av Azure-portalen](howto-manage-firewall-portal.md)  

<!--or [Azure CLI](howto-manage-firewall-using-cli.md).-->

---
title: Skapa användare – Azure Database for MySQL
description: I den här artikeln beskrivs hur du kan skapa nya användar konton för att interagera med en Azure Database for MySQL-server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/2/2020
ms.openlocfilehash: f8c020da72e9cdf8777a3eefac266e97e4a312bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81263479"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Skapa användare i Azure Database for MySQL Server

I den här artikeln beskrivs hur du kan skapa användare i en Azure Database for MySQL-server.

När du först skapade din Azure Database for MySQL angav du ett användar namn och lösen ord för Server Administratörs inloggning. För mer information, kan du följa [snabb](quickstart-create-mysql-server-database-using-azure-portal.md)starten. Du kan hitta inloggnings användar namnet för Server administratören från Azure Portal.

Server administratörs användaren får vissa behörigheter för servern enligt listan: Välj, infoga, uppdatera, ta bort, skapa, ta bort, läsa in, bearbeta, REFERERA, INDEXERA, ändra, Visa databaser, skapa TEMPORÄRa tabeller, Lås tabeller, köra, replikering slav, REPLIKERINGSPARTNER, skapa vy, Visa, skapa rutin, ändra rutin, skapa användare, händelse, utlösare

När Azure Database for MySQL-servern har skapats kan du använda det första server administratörs användar kontot för att skapa ytterligare användare och ge administratörs åtkomst till dem. Server administratörs kontot kan också användas för att skapa mindre privilegierade användare som har åtkomst till enskilda databas scheman.

> [!NOTE]
> Superprivilegiumet och DBA-rollen stöds inte. Granska [privilegierna](concepts-limits.md#privilege-support) i artikeln begränsningar för att förstå vad som inte stöds i tjänsten.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Så här skapar du ytterligare administratörs användare i Azure Database for MySQL

1. Hämta anslutnings informationen och administratörs användar namnet.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta server namn och inloggnings information från sidan Server **Översikt** eller sidan **Egenskaper** i Azure Portal.

2. Använd administratörs kontot och lösen ordet för att ansluta till din databas server. Använd önskat klient verktyg, till exempel MySQL Workbench, MySQL. exe, HeidiSQL eller andra.
   Om du är osäker på hur du ansluter, se [Använd MySQL Workbench för att ansluta och fråga efter data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt det nya användar namnet med plats hållarens `new_master_user`värde. Den här syntaxen beviljar de angivna behörigheterna för alla databas scheman (*.*) till användar namnet (new_master_user i det här exemplet).

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

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Så här skapar du databas användare i Azure Database for MySQL

1. Hämta anslutnings informationen och administratörs användar namnet.
   Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan enkelt hitta server namn och inloggnings information från sidan Server **Översikt** eller sidan **Egenskaper** i Azure Portal.

2. Använd administratörs kontot och lösen ordet för att ansluta till din databas server. Använd önskat klient verktyg, till exempel MySQL Workbench, MySQL. exe, HeidiSQL eller andra.
   Om du är osäker på hur du ansluter, se [Använd MySQL Workbench för att ansluta och fråga efter data](./connect-workbench.md)

3. Redigera och kör följande SQL-kod. Ersätt placeholder-värdet `db_user` med ditt avsedda nya användar namn och plats hållarens `testdb` värde med ditt eget databas namn.

   Den här SQL-koden skapar en ny databas med namnet testdb i exempel syfte. Sedan skapar den en ny användare i MySQL-tjänsten och beviljar alla behörigheter till det nya databasschemat (testdb\*) för den användaren.

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

5. Logga in på servern och ange den angivna databasen med hjälp av det nya användar namnet och lösen ordet. I det här exemplet visas mysql-kommandoraden. Med det här kommandot uppmanas du att ange lösen ordet för användar namnet. Ersätt ditt eget Server namn, databas namn och användar namn.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>Nästa steg

Öppna brand väggen för IP-adresserna för de nya användarnas datorer så att de kan ansluta: [skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure Portal](howto-manage-firewall-using-portal.md) eller [Azure CLI](howto-manage-firewall-using-cli.md).

Mer information om hantering av användar konton finns i MySQL produkt dokumentation för [hantering av användar konton](https://dev.mysql.com/doc/refman/5.7/en/access-control.html), [beviljande av syntax](https://dev.mysql.com/doc/refman/5.7/en/grant.html)och [privilegier](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html).

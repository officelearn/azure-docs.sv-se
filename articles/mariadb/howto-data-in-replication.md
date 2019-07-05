---
title: Konfigurera Data i replikering i Azure-databas för MariaDB | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in Data i replikering i Azure-databas för MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444796"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurera Data i replikering i Azure-databas för MariaDB

Den här artikeln beskriver hur du ställer in Data i replikering i Azure Database for MariaDB genom att konfigurera master och replik-servrar. Den här artikeln förutsätter att du har tidigare erfarenhet med MariaDB-servrar och databaser.

För att skapa en replik i tjänsten Azure Database for MariaDB, synkroniserar Data i replikering data från en master MariaDB server lokalt, i virtuella datorer (VM) eller i molnet databastjänster.

> [!NOTE]
> Om din huvudservern är version 10,2 eller senare, rekommenderar vi att du ställer in Data i replikering med hjälp av [globala transaktions-ID](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Skapa en MariaDB-server som ska användas som en replik

1. Skapa en ny Azure Database for MariaDB-server (till exempel replica.mariadb.database.azure.com). Servern är replikservern i Data i replikeringen.

    Läs om servern har skapats i [skapa en Azure Database for MariaDB-server med hjälp av Azure-portalen](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Azure Database for MariaDB-server måste du skapa i generell användning eller Minnesoptimerade prisnivåer.

2. Skapa identiska användarkonton och motsvarande behörighet.
    
    Användarkonton replikeras inte från den överordnade servern till replikservern. Om du vill ge användaråtkomst till replikservern, måste du manuellt skapa alla konton och motsvarande behörigheter på den nyligen skapade Azure Database for MariaDB-servern.

## <a name="configure-the-master-server"></a>Konfigurera huvudservern

Följande steg förbereder och konfigurera MariaDB-servern körs lokalt, i en virtuell dator eller i en molndatabastjänst för Data i replikering. MariaDB-servern är master i Data i replikeringen.

1. Aktivera binär loggning.
    
    Om du vill se om binär loggning är aktiverad i bakgrunden, anger du följande kommando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Om variabeln [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) returnerar värdet `ON`, binär loggning är aktiverat på servern.

   Om `log_bin` returnerar värdet `OFF`, redigera den **my.cnf** filen så att `log_bin=ON` aktiverar binär loggning. Starta om servern för att göra ändringen börjar gälla.

2. Konfigurera inställningar för huvudservern.

    Data i replikering kräver att parametern `lower_case_table_names` är konsekvent mellan huvud- och replik-servrar. Den `lower_case_table_names` parametern är inställd på `1` som standard i Azure Database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Skapa en ny replikeringsroll som och konfigurera behörigheter.

   Skapa ett användarkonto på huvudservern som är konfigurerad med behörighet för replikering. Du kan skapa ett konto med hjälp av SQL-kommandon eller MySQL Workbench. Om du planerar att replikera med SSL, måste du ange detta när du skapar användarkontot.
   
   Läs hur du lägger till användarkonton på din huvudservern i den [MariaDB dokumentation](https://mariadb.com/kb/en/library/create-user/).

   Den nya rollen för replikering med hjälp av följande kommandon, kan de komma åt huvudservern från valfri dator, inte bara den dator som är värd för själva originalet. Den här åtkomsten, ange **syncuser\@'%'** i kommandot för att skapa en användare.
   
   Mer information om MariaDB-dokumentation finns [att ange kontonamn](https://mariadb.com/kb/en/library/create-user/#account-names).

   **SQL-kommando**

   - Replikering med SSL

       Om du vill kräva SSL för anslutningar för alla användare, anger du följande kommando för att skapa en användare:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikering utan SSL

       Om SSL krävs inte för alla anslutningar, anger du följande kommando för att skapa en användare:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Skapa replikeringsroll i MySQL Workbench, i den **Management** väljer **användare och behörigheter**. Välj sedan **Lägg till konto**.
 
   ![Användare och behörigheter](./media/howto-data-in-replication/users_privileges.png)

   Ange ett användarnamn i den **inloggningsnamn** fält.

   ![Synkronisera användare](./media/howto-data-in-replication/syncuser.png)
 
   Välj den **administrativa roller** Kontrollpanelen och sedan i listan över **globala behörigheter**väljer **replikering underordnad**. Välj **tillämpa** att skapa replikering rollen.

   ![Underordnad replikering](./media/howto-data-in-replication/replicationslave.png)


4. Ange huvudservern till skrivskyddat läge.

   Innan du dumpa en databas måste servern placeras i skrivskyddat läge. Huvuddatabasen kan inte bearbeta några skrivtransaktioner i skrivskyddat läge. För att undvika påverkan på verksamheten, schemalägga fönstret skrivskyddat läge under en tid med låg belastning.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Hämta aktuella binära loggfilens namn och förskjutning.

   För att fastställa den aktuella binära loggfilens namn och Förskjutning, kör du kommandot [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Resultatet bör likna följande tabell:
   
   ![Resultat för Master-Status](./media/howto-data-in-replication/masterstatus.png)

   Notera namnet på binära filen eftersom den kommer att användas i senare steg.
   
6. Hämta GTID positionen (valfritt, behövs för replikering med GTID).

   Kör funktionen [ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) att hämta GTID positionen för motsvarande binlog filnamnet och förskjutning.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Dumpa och Återställ huvudservern

1. Dumpa alla databaser från huvudservern.

   Använd mysqldump att dumpa alla databaser från huvudservern. Det är inte nödvändigt att dumpa MySQL-bibliotek och testa bibliotek.

    Mer information finns i [säkerhetskopiering och återställning](howto-migrate-dump-restore.md).

2. Ange huvudservern att läsa/skriva-läge.

   När databasen har tagits dumpade, ändra huvudservern MariaDB server tillbaka för att läsa/skriva-läge.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Återställa dumpfilen till den nya servern.

   Återställa dumpfilen till den server som skapats i Azure Database for MariaDB-tjänsten. Se [dumpa och återställa](howto-migrate-dump-restore.md) för hur du återställer den här filen till en MariaDB-server.

   Om dumpfilen är stor, ladda upp den till en virtuell dator i Azure inom samma region som replikservern. Återställa den till Azure Database for MariaDB-server från den virtuella datorn.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Länka master och replik-servrar för att starta Data i replikering

1. Ange huvudservern.

   Alla Data i replikering funktioner utförs av lagrade procedurer. Du kan hitta alla procedurer i [Data i replikerade lagrade procedurer](reference-data-in-stored-procedures.md). Lagrade procedurer kan köras i MySQL-gränssnitt eller MySQL Workbench.

   Logga in på målservern repliken i Azure DB för MariaDB-tjänsten för att länka två servrar och starta replikering. Ange sedan den externa instansen som huvudserver med hjälp av den `mysql.az_replication_change_master` eller `mysql.az_replication_change_master_with_gtid` lagrade proceduren på Azure-databas för MariaDB-server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   eller
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: värdnamn för domänens huvudserver
   - master_user: användarnamn för huvudservern
   - master_password: lösenordet för huvudservern
   - master_log_file: binära loggfilens namn från att köras `show master status`
   - master_log_pos: binär logg position från att köras `show master status`
   - master_gtid_pos: GTID position från att köras `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: CA-certifikat kontext. Om du inte använder SSL, skicka in en tom string.*
    
    
    \* Vi rekommenderar att skicka parametern master_ssl_ca som en variabel. Mer information finns i följande exempel.

   **Exempel**

   - Replikering med SSL

       Skapa sedan variabeln `@cert` genom att köra följande kommandon:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replikering med SSL har konfigurerats mellan en huvudserver som finns i domänen companya.com och en replikserver som finns i Azure Database for MariaDB. Den här lagrade proceduren körs på repliken.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replikering utan SSL

       Replikering utan SSL har konfigurerats mellan en huvudserver som finns i domänen companya.com och en replikserver som finns i Azure Database for MariaDB. Den här lagrade proceduren körs på repliken.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Starta replikering.

   Anropa den `mysql.az_replication_start` lagrade proceduren replikeringen ska börja.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Kontrollera replikeringsstatus för.

   Anropa den [ `show slave status` ](https://mariadb.com/kb/en/library/show-slave-status/) på replikservern för att visa replikeringsstatus.
    
   ```sql
   show slave status;
   ```

   Om `Slave_IO_Running` och `Slave_SQL_Running` har statusen `yes`, och värdet för `Seconds_Behind_Master` är `0`, replikeringen fungerar. `Seconds_Behind_Master` Anger hur sent repliken är. Om värdet är inte `0`, och sedan repliken bearbetar uppdateringar.

4. Uppdatera de motsvarande servervariabler för att göra data i replikering säkrare (krävs endast för replikering utan GTID).
    
    På grund av en inbyggd replikering begränsning i MariaDB, måste du ställa in [ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) och [ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variabler på replikering utan GTID-scenario.

    Kontrollera din underordnad server `sync_master_info` och `sync_relay_log_info` variabler för att se till att data i replikeringen är stabil och ange variabler `1`.
    
## <a name="other-stored-procedures"></a>Andra lagrade procedurer

### <a name="stop-replication"></a>Stoppa replikering

Använd följande lagrade procedur om du vill stoppa replikering mellan huvud- och repliken servern:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Ta bort replikeringsrelationen

Ta bort relationen mellan huvud- och repliken servern med följande lagrade procedur:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Hoppa över replikeringsfel

Om du vill hoppa över ett replikeringsfel och tillåta replikering, använder du följande lagrade procedur:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Nästa steg
Läs mer om [Data i replikering](concepts-data-in-replication.md) för Azure Database for MariaDB.

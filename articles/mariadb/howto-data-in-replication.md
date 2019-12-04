---
title: Konfigurera data-i replikering – Azure Database for MariaDB
description: I den här artikeln beskrivs hur du konfigurerar Datareplikering i Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0dbbc9b09d5d4770296223db9dc909c17f574fe8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767032"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurera Datareplikering i Azure Database for MariaDB

I den här artikeln beskrivs hur du konfigurerar Datareplikering i Azure Database for MariaDB genom att konfigurera huvud-och replik servrar. Den här artikeln förutsätter att du har tidigare erfarenhet av MariaDB-servrar och-databaser.

Om du vill skapa en replik i Azure Database for MariaDB-tjänsten synkroniserar Datareplikering data från en lokal huvud-MariaDB-server i virtuella datorer (VM) eller i moln databas tjänster.

> [!NOTE]
> Om huvud servern är version 10,2 eller senare rekommenderar vi att du konfigurerar Datareplikering med hjälp av [globalt transaktions-ID](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Skapa en MariaDB-server som ska användas som en replik

1. Skapa en ny Azure Database for MariaDB Server (till exempel replica.mariadb.database.azure.com). Servern är replik servern i Datareplikering.

    Information om hur du skapar servrar finns i [skapa en Azure Database for MariaDB-server med hjälp av Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Du måste skapa Azure Database for MariaDB-servern på Generell användning eller minnesoptimerade pris nivåer.

2. Skapa identiska användar konton och motsvarande privilegier.
    
    Användar konton replikeras inte från huvud servern till replik servern. Om du vill ge användare åtkomst till replik servern måste du manuellt skapa alla konton och motsvarande behörigheter på den nyskapade Azure Database for MariaDB-servern.

## <a name="configure-the-master-server"></a>Konfigurera huvud servern

Följande steg förbereder och konfigurerar den MariaDB-server som finns lokalt, i en virtuell dator eller i en moln databas tjänst för Datareplikering. MariaDB-servern är huvud servern i Datareplikering.

1. Aktivera binär loggning.
    
    Ange följande kommando för att se om binär loggning har Aktiver ATS på huvud servern:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Om variabeln [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) returnerar värdet `ON`, aktive ras binär loggning på servern.

   Om `log_bin` returnerar värdet `OFF`redigerar du filen **My. cnf** så att `log_bin=ON` aktiverar binär loggning. Starta om servern så att ändringen börjar gälla.

2. Konfigurera huvud Server inställningar.

    Datareplikering kräver att parametern `lower_case_table_names` vara konsekvent mellan huvud-och replik servrar. Parametern `lower_case_table_names` anges som `1` som standard i Azure Database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Skapa en ny replikeringsprincip och konfigurera behörigheter.

   Skapa ett användar konto på huvud servern som har kon figurer ATS med behörighet för replikering. Du kan skapa ett konto med hjälp av SQL-kommandon eller MySQL Workbench. Om du planerar att replikera med SSL måste du ange detta när du skapar användar kontot.
   
   Information om hur du lägger till användar konton på huvud servern finns i [MariaDB-dokumentationen](https://mariadb.com/kb/en/library/create-user/).

   Genom att använda följande kommandon, kan den nya replikeringsprincipen komma åt originalet från vilken dator som helst, inte bara den dator som är värd för själva huvud servern. För den här åtkomsten anger du **syncuser\@%** i kommandot för att skapa en användare.
   
   Mer information om MariaDB-dokumentationen finns i [Ange konto namn](https://mariadb.com/kb/en/library/create-user/#account-names).

   **SQL-kommando**

   - Replikering med SSL

       Om du vill kräva SSL för alla användar anslutningar anger du följande kommando för att skapa en användare:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikering utan SSL

       Om SSL inte krävs för alla anslutningar, anger du följande kommando för att skapa en användare:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Om du vill skapa en replikeringsprincip i MySQL Workbench väljer du **användare och behörigheter**i fönstret **hantering** . Välj sedan **Lägg till konto**.
 
   ![Användare och behörigheter](./media/howto-data-in-replication/users_privileges.png)

   Ange ett användar namn i fältet **inloggnings namn** .

   ![Synkronisera användare](./media/howto-data-in-replication/syncuser.png)
 
   Välj panelen **administrativa roller** och välj **replikering slav**i listan över **globala privilegier**. Välj **Använd** för att skapa en replikeringsprincip.

   ![Replikering slav](./media/howto-data-in-replication/replicationslave.png)


4. Ställ in huvud servern på skrivskyddat läge.

   Innan du dumpar en databas måste servern placeras i skrivskyddat läge. I skrivskyddat läge kan inte originalet bearbeta några Skriv transaktioner. För att undvika affärs påverkan kan du schemalägga det skrivskyddade fönstret under låg belastnings tid.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Hämta den aktuella binära logg filens namn och offset.

   Du kan ta reda på den aktuella binära logg filens namn och förskjutning genom att köra kommandot [`show master status`](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Resultaten bör likna följande tabell:
   
   ![Huvud status resultat](./media/howto-data-in-replication/masterstatus.png)

   Anteckna namnet på den binära filen, eftersom det används i senare steg.
   
6. Hämta GTID position (valfritt, krävs för replikering med GTID).

   Kör funktionen [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) för att hämta GTID position för motsvarande BinLog-filnamn och offset.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Dumpa och Återställ huvud servern

1. Dumpa alla databaser från huvud servern.

   Använd mysqldump för att dumpa alla databaser från huvud servern. Det är inte nödvändigt att dumpa MySQL-biblioteket och test biblioteket.

    Mer information finns i [dumpa och Återställ](howto-migrate-dump-restore.md).

2. Ställ in huvud servern på läsa/Skriv-läge.

   När databasen har dump ATS ändrar du tillbaka huvud MariaDB-servern till läsa/Skriv-läge.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Återställ dumpfilen till den nya servern.

   Återställ dumpfilen till servern som skapats i Azure Database for MariaDB-tjänsten. Se [dump & Restore](howto-migrate-dump-restore.md) for How to Restore dump File to a MariaDB Server.

   Om dumpfilen är stor laddar du upp den till en virtuell dator i Azure inom samma region som replik servern. Återställ den till Azure Database for MariaDB-servern från den virtuella datorn.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Länka huvud-och replik servrar för att starta Datareplikering

1. Ange huvud servern.

   Alla Datareplikering funktioner utförs med lagrade procedurer. Du hittar alla procedurer på [datareplikering lagrade procedurer](reference-data-in-stored-procedures.md). Lagrade procedurer kan köras i MySQL-gränssnittet eller MySQL Workbench.

   Logga in på mål replik servern i Azure DB for MariaDB-tjänsten för att länka två servrar och starta replikering. Ange sedan den externa instansen som huvud server genom att använda `mysql.az_replication_change_master` eller `mysql.az_replication_change_master_with_gtid` lagrade proceduren på Azure DB för MariaDB-servern.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   eller
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: värd namnet för huvud servern
   - master_user: användar namn för huvud servern
   - master_password: lösen ordet för huvud servern
   - master_log_file: ett binärt logg fils namn körs `show master status`
   - master_log_pos: binär logg position körs `show master status`
   - master_gtid_pos: GTID position körs `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: certifikat utfärdarens kontext. Om du inte använder SSL skickar du en tom sträng. *
    
    
    \* Vi rekommenderar att du skickar parametern master_ssl_ca som en variabel. Mer information finns i följande exempel.

   **Exempel**

   - Replikering med SSL

       Skapa variabeln `@cert` genom att köra följande kommandon:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replikering med SSL konfigureras mellan en huvud server som finns i domänen companya.com och en replik server som är värd för Azure Database for MariaDB. Den här lagrade proceduren körs på repliken.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replikering utan SSL

       Replikering utan SSL konfigureras mellan en huvud server som finns i domänen companya.com och en replik server som är värd för Azure Database for MariaDB. Den här lagrade proceduren körs på repliken.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Starta replikering.

   Anropa den `mysql.az_replication_start` lagrade proceduren för att starta replikeringen.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Kontrol lera replikeringsstatus.

   Anropa kommandot [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) på replik servern för att visa replikeringsstatus.
    
   ```sql
   show slave status;
   ```

   Om `Slave_IO_Running` och `Slave_SQL_Running` är i läget `yes`och värdet för `Seconds_Behind_Master` är `0`, fungerar replikeringen. `Seconds_Behind_Master` anger hur sen repliken är. Om värdet inte `0`, bearbetar repliken uppdateringar.

4. Uppdatera motsvarande servervariabler för att göra datareplikering säkrare (krävs endast för replikering utan GTID).
    
    På grund av en begränsning för ursprunglig replikering i MariaDB måste du ange [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) och [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variabler på replikering utan GTID-scenariot.

    Kontrol lera att den sekundära serverns `sync_master_info` och `sync_relay_log_info` variabler för att se till att datareplikeringen är stabil och ange variablerna som ska `1`s.
    
## <a name="other-stored-procedures"></a>Andra lagrade procedurer

### <a name="stop-replication"></a>Stoppa replikering

Använd följande lagrade procedur för att stoppa replikeringen mellan huvud-och replik servern:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Ta bort replikeringsrelationen

Använd följande lagrade procedur om du vill ta bort relationen mellan huvud-och replik servern:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Hoppa över replikeringsfel

Använd följande lagrade procedur om du vill hoppa över ett replikeringsfel och tillåta replikering:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Nästa steg
Läs mer om [datareplikering](concepts-data-in-replication.md) för Azure Database for MariaDB.

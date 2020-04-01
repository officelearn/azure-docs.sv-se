---
title: Konfigurera data-in Replication - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du konfigurerar data-in Replication i Azure Database för MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/30/2020
ms.openlocfilehash: 332feffead74174ba0b9b278d8de1c5957d5b9e6
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422476"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurera datainsamling i Azure Database för MariaDB

I den här artikeln beskrivs hur du konfigurerar data-in Replication i Azure Database för MariaDB genom att konfigurera huvud- och replikservrarna. Den här artikeln förutsätter att du har viss tidigare erfarenhet av MariaDB-servrar och databaser.

Om du vill skapa en replik i Azure Database for MariaDB-tjänsten synkroniserar Data-in Replication data från en lokal huvudserver, i virtuella datorer eller i molndatabastjänster.

Granska [begränsningarna och kraven](concepts-data-in-replication.md#limitations-and-considerations) för data-in-replikering innan du utför stegen i den här artikeln.

> [!NOTE]
> Om huvudservern är version 10.2 eller nyare rekommenderar vi att du ställer in datareplikering med hjälp av [globalt transaktions-ID](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Skapa en MariaDB-server som ska användas som replik

1. Skapa en ny Azure-databas för MariaDB-server (till exempel replica.mariadb.database.azure.com). Servern är replikservern i Data-in Replication.

    Mer information om servergenerering finns i [Skapa en Azure-databas för MariaDB-server med hjälp av Azure-portalen](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Du måste skapa Azure-databasen för MariaDB-servern på prisnivåerna Allmänt ändamål eller Minne optimerad.

2. Skapa identiska användarkonton och motsvarande privilegier.
    
    Användarkonton replikeras inte från huvudservern till replikservern. Om du vill ge användaråtkomst till replikservern måste du manuellt skapa alla konton och motsvarande privilegier på den nyligen skapade Azure-databasen för MariaDB-servern.

3. Lägg till huvudserverns IP-adress i replikens brandväggsregler. 

   Uppdatera brandväggsregler med hjälp av [Azure-portalen](howto-manage-firewall-portal.md) eller [Azure CLI](howto-manage-firewall-cli.md).

## <a name="configure-the-master-server"></a>Konfigurera huvudservern

Följande steg förbereder och konfigurerar MariaDB-servern som finns lokalt, i en virtuell dator eller i en molndatabastjänst för datareplikering. MariaDB-servern är huvudansvarig i Data-in Replication.

1. Granska [huvudserverkraven](concepts-data-in-replication.md#requirements) innan du fortsätter. 

   Kontrollera till exempel att huvudservern tillåter både inkommande och utgående trafik på port 3306 och att huvudservern har en **offentlig IP-adress**, DNS är allmänt tillgänglig eller har ett fullständigt kvalificerat domännamn (FQDN). 
   
   Testa anslutningen till huvudservern genom att försöka ansluta från ett verktyg som mySQL-kommandoraden som finns på en annan dator eller från [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) som är tillgängligt i Azure-portalen.

2. Aktivera binär loggning.
    
    Om du vill se om binär loggning är aktiverad på bakgrunden anger du följande kommando:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Om variabeln [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) returnerar värdet `ON`aktiveras binär loggning på servern.

   Om `log_bin` värdet `OFF`returneras redigerar du filen `log_bin=ON` **my.cnf** så att den aktiverar binär loggning. Starta om servern för att ändringen ska börja gälla.

3. Konfigurera huvudserverinställningar.

    Data-in Replication kräver `lower_case_table_names` att parametern är konsekvent mellan huvud- och replikservrarna. Parametern `lower_case_table_names` är `1` inställd på som standard i Azure Database för MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Skapa en ny replikeringsroll och ställ in behörigheter.

   Skapa ett användarkonto på huvudservern som är konfigurerad med replikeringsbehörighet. Du kan skapa ett konto med hjälp av SQL-kommandon eller MySQL Workbench. Om du planerar att replikera med SSL måste du ange detta när du skapar användarkontot.
   
   Mer information om hur du lägger till användarkonton på huvudservern finns i [MariaDB-dokumentationen](https://mariadb.com/kb/en/library/create-user/).

   Genom att använda följande kommandon kan den nya replikeringsrollen komma åt mastern från vilken dator som helst, inte bara den dator som är värd för själva mastern. För den här åtkomsten anger du **syncuser\@'%'** i kommandot för att skapa en användare.
   
   Mer information om MariaDB-dokumentation finns i [ange kontonamn](https://mariadb.com/kb/en/library/create-user/#account-names).

   **SQL-kommando**

   - Replikering med SSL

       Om du vill kräva SSL för alla användaranslutningar anger du följande kommando för att skapa en användare:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikering utan SSL

       Om SSL inte krävs för alla anslutningar anger du följande kommando för att skapa en användare:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Om du vill skapa replikeringsrollen i MySQL Workbench väljer du Användare och privilegier i **hanteringsfönstret**. **Management** Välj sedan **Lägg till konto**.
 
   ![Användare och privilegier](./media/howto-data-in-replication/users_privileges.png)

   Ange ett användarnamn i fältet **Inloggningsnamn.**

   ![Synkronisera användare](./media/howto-data-in-replication/syncuser.png)
 
   Välj panelen **Administrativa roller** och välj sedan **Replikeringsslav**i listan över **globala privilegier**. Välj **Använd** om du vill skapa replikeringsrollen.

   ![Replikeringslav](./media/howto-data-in-replication/replicationslave.png)


5. Ställ in huvudservern i skrivskyddat läge.

   Innan du dumpar en databas måste servern placeras i skrivskyddat läge. I skrivskyddat läge kan befälhavaren inte bearbeta några skrivtransaktioner. Schemalägg skrivskyddat fönster under en lågbelastningstid för att undvika påverkan på verksamheten.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Hämta det aktuella binära loggfilsnamnet och förskjutningen.

   Om du vill ta reda på det [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)aktuella binära loggfilsnamnet och förskjutningen kör du kommandot .
    
   ```sql
   show master status;
   ```
   Resultaten bör likna följande tabell:
   
   ![Resultat av huvudstatus](./media/howto-data-in-replication/masterstatus.png)

   Observera det binära filnamnet, eftersom det kommer att användas i senare steg.
   
7. Hämta GTID-positionen (tillval, som behövs för replikering med GTID).

   Kör funktionen [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) för att hämta GTID-positionen för motsvarande binlogfilnamn och förskjutning.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Dumpa och återställa huvudservern

1. Dumpa alla databaser från huvudservern.

   Använd mysqldump för att dumpa alla databaser från huvudservern. Det är inte nödvändigt att dumpa MySQL-biblioteket och testbiblioteket.

    Mer information finns i [Dumpa och återställa](howto-migrate-dump-restore.md).

2. Ställ in huvudservern på läs-/skrivläge.

   När databasen har dumpats ändrar du tillbaka huvudservern för MariaDB till läs-/skrivläge.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Återställ dumpfilen till den nya servern.

   Återställ dumpfilen till servern som skapats i Azure Database for MariaDB-tjänsten. Se [Dumpa & Återställning](howto-migrate-dump-restore.md) för hur du återställer en dumpfil till en MariaDB-server.

   Om dumpfilen är stor överför du den till en virtuell dator i Azure inom samma region som replikservern. Återställa den till Azure Database för MariaDB-servern från den virtuella datorn.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Länka huvud- och replikservrarna för att starta datainsamling

1. Ställ in huvudservern.

   Alla funktioner för datareplikation utförs genom lagrade procedurer. Du hittar alla procedurer vid [datainsamlingsbetonade procedurer](reference-data-in-stored-procedures.md). Lagrade procedurer kan köras i MySQL-skalet eller MySQL Workbench.

   Om du vill länka två servrar och starta replikeringen loggar du in på målreplikservern i Azure DB för MariaDB-tjänsten. Ange sedan den externa instansen som `mysql.az_replication_change_master` huvudserver med hjälp av eller `mysql.az_replication_change_master_with_gtid` lagrad procedur på Azure DB för MariaDB-servern.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   eller
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: värdnamn för huvudservern
   - master_user: användarnamn för huvudservern
   - master_password: lösenord för huvudservern
   - master_log_file: binärt loggfilsnamn från att köras`show master status`
   - master_log_pos: binär loggposition från att köras`show master status`
   - master_gtid_pos: GTID-position från löpning`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: CA-certifikatets kontext. Om du inte använder SSL skickar du en tom sträng.*
    
    
    *Vi rekommenderar att du skickar in parametern master_ssl_ca som en variabel. Mer information finns i följande exempel.

   **Exempel**

   - Replikering med SSL

       Skapa variabeln `@cert` genom att köra följande kommandon:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replikering med SSL har konfigurerats mellan en huvudserver som finns i domänen companya.com och en replikserver som finns i Azure Database för MariaDB. Den här lagrade proceduren körs på repliken.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replikering utan SSL

       Replikering utan SSL ställs in mellan en huvudserver som finns i domänen companya.com och en replikserver som finns i Azure Database för MariaDB. Den här lagrade proceduren körs på repliken.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Starta replikeringen.

   Anropa `mysql.az_replication_start` den lagrade proceduren för att starta replikeringen.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Kontrollera replikeringsstatus.

   Anropa [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) kommandot på replikservern för att visa replikeringsstatusen.
    
   ```sql
   show slave status;
   ```

   Om `Slave_IO_Running` `Slave_SQL_Running` och är `yes`i tillståndet och `Seconds_Behind_Master` `0`värdet för är , replikering fungerar. `Seconds_Behind_Master`anger hur sen repliken är. Om värdet inte `0`är bearbetar repliken uppdateringar.

4. Uppdatera motsvarande servervariabler för att göra data-in-replikering säkrare (krävs endast för replikering utan GTID).
    
    På grund av en inbyggd replikeringsbegränsning i MariaDB måste du ange [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) och [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variabler för replikering utan GTID-scenariot.

    Kontrollera slavserverns `sync_master_info` och `sync_relay_log_info` variablerna för att se till att datainstalleringen `1`är stabil och ställ in variablerna på .
    
## <a name="other-stored-procedures"></a>Andra lagrade procedurer

### <a name="stop-replication"></a>Stoppa replikering

Om du vill stoppa replikeringen mellan huvud- och replikservern använder du följande lagrade procedur:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Ta bort replikeringsrelationen

Om du vill ta bort relationen mellan huvud- och replikservern använder du följande lagrade procedur:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Hoppa över replikeringsfelet

Om du vill hoppa över ett replikeringsfel och tillåta replikering använder du följande lagrade procedur:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Nästa steg
Läs mer om [datareplikering](concepts-data-in-replication.md) för Azure-databas för MariaDB.

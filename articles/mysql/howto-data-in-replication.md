---
title: Konfigurera data-in replikering - Azure Database för MySQL
description: I den här artikeln beskrivs hur du konfigurerar data-in Replication för Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.openlocfilehash: 2148ce41267627d9d6e0437897a99a8dbdbe0746
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382774"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Konfigurera Azure Database för MySQL-data-in Replication

I den här artikeln får du lära dig hur du konfigurerar data-in Replication i Azure Database for MySQL-tjänsten genom att konfigurera huvud- och replikservrarna. Med data-in Replication kan du synkronisera data från en huvud-MySQL-server som körs lokalt, i virtuella datorer eller databastjänster som finns hos andra molnleverantörer till en replik i Azure Database for MySQL-tjänsten. 

Den här artikeln förutsätter att du har åtminstone några tidigare erfarenheter med MySQL-servrar och databaser.

Granska [begränsningarna och kraven](concepts-data-in-replication.md#limitations-and-considerations) för data-in-replikering innan du utför stegen i den här artikeln.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Skapa en MySQL-server som ska användas som replik

1. Skapa en ny Azure-databas för MySQL-server

   Skapa en ny MySQL-server (t.ex. "replica.mysql.database.azure.com"). Se [Skapa en Azure-databas för MySQL-server med hjälp av Azure-portalen](quickstart-create-mysql-server-database-using-azure-portal.md) för servergenerering. Den här servern är "replikservern" i Data-in Replication.

   > [!IMPORTANT]
   > Azure-databasen för MySQL-servern måste skapas på prisnivåerna Allmänt ändamål eller Minne optimerad.
   > 

2. Skapa samma användarkonton och motsvarande privilegier

   Användarkonton replikeras inte från huvudservern till replikservern. Om du planerar att ge användare åtkomst till replikservern måste du manuellt skapa alla konton och motsvarande privilegier på den här nyligen skapade Azure-databasen för MySQL-servern.

3. Lägg till huvudserverns IP-adress i replikens brandväggsregler. 

   Uppdatera brandväggsregler med hjälp av [Azure-portalen](howto-manage-firewall-using-portal.md) eller [Azure CLI](howto-manage-firewall-using-cli.md).

## <a name="configure-the-master-server"></a>Konfigurera huvudservern
Följande steg förbereder och konfigurerar MySQL-servern som finns lokalt, på en virtuell dator eller databastjänst som finns hos andra molnleverantörer för databaserad replikering. Den här servern är "master" i Data-in-replikering.


1. Granska [huvudserverkraven](concepts-data-in-replication.md#requirements) innan du fortsätter. 

   Kontrollera till exempel att huvudservern tillåter både inkommande och utgående trafik på port 3306 och att huvudservern har en **offentlig IP-adress**, DNS är allmänt tillgänglig eller har ett fullständigt kvalificerat domännamn (FQDN). 
   
   Testa anslutningen till huvudservern genom att försöka ansluta från ett verktyg som mySQL-kommandoraden som finns på en annan dator eller från [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) som är tillgängligt i Azure-portalen 

2. Aktivera binär loggning

   Kontrollera om binär loggning har aktiverats på bakgrunden genom att köra följande kommando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Om variabeln [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) returneras med värdet "ON" aktiveras binär loggning på servern. 

   Om `log_bin` returneras med värdet "OFF" aktiverar du binär loggning genom att `log_bin=ON` redigera filen my.cnf så att och starta om servern för att ändringen ska börja gälla.

3. Huvudserverinställningar

   Data-in Replication `lower_case_table_names` kräver att parametern är konsekvent mellan huvud- och replikservrarna. Den här parametern är 1 som standard i Azure Database for MySQL. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Skapa en ny replikeringsroll och ställ in behörighet

   Skapa ett användarkonto på huvudservern som har konfigurerats med replikeringsbehörighet. Detta kan göras via SQL-kommandon eller ett verktyg som MySQL Workbench. Överväg om du planerar att replikera med SSL eftersom detta måste anges när du skapar användaren. Se MySQL-dokumentationen för att förstå hur du lägger till [användarkonton](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) på huvudservern. 

   I kommandona nedan kan den nya replikeringsrollen som skapas komma åt mastern från vilken dator som helst, inte bara den dator som är värd för själva mastern. Detta görs genom att ange "syncuser@'%'" i kommandot skapa användare. Mer information om hur du [anger kontonamn](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)finns i MySQL-dokumentationen.

   **SQL-kommando**

   *Replikering med SSL*

   Om du vill kräva SSL för alla användaranslutningar använder du följande kommando för att skapa en användare: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikering utan SSL*

   Om SSL inte krävs för alla anslutningar använder du följande kommando för att skapa en användare:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Om du vill skapa replikeringsrollen i MySQL Workbench öppnar du panelen Användare och privilegier från **hanteringspanelen.** **Management** Klicka sedan på **Lägg till konto**. 
 
   ![Användare och privilegier](./media/howto-data-in-replication/users_privileges.png)

   Skriv in användarnamnet i fältet **Inloggningsnamn.** 

   ![Synkronisera användare](./media/howto-data-in-replication/syncuser.png)
 
   Klicka på panelen **Administrativa roller** och välj sedan **Replikeringsslav** i listan över **globala privilegier**. Klicka sedan på **Verkställ** för att skapa replikeringsrollen.

   ![Replikeringslav](./media/howto-data-in-replication/replicationslave.png)


5. Ställa in huvudservern på skrivskyddat läge

   Innan du börjar dumpa databasen måste servern placeras i skrivskyddat läge. I skrivskyddat läge kan befälhavaren inte bearbeta några skrivtransaktioner. Utvärdera effekten på ditt företag och schemalägg skrivskyddade fönstret i en lågbelastningstid om det behövs.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Hämta binärt loggfilsnamn och förskjutning

   Kör [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) kommandot för att fastställa det aktuella binära loggfilsnamnet och förskjutningen.
    
   ```sql
   show master status;
   ```
   Resultaten bör vara som följande. Se till att notera det binära filnamnet eftersom det kommer att användas i senare steg.

   ![Resultat av huvudstatus](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Dumpa och återställa huvudservern

1. Dumpa alla databaser från huvudservern

   Du kan använda mysqldump för att dumpa databaser från din herre. Mer information finns i [Dump & Restore](concepts-migrate-dump-restore.md). Det är onödigt att dumpa MySQL-bibliotek och testbibliotek.

2. Ange att huvudservern ska läsas/skriva-läge

   När databasen har dumpats ändrar du tillbaka mysql-servern till läs-/skrivläge.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Återställa dumpfilen till ny server

   Återställ dumpfilen till servern som skapats i Azure Database for MySQL-tjänsten. Se [Dump & Restore](concepts-migrate-dump-restore.md) för hur du återställer en dumpfil till en MySQL-server. Om dumpfilen är stor överför du den till en virtuell dator i Azure inom samma region som replikservern. Återställ den till Azure Database for MySQL-servern från den virtuella datorn.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Länka huvud- och replikservrar för att starta datainsamling

1. Ange huvudserver

   Alla funktioner för datareplikation utförs genom lagrade procedurer. Du hittar alla procedurer vid [datainsamlingsbetonade procedurer](reference-data-in-stored-procedures.md). De lagrade procedurerna kan köras i MySQL-skalet eller MySQL Workbench. 

   Om du vill länka två servrar och starta replikeringen loggar du in på målreplikservern i Azure DB för MySQL-tjänsten och anger den externa instansen som huvudserver. Detta görs med `mysql.az_replication_change_master` hjälp av den lagrade proceduren på Azure DB för MySQL-servern.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: värdnamn för huvudservern
   - master_user: användarnamn för huvudservern
   - master_password: lösenord för huvudservern
   - master_log_file: binärt loggfilsnamn från att köras`show master status`
   - master_log_pos: binär loggposition från att köras`show master status`
   - master_ssl_ca: CA-certifikatets kontext. Om du inte använder SSL, passera i tom sträng.
       - Vi rekommenderar att du skickar in den här parametern som en variabel. Mer information finns i följande exempel.

> [!NOTE]
> Om huvudservern finns i en virtuell Azure-dator anger du "Tillåt åtkomst till Azure-tjänster" till "ON" så att huvud- och replikservrarna kan kommunicera med varandra. Den här inställningen kan ändras från **säkerhetsalternativen För Anslutning.** Mer information finns i [hantera brandväggsregler med hjälp av portalen.](howto-manage-firewall-using-portal.md)

   **Exempel**

   *Replikering med SSL*

   Variabeln `@cert` skapas genom att köra följande MySQL-kommandon: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replikering med SSL har konfigurerats mellan en huvudserver som finns i domänen "companya.com" och en replikserver som finns i Azure Database for MySQL. Den här lagrade proceduren körs på repliken. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replikering utan SSL*

   Replikering utan SSL ställs in mellan en huvudserver som finns i domänen "companya.com" och en replikserver som finns i Azure Database for MySQL. Den här lagrade proceduren körs på repliken.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

1. Starta replikeringen

   Anropa `mysql.az_replication_start` den lagrade proceduren för att initiera replikering.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Kontrollera replikeringsstatus

   Anropa [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) kommandot på replikservern för att visa replikeringsstatusen.
    
   ```sql
   show slave status;
   ```

   Om tillståndet `Slave_IO_Running` för `Slave_SQL_Running` och är "ja" `Seconds_Behind_Master` och värdet på är "0", är replikering fungerar bra. `Seconds_Behind_Master`anger hur sen repliken är. Om värdet inte är "0" betyder det att repliken bearbetar uppdateringar. 

## <a name="other-stored-procedures"></a>Andra lagrade procedurer

### <a name="stop-replication"></a>Stoppa replikering

Om du vill stoppa replikeringen mellan huvud- och replikservern använder du följande lagrade procedur:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Ta bort replikeringsrelation

Om du vill ta bort relationen mellan huvud- och replikservern använder du följande lagrade procedur:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Hoppa över replikeringsfel

Om du vill hoppa över ett replikeringsfel och tillåta att replikeringen fortsätter använder du följande lagrade procedur:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [datareplikering](concepts-data-in-replication.md) för Azure Database för MySQL. 

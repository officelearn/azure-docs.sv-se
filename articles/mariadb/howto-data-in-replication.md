---
title: Konfigurera Data i replikering för att replikera data till Azure Database for MariaDB.
description: Den här artikeln beskriver hur du ställer in Data i replikering för Azure-databas för MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 13d1353b0f8b3320972973d428f4aedb0fb68ac8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990696"
---
# <a name="how-to-configure-azure-database-for-mariadb-data-in-replication"></a>Så här konfigurerar du Azure-databas för MariaDB-Data i replikering

I den här artikeln får du lära dig hur du ställer in Data i replikering i tjänsten Azure Database for MariaDB genom att konfigurera master och replik-servrar. Data i replikering kan du synkronisera data från en huvudserver MariaDB som körs lokalt, i virtuella datorer eller som andra molnleverantörer värd till en replik i tjänsten Azure Database for MariaDB-databastjänster. 

Den här artikeln förutsätter att du har minst tidigare erfarenhet med MariaDB-servrar och databaser.

## <a name="create-a-mariadb-server-to-be-used-as-replica"></a>Skapa en MariaDB-server som ska användas som replik

1. Skapa en ny Azure-databas för MariaDB-server

   Skapa en ny MariaDB-server (t.ex. ”replica.mariadb.database.azure.com”). Referera till [skapa en Azure Database for MariaDB-server med hjälp av Azure-portalen](quickstart-create-mariadb-server-database-using-azure-portal.md) för servern har skapats. Den här servern är ”replikservern” i Data i replikeringen.

   > [!IMPORTANT]
   > Azure Database for MariaDB-server måste skapas i generell användning eller Minnesoptimerade prisnivåer.
   > 

2. Skapa samma användarkonton och motsvarande privilegier

   Användarkonton replikeras inte från den överordnade servern till replikservern. Om du planerar att ge användarna åtkomst till replikservern, måste du manuellt skapa alla konton och motsvarande behörigheter på det nyligen skapade Azure Database for MariaDB-server.

## <a name="configure-the-master-server"></a>Konfigurera huvudservern
Följande steg förbereder och konfigurera MariaDB-servern körs lokalt, i en virtuell dator eller en databastjänst som är värd för andra molnleverantörer för Data i replikering. Den här servern är ”master” i Data i replikeringen. 

1. Aktivera binär loggning

   Kontrollera om binär loggning har aktiverats på huvudmålservern genom att köra följande kommando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Om variabeln [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) returneras med värdet ”på”, binär loggning är aktiverat på servern. 

   Om `log_bin` är returneras med värdet ”OFF” aktivera binär loggning genom att redigera filen my.cnf så som `log_bin=ON` och starta om servern för att ändringen ska börja gälla.

2. Master-serverinställningar

   Data i replikering kräver parametern `lower_case_table_names` är konsekvent mellan huvud- och replik-servrar. Den här parametern är 1 som standard i Azure Database for MariaDB. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Skapa en ny replikeringsroll som och ställa in behörighet

   Skapa ett användarkonto på huvudservern som är konfigurerad med behörighet för replikering. Detta kan göras via SQL-kommandon eller ett verktyg som MySQL Workbench. Överväg om du vill replikera med SSL, som måste anges när du skapar användaren. MariaDB-dokumentationen för att förstå hur du [lägga till användarkonton](https://mariadb.com/kb/en/library/create-user/) på din huvudservern. 

   I kommandona nedan är den nya replikering rollen skapats åtkomst till huvuddatabasen från en valfri dator, inte bara den dator som är värd för själva originalet. Detta görs genom att ange ”syncuser@'%'” i kommandot för att skapa användaren. Finns i MariaDB-dokumentationen för mer information om [att ange kontonamn](https://mariadb.com/kb/en/library/create-user/#account-names).

   **SQL-kommando**

   *Replikering med SSL*

   Om du vill kräva SSL för anslutningar för alla användare, använder du följande kommando för att skapa en användare: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikering utan SSL*

   Om SSL inte krävs för alla anslutningar, använder du följande kommando för att skapa en användare:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   För att skapa rollen replikering i MySQL Workbench, öppna den **användare och behörigheter** panelen från den **Management** panelen. Klicka sedan på **Lägg till konto**. 
 
   ![Användare och behörigheter](./media/howto-data-in-replication/users_privileges.png)

   Ange användarnamnet i den **inloggningsnamn** fält. 

   ![Synkronisera användare](./media/howto-data-in-replication/syncuser.png)
 
   Klicka på den **administrativa roller** panelen och välj sedan **replikering underordnad** från listan över **globala behörigheter**. Klicka sedan på **tillämpa** att skapa replikering rollen.

   ![Underordnad replikering](./media/howto-data-in-replication/replicationslave.png)


4. Ange huvudservern till skrivskyddat läge

   Innan du börjar att dumpa ut databasen måste servern ska placeras i skrivskyddat läge. När du är i skrivskyddat läge, går huvudservern inte att bearbeta alla skrivtransaktioner. Utvärdera inverkan på din verksamhet och schemalägga fönstret skrivskyddad i tider om det behövs.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Hämta binära loggfilens namn och förskjutning

   Kör den [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/) kommando för att fastställa aktuell binära loggfilens namn och förskjutning.
    
   ```sql
   show master status;
   ```
   Resultatet bör vara som här. Se till att notera namnet på binära filen eftersom den används i senare steg.

   ![Resultat för Master-Status](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>Dumpa och Återställ huvudserver

1. Dumpa alla databaser från huvudserver

   Du kan använda mysqldump till dump databaser från din originalet. Mer information finns att [dumpa och återställa](howto-migrate-dump-restore.md). Det är inte nödvändigt att dumpa MySQL-bibliotek och testa bibliotek.

2. Ange huvudservern för att läsa/skriva-läge

   När databasen har tagits dumpade, ändra huvudservern MariaDB server tillbaka för att läsa/skriva-läge.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Återställ dumpfilen till ny server

   Återställa dumpfilen till den server som skapats i Azure Database for MariaDB-tjänsten. Referera till [dumpa och återställa](howto-migrate-dump-restore.md) för hur du återställer den här filen till en MariaDB-server. Om dumpfilen är stor kan du överföra den till en virtuell dator i Azure inom samma region som replikservern. Återställa den till Azure Database for MariaDB-server från den virtuella datorn.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>Länka huvud- och repliken servrar Data i replikeringen ska börja

1. Set-huvudservern

   Alla Data i replikering funktioner utförs av lagrade procedurer. Du kan hitta alla procedurer i [Data i replikerade lagrade procedurer](reference-data-in-stored-procedures.md). De lagrade procedurerna kan köras i MySQL-gränssnitt eller MySQL Workbench.

   Länka två servrar och starta replikering, logga in på målservern för repliken i Azure DB för MariaDB-tjänsten och ange den externa instansen huvudservern. Detta görs med hjälp av den `mysql.az_replication_change_master` lagrade proceduren på Azure-databas för MariaDB-server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: värdnamn för domänens huvudserver
   - master_user: användarnamn för huvudservern
   - master_password: lösenordet för huvudservern
   - master_log_file: binära loggfilens namn från att köras `show master status`
   - master_log_pos: binär logg position från att köras `show master status`
   - master_ssl_ca: kontexten för CA-certifikat. Om du inte använder SSL skicka tom sträng.
       - Det rekommenderas att skicka den här parametern i som en variabel. Se följande exempel för mer information.

   **Exempel**

   *Replikering med SSL*

   Variabeln `@cert` har skapats genom att köra följande kommandon: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replikering med SSL har konfigurerats mellan en huvudserver som finns i domänen ”companya.com” och en replikserver som finns i Azure Database for MariaDB. Den här lagrade proceduren körs på repliken. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
   ```
   *Replikering utan SSL*

   Replikering utan SSL har konfigurerats mellan en huvudserver som finns i domänen ”companya.com” och en replikserver som finns i Azure Database for MariaDB. Den här lagrade proceduren körs på repliken.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
   ```

2. Starta replikering

   Anropa den `mysql.az_replication_start` lagrade proceduren för att initiera replikering.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Kontrollera replikeringsstatus

   Anropa den [ `show slave status` ](https://mariadb.com/kb/en/library/show-slave-status/) på replikservern för att visa replikeringsstatus.
    
   ```sql
   show slave status;
   ```

   Om tillståndet för `Slave_IO_Running` och `Slave_SQL_Running` är ”yes” och värdet för `Seconds_Behind_Master` är ”0”, replikeringen fungerar. `Seconds_Behind_Master` Anger hur sent repliken är. Om värdet är ”0”, betyder det att repliken bearbetar uppdateringar. 

## <a name="other-stored-procedures"></a>Andra lagrade procedurer

### <a name="stop-replication"></a>Stoppa replikering

Använd följande lagrade procedur om du vill stoppa replikering mellan huvud- och repliken servern:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Ta bort replikeringsrelationen

Ta bort relationen mellan huvud- och repliken server med följande lagrade procedur:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Hoppa över replikeringsfel

Om du vill hoppa över ett replikeringsfel och att replikeringen kan fortsätta att använda följande lagrade procedur:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Data i replikering](concepts-data-in-replication.md) för Azure Database for MariaDB.
---
title: Konfigurera Data i replikering för att replikera data till Azure-databas för MySQL.
description: Den här artikeln beskriver hur du ställer in Data i replikering för Azure-databas för MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: e099597eae419653a2a40c7f01ee7abbbc4657f0
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294429"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Så här konfigurerar du Azure-databas för MySQL Data i replikering

I den här artikeln får du lära dig hur du ställer in Data i replikering i Azure-databas för MySQL-tjänst genom att konfigurera de primära servern och repliken servrarna. Data i replikering kan du synkronisera data från en primär MySQL-server som körs lokalt, i virtuella datorer eller databastjänster hos andra molntjänstleverantörer till en replik i Azure-databas för MySQL-tjänst. 

Den här artikeln förutsätter att du har minst tidigare erfarenhet med MySQL-servrar och databaser.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Skapa en MySQL-server som ska användas som replik

1. Skapa en ny Azure-databas för MySQL-server

   Skapa en ny MySQL-server (t.ex. ”replica.mysql.database.azure.com”). Referera till [skapa en Azure-databas för MySQL-servern med hjälp av Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md) för att skapa en server. Den här servern är ”replikservern” i Data i replikeringen.

   > [!IMPORTANT]
   > Azure-databas för MySQL-servern måste skapas i generella eller Minnesoptimerade prisnivåer.
   > 

2. Skapa samma användarkonton och motsvarande behörigheter

   Användarkonton replikeras inte från den primära servern till replikservern. Om du planerar att ge användarna åtkomst till replikservern, måste du manuellt skapa alla konton och motsvarande behörigheter på den nya Azure-databas för MySQL-servern.

## <a name="configure-the-primary-server"></a>Konfigurera den primära servern
Följande steg förbereder och konfigurera den MySQL-server lokalt, i en virtuell dator eller databastjänsten hos andra molntjänstleverantörer för Data i replikering. Den här servern är ”primär” i Data i replikeringen. 

1. Aktivera binär loggning

   Kontrollera om binär loggning har aktiverats på primärt genom att köra följande kommando: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Om variabeln [ `log_bin` ](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) returneras med värdet ”ON”, binär loggning är aktiverat på servern. 

   Om `log_bin` är returneras med värdet ”OFF” aktivera binär loggning genom att redigera filen my.cnf så som `log_bin=ON` och starta om servern för att ändringarna ska börja gälla.

2. Primär server-inställningar

   Data i replikering kräver parametern `lower_case_table_names` konsekvent mellan den primära servern och repliken. Den här parametern är 1 som standard i Azure för MySQL-databas. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Skapa en ny roll för replikering och Ställ in behörighet

   Skapa ett användarkonto på den primära servern som är konfigurerad med behörighet för replikering. Detta kan göras via SQL-kommandon eller ett verktyg som MySQL-arbetsstationen. Överväg om du tänker replikerar med SSL eftersom det måste anges när användaren. MySQL-dokumentationen för att förstå hur du [lägga till användarkonton](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html) på den primära servern. 

   Den nya replikering rollen skapats har tillgång till primärt från en dator, inte bara den datorn som är värd för primärt själva i kommandona nedan. Detta görs genom att ange ”syncuser@'%'” i kommandot för att skapa användaren. Se MySQL-dokumentation för mer information om [Ange kontonamn](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **SQL-kommando**

   *Replikering med SSL*

   Använder du följande kommando för att kräva SSL för alla användare från att skapa en användare: 

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

   **MySQL-arbetsstationen**

   Om du vill skapa rollen replikering i MySQL arbetsstationen, öppna den **användare och behörigheter** panelen från den **Management** panelen. Klicka på **Lägg till konto**. 
 
   ![Användare och behörigheter](./media/howto-data-in-replication/users_privileges.png)

   Ange användarnamnet i den **inloggningsnamnet** fältet. 

   ![Synkronisera användare](./media/howto-data-in-replication/syncuser.png)
 
   Klicka på den **administrativa roller** panelen och välj sedan **replikering slavserver** från listan över **globala privilegier**. Klicka på **tillämpa** att skapa en roll för replikering.

   ![Replikering slavserver](./media/howto-data-in-replication/replicationslave.png)


4. Ange den primära servern till skrivskyddat läge

   Innan du börjar dump databasen, måste servern ska placeras i skrivskyddat läge. Primärt kommer att kunna bearbeta alla skrivtransaktioner i skrivskyddat läge. Utvärdera påverkan på verksamheten och schemalägga fönstret skrivskyddad i en tid om det behövs.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Hämta binära loggfilens namn och offset

   Kör den [ `show master status` ](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) kommando för att fastställa aktuella binära loggfilens namn och offset.
    
   ```sql
   show master status;
   ```
   Resultatet bör vara som nedan. Se till att anteckna namnet på binära filen som kommer att användas i senare steg.

   ![Resultat för Master-Status](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-primary-server"></a>Dump och återställa primär server

1. Dump alla databaser från primär server

   Du kan använda mysqldump till dump databaser från din primära. Mer information finns i [Dump & återställa](concepts-migrate-dump-restore.md). Det är inte nödvändigt att dump MySQL-bibliotek och testa biblioteket.

2. Ange primär server att läsa/skriva-läge

   När databasen har tagits dumpade, ändra primärt MySQL-servern tillbaka till läsa/skriva-läge.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Återställa dumpfilen till ny server

   Återställa dumpfilen till servern som skapats i Azure-databasen för MySQL-tjänst. Referera till [Dump & återställa](concepts-migrate-dump-restore.md) för hur du återställer den här filen till en MySQL-server. Om dumpfilen är stor, kan du överföra den till en virtuell dator i Azure i samma region som replikservern. Återställa den till Azure-databasen för MySQL-servern från den virtuella datorn.

## <a name="link-primary-and-replica-servers-to-start-data-in-replication"></a>Länka den primära servern och repliken servrar för att starta Data i replikering

1. Ange primär server

   Funktioner för alla Data i replikering sköts av lagrade procedurer. Du hittar alla procedurer i [Data i replikerade lagrade procedurer](reference-data-in-stored-procedures.md). Lagrade procedurer kan köras i MySQL-gränssnitt eller MySQL-arbetsstationen. 

   Länka två servrar och starta replikering, logga in på målservern för replikering i Azure DB för MySQL-tjänst och ange den externa instansen som den primära servern. Detta görs med hjälp av den `mysql.az_replication_change_primary` lagrade proceduren på Azure DB för MySQL-servern.

   ```sql
   CALL mysql.az_replication_change_primary('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: värdnamnet för den primära servern
   - master_user: användarnamn för den primära servern
   - master_password: lösenordet för den primära servern
   - master_log_file: binära loggfilens namn från att köras `show master status`
   - master_log_pos: binär logg position körs `show master status`
   - master_ssl_ca: kontexten för CA-certifikat. Om du inte använder SSL skicka i tom sträng.
       - Det rekommenderas att skicka den här parametern i som en variabel. Se följande exempel för mer information.

   **Exempel**

   *Replikering med SSL*

   Variabeln `@cert` har skapats genom att köra följande kommandon för MySQL: 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   Replikering med SSL har ställts in mellan en primär server som finns i domänen ”companya.com” och en replikserver som finns i Azure-databas för MySQL. Den här lagrade proceduren körs på repliken. 

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *Replikering utan SSL*

   Replikering utan SSL har ställts in mellan en primär server som finns i domänen ”companya.com” och en replikserver som finns i Azure-databas för MySQL. Den här lagrade proceduren körs på repliken.

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

2. Starta replikering

   Anropa den `mysql.az_replication_start` lagrade proceduren för att initiera replikering.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Kontrollera replikeringsstatus

   Anropa den [ `show slave status` ](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) på replikservern att visa replikeringsstatus.
    
   ```sql
   show slave status;
   ```

   Om tillståndet för `Slave_IO_Running` och `Slave_SQL_Running` är ”yes” och värdet för `Seconds_Behind_Master` är ”0”, replikeringen fungerar. `Seconds_Behind_Master` Anger hur sen repliken. Om värdet inte är ”0” betyder det att repliken bearbetar uppdateringar. 

## <a name="other-stored-procedures"></a>Andra lagrade procedurer

### <a name="stop-replication"></a>Stoppa replikering

Använd följande lagrade procedur för att stoppa replikering mellan den primära servern och repliken servern:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Ta bort replikeringsrelationen

Ta bort relationen mellan den primära servern och repliken server med följande lagrade procedur:

```sql
CALL mysql.az_replication_remove_primary;
```

### <a name="skip-replication-error"></a>Hoppa över replikeringsfel

Hoppa över ett replikeringsfel och tillåta replikering om du vill fortsätta använda följande lagrade procedur:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Data i replikering](concepts-data-in-replication.md) för Azure-databas för MySQL. 
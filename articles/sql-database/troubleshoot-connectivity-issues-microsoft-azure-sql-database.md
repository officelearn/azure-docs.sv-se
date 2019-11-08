---
title: Felsök anslutnings problem
description: Beskriver hur du felsöker anslutnings problem i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 20988296b5eac7152c53abd6d238043288feacc8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73807275"
---
# <a name="troubleshooting-connectivity-issues-with-microsoft-azure-sql-database"></a>Felsöka anslutnings problem med Microsoft Azure SQL Database

Du får felmeddelanden när anslutningen till Azure SQL Database misslyckas. Dessa anslutnings problem kan orsakas av SQL Azure databas omkonfiguration, brand Väggs inställningar, timeout för anslutning eller felaktig inloggnings information. Om max gränsen på vissa Azure SQL Database resurser nås kan du dessutom inte ansluta till Azure SQL Database.

## <a name="error-40613-database--x--on-server--y--is-not-currently-available"></a>Fel 40613: databas < x > på Server < y > är inte tillgänglig för tillfället

**Detaljerat fel**

``40613: Database <DBname> on server < server name > is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of '< Tracing ID >'.``

Gör så här för att lösa problemet:

1. Kontrol lera [instrument panelen för Microsoft Azures tjänsten](https://status.azure.com/status) för eventuella kända avbrott. 
2. Om det inte finns några kända avbrott går du till [webbplatsen för Microsoft Azure support](https://azure.microsoft.com/support/options) för att öppna ett support ärende.

Mer information finns i [Felsöka fel meddelandet "databasen på servern är inte tillgänglig för tillfället"](sql-database-troubleshoot-common-connection-issues.md#troubleshoot-transient-errors).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server"></a>Ett nätverksrelaterade eller instans fel inträffade när en anslutning upprättades till SQL Server

Problemet uppstår om programmet inte kan ansluta till servern.

Lös problemet genom att prova stegen (i den ordning som anges) i avsnittet [steg för att åtgärda vanliga anslutnings problem](#steps-to-fix-common-connection-issues) .

## <a name="the-server-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Det gick inte att hitta servern eller så var den inte tillgänglig (fel 26, 40, 10053)

### <a name="error-26-error-locating-serverinstance-specified"></a>Fel 26: det gick inte att hitta den angivna servern/instansen

**Detaljerat fel**

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

### <a name="error-40-could-not-open-a-connection-to-sql-server"></a>Fel 40: det gick inte att öppna en anslutning till SQL Server

**Detaljerat fel**

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Fel 10053: det uppstod ett fel på transport nivå när resultat togs emot från servern

**Detaljerat fel**

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Felen beror på att programmet inte kan ansluta till servern.

Lös problemet genom att prova stegen (i den ordning som anges) i avsnittet [steg för att åtgärda vanliga anslutnings problem](#steps-to-fix-common-connection-issues) .

## <a name="cannot-connect-to-servername-due-to-firewall-issues"></a>Det går inte att ansluta till <servername> på grund av brand Väggs problem

### <a name="error-40615-cannot-connect-to--servername-"></a>Fel 40615: det går inte att ansluta till < servername >

Lös problemet genom att [Konfigurera brand Väggs inställningar på SQL Database via Azure Portal.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Fel 5: det går inte att ansluta till < servername >

Lös problemet genom att kontrol lera att port 1433 är öppen för utgående anslutningar på alla brand väggar mellan klienten och Internet.

Mer information finns i [Konfigurera Windows-brandväggen för att tillåta SQL Server åtkomst](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Det går inte att logga in på servern (fel 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Inloggningen misslyckades för användaren < användar namn >

**Detaljerat fel**

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Kontakta tjänst administratören för att få ett giltigt SQL Server användar namn och lösen ord för att lösa problemet.

Tjänst administratören kan normalt använda följande steg för att lägga till inloggnings uppgifterna:

1. Logga in på servern med SQL Server Management Studio (SSMS).
2. Kör följande SQL-fråga för att kontrol lera om inloggnings namnet är inaktiverat:

   ```
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Om motsvarande namn har inaktiverats kan du aktivera det med hjälp av följande beskrivning: 

   ```
   Alter login <User name> enable
   ```

4. Om SQL-inloggningens användar namn inte finns skapar du det genom att följa dessa steg:

   1. I SSMS dubbelklickar du på **säkerhet** för att expandera den. 
   2. Högerklicka på **Inloggningar** och välj sedan **Ny inloggning**. 
   3. I det genererade skriptet med plats hållare redigerar du och kör följande SQL-fr åga:
 
   ```
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```       
5. Dubbelklicka på **Databas**. 
6. Välj den databas som du vill ge användaren behörighet till.
7. Dubbelklicka på **Säkerhet**. 
8. Högerklicka på **Användare** och välj **Ny användare**. 
9. I det genererade skriptet med plats hållare redigerar du och kör följande SQL-fr åga: 

   ```
   CREATE USER <user_name, sysname, user_name>          
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```
   
   > [!NOTE]
   > Du kan också använda `sp_addrolemember` för att mappa vissa användare till vissa databas roller.

Mer information finns i [Hantera databaser och inloggningar i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Fel vid tids gräns för anslutning

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): anslutningens tids gräns har överskridits

**Detaljerat fel**

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): tids gränsen har gått ut

**Detaljerat fel**

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. data. Entity. Core. EntityException: den underliggande providern misslyckades vid öppning

**Detaljerat fel**

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Det går inte att ansluta till < Server namn >

**Detaljerat fel**

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Dessa undantag kan inträffa antingen på grund av problem med anslutningen eller frågan. För att bekräfta att det här felet orsakas av anslutnings problem, se [bekräfta om ett fel orsakas av ett anslutnings problem](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Timeout för anslutningar inträffar eftersom programmet inte kan ansluta till servern. Lös problemet genom att prova stegen (i den ordning som anges) i avsnittet [steg för att åtgärda vanliga anslutnings problem](#steps-to-fix-common-connection-issues) .

## <a name="transient-errors-errors-40197-40545"></a>Tillfälliga fel (fel 40197, 40545)

### <a name="error-40197-the-service-has-encountered-an-error-processing-your-request-please-try-again-error-code--code-"></a>Fel 40197: tjänsten har påträffat ett fel när din begäran bearbetades. Försök igen. Felkod < kod >

Det här problemet uppstår på grund av ett tillfälligt fel som uppstått under en omkonfiguration eller redundansväxling på Server delen.

Lös problemet genom att vänta en kort period och försök igen. Inget support ärende krävs om problemet kvarstår.

Vi rekommenderar att du kontrollerar att logiken för omprövning är på plats. Mer information om logiken för omprövning finns i [Felsöka tillfälliga fel och anslutnings fel till SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

## <a name="connection-terminated-because-of-a-system-defined-limit"></a>Anslutningen avbröts på grund av en systemdefinierad gräns

### <a name="error-10928-resource-id-d"></a>Fel 10928: resurs-ID:% d

**Detaljerat fel**

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Undvik det här problemet genom att prova någon av följande metoder:

* Kontrol lera om det finns tids krävande frågor.

  > [!NOTE]
  > Det här är en minimalist metod som kanske inte löser problemet.

  1. Kör följande SQL-fråga för att kontrol lera vyn [sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) för att se eventuella spärrnings begär Anden:

             ```
             SELECT * FROM dm_exec_requests
             ```

  2. Ta reda på **indatabufferten** för huvud blocket.
  3. Finjustera frågan om huvud Blocker.

    En djupgående fel söknings procedur finns i finns [min fråga som körs i molnet?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

* Om databasen ständigt når gränsen trots att blockera och långvariga frågor, bör du överväga att uppgradera till en av de nya för hands versionerna (till exempel [standard-eller Premium versionen](https://azure.microsoft.com/pricing/details/sql-database/)).

Mer information om SQL Database pris alternativ finns [Azure SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/single/).

Mer information om vyer för dynamisk hantering finns i [vyer för system dynamisk hantering](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Mer information om det här fel meddelandet finns i [SQL Database resurs gränser för Azure SQL Database Server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Fel 10929: resurs-ID: 1

**Detaljerat fel**

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

Mer information om det här felet finns i [fel meddelanden för SQL Database-klient program](https://docs.microsoft.com/azure/sql-database/sql-database-develop-error-messages).

### <a name="error-40501-the-service-is-currently-busy"></a>Fel 40501: tjänsten är upptagen för tillfället

**Detaljerat fel**

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Det här är ett fel i motorns begränsning, en indikation på att resurs gränserna överskrids.

Mer information om resurs begränsningar finns i [gränser för databas server resurser](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Fel 40544: databasen har nått sin storleks kvot

**Detaljerat fel**

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Det här felet uppstår när databasen har nått sin storleks kvot.

Följande steg kan antingen hjälpa dig att komma runt problemet eller ge dig ytterligare alternativ:

1. Kontrol lera databasens aktuella storlek med hjälp av instrument panelen i Azure Portal.

   > [!NOTE]
   > Kör följande SQL-fråga för att identifiera vilka tabeller som utnyttjar det mesta av utrymmet och är därför potentiella kandidater för rensning:

   ```
   SELECT o.name,
    a.SUM(p.row_count) AS 'Row Count',
    b.SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Om den aktuella storleken inte överskrider den maximala storlek som stöds för din utgåva kan du använda ALTER DATABASE för att öka inställningen MAXSIZE. 
3. Om databasen redan har passerat den maximala storleken som stöds för din utgåva kan du prova ett eller flera av följande steg:
   - Utför normala databas rensnings aktiviteter. Du kan till exempel rensa oönskade data genom att använda trunkera/ta bort eller flytta data med hjälp av SQL Server Integration Services (SSIS) eller verktyget för Mass kopierings program (BCP).
   - Partitionera eller ta bort data, släpp index eller Läs om möjliga lösningar i dokumentationen.

   - För databas skalning, se [skala resurser för enkel databas](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) och [skala elastiska pooler](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Fel 40549: sessionen avslutas eftersom du har en tids krävande transaktion

**Detaljerat fel**

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Om du upprepade gånger stöter på det här felet kan du försöka lösa problemet genom att följa dessa steg: 

1. Kontrol lera vyn sys. dm_exec_requests för att se alla öppna sessioner som har ett högt värde för kolumnen total_elapsed_time. Utför den här kontrollen genom att köra följande SQL-skript:

   ```
   SELECT * FROM dm_exec_requests
   ```
2. Fastställ indatabufferten för den tids krävande frågan. 
3. Finjustera frågan.

Överväg också att gruppera dina frågor. Information om batching finns i [så här använder du batching för att förbättra SQL Database program prestanda](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

En djupgående fel söknings procedur finns i finns [min fråga som körs i molnet?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Fel 40551: sessionen har avslut ATS på grund av överdriven TEMPDB-användning

**Detaljerat fel**

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Undvik det här problemet genom att följa dessa steg:

1. Ändra frågorna för att minska användningen av temporärt tabell utrymme. 
2. Släpp tillfälliga objekt när de inte längre behövs. 
3. Trunkera tabeller eller ta bort oanvända tabeller.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Fel 40552: sessionen har avslut ATS på grund av överdriven användning av transaktions logg utrymme

**Detaljerat fel**

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Prova att lösa problemet med hjälp av följande metoder:

* Problemet kan uppstå på grund av åtgärder för att infoga, uppdatera eller ta bort. Försök att minska antalet rader som körs omedelbart genom att implementera batchbearbetning eller dela upp i flera mindre transaktioner.
* Problemet kan uppstå på grund av åtgärder för att återskapa index. Undvik det här problemet genom att se till att antalet rader som påverkas i tabellen * (genomsnittlig storlek på fält som uppdateras i byte + 80) < 2 gigabyte (GB).

  > [!NOTE]
  > För att återskapa indexet ska den genomsnittliga storleken för det fält som uppdateras ersättas av den genomsnittliga index storleken.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Fel 40553: sessionen har avslut ATS på grund av överdriven minnes användning

**Detaljerat fel**

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Undvik det här problemet genom att försöka optimera frågan.

En djupgående fel söknings procedur finns i finns [min fråga som körs i molnet?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).


### <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Det går inte att öppna databasen "Master" som begärdes av inloggningen. Inloggningen misslyckades.

Det här problemet uppstår eftersom kontot inte har behörighet att komma åt Master-databasen. Men SQL Server Management Studio (SSMS) försöker som standard att ansluta till huvud databasen.

Följ dessa anvisningar för att lösa problemet:

1. Välj **alternativ**på inloggnings skärmen på SSMS och välj sedan **anslutnings egenskaper**. 
2. I fältet **Anslut till databas** anger du användarens standard databas namn som standard inloggnings databas och väljer sedan **Anslut**.

   ![Anslutningsegenskaper](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Bekräfta om ett fel orsakas av ett anslutnings problem

Du kan kontrol lera om ett fel orsakas av ett anslutnings problem genom att granska stack spårningen för ramar som visar anrop för att öppna en anslutning som följande (Observera referensen till **SQLConnection** -klassen):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

När undantaget utlöses av frågor om frågor kommer du att märka en anrops stack som liknar följande (Observera referensen till klassen **SqlCommand** ). I den här situationen kan [du finjustera dina frågor](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```
Ytterligare anvisningar om fin justering av prestanda finns i följande resurser:

* [Så här hanterar du Azure SQL-index och-statistik](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Justera prestanda för manuell prestanda i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Övervaka prestanda Azure SQL Database med hjälp av vyer för dynamisk hantering](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Kör Frågearkivet i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)


## <a name="steps-to-fix-common-connection-issues"></a>Steg för att åtgärda vanliga anslutnings problem

1. Kontrol lera att TCP/IP är aktiverat som ett klient protokoll på program servern. Mer information finns i [Konfigurera klient protokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). På program servrar där du inte har SQL Server verktyg installerade kontrollerar du att TCP/IP är aktiverat genom att köra **cliconfg. exe** (SQL Server klient nätverks verktyg). 
2. Kontrol lera programmets anslutnings sträng för att kontrol lera att den är korrekt konfigurerad. Kontrol lera till exempel att anslutnings strängen anger rätt port (1433) och ett fullständigt kvalificerat Server namn.
Se [Hämta information om SQL Server-anslutning](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Försök att öka timeout-värdet för anslutning. Vi rekommenderar att du använder en tids gräns för anslutning på minst 30 sekunder. 
4. Testa anslutningen mellan program servern och Azure SQL-databasen med hjälp av [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms), en UDL-fil, ping eller Telnet. Mer information finns i [fel sökning av problem med SQL Server anslutning](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) och [diagnostik för anslutnings problem](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Som ett fel söknings steg kan du också testa anslutningen på en annan klient dator.

5. Vi rekommenderar att du kontrollerar att logiken för omprövning är på plats. Mer information om logik för omprövning finns i [Felsöka tillfälliga fel och anslutnings fel till SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Om de här stegen inte löser problemet kan du försöka samla in mer data och sedan kontakta supporten. Aktivera loggning om ditt program är en moln tjänst. Det här steget returnerar en tidsstämpel för UTC-tid för felet. Dessutom returnerar SQL Azure spårnings-ID. [Microsofts kund support tjänster](https://azure.microsoft.com/support/options/) kan använda den här informationen. 

Mer information om hur du aktiverar loggning finns i [Aktivera diagnostikloggning för appar i Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

**Relaterade dokument**

* [Arkitektur för Azure SQL-anslutning](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)<br>
* [Kontroll av nätverks åtkomst för Azure SQL Database och informations lager](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)

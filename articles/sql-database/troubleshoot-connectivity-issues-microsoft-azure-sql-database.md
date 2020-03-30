---
title: Felsöka vanliga anslutningsproblem till SQL-databas
description: Innehåller steg för felsökning av Azure SQL Database-anslutningsproblem och lösa andra SQL Database-specifika problem
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019
author: ramakoni1
ms.author: ramakoni
ms.reviewer: carlrab,vanto
ms.date: 01/14/2020
ms.openlocfilehash: 578d076dfc2fc383051c0c3a8528cdbb0fbcdb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208785"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-microsoft-azure-sql-database"></a>Felsökning av anslutningsproblem och andra fel med Microsoft Azure SQL Database

Du får felmeddelanden när anslutningen till Azure SQL Database misslyckas. Dessa anslutningsproblem kan orsakas av omkonfigurering av Azure SQL Database, brandväggsinställningar, en tidsgränsen för anslutning, felaktig inloggningsinformation eller underlåtenhet att tillämpa metodtips och designriktlinjer under [programdesignprocessen.](sql-database-develop-overview.md) Om den maximala gränsen för vissa Azure SQL Database-resurser har uppnåtts kan du inte ansluta till Azure SQL Database.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Tillfälliga felmeddelanden (40197, 40613 och andra)

Azure-infrastrukturen har kapacitet att dynamiskt omkonfigurera servrar vid ökad arbetsbelastning i SQL Database-tjänsten.  Den här dynamiska funktionen kan göra ditt klientprogram tappar kontakten med SQL Database. Den här typen av feltillstånd kallas ett *tillfälligt fel*. Databasomkonfigurationshändelser inträffar på grund av en planerad händelse (till exempel en uppgradering av programvara) eller en oplanerad händelse (till exempel en processkrasch eller belastningsutjämning). De flesta omkonfigurationshändelser är i allmänhet kortlivade och bör slutföras på högst 60 sekunder. Dessa händelser kan dock ibland ta längre tid att slutföra, till exempel när en stor transaktion orsakar en tidskrävande återställning. I följande tabell visas olika tillfälliga fel som program kan ta emot när de ansluter till SQL Database

### <a name="list-of-transient-fault-error-codes"></a>Lista över tillfälliga felfelkoder


| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 4060 |16 |Det går inte att öppna databasen %.&#x2a;ls som begärs av inloggningen. Inloggningen misslyckades. Mer information finns i [Fel 4000 till 4999](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Tjänsten har påträffat ett fel som bearbetar din begäran. Försök igen. Felkod %d.<br/><br/>Det här felet visas när tjänsten är nere på grund av uppgraderingar av programvara eller maskinvara, maskinvarufel eller andra redundansproblem. Felkoden (%d) som är inbäddad i meddelandet om fel 40197] innehåller ytterligare information om vilken typ av fel eller redundans som inträffade. Några exempel på felkoderna bäddas in i meddelandet om fel 40197 är 40020, 40143, 40166 och 40540.<br/><br/>Om du ansluter till SQL Database-servern ansluts du automatiskt till en felfri kopia av databasen. Programmet måste fånga fel 40197, logga den inbäddade felkoden (%d) i meddelandet för felsökning och försök återansluta till SQL Database tills resurserna är tillgängliga och anslutningen upprättas igen. Mer information finns i [Tillfälliga fel](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Tjänsten är upptagen för närvarande. Försök igen efter 10 sekunder. Incident-ID: %ls. Kod: %d. Mer information finns i: <br/>&bull;&nbsp; [Resursbegränsningar för databasserver](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Begränsningar för hanterade instansresurser](sql-database-managed-instance-resource-limits.md).|
| 40613 |17 |Databasen %.&#x2a;ls' på servern %.&#x2a;ls är inte tillgänglig för närvarande. Försök igen anslutningen senare. Om problemet kvarstår kontaktar du kundsupporten och ger dem sessionsspårnings-ID:et %.&#x2a;ls..<br/><br/> Det här felet kan uppstå om det redan finns en befintlig dedikerad administratörsanslutning (DAC) som upprättats till databasen. Mer information finns i [Tillfälliga fel](sql-database-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Det går inte att behandla begäran. Det finns inte tillräckligt med resurser för att bearbeta begäran.<br/><br/>Tjänsten är upptagen för närvarande. Försök igen begäran senare. Mer information finns i: <br/>&bull;&nbsp; [Resursbegränsningar för databasserver](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Begränsningar för hanterade instansresurser](sql-database-managed-instance-resource-limits.md). |
| 49919 |16 |Det går inte att bearbeta en begäran om att skapa eller uppdatera. För många skapa eller uppdatera åtgärder som pågår för prenumerationen %ld.<br/><br/>Tjänsten är upptagen med att bearbeta flera skapa eller uppdatera förfrågningar för din prenumeration eller server. Begäranden är för närvarande blockerade för resursoptimering. Fråga [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) för väntande åtgärder. Vänta tills väntande skapa eller uppdatera begäranden är klar eller ta bort en av dina väntande begäranden och försök igen din begäran senare. Mer information finns i: <br/>&bull;&nbsp; [Resursbegränsningar för databasserver](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Begränsningar för hanterade instansresurser](sql-database-managed-instance-resource-limits.md). |
| 49920 |16 |Det går inte att behandla begäran. För många åtgärder pågår för prenumerationen %ld.<br/><br/>Tjänsten är upptagen med att bearbeta flera begäranden för den här prenumerationen. Begäranden är för närvarande blockerade för resursoptimering. Fråga [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx) för åtgärdsstatus. Vänta tills väntande begäranden har slutförts eller ta bort en av dina väntande begäranden och försök igen med din begäran senare. Mer information finns i: <br/>&bull;&nbsp; [Resursbegränsningar för databasserver](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Begränsningar för hanterade instansresurser](sql-database-managed-instance-resource-limits.md). |
| 4221 |16 |Inloggning till läs-sekundär misslyckades på grund av lång väntan på "HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING". Repliken är inte tillgänglig för inloggning eftersom radversioner saknas för transaktioner som var under flygning när repliken återvanns. Problemet kan lösas genom att återställa eller genomföra de aktiva transaktionerna på den primära repliken. Förekomster av det här villkoret kan minimeras genom att undvika långa skrivtransaktioner på den primära. |


### <a name="steps-to-resolve-transient-connectivity-issues"></a>Åtgärder för att lösa tillfälliga anslutningsproblem

1. Kontrollera [Microsoft Azure Service Dashboard](https://azure.microsoft.com/status) för alla kända avbrott som inträffade under den tid då felen rapporterades av programmet.
2. Program som ansluter till en molntjänst som Azure SQL Database bör förvänta sig periodiska omkonfigurationshändelser och implementera logik för återförsök för att hantera dessa fel i stället för att visa dessa som programfel för användare. 
3. När en databas närmar sig sina resursgränser kan det verka vara ett tillfälligt anslutningsproblem. Se [Resursgränser](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Om anslutningsproblemen fortsätter, eller om den varaktighet som ditt program stöter på felet överskrider 60 sekunder eller om du ser flera förekomster av felet under en viss dag, lämnar du in en Azure-supportbegäran genom att välja **Hämta support** på [Azure-supportwebbplatsen.](https://azure.microsoft.com/support/options)

#### <a name="implementing-retry-logic"></a>Implementera logik för återförsök
Vi rekommenderar starkt att klientprogrammet har återförsökslogik så att den kan återupprätta en anslutning efter att ha gett den tillfälliga feltiden för att korrigera sig själv.  Vi rekommenderar att du dröjer 5 sekunder innan du försöker igen. Försöker du igen efter en fördröjning som är kortare än 5 sekunder riskerar att överväldiga molntjänsten. För varje efterföljande återförsök bör fördröjningen växa exponentiellt, upp till högst 60 sekunder.

Kodexempel på logik för återförsök finns i:
- [Ansluta elastiskt till SQL med ADO.NET](https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Ansluta elastiskt till SQL med PHP](https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

Mer information om hur du hanterar tillfälliga fel i programgranskningen
* [Felsöka tillfälliga anslutningsfel till SQL Database](sql-database-connectivity-issues.md)

En diskussion om *blockeringsperioden* för klienter som använder ADO.NET är tillgänglig i [SQL Server Connection Pooling (ADO.NET).](https://msdn.microsoft.com/library/8xx3tyca.aspx)

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-database-server"></a>Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning till SQL Database-servern upprättas

Problemet uppstår om programmet inte kan ansluta till servern.

LÃ¶s problemet genom att hÃ¤nde kÃ¶ %, hÃ¤nde steg (i den ordning som visas) i [avsnittet Steg fÃ¶r att åtgärda vanliga anslutningsproblem.](#steps-to-fix-common-connection-issues)

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Servern/instansen hittades inte eller var inte tillgänglig (fel 26, 40, 10053)

#### <a name="error-26-error-locating-server-specified"></a>Fel 26: Fel vid serverning

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Fel 40: Det gick inte att öppna en anslutning till servern

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Fel 10053: Ett transportnivåfel uppstod när resultat från servern tas emot

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

Dessa problem uppstår om programmet inte kan ansluta till servern.

LÃ¶s problemen genom att hÃ¤nde kÃ¶ %, hÃ¤nde kÃ¶r stegen (i den ordning som visas) i [avsnittet Steg fÃ¶r att åtgärda vanliga anslutningsproblem.](#steps-to-fix-common-connection-issues)

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Det går inte att ansluta till servern på grund av brandväggsproblem

### <a name="error-40615-cannot-connect-to--servername-"></a>Fel 40615: Det går inte att ansluta till < servernamn >

LÃ¶s problemet genom [att konfigurera brandväggsinställningar fÃ¶r SQL Database via Azure-portalen.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

### <a name="error-5-cannot-connect-to--servername-"></a>Fel 5: Det går inte att ansluta till < servernamn >

LÃ¶s problemet genom att kontrollera att port 1433 är öppen fÃ¶r utgående anslutningar på alla brandväggar mellan klienten och internet.

Mer information finns [i Konfigurera Windows-brandväggen så att SQL Server får åtkomst](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure).

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Det gick inte att logga in på servern (fel 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Inloggningen misslyckades för användarens < användarnamn >'

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

LÃ¶s problemet genom att kontakta tjänstadministratören fÃ¶r att ange ett giltigt SQL Server-användarnamn och lösenord.

Vanligtvis kan tjänstadministratören använda följande steg för att lägga till inloggningsuppgifter:

1. Logga in på servern med hjälp av SQL Server Management Studio (SSMS).
2. Kör följande SQL-fråga för att kontrollera om inloggningsnamnet är inaktiverat:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Om motsvarande namn har inaktiverats kan du aktivera det med hjälp av följande beskrivning:

   ```sql
   Alter login <User name> enable
   ```

4. Om användarnamnet för SQL-inloggning inte finns skapar du det med följande steg:

   1. Dubbelklicka på **Säkerhet** i SSMS för att expandera den.
   2. Högerklicka på **Inloggningar** och välj sedan **Ny inloggning**.
   3. Redigera och kör följande SQL-fråga i det genererade skriptet med platshållare:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = ‘<password, sysname, Change_Password>’
   GO
   ```

5. Dubbelklicka på **Databas**.
6. Markera den databas som du vill bevilja användaren behörighet till.
7. Dubbelklicka på **Säkerhet**.
8. Högerklicka på **Användare** och välj **Ny användare**.
9. Redigera och kör följande SQL-fråga i det genererade skriptet med platshållare:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N’db_owner’, N’<user_name, sysname, user_name>’
   GO
   ```

   > [!NOTE]
   > Du kan `sp_addrolemember` också använda för att mappa specifika användare till specifika databasroller.

Mer information finns i [Hantera databaser och inloggningar i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="connection-timeout-expired-errors"></a>Tidsgränsen för anslutning har gått ut

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Tidsgränsen för anslutning har upphört att gälla

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System.Data.SqlClient.SqlException (0x80131904): Timeout har upphört att gälla

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System.Data.Entity.Core.EntityException: Den underliggande providern misslyckades på Open

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Det går inte att ansluta till < servernamn >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Dessa undantag kan uppstå antingen på grund av anslutnings- eller frågeproblem. Kontrollera [om ett fel orsakas av anslutningsproblem.](#confirm-whether-an-error-is-caused-by-a-connectivity-issue)

Tidsutsebara anslutningar för anslutning uppstår eftersom programmet inte kan ansluta till servern. LÃ¶s problemet genom att hÃ¤nde kÃ¶ %, hÃ¤nde steg (i den ordning som visas) i [avsnittet Steg fÃ¶r att åtgärda vanliga anslutningsproblem.](#steps-to-fix-common-connection-issues)

## <a name="resource-governance-errors"></a>Fel i resursstyrning

### <a name="error-10928-resource-id-d"></a>Fel 10928: Resurs-ID: %d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Försök med något av följande metoder om du vill lösa problemet:

* Kontrollera om det finns tidskrävande frågor.

  > [!NOTE]
  > Detta är en minimalistisk metod som kanske inte löser problemet.

1. Kör följande SQL-fråga för att kontrollera [vyn sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) för att se eventuella blockerande begäranden:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Bestäm **indatabufferten** för huvudblockeraren.
3. Ställ in frågan om huvudblockerare.

   En djupgående felsökningsprocedur finns i [Fungerar min fråga bra i molnet?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

Om databasen konsekvent når sin gräns trots att den adresserar blockerings- och tidskrävande frågor kan du överväga att uppgradera till en utgåva med fler resurser [.](https://azure.microsoft.com/pricing/details/sql-database/)

Mer information om dynamiska hanteringsvyer finns i [System dynamiska hanteringsvyer](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Mer information om databasbegränsningar finns i [SQL Database-resursgränser för Azure SQL Database server](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-10929-resource-id-1"></a>Fel 10929: Resurs-ID: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Fel 40501: Tjänsten är upptagen

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Detta är ett motorstrypningsfel, en indikation på att resursgränser överskrids.

Mer information om resursbegränsningar finns i [Begränsningar för databasserverresurser](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Fel 40544: Databasen har nått sin storlekskvot

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Det här felet uppstår när databasen har nått sin storlekskvot.

Följande steg kan antingen hjälpa dig att komma runt problemet eller ge dig ytterligare alternativ:

1. Kontrollera databasens aktuella storlek med hjälp av instrumentpanelen i Azure-portalen.

   > [!NOTE]
   > Om du vill identifiera vilka tabeller som förbrukar mest utrymme och därför är potentiella kandidater för rensning kör du följande SQL-fråga:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC
   ```

2. Om den aktuella storleken inte överskrider den maximala storleken som stöds för din utgåva kan du använda ALTER DATABASE för att öka maxsize-inställningen.
3. Om databasen redan har passerat den maximala storleken som stöds för din utgåva kan du prova ett eller flera av följande steg:

   * Utför normala rensningsaktiviteter för databasen. Rensa till exempel oönskade data med hjälp av trunkera/ta bort eller flytta data ut med hjälp av SQL Server Integration Services (SSIS) eller masskopieringsprogrammet (bcp) verktyget.
   * Partitionera eller ta bort data, släpp index eller läs dokumentationen för möjliga lösningar.
   * För databasskalning finns i [Skala enskilda databasresurser](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-scale) och [Skala elastiska poolresurser](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool-scale).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Fel 40549: Sessionen avslutas eftersom du har en tidskrävande transaktion

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Om du upprepade gånger stöter på det här felet kan du försöka lösa problemet genom att följa följande steg:

1. Kontrollera vyn sys.dm_exec_requests om du vill visa alla öppna sessioner som har ett högt värde för kolumnen total_elapsed_time. Utför den här kontrollen genom att köra följande SQL-skript:

   ```sql
   SELECT * FROM dm_exec_requests
   ```

2. Bestäm indatabufferten för den tidskrävande frågan.
3. Ställ in frågan.

Också överväga batching dina frågor. Information om batchbearbetning finns i [Så här använder du batchbearbetning för att förbättra SQL Database-programmets prestanda](https://docs.microsoft.com/azure/sql-database/sql-database-use-batching-to-improve-performance).

En djupgående felsökningsprocedur finns i [Fungerar min fråga bra i molnet?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Fel 40551: Sessionen har avslutats på grund av överdriven TEMPDB-användning

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Så här kan du lösa problemet:

1. Ändra frågorna för att minska tillfällig användning av tabellutrymme.
2. Släpp tillfälliga objekt när de inte längre behövs.
3. Trunkera tabeller eller ta bort oanvända tabeller.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Fel 40552: Sessionen har avslutats på grund av överdriven användning av transaktionsloggutrymme

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Prova att lösa problemet med hjälp av följande metoder:

* Problemet kan uppstå på grund av infoga, uppdatera eller ta bort åtgärder.
Försök att minska antalet rader som drivs omedelbart genom att implementera batchbearbetning eller delning i flera mindre transaktioner.
* Problemet kan uppstå på grund av index återskapa åtgärder. LÃ¤t problemet kontrollerar du att antalet rader som påverkas i tabellen * (genomsnittlig storlek på fältet som uppdateras i byte + 80) < 2 GB.

  > [!NOTE]
  > För en ombyggnad av index bör den genomsnittliga storleken på det fält som uppdateras ersättas med den genomsnittliga indexstorleken.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Fel 40553: Sessionen har avslutats på grund av överdriven minnesanvändning

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Försök att optimera frågan om du vill lösa problemet.

En djupgående felsökningsprocedur finns i [Fungerar min fråga bra i molnet?](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabell över ytterligare felmeddelanden om resursstyrning

| Felkod | Severity | Beskrivning |
| ---:| ---:|:--- |
| 10928 |20 |Resurs-ID: %d. %s-gränsen för databasen är %d och har uppnåtts. Mer information finns i [RESURSGRÄNSER FÖR SQL Database för enskilda och poolade databaser](sql-database-resource-limits-database-server.md).<br/><br/>Resurs-ID anger den resurs som har nått gränsen. För arbetstrådar är resurs-ID = 1. För sessioner, resurs-ID = 2.<br/><br/>Mer information om det här felet och hur du löser det finns i: <br/>&bull;&nbsp; [Resursbegränsningar för databasserver](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Begränsningar för hanterade instansresurser](sql-database-managed-instance-resource-limits.md). |
| 10929 |20 |Resurs-ID: %d. Minimigarantin %s är %d, den maximala gränsen %d och den aktuella användningen för databasen är %d. Servern är dock för närvarande för upptagen för att stödja begäranden som är större än %d för den här databasen. Resurs-ID anger den resurs som har nått gränsen. För arbetstrådar är resurs-ID = 1. För sessioner, resurs-ID = 2. Mer information finns i: <br/>&bull;&nbsp; [Resursbegränsningar för databasserver](sql-database-resource-limits-database-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](sql-database-service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Begränsningar för hanterade instansresurser](sql-database-managed-instance-resource-limits.md). <br/>Annars kan du försöka igen senare. |
| 40544 |20 |Databasen har nått sin storlekskvot. Partitionera eller ta bort data, släpp index eller läs dokumentationen för möjliga lösningar. För databasskalning finns i [Skala enskilda databasresurser](sql-database-single-database-scale.md) och [Skala elastiska poolresurser](sql-database-elastic-pool-scale.md).|
| 40549 |16 |Sessionen avslutas eftersom du har en tidskrävande transaktion. Försök att förkorta transaktionen. Information om batchbearbetning finns i [Så här använder du batchbearbetning för att förbättra SQL Database-programmets prestanda](sql-database-use-batching-to-improve-performance.md).|
| 40550 |16 |Sessionen har avslutats eftersom den har skaffat för många lås. Prova att läsa eller ändra färre rader i en enda transaktion. Information om batchbearbetning finns i [Så här använder du batchbearbetning för att förbättra SQL Database-programmets prestanda](sql-database-use-batching-to-improve-performance.md).|
| 40551 |16 |Sessionen har avslutats på `TEMPDB` grund av överdriven användning. Prova att ändra frågan för att minska den tillfälliga användningen av tabellutrymme.<br/><br/>Om du använder tillfälliga objekt sparar du `TEMPDB` utrymme i databasen genom att släppa tillfälliga objekt när de inte längre behövs av sessionen. Mer information om tempdb-användning i SQL Database finns [i Tempdb-databasen i SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Sessionen har avslutats på grund av överdriven användning av transaktionsloggutrymme. Försök att ändra färre rader i en enda transaktion. Information om batchbearbetning finns i [Så här använder du batchbearbetning för att förbättra SQL Database-programmets prestanda](sql-database-use-batching-to-improve-performance.md).<br/><br/>Om du utför `bcp.exe` massinfogningar `System.Data.SqlClient.SqlBulkCopy` med hjälp av `-b batchsize` `BatchSize` verktyget eller klassen kan du prova att använda alternativen eller för att begränsa antalet rader som kopieras till servern i varje transaktion. Om du återskapar ett `ALTER INDEX` index med uttrycket kan du prova att använda `REBUILD WITH ONLINE = ON` alternativet. Information om transaktionsloggstorlekar för inköpsmodellen för vCore finns i: <br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](sql-database-vcore-resource-limits-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [Begränsningar för hanterade instansresurser](sql-database-managed-instance-resource-limits.md).|
| 40553 |16 |Sessionen har avslutats på grund av överdriven minnesanvändning. Prova att ändra frågan för att bearbeta färre rader.<br/><br/>Om du `ORDER BY` minskar `GROUP BY` antalet och åtgärder i transact-SQL-koden minskar frågans minneskrav. För databasskalning finns i [Skala enskilda databasresurser](sql-database-single-database-scale.md) och [Skala elastiska poolresurser](sql-database-elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Elastiska poolfel

Följande fel är relaterade till att skapa och använda elastiska pooler:

| Felkod | Severity | Beskrivning | Korrigeringsåtgärder |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Den elastiska poolen har nått sin lagringsgräns. Lagringsanvändningen för den elastiska poolen får inte överstiga (%d) MBs. Försöker skriva data till en databas när lagringsgränsen för den elastiska poolen har nåtts. Information om resursbegränsningar finns i: <br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md). <br/> |Överväg att öka DU:erna för och/eller lägga till lagring i den elastiska poolen om möjligt för att öka lagringsgränsen, minska lagringen som används av enskilda databaser i den elastiska poolen eller ta bort databaser från den elastiska poolen. För elastisk poolskalning finns i [Skala elastiska poolresurser](sql-database-elastic-pool-scale.md).|
| 10929 | 16 |Minimigarantin %s är %d, den maximala gränsen %d och den aktuella användningen för databasen är %d. Servern är dock för närvarande för upptagen för att stödja begäranden som är större än %d för den här databasen. Information om resursbegränsningar finns i: <br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](sql-database-dtu-resource-limits-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md). <br/> Annars kan du försöka igen senare. DTU / vCore min per databas; DTU / vCore max per databas. Det totala antalet samtidiga arbetare (begäranden) i alla databaser i den elastiska poolen försökte överskrida poolgränsen. |Överväg att öka DU:erna eller virtuella kärnor i den elastiska poolen om möjligt för att öka dess arbetsgräns eller ta bort databaser från den elastiska poolen. |
| 40844 | 16 |Databasen %ls på servern %ls är en versionsdatabas %ls i en elastisk pool och kan inte ha en kontinuerlig kopieringsrelation.  |Ej tillämpligt |
| 40857 | 16 |Elastisk pool hittades inte för servern: %ls, elastiskt poolnamn: %ls. Den angivna elastiska poolen finns inte i den angivna servern. | Ange ett giltigt elastiskt poolnamn. |
| 40858 | 16 |Elastisk pool %ls finns redan i servern: %ls. Den angivna elastiska poolen finns redan i den angivna SQL Database-servern. | Ange nytt elastiskt poolnamn. |
| 40859 | 16 |Elastisk pool stöder inte servicenivån %ls. Den angivna tjänstnivån stöds inte för etablering av elastisk pool. |Ange rätt utgåva eller lämna tjänstnivån tom om du vill använda standardtjänstnivån. |
| 40860 | 16 |Kombinationen %ls och servicemålet %ls är ogiltiga. Elastisk pool och servicenivå kan bara anges tillsammans om resurstypen anges som "ElasticPool". |Ange rätt kombination av elastisk pool och servicenivå. |
| 40861 | 16 |Databasutgåvan %. *ls kan inte vara annorlunda än den elastiska pooltjänstnivån som är %.* Ls'. Databasutgåvan skiljer sig från den elastiska pooltjänstnivån. |Ange inte en databasutgåva som skiljer sig från den elastiska pooltjänstnivån.  Observera att databasutgåvan inte behöver anges. |
| 40862 | 16 |Elastiskt poolnamn måste anges om målsättningen för elastisk poolservice anges. Elastisk pool service mål inte unikt identifiera en elastisk pool. |Ange namnet på den elastiska poolen om du använder servicemålet för elastisk pool. |
| 40864 | 16 |DU:erna för den elastiska poolen måste vara minst (%d) DTUs för tjänstnivån %.*ls. Försöker ställa in DU:erna för den elastiska poolen under minimigränsen. |Försök att ställa in DU:erna för den elastiska poolen på minst minimigränsen. |
| 40865 | 16 |DU:erna för den elastiska poolen får inte överstiga (%d) DU:er för tjänstnivån %.*ls. Försöker ställa in DTU:erna för den elastiska poolen över maxgränsen. |Försök att ställa in DTU:erna för den elastiska poolen till högst den maximala gränsen. |
| 40867 | 16 |DTU max per databas måste vara minst (%d) för tjänstnivån %.*ls. Försöker ställa in DTU max per databas under den gräns som stöds. | Överväg att använda den elastiska pooltjänstnivån som stöder önskad inställning. |
| 40868 | 16 |DTU-max per databas får inte överstiga (%d) för tjänstnivån %.*ls. Försöker ange DTU max per databas utöver den gräns som stöds. | Överväg att använda den elastiska pooltjänstnivån som stöder önskad inställning. |
| 40870 | 16 |DTU-min per databas får inte överstiga (%d) för tjänstnivån %.*ls. Försöker ställa in DTU min per databas utöver den gräns som stöds. | Överväg att använda den elastiska pooltjänstnivån som stöder önskad inställning. |
| 40873 | 16 |Antalet databaser (%d) och DTU min per databas (%d) får inte överstiga DTU:erna för den elastiska poolen (%d). Försöker ange DTU min för databaser i den elastiska poolen som överskrider DTU:erna för den elastiska poolen. | Överväg att öka DTU:erna för den elastiska poolen eller minska DTU-min per databas, eller minska antalet databaser i den elastiska poolen. |
| 40877 | 16 |Det går inte att ta bort en elastisk pool om den inte innehåller några databaser. Den elastiska poolen innehåller en eller flera databaser och kan därför inte tas bort. |Ta bort databaser från den elastiska poolen för att ta bort den. |
| 40881 | 16 |Den elastiska poolen %.*ls har nått sin databasräkningsgräns.  Gränsen för databasantal för den elastiska poolen får inte överstiga (%d) för en elastisk pool med (%d) DU:er. Försöker skapa eller lägga till databas i elastisk pool när databasantalgränsen för den elastiska poolen har nåtts. | Överväg att öka DU:erna för den elastiska poolen om möjligt för att öka dess databasgräns eller ta bort databaser från den elastiska poolen. |
| 40889 | 16 |D RU:er eller lagringsgränsen för den elastiska poolen %.*ls kan inte minskas eftersom det inte skulle ge tillräckligt med lagringsutrymme för dess databaser. Försöker minska lagringsgränsen för den elastiska poolen under lagringsanvändningen. | Överväg att minska lagringsanvändningen av enskilda databaser i den elastiska poolen eller ta bort databaser från poolen för att minska dess DTI eller lagringsgräns. |
| 40891 | 16 |DTU min per databas (%d) får inte överstiga DTU max per databas (%d). Försöker ställa in DTU min per databas högre än DTU max per databas. |Se till att DTU-min per databas inte överskrider DTU max per databas. |
| TBD | 16 |Lagringsstorleken för en enskild databas i en elastisk pool får inte överstiga den högsta storlek som tillåts av den elastiska servicenivån %.*ls-tjänstnivå. Den maximala storleken för databasen överskrider den maximala storleken som tillåts av den elastiska pooltjänstnivån. |Ange databasens maxstorlek inom gränserna för den högsta storlek som tillåts av den elastiska pooltjänstnivån. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Det går inte att öppna databasens "huvud" som begärs av inloggningen. Inloggningen misslyckades

Det här problemet beror på att kontot inte har behörighet att komma åt huvuddatabasen. Men som standard försöker SQL Server Management Studio (SSMS) ansluta till huvuddatabasen.

Följ dessa anvisningar för att lösa problemet:

1. På inloggningsskärmen för SSMS väljer du **Alternativ**och väljer sedan **Anslutningsegenskaper**.
2. I fältet **Anslut till databas** anger du användarens standarddatabasnamn som standardinloggningsdatabas och väljer sedan **Anslut**.

   ![Anslutningsegenskaper](media/troubleshoot-connectivity-issues-microsoft-azure-sql-database/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Bekräfta om ett fel orsakas av ett anslutningsproblem

För att bekräfta om ett fel orsakas av ett anslutningsproblem, granska stackspårningen för ramar som visar anrop för att öppna en anslutning som följande (notera referensen till **klassen SqlConnection):**

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

När undantaget utlöses av frågeproblem kommer du att märka en anropsstack som liknar följande (notera referensen till **klassen SqlCommand).** I det här [fallet, ställa in dina frågor](https://blogs.msdn.com/b/sqlblog/archive/2013/11/01/is-my-query-running-fine-in-the-cloud.aspx).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Mer information om finjusteringsprestanda finns i följande resurser:

* [Så här underhåller du Azure SQL-index och statistik](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
* [Prestanda för manuell inställning av frågor i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-performance-guidance)
* [Övervaka prestanda Azure SQL Database med hjälp av dynamiska hanteringsvyer](https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-with-dmvs)
* [Driva Frågearkivet i Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-operate-query-store)

## <a name="steps-to-fix-common-connection-issues"></a>Steg för att åtgärda vanliga anslutningsproblem

1. Kontrollera att TCP/IP är aktiverat som ett klientprotokoll på programservern. Mer information finns i [Konfigurera klientprotokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-client-protocols). Kontrollera att TCP/IP är aktiverat genom att köra **verktyget cliconfg.exe** (SQL Server Client Network) på programservrar där du inte har INSTALLERAT SQL Server-verktyg.
2. Kontrollera programmets anslutningssträng för att kontrollera att den är korrekt konfigurerad. Kontrollera till exempel att anslutningssträngen anger rätt port (1433) och fullständigt kvalificerat servernamn.
Se [Hämta sql server-anslutningsinformation](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms#get-sql-server-connection-information).
3. Försök att öka timeout-värdet för anslutningen. Vi rekommenderar att du använder en anslutningstidsutskrift på minst 30 sekunder.
4. Testa anslutningen mellan programservern och Azure SQL-databasen med hjälp av [SQL Server Management Studio (SSMS),](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms)en UDL-fil, ping eller telnet. Mer information finns i [Felsöka SQL Server-anslutningsproblem](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) och [diagnostik för anslutningsproblem](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues#diagnostics).

   > [!NOTE]
   > Som ett felsökningssteg kan du även testa anslutningen på en annan klientdator.

5. Se till att logiken för återförsök finns på plats. Mer information om logik för återförsök finns i [Felsöka tillfälliga fel och anslutningsfel till SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-issues).

Om de här stegen inte löser problemet kan du försöka samla in mer data och sedan kontakta supporten. Om ditt program är en molntjänst aktiverar du loggning. Det här steget returnerar en UTC-tidsstämpel för felet. Dessutom returnerar SQL Azure spårnings-ID:et. [Microsofts kundtjänst](https://azure.microsoft.com/support/options/) kan använda den här informationen.

Mer information om hur du aktiverar loggning finns i [Aktivera diagnostikloggning för appar i Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/).

## <a name="next-steps"></a>Nästa steg

* [Azure SQL-anslutningsarkitektur](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture)
* [Azure SQL Database- och Data Warehouse-nätverksåtkomstkontroller](https://docs.microsoft.com/azure/sql-database/sql-database-networkaccess-overview)

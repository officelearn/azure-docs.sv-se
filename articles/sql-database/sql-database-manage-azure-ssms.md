---
title: Hantera en SQL Database med SSMS | Microsoft Docs
description: "Ta reda på hur du kan använda SQL Server Management Studio för att hantera SQL Database-servrar och -databaser."
services: sql-database
documentationcenter: .net
author: stevestein
manager: jhubbard
editor: tysonn
ms.assetid: da6f3608-5993-4134-a497-ff2811e9f31f
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: d9ff74a49742fa77f5989b8b05e0567e3ca81dc5
ms.openlocfilehash: 89cb8827745b31b3a77b64d5cafd586957d60d30


---
# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Hantera Azure SQL Database med SQL Server Management Studio
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-manage-portal.md)
> * [SSMS](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Du kan använda SQL Server Management Studio (SSMS) för att administrera Azure SQL Database-servrar och -databaser. Den här artikeln vägleder dig genom vanliga aktiviteter med SSMS. Du bör redan ha en server och en databas skapad i Azure SQL Database innan du börjar. Se [Skapa din första Azure SQL Database](sql-database-get-started.md) och [Ansluta och fråga med SSMS](sql-database-connect-query-ssms.md) för mer information.

> [!TIP]
> En genomgång som visar dig hur du skapar en server, skapar en serverbaserad brandvägg, visar egenskaper, ansluter med SQL Server Management Studio, frågar huvuddatabasen, skapar en exempeldatabas och en tom databas, frågar om databasegenskaper, ansluter med SQL Server Management Studio och frågar exempeldatabasen, finns i[	Komma igång - Självstudier](sql-database-get-started.md).
>

Vi rekommenderar att du använder den senaste versionen av SSMS när du arbetar med Azure SQL Database. 

> [!IMPORTANT]
> Använd alltid den senaste versionen av SSMS eftersom den förbättras kontinuerligt för att fungera med de senaste uppdateringarna för Azure och SQL Database. Information om att hämta och installera den senaste versionen finns i [Hämta SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="create-and-manage-azure-sql-databases"></a>Skapa och hantera Azure SQL-databaser
När du är ansluten till **huvud**databasen, kan du skapa databaser på servern och ändra eller ta bort befintliga databaser. Följande steg beskriver hur du utför flera gemensamma databashanteringsuppgifter via Management Studio. Om du vill utföra dessa uppgifter ska du se till att du är ansluten till **huvud**databasen med den primära inloggningen på servernivå som du skapade när du konfigurerade din server.

För att öppna ett frågefönster i Management Studio, öppnar du mappen Databaser, expanderar mappen **Systemdatabaser**, högerklickar på **Huvud** och klickar sedan på **Ny fråga**.

* Använd uttrycket **CREATE DATABASE** för att skapa en databas. Mer information finns i [SKAPA DATABAS (SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx). Följande exempel skapar en databas med namnet **myTestDB** och anger att den är en S0-databas av standardversion med en maximal standardstorlek på 250 GB.
  
      CREATE DATABASE myTestDB
      (EDITION='Standard',
       SERVICE_OBJECTIVE='S0');

Klicka på **Kör** att köra frågan.

* Använd uttrycket **ALTER DATABASE** för att ändra en befintlig databas, till exempel om du vill ändra namnet och utgåvan på databasen. Mer information finns i [ÄNDRA DATABAS (SQL Database)](https://msdn.microsoft.com/library/ms174269.aspx). Följande uttryck ändrar databasen som du skapade i föregående steg till att ändra versionen till Standard S1.
  
      ALTER DATABASE myTestDB
      MODIFY
      (SERVICE_OBJECTIVE='S1');
* Använd uttrycket **DROP DATABASE** för att ta bort en befintlig databas. Mer information finns i [TA BORT DATABAS (SQL Database)](https://msdn.microsoft.com/library/ms178613.aspx). Följande uttryck tar bort databasen **myTestDB**, men ta inte bort den nu eftersom du kommer att använda den för att skapa inloggningar i nästa steg.
  
      DROP DATABASE myTestBase;
* Huvuddatabasen har vyn **sys.databases** som du kan använda för att visa detaljer om alla databaser. Om du vill visa alla befintliga databaser, kör du följande uttryck:
  
      SELECT * FROM sys.databases;
* I SQL Database stöds inte uttrycket **USE** för att växla mellan databaser. I stället måste du upprätta en anslutning direkt till måldatabasen.

> [!NOTE]
> Många av Transact-SQL-uttrycken som skapar eller ändrar en databas måste köras i sin egen batch och kan inte grupperas med andra Transact-SQL-uttryck. Mer information finns i uttrycksspecifik information.
> 
> 

## <a name="create-and-manage-logins"></a>Skapa och hantera inloggningar
**Huvud**databasen innehåller inloggningar och dessa inloggningar har behörighet att skapa databaser eller andra inloggningar. Hantera inloggningar genom att ansluta till **huvud**databasen med den primära inloggningen på servernivå som du skapade när du konfigurerade din server. Du kan använda uttrycken **CREATE LOGIN**, **ALTER LOGIN** eller **DROP LOGIN** för att köra frågor mot huvuddatabasen som hanterar inloggningar över hela servern. Mer information finns i [Hantera databaser och inloggningar i SQL Database](http://msdn.microsoft.com/library/azure/ee336235.aspx). 

* Använd uttrycket **CREATE LOGIN** för att skapa en inloggning på servernivå. Mer information finns i [SKAPA INLOGGNING (SQL Database)](https://msdn.microsoft.com/library/ms189751.aspx). Följande exempel skapar en inloggning som kallas **login1**. Ersätt **password1** med det lösenord du önskar.
  
      CREATE LOGIN login1 WITH password='password1';
* Använd uttrycket **CREATE USER** för att bevilja behörigheter på databasen. Alla inloggningar måste skapas i **huvud**databasen. För att en inloggning ska ansluta till en annan databas måste du bevilja den behörighet på databasnivå med hjälp av uttrycket **CREATE USER** i databasen. Mer information finns i [SKAPA ANVÄNDARE (SQL Database)](https://msdn.microsoft.com/library/ms173463.aspx). 
* För att ge login1 behörigheter till en databas som kallas **myTestDB**, gör du följande:
  
  1. Uppdatera Object Explorer för att visa databasen **myTestDB** som du skapade, högerklicka på servernamnet i Object Explorer och klicka sedan på **Uppdatera**.  
     
     Om du har stängt anslutningen kan du återansluta genom att välja **Ansluta Object Explorer** på menyn Arkiv.
  2. Högerklicka på databasen **myTestDB** och välj **Ny fråga**.
  3. Kör följande inställning mot myTestDB-databasen för att skapa en databasanvändare med namnet **login1User** som motsvarar inloggningen på servernivå **login1**.
     
         CREATE USER login1User FROM LOGIN login1;
* Använd den lagrade proceduren **sp\_addrolemember** för att ge användarkontot lämplig nivå av behörigheter för databasen. Mer information finns i [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). Följande uttryck ger **login1User** läsbehörighet till databasen genom att lägga till **login1User** till rollen **db\_datareader**.
  
      exec sp_addrolemember 'db_datareader', 'login1User';    
* Använd uttrycket **ALTER LOGIN** för att ändra en befintlig inloggning till exempel om du vill ändra lösenordet för inloggningen. Mer information finns i [ÄNDRA INLOGGNING (SQL Database)](https://msdn.microsoft.com/library/ms189828.aspx). Uttrycket **ALTER LOGIN** ska köras mot **huvud**databasen. Gå tillbaka till frågefönstret som är anslutet till databasen. Följande uttryck ändrar inloggningen **login1** för återställning av lösenordet. Ersätt **newPassword** med det lösenord du önskar och **oldPassword** med det aktuella lösenordet för inloggningen.
  
      ALTER LOGIN login1
      WITH PASSWORD = 'newPassword'
      OLD_PASSWORD = 'oldPassword';
* Använd uttrycket **DROP LOGIN** för att ta bort en befintlig inloggning. En inloggning på servernivå tar även bort alla associerade användarkonton på databasen. Mer information finns i [TA BORT DATABAS (SQL Database)](https://msdn.microsoft.com/library/ms178613.aspx). Uttrycket **DROP LOGIN** ska köras mot **huvud**databasen. Uttrycket tar bort den **login1**-inloggningen.
  
      DROP LOGIN login1;
* Huvuddatabasen har vyn **sys.sql\_logins** som du kan använda för att visa inloggningar. Om du vill visa alla befintliga inloggningar, kör du följande uttryck:
  
      SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Övervaka SQL Database med DMV (Data Management Views)
SQL Database har stöd för flera vyer för dynamisk hantering som du kan använda för att övervaka en individuell databas. Följande är några exempel på typer av övervakningsdata som du kan hämta via vyerna. Fullständig information och mer användningsexempel finns i [Övervaka SQL Database med Dynamic Management Views](https://msdn.microsoft.com/library/azure/ff394114.aspx).

* För att skicka en fråga till en dynamisk hanteringsvy krävs behörigheter för att **VISA DATABASTILLSTÅND**. Bevilja behörigheten **VISA DATABASTILLSTÅND** för en viss databasanvändare, anslut till databasen och kör följande uttryck mot databasen:
  
      GRANT VIEW DATABASE STATE TO login1User;
* Beräkna storleken på databasen med hjälp av vyn **sys.dm\_db\_partition\_stats**. Vyn **sys.dm\_db\_partition\_stats** returnerar information om sidan och antal rader för varje partition i databasen, som du kan använda för att beräkna databasens storlek. Följande fråga returnerar databasens storlek i MB:
  
      SELECT SUM(reserved_page_count)*8.0/1024
      FROM sys.dm_db_partition_stats;   
* Använd vyn **sys.dm\_exec\_connections** och **sys.dm\_exec\_sessions** för att hämta information om aktuella användaranslutningar och interna aktiviteter som är kopplade till databasen. Följande fråga returnerar information om den aktuella anslutningen:
  
      SELECT
          e.connection_id,
          s.session_id,
          s.login_name,
          s.last_request_end_time,
          s.cpu_time
      FROM
          sys.dm_exec_sessions s
          INNER JOIN sys.dm_exec_connections e
            ON s.session_id = e.session_id;
* Använd vyn **sys.dm\_exec\_query\_stats** för att hämta sammanställd prestandastatistik för cachelagrade frågeplaner. Följande fråga returnerar information om de översta fem frågorna rangordnade med genomsnittlig CPU-tid.
  
      SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          MIN(query_stats.statement_text) AS "Statement Text"
      FROM
          (SELECT QS.*,
          SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          ((CASE statement_end_offset
              WHEN -1 THEN DATALENGTH(ST.text)
              ELSE QS.statement_end_offset END
                  - QS.statement_start_offset)/2) + 1) AS statement_text
           FROM sys.dm_exec_query_stats AS QS
           CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      GROUP BY query_stats.query_hash
      ORDER BY 2 DESC;




<!--HONumber=Jan17_HO1-->



---
title: Hantera grupper med Azure SQL-databaser | Microsoft Docs
description: Gå igenom skapande och hantering av ett elastiska jobb.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: cd681a19fcc460427d36c1b827eb0dacd41c18d5
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Skapa och hantera skaländras ut Azure SQL-databaser med elastiska jobb (förhandsgranskning)


**Den elastiska databasen jobb** förenkla hanteringen av grupper av databaser genom att utföra administrativa åtgärder, till exempel schemaändringar, hantering av autentiseringsuppgifter, referens uppdateringar, insamling av prestandadata eller klient (kunden) telemetri samling. Elastiska jobb i databasen är tillgänglig via Azure portal och PowerShell-cmdlets. Dock Azure portal hämtar nedsatt funktionalitet begränsad till körning över alla databaser i en [elastisk pool (förhandsgranskning)](sql-database-elastic-pool.md). Få tillgång till ytterligare funktioner och körning av skript via en grupp databaser inklusive en egendefinierat samling eller en Fragmentera ange (skapat med hjälp av [klientbibliotek för elastisk databas](sql-database-elastic-scale-introduction.md)), se [skapa och hantera jobb med hjälp av PowerShell](sql-database-elastic-jobs-powershell.md). Mer information om jobb finns [elastisk databas översikt över](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. För en kostnadsfri utvärderingsversion finns [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En elastisk pool. Se [om elastiska pooler](sql-database-elastic-pool.md).
* Installation av tjänstkomponenter för elastisk databas jobb. Se [installera tjänsten elastisk databas jobbet](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Jobb
1. Med hjälp av den [Azure-portalen](https://portal.azure.com), från en befintlig elastisk jobbet databaspool, klicka på **skapa jobbet**.
2. Ange användarnamnet och lösenordet för databasadministratören (skapas vid installationen av jobb) för jobb kontrollen databasen (metadata lagring för jobb).
   
    ![Namnge jobbet, Skriv eller klistra in koden och klicka på Kör][1]
3. I den **skapa jobbet** bladet, ange ett namn för jobbet.
4. Ange användarnamn och lösenord för att ansluta till måldatabaserna med tillräcklig behörighet för att kunna köra skript.
5. Klistra in eller ange T-SQL-skript.
6. Klicka på **spara** och klicka sedan på **kör**.
   
    ![Skapa jobb och köra][5]

## <a name="run-idempotent-jobs"></a>Kör jobb för idempotent
När du kör ett skript mot en uppsättning databaser, måste du vara säker på att skriptet finns idempotent. Det vill säga måste skriptet kunna köras flera gånger, även om den har misslyckats innan i ett ofullständigt tillstånd. Till exempel när ett skript inte jobbet automatiskt görs tills den lyckas (inom intervallet, som det nya försöket logik slutligen upphör den försöker igen). Sätt att göra detta är att använda den en ”om det finns”-sats och ta bort alla hittade instansen innan du skapar ett nytt objekt. Här visas ett exempel:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Du kan också använda en ”om inte finns”-sats innan du skapar en ny instans:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Det här skriptet uppdaterar tabellen som skapats tidigare.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Kontrollera jobbstatus
När ett jobb har startat, du kan kontrollera förloppet.

1. Elastisk pool-sidan klickar du på **hantera jobb**.
   
    ![Klicka på ”Hantera jobb”][2]
2. Klicka på namnet (a) för ett jobb. Den **STATUS** kan vara ”slutfört” eller ”misslyckades”. Jobbets information visas (b) med dess datum och tid för att skapa och köra. I listan (c) som visar status för skript mot varje databas i poolen, vilket ger information om datum och tid.
   
    ![Kontrollen en klar][3]

## <a name="checking-failed-jobs"></a>Det gick inte att kontrollera jobb
Om ett jobb inte kan hitta en logg över exekveringen. Klicka på namnet på det misslyckade jobbet om du vill visa information.

![Markera ett jobb som misslyckades][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png



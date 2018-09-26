---
title: Hantera grupper av Azure SQL-databaser | Microsoft Docs
description: Gå igenom skapandet och hanteringen av ett Elastiskt jobb.
services: sql-database
ms.service: sql-database
subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: dca4ae2c8aa75b7af40ed4f8430968b8477c6802
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165678"
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Skapa och hantera utskalade Azure SQL-databaser med elastiska jobb (förhandsversion)


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Elastic Database-jobb** förenkla hanteringen av grupper av databaser genom att köra administrativa åtgärder, till exempel schemaändringar, hantering av autentiseringsuppgifter, uppdateringar av referensdata, insamling av prestandadata eller klient (kund)-telemetri samling. Elastic Database-jobb är för närvarande tillgänglig via Azure-portalen och PowerShell-cmdletar. Men Azure portal hämtar nedsatt funktionalitet begränsad till körning över alla databaser i en [elastisk pool](sql-database-elastic-pool.md). Ställ in åtkomst till ytterligare funktioner och körning av skript i en grupp med databaser, till exempel en samling med fördefinierade eller ett fragment (skapats med hjälp av [Elastic Database-klientbiblioteket](sql-database-elastic-scale-introduction.md)), finns i [skapa och hantera jobb med PowerShell](sql-database-elastic-jobs-powershell.md). Mer information om jobb finns i [översikt över elastiska databasjobb](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Förutsättningar
* En Azure-prenumeration. En kostnadsfri utvärderingsversion, se [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En elastisk pool. Se [om elastiska pooler](sql-database-elastic-pool.md).
* Installationen av tjänstkomponenter för elastic database-jobb. Se [installera elastiska jobb databastjänsten](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Skapa jobb
1. Med hjälp av den [Azure-portalen](https://portal.azure.com), från en befintlig elastisk databas jobbet pool, klickar du på **skapa jobb**.
2. Ange användarnamnet och lösenordet av databasadministratören (som skapats vid installationen av jobb) för kontrolldatabas jobb (metadata lagring för jobb).
   
    ![Namnge jobbet, Skriv eller klistra sedan in kod och klicka på Kör][1]
3. I den **skapa jobbet** bladet anger du ett namn för jobbet.
4. Ange användarnamn och lösenord för att ansluta till måldatabaserna med tillräcklig behörighet för att kunna köra skript.
5. Klistra in eller Skriv i T-SQL-skript.
6. Klicka på **spara** och klicka sedan på **kör**.
   
    ![Skapa jobb och kör][5]

## <a name="run-idempotent-jobs"></a>Kör jobb för idempotenta
När du kör ett skript mot en uppsättning databaser måste du vara säker på att skriptet är idempotent. Skriptet måste det vill säga att kunna köra flera gånger, även om det har misslyckats innan i ett ofullständigt tillstånd. Till exempel när ett skript inte, jobbet automatiskt görs tills det lyckas (inom gränserna, som återförsök logic så småningom upphör den försöker igen). Sätt att göra detta är att använda den en ”IF finns”-sats och ta bort valfri hittades instans innan du skapar ett nytt objekt. Här visas ett exempel:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Du kan också använda en ”IF NOT finns”-sats innan du skapar en ny instans:

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

Det här skriptet uppdaterar sedan tabellen som skapades tidigare.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Kontrollera jobbstatus
När ett jobb har startat, kan du kontrollera förloppet.

1. Elastisk pool-sidan klickar du på **hantera jobb**.
   
    ![Klicka på ”Hantera jobb”][2]
2. Klicka på namn (a) för ett jobb. Den **STATUS** kan vara ”slutfört” eller ”misslyckades”. Information för jobbets visas (b) med dess datum och tid för att skapa och köra. I listan (c) under den som visas förloppet för skript mot varje databas i poolen, vilket ger information om datum och tid.
   
    ![Kontrollerar ett klar jobb][3]

## <a name="checking-failed-jobs"></a>Det gick inte att kontrollera jobb
Om ett jobb inte går att hitta en logg över den kan körningen. Klicka på namnet på det misslyckade jobbet för att visa information om.

![Markera ett jobb som misslyckades][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png



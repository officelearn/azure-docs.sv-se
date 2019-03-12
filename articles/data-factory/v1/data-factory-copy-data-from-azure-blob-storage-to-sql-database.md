---
title: Kopiera data från Blob Storage till SQL-databas – Azure | Microsoft Docs
description: Den här självstudien visar hur du använder Kopieringsaktivitet i en Azure Data Factory-pipeline för att kopiera data från Blob storage till SQL-databas.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7accb8f2d97f5e4b4dc19ba93ab9edc1a4e6ce8a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57541341"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Självstudier: Kopiera data från Blob Storage till SQL Database med Data Factory
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten bör du gå igenom [självstudien för kopieringsaktiviteter](../quickstart-create-data-factory-dot-net.md). 

I den här självstudien skapar du en datafabrik med en pipeline för att kopiera data från Blob storage till SQL-databas.

Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.  

> [!NOTE]
> En detaljerad översikt över Data Factory-tjänsten finns i den [introduktion till Azure Data Factory](data-factory-introduction.md) artikeln.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Förutsättningar för självstudien
Innan du påbörjar den här självstudien måste du ha följande krav:

* **Azure-prenumeration**.  Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto på ett par minuter. Se den [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/) nedan för information.
* **Azure Storage-konto**. Du kan använda bloblagringen som en **källa** i den här självstudien. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md).
* **Azure SQL Database**. Du använder en Azure SQL-databas som en **mål** i den här självstudien. Om du inte har en Azure SQL-databas som du kan använda i självstudien Se [hur du skapar och konfigurerar en Azure SQL Database](../../sql-database/sql-database-get-started.md) att skapa en.
* **SQL Server 2012/2014 eller Visual Studio 2013**. Du använder SQL Server Management Studio eller Visual Studio för att skapa en exempeldatabas och att visa Resultatdata i databasen.  

## <a name="collect-blob-storage-account-name-and-key"></a>Samla in blob storage-kontonamn och nyckel
Du behöver kontonamnet och kontonyckeln för Azure storage-kontot att göra den här självstudien. Anteckna **kontonamn** och **kontonyckel** för ditt Azure storage-konto.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **alla tjänster** på den vänstra menyn och välj **Lagringskonton**.

    ![Bläddra - Storage-konton](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. I den **Lagringskonton** bladet och välja den **Azure storage-konto** som du vill använda i den här självstudien.
4. Välj **åtkomstnycklar** länka **inställningar**.
5. Klicka på **kopia** (bild)-knappen bredvid **lagringskontonamn** text rutan och spara och klistra in den någonstans (till exempel: i en textfil).
6. Upprepa föregående steg om du vill kopiera eller anteckna den **key1**.

    ![Lagringsåtkomstnyckel](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Stänga alla blad genom att klicka på **X**.

## <a name="collect-sql-server-database-user-names"></a>Samla in SQLServer, databas, användarnamn
Du måste namnen på Azure SQL server, databas och användaren att göra den här självstudien. Anteckna namnen på **server**, **databasen**, och **användaren** för Azure SQL-databasen.

1. I den **Azure-portalen**, klickar du på **alla tjänster** till vänster och välj **SQL-databaser**.
2. I den **SQL-databaser bladet**väljer den **databasen** som du vill använda i den här självstudien. Anteckna den **databasnamn**.  
3. I den **SQL-databas** bladet klickar du på **egenskaper** under **inställningar**.
4. Anteckna värdena för **servernamn** och **SERVERADMINISTRATÖR**.
5. Stänga alla blad genom att klicka på **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Ge Azure-tjänster åtkomst till SQLServer
Se till att **Tillåt åtkomst till Azure-tjänster** inställningen aktiverade **på** för Azure SQL-servern så att Data Factory-tjänsten kan komma åt Azure SQL-servern. Gör så här för att kontrollera och aktivera den här inställningen:

1. Klicka på **alla tjänster** hub till vänster och klicka på **SQL-servrar**.
2. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
3. På bladet **Brandväggsinställningar** klickar du på **På** för **Tillåt åtkomst till Azure-tjänster**.
4. Stänga alla blad genom att klicka på **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Förbered Blob Storage och SQL-databas
Förbered nu Azure blob storage och Azure SQL-databas för självstudien genom att utföra följande steg:  

1. Öppna Anteckningar. Kopiera följande text och spara den som **emp.txt** till **C:\ADFGetStarted** mapp på hårddisken.

    ```
    John, Doe
    Jane, Doe
    ```
2. Använd verktyg som t.ex. [Azure Lagringsutforskaren](https://storageexplorer.com/) till att skapa containern **adftutorial** och för att ladda upp filen **emp.txt** till containern.

    ![Azure Storage Explorer. Kopiera data från Blob storage till SQL-databas](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Använd följande SQL-skript för att skapa tabellen **emp** i din Azure SQL Database.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Om du har SQL Server 2012/2014 är installerat på datorn:** följer du anvisningarna i [hantera Azure SQL Database med SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) att ansluta till Azure SQL-servern och köra SQL-skript. 

    Om klienten inte har åtkomst till Azure SQL-servern måste du konfigurera brandväggen för din Azure SQL-server och tillåta åtkomst från din dator (IP-adress). Anvisningar för hur du konfigurerar brandväggen för Azure SQL-servern finns i [den här artikeln](../../sql-database/sql-database-configure-firewall-settings.md).

## <a name="create-a-data-factory"></a>Skapa en datafabrik
Du har slutfört förutsättningarna. Du kan skapa en datafabrik med hjälp av något av följande sätt. Klicka på något av alternativen i listrutan längst upp eller följande länkar för att genomföra kursen.     

* [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
* [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Det transformerar inte indata för att generera utdata. En självstudie om hur du omvandlar data med Azure Data Factory finns i [Självstudie: Bygg din första pipeline för att omvandla data med Hadoop-kluster](data-factory-build-your-first-pipeline.md).
> 
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory). 

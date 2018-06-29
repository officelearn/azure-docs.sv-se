---
title: Kopiera data från Blob Storage till SQL Database - Azure | Microsoft Docs
description: Den här kursen visar hur du använder Kopieringsaktiviteten i ett Azure Data Factory-pipelinen för att kopiera data från Blob storage till SQL-databas.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4538e5b49b161f22ba6d5979234786a58cae5783
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047734"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Självstudier: Kopiera data från Blob Storage till SQL-databas med hjälp av Data Factory
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
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [kopiera aktivitet kursen](../quickstart-create-data-factory-dot-net.md). 

I kursen får skapa du en datafabrik med en rörledning för att kopiera data från Blob storage till SQL-databas.

Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.  

> [!NOTE]
> En detaljerad översikt över Data Factory-tjänsten finns i [introduktion till Azure Data Factory](data-factory-introduction.md) artikel.
>
>

## <a name="prerequisites-for-the-tutorial"></a>Förutsättningar för självstudiekursen
Innan du påbörjar den här självstudien måste du ha följande krav:

* **Azure-prenumeration**.  Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto på ett par minuter. Finns det [kostnadsfri utvärderingsversion](http://azure.microsoft.com/pricing/free-trial/) artikeln för information.
* **Azure Storage-konto**. Du använder blobblagring som en **källa** datalager i den här självstudiekursen. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **Azure SQL Database**. Du använder en Azure SQL-databas som en **mål** datalager i den här självstudiekursen. Om du inte har en Azure SQL-databas som du kan använda i kursen, se [hur du skapar och konfigurerar en Azure SQL Database](../../sql-database/sql-database-get-started.md) att skapa en.
* **2014-SQL Server 2012 eller Visual Studio 2013**. Du använder SQL Server Management Studio eller Visual Studio för att skapa en exempeldatabas och visa resulterande data i databasen.  

## <a name="collect-blob-storage-account-name-and-key"></a>Samla in blob storage-kontonamnet och nyckeln
Du behöver kontonamnet och kontonyckel av Azure storage-konto för att göra den här kursen. Notera **kontonamn** och **kontonyckel** för Azure storage-konto.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **alla tjänster** på den vänstra menyn och välj **Lagringskonton**.

    ![Bläddra - Storage-konton](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. I den **Lagringskonton** bladet väljer den **Azure storage-konto** som du vill använda i den här självstudiekursen.
4. Välj **åtkomstnycklar** länken under **inställningar**.
5. Klicka på **kopiera** (bild)-knappen bredvid **lagringskontonamnet** text rutan och spara och klistra in den någonstans (till exempel: i en textfil).
6. Upprepa det föregående steget för att kopiera eller Skriv ned den **key1**.

    ![Lagringsåtkomstnyckel](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Stäng alla blad genom att klicka på **X**.

## <a name="collect-sql-server-database-user-names"></a>Samla in SQLServer, databas, användarnamn
Du måste namnen på Azure SQL server-databasen och användaren att göra den här kursen. Skriv ner namnen på **server**, **databasen**, och **användaren** för din Azure SQL-databas.

1. I den **Azure-portalen**, klickar du på **alla tjänster** till vänster och välj **SQL-databaser**.
2. I den **SQL-databaser bladet**, Välj den **databasen** som du vill använda i den här självstudiekursen. Notera den **databasnamnet**.  
3. I den **SQL-databas** bladet, klickar du på **egenskaper** under **inställningar**.
4. Skriv ner värdena för **servernamn** och **inloggning för SERVERADMINISTRATÖR**.
5. Stäng alla blad genom att klicka på **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Ge Azure-tjänster åtkomst till SQLServer
Se till att **Tillåt åtkomst till Azure-tjänster** inställningen aktiverade **på** för Azure SQL-servern så att Data Factory-tjänsten har åtkomst till Azure SQL-servern. Gör så här för att kontrollera och aktivera den här inställningen:

1. Klicka på **alla tjänster** hubb till vänster och klicka på **SQL-servrar**.
2. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
3. På bladet **Brandväggsinställningar** klickar du på **På** för **Tillåt åtkomst till Azure-tjänster**.
4. Stäng alla blad genom att klicka på **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Förbereda Blob Storage och SQL-databas
Nu kan förbereda Azure blob storage och Azure SQL-databas för kursen genom att utföra följande steg:  

1. Öppna Anteckningar. Kopiera följande text och spara den som **emp.txt** till **C:\ADFGetStarted** mapp på hårddisken.

    ```
    John, Doe
    Jane, Doe
    ```
2. Använd verktyg som t.ex. [Azure Lagringsutforskaren](http://storageexplorer.com/) till att skapa behållaren **adftutorial** och för att ladda upp filen **emp.txt** till behållaren.

    ![Azure Lagringsutforskaren. Kopiera data från Blob storage till SQL-databas](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
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

    **Om du har SQL Server 2012 2014 installerad på datorn:** följer du anvisningarna från [hantera Azure SQL Database med SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) att ansluta till din Azure SQL-server och köra SQL-skript. 

    Om klienten inte har åtkomst till Azure SQL-servern måste du konfigurera brandväggen för din Azure SQL-server och tillåta åtkomst från din dator (IP-adress). Anvisningar för hur du konfigurerar brandväggen för Azure SQL-servern finns i [den här artikeln](../../sql-database/sql-database-configure-firewall-settings.md).

## <a name="create-a-data-factory"></a>Skapa en datafabrik
Du har slutfört de nödvändiga förutsättningarna. Du kan skapa en datafabrik som använder något av följande sätt. Klicka på något av alternativen i listrutan längst upp eller följande länkar för att genomföra kursen.     

* [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
* [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Det transformerar inte indata för att generera utdata. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build your first pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg din första pipeline för att omvandla data med Hadoop-kluster).
> 
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory). 

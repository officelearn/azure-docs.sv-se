---
title: Kopiera data från Blob Storage till SQL Database - Azure
description: Den här självstudien visar hur du använder Kopiera aktivitet i en Azure Data Factory-pipeline för att kopiera data från Blob-lagring till SQL-databas.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cc2f0a513219a671dd8a75ee00af4fc9d4c6a68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979730"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Självstudiekurs: Kopiera data från Blob Storage till SQL Database med Data Factory
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [Powershell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [copy activity tutorial in version 2 documentation](../quickstart-create-data-factory-dot-net.md) (kopiera aktivitetssjälvstudien i dokumentationen för version 2) om du använder den aktuella versionen av Data Factory-tjänsten.

I den här självstudien skapar du en datafabrik med en pipeline för att kopiera data från Blob-lagring till SQL-databas.

Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.  

> [!NOTE]
> En detaljerad översikt över datafabrikstjänsten finns i artikeln [Introduktion till Azure Data Factory.](data-factory-introduction.md)
>
>

## <a name="prerequisites-for-the-tutorial"></a>Förutsättningar för handledningen
Innan du börjar den här självstudien måste du ha följande förutsättningar:

* **Azure-prenumeration**.  Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto på ett par minuter. Mer information finns i artikeln [Om kostnadsfri utvärderingsversion.](https://azure.microsoft.com/pricing/free-trial/)
* **Azure Storage-konto**. Du använder blob-lagringen som ett **källdatalager** i den här självstudien. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).
* **Azure SQL-databas**. Du använder en Azure SQL-databas som **ett måldatalager** i den här självstudien. Om du inte har en Azure SQL-databas som du kan använda i självstudien läser du [Hur du skapar och konfigurerar en Azure SQL-databas](../../sql-database/sql-database-get-started.md) för att skapa en.
* **SQL Server 2012/2014 eller Visual Studio 2013**. Du använder SQL Server Management Studio eller Visual Studio för att skapa en exempeldatabas och för att visa resultatdata i databasen.  

## <a name="collect-blob-storage-account-name-and-key"></a>Samla in namn och nyckel för blob-lagringskonto
Du behöver kontonamnet och kontonyckeln för ditt Azure-lagringskonto för att kunna göra den här självstudien. Obs ner **kontonamn** och **kontonyckel** för ditt Azure-lagringskonto.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **Alla tjänster** på den vänstra menyn och välj **Lagringskonton**.

    ![Bläddra - Lagringskonton](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. I bladet **Lagringskonton** väljer du det **Azure-lagringskonto** som du vill använda i den här självstudien.
4. Välj **länken Åtkomstnycklar** under **INSTÄLLNINGAR**.
5. Klicka på **knappen Kopiera** (bild) bredvid Textrutan **för lagringskontonamn** och spara/klistra in den någonstans (till exempel i en textfil).
6. Upprepa föregående steg för att kopiera eller notera ner **för nyckeln1**.

    ![Åtkomstnyckel för lagring](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Stäng alla blad genom att klicka på **X**.

## <a name="collect-sql-server-database-user-names"></a>Samla in SQL-server, databas, användarnamn
Du behöver namnen på Azure SQL-server, databas och användare för att göra den här självstudien. Anteckna namn på **server,** **databas**och **användare** för din Azure SQL-databas.

1. Klicka på Alla **tjänster** till vänster i **Azure-portalen**och välj **SQL-databaser**.
2. Markera den **databas** som du vill använda i den här självstudien i **sql-databasbladet.** Anteckna **databasnamnet**.  
3. Klicka på **Egenskaper** under **INSTÄLLNINGAR**i **SQL-databasbladet** .
4. Notera värdena för **SERVER NAMN** och SERVER **ADMIN LOGIN**.
5. Stäng alla blad genom att klicka på **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Tillåt Azure-tjänster att komma åt SQL-servern
Kontrollera att **tillåt åtkomst till Azure-tjänstinställningen** **aktiverad** för din Azure SQL-server så att Tjänsten Data Factory kan komma åt din Azure SQL-server. Gör så här för att kontrollera och aktivera den här inställningen:

1. Klicka på **Alla tjänster** nav till vänster och klicka på **SQL-servrar**.
2. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
3. På bladet **Brandväggsinställningar** klickar du på **På** för **Tillåt åtkomst till Azure-tjänster**.
4. Stäng alla blad genom att klicka på **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Förbereda Blob Storage och SQL Database
Förbered nu din Azure blob storage och Azure SQL-databas för självstudien genom att utföra följande steg:  

1. Öppna Anteckningar. Kopiera följande text och spara den som **emp.txt** till **mappen C:\ADFGetStarted** på hårddisken.

    ```
    John, Doe
    Jane, Doe
    ```
2. Använd verktyg som t.ex. [Azure Lagringsutforskaren](https://storageexplorer.com/) till att skapa containern **adftutorial** och för att ladda upp filen **emp.txt** till containern.

3. Använd följande SQL-skript för att skapa tabellen **emp** i din Azure SQL-databas.  

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

    **Om du har SQL Server 2012/2014 installerat på datorn:** följ instruktionerna från [Hantera Azure SQL Database med SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) för att ansluta till din Azure SQL-server och köra SQL-skriptet.

    Om klienten inte har åtkomst till Azure SQL-servern måste du konfigurera brandväggen för din Azure SQL-server och tillåta åtkomst från din dator (IP-adress). Anvisningar för hur du konfigurerar brandväggen för Azure SQL-servern finns i [den här artikeln](../../sql-database/sql-database-configure-firewall-settings.md).

## <a name="create-a-data-factory"></a>Skapa en datafabrik
Du har slutfört förutsättningarna. Du kan skapa en datafabrik på något av följande sätt. Klicka på ett av alternativen i listrutan högst upp eller följande länkar för att utföra självstudien.     

* [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [Powershell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Det transformerar inte indata för att generera utdata. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build your first pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg din första pipeline för att omvandla data med Hadoop-kluster).
>
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory).

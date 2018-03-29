---
title: Kopiera data med Azure-verktyget Kopiera data | Microsoft Docs
description: Skapa en Azure-datafabrik och kopiera sedan data från Azure Blob Storage till en SQL-databas.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: d2f1d089c6a08a1dc90f82fd9d1c3cb2b6f6dc0a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopiera data från Azure Blob Storage till en SQL-databas med verktyget för att kopiera data
> [!div class="op_single_selector" title1="Select the version of the Data Factory service that you're using:"]
> * [Version 1 – Allmänt tillgänglig](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 – förhandsversion](tutorial-copy-data-tool.md)

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från Azure Blob Storage till en SQL-databas. 

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).
>
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory, som är allmänt tillgänglig, kan du läsa [Kom igång med Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure-lagringskonto**: Använd Blob Storage som datalager för _källan_. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **Azure SQL Database**: Använd en SQL-databas som datalager för _kanalmottagaren_. Om du inte har någon SQL-databas kan du läsa instruktionerna i [Skapa en SQL-databas](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered din Blob Storage och SQL-databas för självstudien genom att utföra följande steg.

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Starta **Anteckningar**. Kopiera följande text och spara den i en fil med namnet **inputEmp.txt** på din disk:

    ```
    John|Doe
    Jane|Doe
    ```

2. Skapa en behållare med namnet **adfv2tutorial** och ladda upp filen inputEmp.txt till behållaren. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](http://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Skapa en SQL-mottagartabell

1. Använd följande SQL-skript för att skapa en tabell med namnet **dbo.emp** i din SQL-databas:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Ge Azure-tjänsterna åtkomst till SQL Server. Kontrollera att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för servern som kör SQL Server. Med den här inställningen kan Data Factory skriva data till din SQL-serverinstans. Gör så här för att kontrollera och aktivera inställningen:

    a. Till vänster väljer du **Fler tjänster** och sedan **SQL-servrar**.

    b. Välj server och sedan **INSTÄLLNINGAR** > **Brandvägg**.

    c. På sidan **Brandväggsinställningar** ställer du in alternativet **Tillåt åtkomst till Azure-tjänster** till **PÅ**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. I den vänstra menyn väljer du **+ Nytt** > **Data och analys** > **Data Factory**: 
   
   ![Skapa ny datafabrik](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. I fönstret **Ny datafabrik**, under **Namn**, anger du **ADFTutorialDataFactory**. 
      
     ![Ny datafabrik](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:
   
   ![Felmeddelande för ny datafabrik](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. Gör något av följande för **Resursgrupp**:
     
    a. Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

    b. Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).

5. Under **version** väljer du **V2 (förhandsversion)** för versionen.
6. Under **plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL Database) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas på andra platser och i andra regioner.
7. Välj **fäst till instrumentpanelen**. 
8. Välj **Skapa**.
9. På instrumentpanelen visar panelen **Distribuera Data Factory** processens status.

    ![Panelen Distribuera datafabrik](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. När den har skapats visas startsidan för **Data Factory**.
   
    ![Datafabrikens startsida](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory i en separat flik. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. Sidan **Nu sätter vi igång** visas. Välj panelen **Kopiera data** för att starta verktyget Kopiera data. 

   ![Panel för verktyget Kopiera data](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. På sidan **Egenskaper** under **Aktivitetsnamn**, anger du **CopyFromBlobToSqlPipeline**. Välj sedan **Nästa**. Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn. 

    ![Sidan Egenskaper](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. Välj **Azure Blob Storage** på sidan om **källdatalager** och klicka på **Nästa**. Källdatan finns i Blob Storage. 

    ![Sidan Källdatalager](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. Gör följande på sidan **Ange Azure Blob Storage-konto**:

    a. Under **Anslutningsnamn** anger du **AzureStorageLinkedService**.

    b. Välj ditt lagringskontonamn i listrutan **Lagringskontonamn**.

    c. Välj **Nästa**. 

    ![Ange lagringskontot](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

    En länkad tjänst länkar ett datalager eller en beräkning till datafabriken. I det här fallet skapar du en länkad lagringstjänst som länkar ditt lagringskonto till datalagret. Den länkade tjänsten har anslutningsinformationen som Data Factory använder för att ansluta till Blob Storage vid körning. Datauppsättningen anger behållaren, mappen och filen (valfritt) som innehåller källdata. 

5. Gör följande på sidan **Välj indatafil eller mapp**:
    
    a. Bläddra till mappen **adfv2tutorial/input**.

    b. Välj filen **inputEmp.txt**.

    c. Klicka på **Välj**. Du kan också dubbelklicka på **inputEmp.txt**.

    d. Välj **Nästa**. 

    ![Välj indatafil eller mapp](./media/tutorial-copy-data-tool/choose-input-file-folder.png)

6. Sidan med **filformatinställningar** visas. Observera att verktyget automatiskt identifierar kolumn- och radavgränsare. Välj **Nästa**. Du kan också förhandsgranska data och visa schemat för indata på den här sidan. 

    ![Filformatinställningar](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. På sidan **Måldatalager** väljer du **Azure SQL Database** och sedan **Nästa**.

    ![Måldatalager](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. Gör följande på sidan **Specify the Azure SQL database** (Ange Azure SQL-databas): 

    a. Under **Anslutningsnamn** anger du **AzureSqlDatabaseLinkedService**.

    b. Under **Servernamn** väljer du din SQL Server-instans.

    c. Under **Databasnamn** väljer du din SQL-databas.

    d. Under **Användarnamn** anger du namnet på användaren.

    e. Under **Lösenord** anger du användarens lösenord.

    f. Välj **Nästa**. 

    ![Ange SQL-databasen](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

    En datauppsättning måste associeras med en länkad tjänst. Den länkade tjänsten har anslutningssträngen som Data Factory använder för att ansluta till SQL-databasen vid körning. Datauppsättningen anger den behållare, mapp och fil (valfritt) som data kopieras till.

9. På sidan **Tabellmappning** väljer du tabellen **[dbo].[emp]** och sedan **Nästa**. 

    ![Tabellmappning](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. Observera att den första och andra kolumnen är mappade till kolumnerna **FirstName** och **LastName** för tabellen **emp** på sidan **Schemamappning**.

    ![Sidan för schemamappning](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. Sidan **Settings** (Inställningar) visas. Välj **Nästa**. 

    ![Sidan Inställningar](./media/tutorial-copy-data-tool/settings-page.png)
12. Granska inställningarna på sidan **Sammanfattning** och klicka på **Nästa**.

    ![Sammanfattningssida](./media/tutorial-copy-data-tool/summary-page.png)
13. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).

    ![Distributionssida](./media/tutorial-copy-data-tool/deployment-page.png)
14. Observera att fliken **Övervaka** till vänster väljs automatiskt. I kolumnen **Åtgärder** finns länkar som visar information om aktivitetskörningen och för att köra pipelinen igen. Om du vill uppdatera listan väljer du **Uppdatera**. 

    ![Övervaka pipelinekörningar](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. Om du vill se aktivitetskörningar som är associerade med pipelinekörningen, väljer du länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Åtgärder**. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill se mer information om kopieringsåtgärden väljer du länken **Information** (glasögonikonen) i kolumnen **Åtgärder**. Om du vill växla tillbaka till vyn **Pipeline Runs** (Pipelinekörningar) klickar du på länken **Pipeliner** högst upp. Välj **Uppdatera** för att uppdatera vyn. 

    ![Övervaka aktivitetskörningar](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. Klicka på fliken **Redigera** till vänster för att växla till redigeringsläget. Du kan uppdatera de länkade tjänster, datauppsättningar och pipeliner som skapats med verktyget med hjälp av redigeraren. Välj **Kod** för att visa JSON-koden för enheten som är öppen i redigeraren. Mer information om hur du redigerar dessa entiteter i användargränssnittet för Data Factory finns i [Azure Portal-versionen av den här självstudiekursen](tutorial-copy-data-portal.md).

    ![Flik för redigerare](./media/tutorial-copy-data-tool/edit-tab.png)
17. Kontrollera att datan infogas i tabellen **emp** i din SQL-databas.

    ![Kontrollera SQL-utdata](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från Blob Storage till en SQL-databas. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

Fortsätt till nästa självstudie om du vill lära dig att kopiera data från en lokal plats till molnet: 

> [!div class="nextstepaction"]
>[Kopiera data från en lokal plats till molnet](tutorial-hybrid-copy-data-tool.md)

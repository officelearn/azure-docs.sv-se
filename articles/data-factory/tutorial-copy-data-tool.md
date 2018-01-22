---
title: Kopiera data med Azure-verktyget Kopiera data | Microsoft Docs
description: "Skapa en Azure-datafabrik och kopiera sedan data från Azure Blob Storage till Azure SQL Database."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: a8c7035ebf462bb168147e9d8eb60742333ce8b8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-copy-data-tool"></a>Kopiera data från Azure Blob till Azure SQL Database med verktyget Kopiera data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 – förhandsversion](tutorial-copy-data-tool.md)

I den här självstudien skapar du en datafabrik med Azure Portal. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från en Azure Blob Storage till en Azure SQL Database. 

> [!NOTE]
> - Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).
>
> - Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Läs [get started with Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (kom igång med Data Factory version 1) om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig.


I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
* **Azure Storage-konto**. Du kan använda blob-lagringen som **källa** för datalagringen. Om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) .
* **Azure SQL Database**. Du använder databasen som **mottagare** för datalagringen. Om du inte har någon Azure SQL Database kan du läsa om hur du skapar en i [Skapa en Azure SQL Database](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered nu Azure-blobblagringen och Azure SQL-databasen för den här självstudien genom att utföra följande steg:

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Öppna Anteckningar. Kopiera följande text och spara den som en **inputEmp.txt**-fil på din disk.

    ```
    John|Doe
    Jane|Doe
    ```

2. Använd verktyg som t.ex. [Azure Lagringsutforskaren](http://storageexplorer.com/) till att skapa behållaren **adfv2tutorial** och för att ladda upp filen **inputEmp.txt** till behållaren.

#### <a name="create-a-sink-sql-table"></a>Skapa en SQL-mottagartabell

1. Använd följande SQL-skript för att skapa tabellen **dbo.emp** i din Azure SQL Database.

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

2. Ge Azure-tjänster åtkomst till SQL-servern. Kontrollera att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för din Azure SQL-server så att tjänsten Data Factory kan skriva data till din Azure SQL-server. Gör så här för att kontrollera och aktivera den här inställningen:

    1. Klicka på hubben **Fler tjänster** till vänster och klicka på **SQL-servrar**.
    2. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
    3. På sidan **Brandväggsinställningar** väljer du **På** för **Tillåt åtkomst till Azure-tjänster**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sida för ny datafabrik](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
     ![Sida för ny datafabrik](./media/tutorial-copy-data-tool/name-not-available-error.png)
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
      Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (Azure Storage, Azure SQL Database o.s.v.) och beräkningarna (HDInsight o.s.v.) som används i Data Factory kan finnas på andra platser/regioner.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-copy-data-tool/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
   ![Datafabrikens startsida](./media/tutorial-copy-data-tool/data-factory-home-page.png)
10. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. Klicka på panelen **Kopiera data** för att starta verktyget Kopiera data. 

   ![Panel för verktyget Kopiera data](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
2. På sidan **Egenskaper** för verktyget Kopiera data anger du **CopyFromBlobToSqlPipeline** för **Task name** (Aktivitetsnamn) och klickar på **Nästa**. Med användargränssnittet för Data Factory skapas en pipeline med det namn som du har angett som aktivitetsnamn. 

    ![Verktyget Kopiera data – sidan Egenskaper](./media/tutorial-copy-data-tool/copy-data-tool-properties-page.png)
3. Välj **Azure Blob Storage** på sidan **Källdatalager** och klicka på **Nästa**. Källdata finns i en Azure Blob Storage. 

    ![Sidan Källdatalager](./media/tutorial-copy-data-tool/source-data-store-page.png)
4. Gör följande på sidan **Specify the Azure Blob storage account** (Ange Azure Blob Storage-konto): 
    1. Ange **AzureStorageLinkedService** som **anslutningsnamn**.
    2. Välj ditt **lagringskontonamn** från listrutan.
    3. Klicka på **Nästa**. 

        ![Ange Blob Storage-konto](./media/tutorial-copy-data-tool/specify-blob-storage-account.png)

        En länkad tjänst länkar ett datalager eller en beräkning till datafabriken. I det här fallet skapar du en länkad Azure Storage-tjänst för att länka ditt Azure Storage-konto till datalagret. Den länkade tjänsten har anslutningsinformationen som Data Factory-tjänsten använder för att ansluta till bloblagringen vid körning. Datauppsättningen anger behållaren, mappen och filen (valfritt) som innehåller källdata. 
5. Gör följande på sidan **Välj indatafil eller mapp**:
    
    1. Navigera till mappen **adfv2tutorial/input**.
    2. Välj **inputEmp.txt**
    3. Klicka på **Välj**. Du kan också dubbelklicka på **inputEmp.txt**. 
    4. Klicka på **Nästa**. 

    ![Välj indatafil eller mapp](./media/tutorial-copy-data-tool/choose-input-file-folder.png)
6. Observera att verktyget automatiskt identifierar kolumn- och radavgränsare på sidan för **filformatinställningar** och klicka på **Nästa**. Du kan också förhandsgranska data och visa schemat för indata på den här sidan. 

    ![Sida för filformatinställningar](./media/tutorial-copy-data-tool/file-format-settings-page.png)
7. På sidan **Måldatalager** väljer du **Azure SQL Database** och klickar sedan på **Nästa**. 

    ![Sidan Måldatalager](./media/tutorial-copy-data-tool/destination-data-storage-page.png)
8. Gör följande på sidan **Specify the Azure SQL database** (Ange Azure SQL-databas): 

    1. Ange **AzureSqlDatabaseLinkedService** som **anslutningsnamn**. 
    2. Välj din Azure SQL-server som **servernamn**.
    3. Välj din Azure SQL-databas som **databasnamn**.
    4. Ange namnet på användaren som **användarnamn**.
    5. Ange användarens lösenord som **lösenord**.
    6. Klicka på **Nästa**. 

        ![Ange Azure SQL-databas](./media/tutorial-copy-data-tool/specify-azure-sql-database.png)

        En datauppsättning måste associeras med en länkad tjänst. De länkade tjänsterna har anslutningssträngen som Data Factory-tjänsten använder för att ansluta till Azure SQL-databasen vid körning. Datauppsättningen anger den behållare, mapp och fil (valfritt) som data kopieras till.
9.  Välj **[dbo].[emp]** på sidan **Table mapping** (Tabellmappning) och klicka på **Nästa**. 

    ![Sidan för tabellmappning](./media/tutorial-copy-data-tool/table-mapping-page.png)
10. Observera att den första och andra kolumnen är mappade till kolumnerna **FirstName** och **LastName** för tabellen **emp** på sidan **Schema mapping** (Schemamappning). 

    ![Sidan för schemamappning](./media/tutorial-copy-data-tool/schema-mapping-page.png)
11. Klicka på **Nästa** på sidan **Inställningar**. 

    ![Sidan Inställningar](./media/tutorial-copy-data-tool/settings-page.png)
12. Granska inställningarna på sidan **Summary** (Sammanfattning) och klicka på **Nästa**.

    ![Sammanfattningssida](./media/tutorial-copy-data-tool/summary-page.png)
13. Klicka på **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).

    ![Distributionssida](./media/tutorial-copy-data-tool/deployment-page.png)
14. Observera att fliken **Övervaka** till vänster väljs automatiskt. Länkar för att visa information om aktivitetskörningar och för att köra pipelinen på nytt visas i kolumnen **Actions** (Åtgärder). Om du vill uppdatera listan klickar du på **Uppdatera**. 

    ![Övervaka pipelinekörningar](./media/tutorial-copy-data-tool/monitor-pipeline-runs.png)
15. Om du vill visa de aktivitetskörningar som är associerade med pipelinekörningarna klickar du på länken **View activity runs** (Visa aktivitetskörningar) i kolumnen **Action** (Åtgärd). Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill se mer information om kopieringsåtgärden klickar du på länken för **detaljer** (glasögonikonen) i kolumnen **Actions** (Åtgärder). Om du vill växla tillbaka till vyn med pipelinekörningar klickar du på länken **Pipeliner** högst upp. Om du vill uppdatera vyn klickar du på **Uppdatera**. 

    ![Övervaka aktivitetskörningar](./media/tutorial-copy-data-tool/monitor-activity-runs.png)
16. Klicka på fliken **Redigera** till vänster för att växla till redigeringsläget. Du kan uppdatera de länkade tjänster, datauppsättningar och pipeliner som skapats med verktyget med hjälp av redigeraren. Klicka på **Code** (Kod) för att visa JSON-koden som är associerad med den entitet som har öppnats i redigeraren. Mer information om hur du redigerar dessa entiteter i användargränssnittet för Data Factory finns i [Azure Portal-versionen av den här självstudiekursen](tutorial-copy-data-portal.md).

    ![Flik för redigerare](./media/tutorial-copy-data-tool/edit-tab.png)
17. Kontrollera att data infogas i tabellen **emp** i Azure SQL Database. 

    ![Kontrollera SQL-utdata](./media/tutorial-copy-data-tool/verify-sql-output.png)

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en Azure Blob Storage till en Azure SQL Database. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline
> * Övervaka pipelinen och aktivitetskörningarna.

Fortsätt till följande självstudie och lär dig att kopiera data från en lokal plats till molnet: 

> [!div class="nextstepaction"]
>[Kopiera data från en lokal plats till molnet](tutorial-hybrid-copy-data-tool.md)
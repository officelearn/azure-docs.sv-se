---
title: Kopiera en tabell stegvis med Azure Data Factory | Microsoft Docs
description: I den här självstudiekursen kommer du att skapa en Azure Data Factory-pipeline som kopierar data stegvis från en Azure SQL-databas till Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: yexu
ms.openlocfilehash: 1bc4bd9b95dc7e45b9b90fbe096ed71c5aa9bedf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60571506"
---
# <a name="incrementally-load-data-from-an-azure-sql-database-to-azure-blob-storage"></a>Läsa in data stegvis från en Azure SQL-databas till Azure Blob Storage
I den här självstudien skapar du en Azure-datafabrik med en pipeline som läser in delta-data från en tabell i en Azure SQL-databas till Azure Blob Storage. 

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Förbered datalagringen för att lagra värdet för vattenstämpeln.
> * Skapa en datafabrik.
> * Skapa länkade tjänster. 
> * Skapa datauppsättningar för källa, mottagare och vattenstämpel.
> * Skapa en pipeline.
> * Köra en pipeline.
> * Övervaka pipelinekörningen. 
> * Granska resultatet
> * Lägg till mer data i källan.
> * Kör pipelinen igen.
> * Övervaka den andra pipelinekörningen
> * Granska resultatet från den andra körningen


## <a name="overview"></a>Översikt
Här är det avancerade diagrammet: 

![Läsa in data stegvis](media/tutorial-Incremental-copy-portal/incrementally-load.png)

Här är några viktiga steg för att skapa den här lösningen: 

1. **Markera vattenstämpelkolumnen**.
    Markera en kolumn i källdatalagringen som går att använda för att dela upp de nya eller uppdaterade posterna för varje körning. Vanligtvis ökar data i den markerade kolumnen (till exempel last_modify_time elle ID) när rader skapas eller uppdateras. Det maximala värdet i den här kolumnen används som vattenstämpel.

2. **Förbered datalagringen för att lagra värdet för vattenstämpeln**. I den här självstudien lagrar du storleksgränsen i en SQL-databas.
    
3. **Skapa en pipeline med följande arbetsflöde**: 
    
    Pipelinen i den här lösningen har följande aktiviteter:
  
    * Skapa två sökningsaktiviteter. Använd den första sökningsaktiviteten för att hämta det sista vattenstämpelvärdet. Använd den andra sökningsaktiviteten för att hämta det nya vattenstämpelvärdet. Vattenstämpelvärdena skickas till kopieringsaktiviteten. 
    * Skapa en kopieringsaktivitet som kopierar raderna från källdatalagringen med värdet för vattenstämpelkolumnen som är större än det gamla värdet och mindre än det nya. Sedan kopieras deltadata från källdatalagringen till Blob-lagring som en ny fil. 
    * Skapa en StoredProcedure-aktivitet som uppdaterar vattenstämpelvärdet för den pipeline som körs nästa gång. 


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
* **Azure SQL Database**. Du använder databasen som källa för datalagringen. Om du inte har någon SQL Database kan du läsa om hur du skapar en i [Skapa en Azure SQL-databas](../sql-database/sql-database-get-started-portal.md).
* **Azure Storage**. Du kan använda blob-lagringen som mottagare för datalagringen. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md). Skapa en container med namnet adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Skapa en datatabell i din SQL-databas
1. Öppna SQL Server Management Studio. I **Server Explorer** högerklickar du på databasen och väljer **Ny fråga**.

2. Kör följande SQL-kommando mot din SQL-databas för att skapa en tabell med namnet `data_source_table` som datakällagringen: 
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    I den här självstudien ska du använda LastModifytime som vattenstämpelkolumn. Data i datakällagringen visas i följande tabell:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Skapa en annan tabell i SQL-databasen för att lagra värdet för högvattenmärket
1. Kör följande SQL-kommando mot din SQL-databas för att skapa en tabell med namnet `watermarktable` för att lagra värdet för högvattenmärket:  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Ställ in standardvärdet för högvattenmärket med tabellnamnet på källdatalagret. I den här självstudien är tabellnamnet: data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Granska informationen i tabellen `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Utdata: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Skapa en lagrad procedur i din SQL-databas 

Kör följande kommando för att skapa en lagrad procedur i din SQL-databas:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. På menyn till vänster väljer **skapa en resurs** > **Data och analys** > **Data Factory**: 
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **Ny datafabrik** anger du **ADFIncCopyTutorialDF** som **namn**. 
      
     ![Sida för ny datafabrik](./media/tutorial-incremental-copy-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om du ser ett rött utropstecken med följande fel ändrar du namnet på datafabriken (till exempel dittnamnADFIncCopyTutorialDF) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
       `Data factory name "ADFIncCopyTutorialDF" is not available`
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
        Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2** för **versionen**.
5. Välj **plats** för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.      
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-incremental-copy-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
   ![Datafabrikens startsida](./media/tutorial-incremental-copy-portal/data-factory-home-page.png)
10. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory i en separat flik.

## <a name="create-a-pipeline"></a>Skapa en pipeline
I den här självstudien skapar du en pipeline med två sökningsaktiviteter, en kopieringsaktivitet och en aktivitet för lagrad procedur i en länkad pipeline. 

1. På sidan **Kom igång** i användargränssnittet för Data Factory klickar du på panelen **Skapa pipeline**. 

   ![Sidan Kom igång i Data Factory-användargränssnittet](./media/tutorial-incremental-copy-portal/get-started-page.png)    
3. På sidan **General** (Allmänt) i fönstret **Egenskaper** för pipelinen anger du namnet **IncrementalCopyPipeline**. 

   ![Namn på pipeline](./media/tutorial-incremental-copy-portal/pipeline-name.png)
4. Vi lägger till den första sökningsaktiviteten för att hämta det gamla vattenstämpelvärdet. I verktygslådan **Aktiviteter** expanderar du **Allmänt** och drar och släpper **sökningen** på pipelinedesignytan. Ändra aktivitetens namn till **LookupOldWaterMarkActivity**.

   ![Den första sökningsaktiviteten – namn](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Växla till fliken **Inställningar** och klicka på **+ Ny** för **källdatauppsättningen**. I det här steget skapar du en datauppsättning för att representera data i **watermarktable**. Den här tabellen innehåller den gamla vattenstämpeln som användes i den tidigare kopieringen. 

   ![Menyn Ny datauppsättning – gammal vattenstämpel](./media/tutorial-incremental-copy-portal/new-dataset-old-watermark.png)
6. Välj **Azure SQL Database** i fönstret **Ny datauppsättning** och klicka på **Slutför**. En ny flik öppnas för datauppsättningen. 

   ![Välja Azure SQL Database](./media/tutorial-incremental-copy-portal/select-azure-sql-database-old-watermark.png)
7. I datauppsättningens egenskapsfönster anger du **WatermarkDataset** som **namn**.

   ![Datauppsättning för vattenstämpel – namn](./media/tutorial-incremental-copy-portal/watermark-dataset-name.png)
8. Växla till fliken **Anslutning**och klicka på **+ Ny** för att upprätta en anslutning (skapa en länkad tjänst) till Azure SQL-databasen. 

   ![Knapp för ny länkad tjänst](./media/tutorial-incremental-copy-portal/watermark-dataset-new-connection-button.png)
9. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):

    1. Ange **AzureSqlDatabaseLinkedService** som **namn**. 
    2. Välj din Azure SQL-server som **servernamn**.
    3. Ange **användarens namn** för åtkomst till Azure SQL Server. 
    4. Ange **lösenordet** för användaren. 
    5. Om du vill testa anslutningen till Azure SQL-databasen klickar du på **Testanslutning**.
    6. Klicka på **Spara**.
    7. På fliken **Anslutning** bekräftar du att **AzureSqlDatabaseLinkedService** är valt som **länkad tjänst**.
       
        ![Fönster för ny länkad tjänst](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
10. Välj **[dbo].[watermarktable]** för **Tabell**. Om du vill förhandsgranska data i tabellen klickar du på **Förhandsgranska data**.

    ![Datauppsättning för vattenstämpel – anslutningsinställningar](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
11. Växla till pipeline-redigeringsprogrammet genom att klicka på pipelinefliken högst upp eller på pipelinenamnet i trädvyn till vänster. I egenskapsfönstret för **sökningsaktiviteten** bekräftar du att **WatermarkDataset** är valt för fältet **Source Dataset** (Källdatauppsättning). 

    ![Pipeline – datauppsättning för gammalt vattenmärke](./media/tutorial-incremental-copy-portal/pipeline-old-watermark-dataset-selected.png)
12. I verktygslådan **Aktiviteter** expanderar du **Allmänt** och drar och släpper ytterligare en **sökning** på pipelinedesignytan och anger namnet **LookupNewWaterMarkActivity** på fliken **Allmänt** i egenskapsfönstret. Med den här sökningsaktiviteten kopieras det nya vattenstämpelvärdet från tabellen med källdata till målet. 

    ![Den andra sökningsaktiviteten – namn](./media/tutorial-incremental-copy-portal/second-lookup-activity-name.png)
13. I egenskapsfönstret för den andra **sökningsaktiviteten** växlar du till fliken **Inställningar** och klickar på **New** (Nytt). Du skapar en datauppsättning för att peka på källtabellen som innehåller det nya vattenstämpelvärdet (det högsta värdet för LastModifyTime). 

    ![Den andra sökningsaktiviteten – ny datauppsättning](./media/tutorial-incremental-copy-portal/second-lookup-activity-settings-new-button.png)
14. Välj **Azure SQL Database** i fönstret **Ny datauppsättning** och klicka på **Slutför**. En ny flik öppnas för datauppsättningen. Du kan också se datauppsättningen i trädvyn. 
15. På fliken **General** (Allmänt) i fönstret Egenskaper skriver du **SourceDataset** som **namn**. 

    ![Källdatauppsättning – namn](./media/tutorial-incremental-copy-portal/source-dataset-name.png)
16. Växla till fliken **Anslutning** och gör följande: 

    1. Välj **AzureSqlDatabaseLinkedService** som **länkad tjänst**.
    2. Välj **[dbo].[data_source_table]** som tabell. Du kan ange en fråga för den här datauppsättningen senare under kursen. Frågan åsidosätter den tabell som du anger i det här steget. 

        ![Den andra sökningsaktiviteten – ny datauppsättning](./media/tutorial-incremental-copy-portal/source-dataset-connection.png)
17. Växla till pipeline-redigeringsprogrammet genom att klicka på pipelinefliken högst upp eller på pipelinenamnet i trädvyn till vänster. I egenskapsfönstret för **sökningsaktiviteten** bekräftar du att **SourceDataset** är valt för fältet **Source Dataset** (Källdatauppsättning). 
18. Välj **Fråga** i fältet **Använd fråga** och ange följande fråga: du väljer endast det högsta värdet för **LastModifytime** från **data_source_table**. Om du inte har den här frågan hämtar datauppsättningen alla rader från tabellen som du angav för tabellnamnet (data_source_table) i datauppsättningsdefinitionen.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Den andra sökningsaktiviteten – fråga](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. I verktygslådan **Aktiviteter** visar du **Dataflöde** och drar och släpper aktiviteten **Kopiera** från verktygslådan Aktiviteter och anger namnet **IncrementalCopyActivity**. 

    ![Kopieringsaktivitet – namn](./media/tutorial-incremental-copy-portal/copy-activity-name.png)
20. **Anslut båda sökningsaktiviteterna till kopieringsaktiviteten** genom att dra den **gröna knappen** som är ansluten till sökningsaktiviteterna till kopieringsaktiviteten. Släpp musknappen när du ser att kantlinjefärgen för kopieringsaktiviteten ändras till blått. 

    ![Anslutning för sökningsaktiviteter till kopieringsaktivitet](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Välj **kopieringsaktiviteten** och bekräfta att du ser egenskaperna för aktiviteten i fönstret **Egenskaper**. 

    ![Kopiera egenskaper för aktivitet](./media/tutorial-incremental-copy-portal/back-to-copy-activity-properties.png)
22. Växla till fliken **Källa** i fönstret **Egenskaper** och utför följande steg:

    1. Markera **SourceDataset** för fältet för **källdatauppsättning**. 
    2. Välj **Fråga** i fältet **Använd fråga**. 
    3. Ange följande SQL-fråga för fältet **Fråga**. 

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```
    
        ![Kopieringsaktivitet – källa](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Växla till fliken **Sink** (Mottagare) och klicka på **+ Ny** för fältet för **datauppsättning för mottagare**. 

    ![Knappen Ny datauppsättning](./media/tutorial-incremental-copy-portal/new-sink-dataset-button.png)
24. I den här självstudien är datalager för mottagare av typen Azure Blob Storage. Välj därför **Azure Blob Storage** i fönstret **Ny datauppsättning** och klicka på **Slutför**. 

    ![Välj Azure Blob Storage](./media/tutorial-incremental-copy-portal/select-azure-blob-storage.png)
25. I datauppsättningens egenskapsfönster anger du **SinkDataset** som **namn** på fliken **General** (Allmänt). 

    ![Namn på datauppsättning för mottagare](./media/tutorial-incremental-copy-portal/sink-dataset-name.png)
26. Växla till fliken **Anslutningar** och klicka på **+ Ny**. I det här steget skapar du en anslutning (länkad tjänst) till **Azure Blob Storage**.

    ![Datauppsättning för mottagare – ny anslutning](./media/tutorial-incremental-copy-portal/sink-dataset-new-connection.png)
26. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureStorageLinkedService** som **namn**. 
    2. Välj ditt Azure Storage-konto i **Lagringskontonamn**.
    3. Klicka på **Spara**. 

        ![Länkad Azure-lagringstjänst – inställningar](./media/tutorial-incremental-copy-portal/azure-storage-linked-service-settings.png)
27. På fliken **Anslutning** och gör följande:

    1. Bekräfta att **AzureStorageLinkedService** har valts för **Länkad tjänst**. 
    2. För **mappdelen** i fältet **Filsökväg** anger du **adftutorial/incrementalcopy**. **adftutorial** är blobcontainerns namn och **incrementalcopy** är mappens namn. Det här kodfragmentet förutsätter att du har en blobcontainer med namnet adftutorial i din blob-lagring. Skapa containern om den inte finns, eller ställ in den för namnet på en befintlig. Azure Data Factory skapar automatiskt utdatamappen **incrementalcopy** om den inte finns. Du kan också använda knappen **Bläddra** för **Filsökväg** för att navigera till en mapp i en blobcontainer. .RunId, '.txt')`.
    3. För **filnamnsdelen** av fältet med **filsökväg** ange du `@CONCAT('Incremental-', pipeline().RunId, '.txt')`. Filnamnet genereras dynamiskt med uttrycket. Varje pipelinekörning har ett unikt ID. Kopieringsaktiviteten använder körnings-ID för att generera filnamnet. 

        ![Datauppsättning för mottagare – anslutningsinställningar](./media/tutorial-incremental-copy-portal/sink-dataset-connection-settings.png)
28. Växla till **pipeline**-redigeringsprogrammet genom att klicka på pipelinefliken högst upp eller på pipelinenamnet i trädvyn till vänster. 
29. I verktygslådan **Aktiviteter** expanderar du **Allmänt** och drar och släpper aktiviteten **Lagrad procedur** från verktygslådan **Aktiviteter** till pipelinedesignerytan. **Anslut** gröna utdata (lyckades) från aktiviteten **Kopiera** till den **lagrade proceduraktiviteten**. 
    
    ![Kopiera aktivitet – källa](./media/tutorial-incremental-copy-portal/connect-copy-to-stored-procedure-activity.png)
24. Välj **Lagrad proceduraktivitet** i pipelinedesignern och ändra dess namn till **StoredProceduretoWriteWatermarkActivity**. 

    ![Lagrad proceduraktivitet – namn](./media/tutorial-incremental-copy-portal/stored-procedure-activity-name.png)
25. Växla till fliken **SQL-konto** och välj *AzureSqlDatabaseLinkedService** som **Länkad tjänst**. 

    ![Lagrad proceduraktivitet – SQL-konto](./media/tutorial-incremental-copy-portal/sp-activity-sql-account-settings.png)
26. Växla till fliken **Lagrad procedur** och gör följande: 

    1. Som **Namn på lagrad procedur** väljer du **usp_write_watermark**. 
    2. När du ska ange värden för parametrarna för lagrad procedur klickar du på **Importera parameter** och anger följande värden för parametern: 

        | Namn | Typ | Värde | 
        | ---- | ---- | ----- | 
        | LastModifiedtime | Datetime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | String | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

    ![Lagrad proceduraktivitet – inställningar för lagrad procedur](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Verifiera pipelineinställningarna genom att klicka på **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Om du vill stänga fönstret med **verifieringsrapporten för pipeline** klickar du på >>.   

    ![Verifiera pipeline](./media/tutorial-incremental-copy-portal/validate-pipeline.png)
28. Publicera entiteter (länkade tjänster, datauppsättningar och pipeliner) till Azure Data Factory-tjänsten genom att välja knappen **Publicera alla**. Vänta tills du ser ett meddelande om att publiceringen är klar. 

    ![Knappen Publicera](./media/tutorial-incremental-copy-portal/publish-button.png)

## <a name="trigger-a-pipeline-run"></a>Utlös en pipelinekörning
1. Klicka på **Utlösare** i verktygsfältet och klicka på **Trigger Now** (Utlös nu). 

    ![Knappen Trigger Now (Utlös nu)](./media/tutorial-incremental-copy-portal/trigger-now.png)
2. I fönstret **Pipeline Run** (Pipelinekörning) väljer du **Slutför**. 

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Växla till fliken **Övervaka** till vänster. Du kan se status för den pipelinekörning som utlöstes av den manuella utlösaren. Om du vill uppdatera listan klickar du på **Uppdatera**. 
    
    ![Pipelinekörningar](./media/tutorial-incremental-copy-portal/pipeline-runs.png)
2. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningarna klickar du på den första länken (**View activity runs** (Visa aktivitetskörningar)) i kolumnen **Action** (Åtgärd). Du kan gå tillbaka till föregående vy genom att klicka på **Pipeliner** högst upp. Om du vill uppdatera listan klickar du på **Uppdatera**.

    ![Aktivitetskörningar](./media/tutorial-incremental-copy-portal/activity-runs.png)

## <a name="review-the-results"></a>Granska resultaten
1. Anslut till Azure Storage-kontot med verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Kontrollera att en utdatafil har skapats i mappen **incrementalcopy** i **adftutorial**-containern.

    ![Den första utdatafilen](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Öppna utdatafilen och notera att alla data har kopierats från **data_source_table** till blobfilen. 

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Kontrollera det senaste värdet från `watermarktable`. Du kan se att vattenstämpeln har uppdaterats.

    ```sql
    Select * from watermarktable
    ```
    
    Här är utdata:
 
    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 | 

## <a name="add-more-data-to-source"></a>Lägg till mer data i källan

Infoga nya data i SQL-databasen (datakällagring).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
``` 

Uppdaterade data i SQL-databasen är:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```


## <a name="trigger-another-pipeline-run"></a>Utlös ytterligare en pipelinekörning
1. Växla till fliken **Redigera**. Klicka på pipelinen i trädvyn om den inte öppnas i designern. 

    ![Knappen Trigger Now (Utlös nu)](./media/tutorial-incremental-copy-portal/edit-tab.png)
2. Klicka på **Utlösare** i verktygsfältet och klicka på **Trigger Now** (Utlös nu). 

    ![Knappen Trigger Now (Utlös nu)](./media/tutorial-incremental-copy-portal/trigger-now.png)

## <a name="monitor-the-second-pipeline-run"></a>Övervaka den andra pipelinekörningen

1. Växla till fliken **Övervaka** till vänster. Du kan se status för den pipelinekörning som utlöstes av den manuella utlösaren. Om du vill uppdatera listan klickar du på **Uppdatera**. 
    
    ![Pipelinekörningar](./media/tutorial-incremental-copy-portal/pipeline-runs-2.png)
2. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningarna klickar du på den första länken (**View activity runs** (Visa aktivitetskörningar)) i kolumnen **Action** (Åtgärd). Du kan gå tillbaka till föregående vy genom att klicka på **Pipeliner** högst upp. Om du vill uppdatera listan klickar du på **Uppdatera**.

    ![Aktivitetskörningar](./media/tutorial-incremental-copy-portal/activity-runs-2.png)

## <a name="verify-the-second-output"></a>Verifiera den andra utdata

1. I blob-lagringen ser du att en annan fil har skapats. I den här självstudien är det nya filnamnet `Incremental-<GUID>.txt`. När du öppnar filen ser du två rader med poster i den.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Kontrollera det senaste värdet från `watermarktable`. Du kan se att vattenstämpeln har uppdaterats igen.

    ```sql
    Select * from watermarktable
    ```
    exempel på utdata: 
    
    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |


     
## <a name="next-steps"></a>Nästa steg
I den här självstudien har du fått: 

> [!div class="checklist"]
> * Förbered datalagringen för att lagra värdet för vattenstämpeln.
> * Skapa en datafabrik.
> * Skapa länkade tjänster. 
> * Skapa datauppsättningar för källa, mottagare och vattenstämpel.
> * Skapa en pipeline.
> * Köra en pipeline.
> * Övervaka pipelinekörningen. 
> * Granska resultatet
> * Lägg till mer data i källan.
> * Kör pipelinen igen.
> * Övervaka den andra pipelinekörningen
> * Granska resultatet från den andra körningen

I den här självstudien kopierade en pipeline data från en enskild tabell i en SQL-databas till Blob-lagring. Fortsätt till följande självstudie för att lära dig hur du kopierar data från flera tabeller till en lokal SQL Server-databas till en SQL-databas. 

> [!div class="nextstepaction"]
>[Läs in data stegvis från flera tabeller i SQL Server till Azure SQL-databas](tutorial-incremental-copy-multiple-tables-portal.md)




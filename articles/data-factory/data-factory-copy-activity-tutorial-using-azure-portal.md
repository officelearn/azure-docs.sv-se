---
title: "Självstudier: Skapa en pipeline med en kopieringsaktivitet med hjälp av Azure-portalen | Microsoft Docs"
description: "I den här självstudien skapar du en Azure Data Factory-pipeline med en kopieringsaktivitet med hjälp av Data Factory-redigeraren på Azure-portalen."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/06/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 4b29fd1c188c76a7c65c4dcff02dc9efdf3ebaee
ms.openlocfilehash: 299a55865c1c91e664d67095de76708f444d30b9


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-portal"></a>Självstudier: Skapa en pipeline med en kopieringsaktivitet med hjälp av Azure-portalen
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

De här självstudierna visar hur du skapar och övervakar en Azure-datafabrik med hjälp av Azure-portalen. Pipelinen i datafabriken använder en kopieringsaktivitet för att kopiera data från Azure Blob Storage till Azure SQL Database.

> [!NOTE]
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Det transformerar inte indata för att generera utdata. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg en pipeline för att omvandla data med Hadoop-kluster).
> 
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory). 

Här är de steg du utför som en del av de här självstudierna:

| Steg | Beskrivning |
| --- | --- |
| [Skapa en Azure Data Factory](#create-data-factory) |I det här steget skapar du en Azure-datafabrik med namnet **ADFTutorialDataFactory**. |
| [Skapa länkade tjänster](#create-linked-services) |I det här steget skapar du två länkade tjänster: **AzureStorageLinkedService** och **AzureSqlLinkedService**. <br/><br/>AzureStorageLinkedService länkar Azure-lagringen och AzureSqlLinkedService länkar Azure SQL-databasen till ADFTutorialDataFactory. Indata för pipelinen finns i en blobbehållare i Azure Blob Storage och utdata lagras i en tabell i Azure SQL-databasen. Därför kan du lägga till dessa två datalager som länkade tjänster i datafabriken. |
| [Skapa datauppsättningar för indata och utdata ](#create-datasets) |I det föregående steget skapade du länkade tjänster som refererar till datalager som innehåller in- och utdata. I det här steget skapar du två datauppsättningar – **InputDataset** och **OutputDataset** – som visar de in- och utdata som lagras i datalagren. <br/><br/>För InputDataset anger du blobbehållaren som innehåller en blobb med källdata, och för OutputDataset anger du SQL-tabellen som lagrar utdata. Du kan också ange andra egenskaper som struktur, tillgänglighet och princip. |
| [Skapa en pipeline](#create-pipeline) |I det här steget skapar du en pipeline med namnet **ADFTutorialPipeline** i ADFTutorialDataFactory. <br/><br/>Du lägger till en **kopieringsaktivitet** till den pipeline som kopierar indata från Azure-bloben till utdata i Azure SQL-tabellen. Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten. |
| [Övervaka pipeline](#monitor-pipeline) |I det här steget ska du övervaka sektorer från indata- och utdatatabeller med hjälp av Azure-portalen. |

## <a name="prerequisites"></a>Krav
Slutför stegen i artikeln [Självstudier – översikt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) innan du fortsätter med självstudierna.

## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget använder du Azure-portalen för att skapa en Azure-datafabrik med namnet **ADFTutorialDataFactory**.

1. När du har loggat in på [Azure-portalen](https://portal.azure.com/) klickar du på **Ny**, väljer **Information + analys** och klickar på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. På bladet **Ny datafabrik**:
   
   1. Ange **ADFTutorialDataFactory** som **namn**. 
      
         ![Bladet Ny datafabrik](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Datafabriksnamnet är inte tillgängligt](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Välj din Azure-**prenumeration**.
   3. För resursgruppen utför du något av följande steg:
      
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
          Vissa av stegen i den här självstudien förutsätter att du använder namnet: **ADFTutorialResourceGroup** på resursgruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
   4. Välj **plats** för datafabriken. Endast regioner som stöds av tjänsten Data Factory visas i listrutan.
   5. Välj **Fäst på Startsidan**.     
   6. Klicka på **Skapa**.
      
      > [!IMPORTANT]
      > Om du vill skapa Data Factory-instanser måste du vara medlem i [Data Factory-deltagarrollen](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) på gruppnivå/resursgrupp.
      > 
      > Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.                
      > 
      > 
3. Om du vill se meddelanden för status/avisering klickar du på klockikonen i verktygsfältet. 
   
   ![Meddelanden](./media/data-factory-copy-activity-tutorial-using-azure-portal/Notifications.png) 
4. När datafabriken har skapats visas **Datafabrik**-bladet som på bilden.
   
   ![Datafabrikens startsida](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster
Länkade tjänster länkar datalager eller Compute Services till en Azure-datafabrik. I [stödda datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) står alla källor och mottagare som stöds av Kopiera aktivitet. Se [Beräkna länkade tjänster](data-factory-compute-linked-services.md) för att se listan över Compute Services som stöds av Data Factory. I den här självstudiekursen använder du ingen tjänst för beräkning. 

I det här steget skapar du två länkade tjänster: **AzureStorageLinkedService** och **AzureSqlLinkedService**. Den länkade AzureStorageLinkedService-tjänsten länkar ett Azure-lagringskonto och AzureSqlLinkedService länkar en Azure SQL-databas till **ADFTutorialDataFactory**. Du ska skapa en pipeline senare i den här självstudiekursen som kopierar data från en blobbehållare i AzureStorageLinkedService till en SQL-tabell i AzureSqlLinkedService.

### <a name="create-a-linked-service-for-the-azure-storage-account"></a>Skapa en länkad tjänst för Azure-lagringskontot
1. På **Datafabrik**-bladet klickar du på **Författare och distribution** för att starta **Redigeraren** för datafabriken.
   
   ![Ikonen Författare och distribution](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. I **Redigeraren** klickar du på knappen **Nytt datalager** i verktygsfältet och väljer **Azure-lagring** på den nedrullningsbara menyn. Du bör se JSON-mallen för att skapa en länkad Azure-lagringstjänst i den högra rutan. 
   
    ![Redigerarens knapp Nytt datalager](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Ersätt `<accountname>` och `<accountkey>` med namnet på ditt Azure-lagringskonto och dess nyckelvärden. 
   
    ![JSON för redigerarens blobblagring](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Klicka på **Distribuera** i verktygsfältet. Du bör se den distribuerade **AzureStorageLinkedService** i trädvyn nu. 
   
    ![Distribuera redigerarens blobblagring](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

> [!NOTE]
> I [Move data from/to Azure Blob (Flytta data från/till Azure Blob)](data-factory-azure-blob-connector.md#azure-storage-linked-service) finns mer information om JSON-egenskaper.
> 
> 

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Skapa en länkad tjänst för Azure SQL Database
1. I **Data Factory-redigeraren** klickar du på knappen **Nytt datalager** i verktygsfältet och väljer **Azure SQL Database** i listrutan. Du bör se JSON-mallen för att skapa en länkad Azure SQL-tjänst i den högra rutan.
2. Ersätt `<servername>`, `<databasename>`, `<username>@<servername>` och `<password>` med namnen på din Azure SQL-server, databas, ditt användarkonto och lösenord. 
3. Klicka på **Distribuera** i verktygsfältet för att skapa och distribuera **AzureSqlLinkedService**.
4. Bekräfta att du ser **AzureSqlLinkedService** i trädvyn. 

> [!NOTE]
> Se [Move data from/to Azure SQL Database (Flytta data från/till Azure SQL Database)](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) för mer information om JSON-egenskaper.
> 
> 

## <a name="create-datasets"></a>Skapa datauppsättningar
I föregående steg skapade du de länkade tjänsterna **AzureStorageLinkedService** och **AzureSqlLinkedService** för att länka ett Azure-lagringskonto och en Azure SQL-databas till datafabriken: **ADFTutorialDataFactory**. I det här steget definierar du två datauppsättningar – **InputDataset** och **OutputDataset** – som visar in- och utdata som lagras i de datalager som refereras till av AzureStorageLinkedService och AzureSqlLinkedService. För InputDataset anger du blobbehållaren som innehåller en blobb med källdata, och för OutputDataset anger du SQL-tabellen som lagrar utdata. 

### <a name="create-input-dataset"></a>Skapa indatauppsättning
I det här steget skapar du en datauppsättning med namnet **InputDataset** som pekar på en blobbehållare i Azure Storage som representeras av den länkade tjänsten **AzureStorageLinkedService**.

1. I **redigeringsprogrammet** för Data Factory klickar du på **... More (Mer)**, **Ny datauppsättning** och sedan på **Azure Blob Storage** i listrutan. 
   
    ![Menyn Ny datauppsättning](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment: 
   
    ```JSON
    {
      "name": "InputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```   
    Observera följande punkter: 
   
   * datauppsättningens **typ** anges till **AzureBlob**.
   * **linkedServiceName** anges till **AzureStorageLinkedService**. Du skapade den här länkade tjänsten i steg 2.
   * **folderPath** anges till behållaren **adftutorial**. Du kan också ange namnet på en blobb i mappen med egenskapen **fileName**. Eftersom du inte anger namnet på någon blobb, anses data från alla blobbar i behållaren vara indata.  
   * formatet **typ** anges till **TextFormat**
   * Det finns två fält i textfilen – **FirstName** och **LastName** – som avgränsas med ett kommatecken (**columnDelimiter**)    
   * **availability** är inställt på **hourly** (**frequency** är inställt på **hour** och **interval** är inställt på **1**). Det betyder att Data Factory söker efter indata varje timme i rotmappen för den angivna blobbehållaren (**adftutorial**). 
     
     Om du inte anger något **fileName** för en **indatauppsättning** betraktas alla filer/blobbar från indatamappen (**folderPath**) som indata. Om du anger ett fileName i JSON, anses endast den angivna filen/blobben vara indata.
     
     Om du inte anger något **fileName** för en **utdatatabell** namnges filerna som genereras i **folderPath** med följande format: Data.&lt;Guid&gt;.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).
     
     Ange **folderPath** och **fileName** dynamiskt utifrån **SliceStart**-tiden med hjälp av **partitionedBy**-egenskapen. I följande exempel använder folderPath Year, Month och Day från SliceStart (starttiden för den sektor som bearbetas) och fileName använder Hour från SliceStart. Om exempelvis en sektor produceras 2016-09-20T08:00:00, anges folderName till wikidatagateway/wikisampledataout/2016/09/20 och fileName anges till 08.csv. 

    ```JSON     
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
       { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
       { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
       { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
       { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],
    ```
3. Klicka på **Distribuera** i verktygsfältet för att distribuera **InputDataset**. Kontrollera att du ser **InputDataset** i trädvyn.

> [!NOTE]
> I [Move data from/to Azure Blob (Flytta data från/till Azure Blob)](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) finns mer information om JSON-egenskaper.
> 
> 

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
I den här delen av steget ska du skapa en utdatauppsättning med namnet **OutputDataset**. Den här datauppsättningen pekar på en SQL-tabell i Azure SQL-databasen som representeras av **AzureSqlLinkedService**. 

1. I **redigeringsprogrammet** för Data Factory klickar du på **... More (Mer)**, **Ny datauppsättning** och sedan på **Azure SQL** i listrutan. 
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment:

    ```JSON   
    {
      "name": "OutputDataset",
      "properties": {
        "structure": [
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "emp"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }
    ```     
    Observera följande punkter: 
   
   * datauppsättningens **typ** anges till **AzureSQLTable**.
   * **linkedServiceName** har angetts till **AzureSqlLinkedService** (du skapade den här länkade tjänsten i steg 2).
   * **tablename** anges till **emp**.
   * Det finns tre kolumner – **ID**, **FirstName** och **LastName** – i emp-tabellen i databasen. ID är en identitetskolumn, så du anger bara **FirstName** och **LastName** här.
   * **Tillgängligheten** anges till **varje timme** (**frekvens** inställd på **timme** och **intervall** inställd på **1**).  Data Factory-tjänsten genererar en utdatasektor varje timme i **emp**-tabellen i Azure SQL-databasen.
3. Klicka på **Distribuera** i verktygsfältet för att distribuera **OutputDataset**. Kontrollera att du ser **OutputDataset** i trädvyn. 

> [!NOTE]
> Se [Move data from/to Azure SQL Database (Flytta data från/till Azure SQL Database)](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) för mer information om JSON-egenskaper.
> 
> 

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget ska du skapa en pipeline med en **kopieringsaktivitet** som använder **InputDataset** som indata och **OutputDataset** som utdata.

1. I **redigeringsprogrammet** för Data Factory klickar du på **... More (Mer)** och sedan på **Ny pipeline**. Alternativt kan du högerklicka på **Pipelines** i trädvyn och klicka på **Ny pipeline**.
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment: 

    ```JSON   
    {
      "name": "ADFTutorialPipeline",
      "properties": {
        "description": "Copy data from a blob to Azure SQL table",
        "activities": [
          {
            "name": "CopyFromBlobToSQL",
            "type": "Copy",
            "inputs": [
              {
                "name": "InputDataset"
              }
            ],
            "outputs": [
              {
                "name": "OutputDataset"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "SqlSink",
                "writeBatchSize": 10000,
                "writeBatchTimeout": "60:00:00"
              }
            },
            "Policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2016-07-12T00:00:00Z",
        "end": "2016-07-13T00:00:00Z"
      }
    } 
    ```   
    
    Observera följande punkter:
   
   * I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**.
   * Indata för aktiviteten är inställd på **InputDataset** och utdata för aktiviteten är inställd på **OutputDataset**.
   * I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp.
     
     Ersätt värdet i **start**egenskapen med den aktuella dagen och **slut**värdet med nästa dag. Du kan ange endast datumdelen och hoppa över tidsvärdet. Till exempel ”2016-02-03” som motsvarar ”2016-02-03T00:00:00Z”
     
     Både start- och slutdatum måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2016-10-14T16:32:41Z. **Sluttiden** är valfri, men vi använder den i den här självstudiekursen. 
     
     Om du inte anger värdet för **slut**egenskapen, beräknas det som ”**start + 48 timmar**”. Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut**egenskapen.
     
     I det föregående exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.
3. Klicka på **Distribuera** i verktygsfältet för att distribuera **ADFTutorialPipeline**. Kontrollera att du ser pipelinen i trädvyn. 
4. Stäng nu bladet **Redigerare** genom att klicka på **X**. Klicka på **X** igen för att se startsidan för **Data Factory** för **ADFTutorialDataFactory**.

**Grattis!** Du har skapat en Azure-datafabrik, länkade tjänster, tabeller och en pipeline som du har schemalagt.   

### <a name="view-the-data-factory-in-a-diagram-view"></a>Visa datafabriken i en diagramvy
1. På **Data Factory**-bladet klickar du på **Diagram**.
   
    ![Data Factory-bladet – Diagramikon](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Du bör se ett diagram som liknar följande bild: 
   
    ![Diagramvy](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)
   
    Du kan zooma in, zooma ut, zooma till 100 %, anpassa zoomningen, placera pipelines och tabeller automatiskt samt visa härkomstinformation (markerar överordnade och underordnade objekt för de valda objekten).  Du kan dubbelklicka på ett objekt (in-/utdatatabell eller pipeline) för att se dess egenskaper. 
3. Högerklicka på **ADFTutorialPipeline** i diagramvyn och klicka på **Öppna pipeline**. 
   
    ![Öppna pipeline](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)
4. Du bör se aktiviteterna i pipelinen tillsammans med indata och utdata i datauppsättningar för aktiviteterna. I den här självstudien får du endast ha en aktivitet i pipelinen (kopieringsaktiviteten) med InputDataset som indatauppsättning och OutputDataset som utdatauppsättning.   
   
    ![Vyn Opened pipeline (Öppnad pipeline)](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenedPipeline.png)
5. Klicka på **Datafabrik** i länken i det övre vänstra hörnet för att återgå till diagramvyn. I diagramvyn visas alla pipelines. I det här exemplet har du bara skapat en pipeline.   

## <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget ska du använda Azure-portalen för att övervaka vad som händer i en Azure-datafabrik. 

### <a name="monitor-pipeline-using-diagram-view"></a>Övervaka pipeline med diagramvyn
1. Klicka på **X** för att stänga vyn **Diagram** och se Data Factory-startsidan för datafabriken. Om du har stängt webbläsaren utför du följande steg: 
   1. Navigera till [Azure-portalen](https://portal.azure.com/). 
   2. Dubbelklicka på **ADFTutorialDataFactory** på **startsidan** (eller) klicka på **Datafabriker** på den vänstra menyn och sök efter ADFTutorialDataFactory. 
2. Du bör se antal och namn på de tabeller och pipelines som du skapade i det här bladet.
   
    ![startsida med namn](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png)
3. Klicka på ikonen **Datauppsättningar**.
4. På bladet **Datauppsättningar** klickar du på **InputDataset**. Den här datauppsättningen är indatauppsättningen för **ADFTutorialPipeline**.
   
    ![Datauppsättningar med InputDataset valt](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Klicka på **... (tre punkter)** för att se alla datasegment.
   
    ![Alla indatasektorer](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Observera att alla datasektorer upp till aktuell tid är **klara** eftersom filen **emp.txt** alltid finns i blobbehållaren: **adftutorial\input**. Kontrollera att inga sektorer visas i avsnittet **Nyligen misslyckade sektorer** längst ned.
   
    Båda listorna **Nyligen uppdaterade sektorer** och **Nyligen misslyckade sektorer** sorteras efter **SENASTE UPPDATERINGSTID**. 
   
    Klicka på **Filter** i verktygsfältet för att filtrera sektorerna.  
   
    ![Filtrera inkommande segment](./media/data-factory-copy-activity-tutorial-using-azure-portal/filter-input-slices.png)
6. Stäng bladen till du ser bladet **Datauppsättningar**. Klicka på **OutputDataset**. Den här datauppsättningen är utdatauppsättningen för **ADFTutorialPipeline**.
   
    ![bladet datauppsättningar](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png)
7. Bladet **OutputDataset** bör visas, som du ser i följande bild:
   
    ![tabellblad](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png) 
8. Observera att datasektorerna upp till den aktuella tiden redan har skapats och är **klara**. Inga sektorer visas i avsnittet **Problemsektorer** längst ned.
9. Klicka på **... (tre punkter)** för att se alla sektorer.
   
    ![datasektorblad](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png)
10. Klicka på någon av datasektorerna i listan. Bladet **Datasektor** visas.
    
     ![datasektorblad](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Om sektorn inte har statusen **Klar** kan du se sektorer uppströms som inte är klara och som blockerar aktuell sektor från att köras i listan **Sektorer uppströms som inte är klara**.
11. På bladet **DATASEKTOR** bör du se alla aktivitetskörningar i listan längst ned. Öppna bladet **Aktivitetskörningsinformation** genom att klicka på en **aktivitetskörning**. 
    
    ![Aktivitetskörningsinformation](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)
12. Klicka på **X** för att stänga alla blad tills du kommer tillbaka till startbladet för **ADFTutorialDataFactory**.
13. (valfritt) Klicka på **Pipelines** på startsidan för **ADFTutorialDataFactory**, klicka på **ADFTutorialPipeline** på bladet **Pipelines** och sök i indatatabellerna (**Förbrukning**) eller utdatatabellerna (**Producerat**).
14. Starta **SQL Server Management Studio**, anslut till Azure SQL Database och kontrollera att raderna infogas i **emp**-tabellen i databasen.
    
    ![sql-frågeresultat](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Övervaka pipeline med övervaknings- och hanteringsappen
Du kan också använda övervaknings- och hanteringsprogrammet till att övervaka dina pipelines. Se [Övervaka och hantera Azure Data Factory-pipelines med övervaknings- och hanteringsappen](data-factory-monitor-manage-app.md) för mer information om att använda programmet.

1. Klicka på ikonen **Övervaka och hantera** på datafabrikens startsida.
   
    ![Ikonen Övervaka och hantera](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Du bör se **Övervakaren och hantera program**. Ändra **Starttid** och **Sluttid** så att de inkluderar starttid (2016-07-12) och sluttid (2016-07-13) i pipelinen. Klicka sedan på **Tillämpa**. 
   
    ![Appen Övervaka och hantera](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png) 
3. Välj ett aktivitetsfönster i listan **Aktivitetsfönster** om du vill se information om det. 
    ![Aktivitetsfönsterinformation](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

## <a name="summary"></a>Sammanfattning
I den här självstudien har du skapat en Azure-datafabrik som kopierar data från en Azure-blobb till en Azure SQL-databas. Du använde Azure-portalen för att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Här är de avancerade steg som du utförde i självstudien:  

1. Du skapade en Azure **Data Factory**.
2. Du skapade **länkade tjänster**:
   1. En länkad **Azure Storage-**tjänst som länkar Azure Storage-kontot som innehåller indata.     
   2. En länkad **Azure SQL**-tjänst som länkar din Azure SQL-databas som innehåller utdata. 
3. Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4. Du skapade en **pipeline** med en **kopieringsaktivitet** med **BlobSource** som källa och **SqlSink** som mottagare.  

## <a name="see-also"></a>Se även
| Avsnitt | Beskrivning |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Den här artikeln beskriver pipelines och aktiviteter i Azure Data Factory. |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |



<!--HONumber=Feb17_HO1-->



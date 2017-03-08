---
title: "Självstudier: Skapa en pipeline med en kopieringsaktivitet med hjälp av Visual Studio | Microsoft Docs"
description: "I de här självstudierna skapar du en Azure Data Factory-pipeline med en kopieringsaktivitet genom att använda Visual Studio."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 7196b4eddc8a00cf2c15e8d8447ef8381db738a3
ms.openlocfilehash: a1ef4ccb6546f011c405a98cab3cae514de813ea
ms.lasthandoff: 03/02/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Självstudie: Skapa en pipeline med en kopieringsaktivitet med hjälp av Visual Studio
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

De här självstudierna visar hur du skapar och övervakar en Azure-datafabrik med hjälp av Visual Studio. Pipelinen i datafabriken använder en kopieringsaktivitet för att kopiera data från Azure Blob Storage till Azure SQL Database.

> [!NOTE]
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Det transformerar inte indata för att generera utdata. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build your first pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg din första pipeline för att omvandla data med Hadoop-kluster).
> 
> Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory).

Här är de steg du utför som en del av de här självstudierna:

1. Skapa två länkade tjänster: **AzureStorageLinkedService1** och **AzureSqlinkedService1**. 
   
    AzureStorageLinkedService1 länkar en Azure Storage och AzureSqlLinkedService1 länkar en Azure SQL-databas till datafabriken: **ADFTutorialDataFactoryVS**. Indatan för pipelinen finns i en blobbehållare i Azure Blob Storage och utdata lagras i en tabell i Azure SQL-databasen. Därför kan du lägga till dessa två datalager som länkade tjänster i datafabriken.
2. Skapa två datauppsättningar: **InputDataset** och **OutputDataset**, som visar de in- och utdata som lagras i datalagren. 
   
    För InputDataset anger du den blobbehållare som innehåller en blobb med källdata. För OutputDataset anger du den SQL-tabell som lagrar utdata. Du kan också ange andra egenskaper som struktur, tillgänglighet och princip.
3. Skapa en pipeline med namnet **ADFTutorialPipeline** i ADFTutorialDataFactoryVS. 
   
    Pipelinen ska ha en **kopieringsaktivitet** som kopierar indata från Azure-bloben till utdata i Azure SQL-tabellen. Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten. 
4. Skapa en datafabrik med namnet **VSTutorialFactory**. Distribuera en datafabrik och alla Data Factory-enheter (länkade tjänster, tabeller och pipelinen).    

## <a name="prerequisites"></a>Krav
1. Läs igenom artikeln [Självstudier – översikt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) och slutför de **nödvändiga** stegen. 
2. Du måste vara **administratör för Azure-prenumerationen** för att kunna publicera Data Factory-enheter i Azure Data Factory.  
3. Du måste ha följande installerat på datorn: 
   * Visual Studio 2013 eller Visual Studio 2015
   * Hämta Azure SDK för Visual Studio 2013 eller Visual Studio 2015. Gå till [Azures hämtningssida](https://azure.microsoft.com/downloads/) och klicka på **VS 2013** eller **VS 2015** i **.NET**-avsnittet.
   * Hämta det senaste Azure Data Factory-plugin-programmet för Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Du kan även uppdatera plugin-programmet genom att göra följande: På menyn klickar du på **Verktyg** -> **Tillägg och uppdateringar** -> **Online** -> **Visual Studio-galleriet** -> **Microsoft Azure Data Factory-verktyg för Visual Studio** -> **Uppdatera**.

## <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt
1. Starta **Visual Studio 2013**. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**. Dialogrutan **Nytt projekt** visas.  
2. I dialogrutan **Nytt projekt** väljer du mallen **DataFactory** och klickar på **Tomt Data Factory-projekt**. Om du inte ser DataFactory-mallen stänger du Visual Studio, installerar Azure SDK för Visual Studio 2013 och öppnar Visual Studio på nytt.  
   
    ![Dialogrutan Nytt projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Ange ett **namn** för projektet, en **plats** och ett namn för **lösningen**. Klicka på **OK**.
   
    ![Solution Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Skapa länkade tjänster
Länkade tjänster länkar datalager eller Compute Services till en Azure-datafabrik. I [stödda datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) står alla källor och mottagare som stöds av Kopiera aktivitet. Se [Beräkna länkade tjänster](data-factory-compute-linked-services.md) för att se listan över Compute Services som stöds av Data Factory. I den här självstudiekursen använder du ingen tjänst för beräkning. 

I det här steget skapar du två länkade tjänster: **AzureStorageLinkedService1** och **AzureSqlLinkedService1**. Den länkade AzureStorageLinkedService1-tjänsten länkar ett Azure Storage-konto och AzureSqlLinkedService länkar en Azure SQL-databas till datafabriken: **ADFTutorialDataFactory**. 

### <a name="create-the-azure-storage-linked-service"></a>Skapa den länkade Azure Storage-tjänsten
1. Högerklicka på **Länkade tjänster** i Solution Explorer, peka på **Lägg till** och klicka på **Nytt objekt**.      
2. I dialogrutan **Lägg till nytt objekt** väljer du **Länkad Azure Storage-tjänst** i listan och klickar på **Lägg till**. 
   
    ![Ny länkad tjänst](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. Ersätt `<accountname>` och `<accountkey>`* med namnet på ditt Azure Storage-konto och dess nyckel. 
   
    ![Länkad Azure Storage-tjänst](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. Spara filen **AzureStorageLinkedService1.json**.

> I [Move data from/to Azure Blob (Flytta data från/till Azure Blob)](data-factory-azure-blob-connector.md#azure-storage-linked-service) finns mer information om JSON-egenskaper.
> 
> 

### <a name="create-the-azure-sql-linked-service"></a>Skapa den länkade Azure SQL-tjänsten
1. Högerklicka på noden **Länkade tjänster** i **Solution Explorer** igen, peka på **Lägg till** och klicka på **Nytt objekt**. 
2. Den här gången väljer du **Länkad Azure SQL-tjänst** och klickar på **Lägg till**. 
3. I filen **AzureSqlLinkedService1.json file** ersätter du `<servername>`, `<databasename>`, `<username@servername>` och `<password>` med namnen på din Azure SQL-server, databas, ditt användarkonto och lösenord.    
4. Spara filen **AzureSqlLinkedService1.json**. 

> [!NOTE]
> Se [Move data from/to Azure SQL Database (Flytta data från/till Azure SQL Database)](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) för mer information om JSON-egenskaper.
> 
> 

## <a name="create-datasets"></a>Skapa datauppsättningar
I föregående steg skapade du de länkade tjänsterna **AzureStorageLinkedService1** och **AzureSqlLinkedService1** för att länka ett Azure Storage-konto och en Azure SQL-databas till datafabriken: **ADFTutorialDataFactory**. I det här steget definierar du två datauppsättningar – **InputDataset** och **OutputDataset** – som visar in- och utdata som lagras i de datalager som refereras till av AzureStorageLinkedService1 och AzureSqlLinkedService1. För InputDataset anger du den blobbehållare som innehåller en blobb med källdata. För OutputDataset anger du den SQL-tabell som lagrar utdata.

### <a name="create-input-dataset"></a>Skapa indatauppsättning
I det här steget skapar du en datauppsättning med namnet **InputDataset** som pekar på en blobbehållare i Azure Storage som representeras av den länkade tjänsten **AzureStorageLinkedService1**. En tabell är en rektangulär datauppsättning och är den enda typen av datauppsättning som stöds för tillfället. 

1. Högerklicka på **Tabeller** i **Solution Explorer**, peka på **Lägg till** och klicka på **Nytt objekt**.
2. I dialogrutan **Lägg till nytt objekt** väljer du **Azure-blobb** och klickar på **Lägg till**.   
3. Ersätt texten JSON med följande text och spara filen **AzureBlobLocation1.json**. 

  ```json   
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
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
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
  
    ```json   
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ```
            
> [!NOTE]
> I [Move data from/to Azure Blob (Flytta data från/till Azure Blob)](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) finns mer information om JSON-egenskaper.
> 
> 

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
I det här steget ska du skapa en utdatauppsättning med namnet **OutputDataset**. Den här datauppsättningen pekar på en SQL-tabell i Azure SQL-databasen som representeras av **AzureSqlLinkedService1**. 

1. Högerklicka på **Tabeller** i **Solution Explorer** igen, peka på **Lägg till** och klicka på **Nytt objekt**.
2. I dialogrutan **Lägg till nytt objekt** väljer du **Azure SQL** och klickar på **Lägg till**. 
3. Ersätt texten JSON med följande JSON och spara filen **AzureSqlTableLocation1.json**.

    ```json
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
       "linkedServiceName": "AzureSqlLinkedService1",
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

> [!NOTE]
> Se [Move data from/to Azure SQL Database (Flytta data från/till Azure SQL Database)](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) för mer information om JSON-egenskaper.
> 
> 

## <a name="create-pipeline"></a>Skapa pipeline
Du har skapat länkade in-/utdatatjänster och tabeller hittills. Nu skapar du en pipeline med en **kopieringsaktivitet** som kopierar data från Azure-bloben till Azure SQL Database. 

1. Högerklicka på **Pipelines** i **Solution Explorer**, peka på **Lägg till** och klicka på **Nytt objekt**.  
2. Välj **Kopiera datapipeline** i dialogrutan **Lägg till nytt objekt** och klicka på **Lägg till**. 
3. Ersätt JSON med följande JSON och spara filen **CopyActivity1.json**.

    ```json   
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
             "style": "StartOfInterval",
             "retry": 0,
             "timeout": "01:00:00"
           }
         }
       ],
       "start": "2015-07-12T00:00:00Z",
       "end": "2015-07-13T00:00:00Z",
       "isPaused": false
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

## <a name="publishdeploy-data-factory-entities"></a>Publicera/distribuera Data Factory-entiteter
I det här steget publicerar du Data Factory-enheter (länkade tjänster, datauppsättningar och pipeline) som du skapade tidigare. Du kan även ange namnet på en ny datafabrik som ska skapas för dessa enheter.  

1. I Solution Explorer högerklickar du på projektet och klickar sedan på **Publicera**. 
2. Om du ser dialogrutan **Logga in på ditt Microsoft-konto**, anger du dina autentiseringsuppgifter för det konto som har Azure-prenumerationen. Klicka sedan på **Logga in**.
3. Du bör se följande dialogruta:
   
   ![Dialogrutan Publicera](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. På sidan Konfigurera datafabrik går du igenom följande steg: 
   
   1. välj alternativet **Skapa ny Data Factory**.
   2. Ange **VSTutorialFactory** som **namn**.  
      
      > [!IMPORTANT]
      > Namnet på Azure Data Factory måste vara globalt unikt. Ändra namnet på datafabriken (till exempel yournameVSTutorialFactory) och försök att publicera igen, om du får ett felmeddelande om namnet på datafabriken när du publicerar. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.        
      > 
      > 
   3. Välj din Azure-prenumeration i fältet **Prenumeration**.
      
      > [!IMPORTANT]
      > Om du inte ser någon prenumeration kontrollerar du att du har loggat in med ett konto som är en administratör eller en medadministratör för prenumerationen.  
      > 
      > 
   4. Välj **resursgrupp** för datafabriken som ska skapas. 
   5. Välj **region** för datafabriken. Endast regioner som stöds av tjänsten Data Factory visas i listrutan.
   6. Klicka på **Nästa** för att växla till sidan **Publicera objekt**.
      
       ![Konfigurera Data Factory-sida](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. På sidan **Publicera objekt** kontrollerar du att alla datafabriksentiteter har valts. Klicka på **Nästa** för att växla till sidan **Sammanfattning**.
   
   ![Sidan Publish items (Publicera objekt)](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Granska sammanfattningen och klicka på **Nästa** för att starta distributionsprocessen och visa **Distributionsstatus**.
   
   ![Sidan Publish summary (Publicera översikt)](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. På sidan **Distributionsstatus** bör du se statusen för distributionen. Klicka på Slutför när distributionen är klar.
 
   ![Statussida för distribution](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Observera följande punkter: 

* Om du får felet: ”**Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory**” gör du något av följande och försöker att publicera igen: 
  
  * I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern. 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad. 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Logga in med Azure-prenumerationen i [Azure Portal](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure Portal. Med den här åtgärden registreras providern automatiskt.
* Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.

> [!IMPORTANT]
> Om du vill skapa Data Factory-instanser måste du vara administratör/medadministratör för Azure-prenumerationen
> 
> 

## <a name="summary"></a>Sammanfattning
I den här självstudien har du skapat en Azure-datafabrik som kopierar data från en Azure-blobb till en Azure SQL-databas. Du använde Visual Studio till att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Här är de avancerade steg som du utförde i självstudierna:  

1. Du skapade en Azure **Data Factory**.
2. Du skapade **länkade tjänster**:
   1. En länkad **Azure Storage-**tjänst som länkar Azure Storage-kontot som innehåller indata.     
   2. En länkad **Azure SQL**-tjänst som länkar din Azure SQL-databas som innehåller utdata. 
3. Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4. Du skapade en **pipeline** med en **kopieringsaktivitet** med **BlobSource** som källa och **SqlSink** som mottagare. 

## <a name="use-server-explorer-to-view-data-factories"></a>Använda Server Explorer för att visa datafabriker
1. I **Visual Studio** klickar du på **Visa** i menyn och därefter på **Server Explorer**.
2. I fönstret Server Explorer expanderar du **Azure** och **Data Factory**. Om du ser **Logga in till Visual Studio** anger du det **konto** som är associerat med din Azure-prenumeration och klickar på **Fortsätt**. Ange **lösenordet** och klicka på **Logga in**. Visual Studio försöker hämta information om alla Azure-datafabriker i din prenumeration. Du ser statusen för den här åtgärden i fönstret **Uppgiftslista för Data Factory**.

    ![Server Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Du kan också högerklicka på en datafabrik och välja Exportera Data Factory till nytt projekt, för att skapa ett Visual Studio-projekt som baseras på en befintlig datafabrik.

    ![Exportera datafabrik till ett VS-projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Uppdatera Data Factory-verktyg för Visual Studio
Om du vill uppdatera Azure Data Factory-verktyg för Visual Studio går du igenom följande steg:

1. Klicka på **Verktyg** i menyn och välj **Tillägg och uppdateringar**. 
2. Välj **Uppdateringar** i den vänstra rutan och välj sedan **Visual Studio-galleriet**.
3. Välj **Azure Data Factory-verktyg för Visual Studio** och klicka på **Uppdatera**. Om du inte ser den här posten har du redan den senaste versionen av verktygen. 

Se [Övervaka datauppsättningar och pipeline](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) för instruktioner om hur du använder Azure Portal till att övervaka pipeline och datauppsättningar som du har skapat i den här självstudien.

## <a name="see-also"></a>Se även
| Avsnitt | Beskrivning |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Den här artikeln beskriver pipelines och aktiviteter i Azure Data Factory |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) |Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. |



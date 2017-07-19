---
title: "Självstudier: Skapa en Azure Data Factory-pipeline för att kopiera data (Azure portal) | Microsoft Docs"
description: "I den här självstudiekursen kommer du att använda Azure Portal för att skapa en Azure-datafabrik och kopiera data från ett Azure-blobb till en Azure SQL-databas."
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
ms.date: 07/10/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 8072a863fab0b304ccbbba639aa56b403e8f37c7
ms.contentlocale: sv-se
ms.lasthandoff: 07/10/2017


---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>Självstudier: Använd Azure Portal för att skapa Data Factory-pipeline för att kopiera data 
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

I den här artikeln får du lära dig hur du använder [Azure-portalen](https://portal.azure.com) för att skapa en datafabrik med en pipeline som kopierar data från en Azure-bloblagring till en Azure SQL-databas. Om du inte har använt Azure Data Factory, bör du läsa igenom artikeln [Introduktion till Azure Data Factory](data-factory-introduction.md) innan du genomför den här självstudien.   

I den här självstudien får du skapa en pipeline i en aktivitet: kopieringsaktivitet. Kopieringsaktiviteten kopierar data från källans datalager till mottagarens datalager. En lista över datakällor som stöds som källor och mottagare finns i [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten.

En pipeline kan ha fler än en aktivitet. Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer information finns i [flera aktiviteter i en pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg en pipeline för att omvandla data med Hadoop-kluster).

## <a name="prerequisites"></a>Krav
Slutför stegen i artikeln [Självstudier – förhandskrav](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) innan du fortsätter med självstudierna.

## <a name="steps"></a>Steg
Här är de steg du utför som en del av de här självstudierna:

1. Skapa en Azure-**datafabrik**. I det här steget skapar du en datafabrik med namnet ADFTutorialDataFactory. 
2. Skapa **länkade tjänster** i den här datafabriken. I det här steget kan du skapa två länkade tjänster: Azure Storage och Azure SQL-databas. 
    
    AzureStorageLinkedService länkar ditt Azure Storage-konto till datafabriken. Du har skapat en behållare och överfört data till det här lagringskontot som en del av [förhandskraven](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

    AzureSqlLinkedService länkar din Azure SQL-databas till datafabriken. Data som kopieras från blob-lagringen sparas i den här databasen. Du har skapat den SQL-tabellen i den här databasen som en del av [förhandskraven](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   
3. Skapa **datauppsättningar** för indata och utdata i datafabriken.  
    
    Den länkade Azure storage-tjänsten anger anslutningssträngen som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure storage-konto. Och en Azure Blob-datauppsättning anger vilken blobbehållare och mapp som innehåller data.  

    Den länkade Azure SQL-databasen anger anslutningssträngen som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure SQL-databas. Och utdatauppsättningen för SQL-tabellen anger tabellen i databasen som data kopieras till från blob-lagringen.
4. Skapa en **pipeline** i datafabriken. I det här steget kan du skapa en pipeline med en kopieringsaktivitet.   
    
    Kopieringsaktiviteten kopierar data från en Azure-blob till en tabell i Azure SQL-databasen. Du kan använda en kopieringsaktivitet i en pipeline för att kopiera data från alla datakällor som stöds till ett mål som stöds. I avsnittet [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md#supported-data-stores-and-formats) finns en lista över datalager som stöds. 
5. Övervaka pipeline. I det här steget ska du **övervaka** sektorer från indata- och utdatauppsättningar med hjälp av Azure-portalen. 

## <a name="create-data-factory"></a>Skapa en datafabrik
> [!IMPORTANT]
> Slutför [förutsättningarna för självstudiekursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) om du inte redan har utfört dessa.   

En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Det kan exempelvis vara en kopieringsaktivitet som kopierar data från en källa till ett måldataarkiv och en HDInsight Hive-aktivitet som kör Hive-skript för att transformera indata till produktutdata. Låt oss börja med att skapa datafabriken i det här steget.

1. När du har loggat in på [Azure-portalen](https://portal.azure.com/) klickar du på **Ny**, väljer **Data + analys** och klickar på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. På bladet **Ny datafabrik**:
   
   1. Ange **ADFTutorialDataFactory** som **namn**. 
      
         ![Bladet Ny datafabrik](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Datafabriksnamnet är inte tillgängligt](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
   3. För **resursgruppen** utför du något av följande steg:
      
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
          Vissa av stegen i den här självstudien förutsätter att du använder namnet: **ADFTutorialResourceGroup** på resursgruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
   4. Välj **plats** för datafabriken. Endast regioner som stöds av tjänsten Data Factory visas i listrutan.
   5. Välj **fäst till instrumentpanelen**.     
   6. Klicka på **Skapa**.
      
      > [!IMPORTANT]
      > Om du vill skapa Data Factory-instanser måste du vara medlem i [Data Factory-deltagarrollen](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) på gruppnivå/resursgrupp.
      > 
      > Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.                
      > 
      > 
3. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. När datafabriken har skapats visas **Datafabrik**-bladet som på bilden.
   
   ![Datafabrikens startsida](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här självstudiekursen kommer använder du inte någon beräkningstjänst, till exempel Azure HDInsight eller Azure Data Lake Analytics. Du använder två datalager av typen Azure Storage (källa) och Azure SQL Database (mål). 

Därför kan du skapa två länkade tjänster som heter AzureStorageLinkedService och AzureSqlLinkedService av typerna: AzureStorage och AzureSqlDatabase.  

AzureStorageLinkedService länkar ditt Azure Storage-konto till datafabriken. Använd det lagringskonto i vilket du skapade en behållare och laddade upp data under [förberedelsestegen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

AzureSqlLinkedService länkar din Azure SQL-databas till datafabriken. Data som kopieras från blob-lagringen sparas i den här databasen. Du har skapat den tomma tabellen i den här databasen som en del av [förhandskraven](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).  

### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
I det här steget länkar du ditt Azure-lagringskonto till datafabriken. Du anger namnet och nyckeln för Azure Storage-kontot i det här avsnittet.  

1. Klicka på **Författare och distribution** på bladet **Data Factory**.
   
   ![Ikonen Författare och distribution](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. Du ser **Data Factory-redigeraren** som visas i följande bild: 

    ![Data Factory-redigeraren](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. I Redigeraren klickar du på knappen **Nytt datalager** i verktygsfältet och väljer **Azure-lagring** på den nedrullningsbara menyn. Du bör se JSON-mallen för att skapa en länkad Azure-lagringstjänst i den högra rutan. 
   
    ![Redigerarens knapp Nytt datalager](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Ersätt `<accountname>` och `<accountkey>` med namnet på ditt Azure-lagringskonto och dess nyckelvärden. 
   
    ![JSON för redigerarens blobblagring](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Klicka på **Distribuera** i verktygsfältet. Du bör se den distribuerade **AzureStorageLinkedService** i trädvyn nu. 
   
    ![Distribuera redigerarens blobblagring](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    Läs mer om JSON-egenskaper i den länkade tjänstdefinitionen i artikeln [Anslutningsapp för Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Skapa en länkad tjänst för Azure SQL Database
I det här steget länkar du Azure SQL-databasen till din datafabrik. Du anger Azure SQL-servernamnet, databasnamnet, användarnamnet och lösenordet i det här avsnittet. 

1. I **Data Factory-redigeraren** klickar du på knappen **Nytt datalager** i verktygsfältet och väljer **Azure SQL Database** i listrutan. Du bör se JSON-mallen för att skapa en länkad Azure SQL-tjänst i den högra rutan.
2. Ersätt `<servername>`, `<databasename>`, `<username>@<servername>` och `<password>` med namnen på din Azure SQL-server, databas, ditt användarkonto och lösenord. 
3. Klicka på **Distribuera** i verktygsfältet för att skapa och distribuera **AzureSqlLinkedService**.
4. Bekräfta att du kan se **AzureSqlLinkedService** i trädvyn under **Länkade tjänster**.  

    Mer information om de här JSON-egenskaperna finns i [Anslutningsapp för Azure SQL Database](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Skapa datauppsättningar
I det föregående steget skapade du kopplade tjänster för att länka ett Azure-lagringskonto och en Azure SQL-databas till datafabriken. I det här steget definierar du två datauppsättningar – InputDataset och OutputDataset – som visar in- och utdata som lagras i de datalager som refereras till av AzureStorageLinkedService och AzureSqlLinkedService.

Den länkade Azure storage-tjänsten anger anslutningssträngen som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure storage-konto. Och en indatauppsättning anger vilken blobbehållare och mapp som innehåller indata.  

Den länkade Azure SQL-databasen anger anslutningssträngen som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure SQL-databas. Och utdatauppsättningen (OutputDataset) för SQL-tabellen anger tabellen i databasen som data kopieras till från blob-lagringen. 

### <a name="create-input-dataset"></a>Skapa indatauppsättning
I det här steget skapar du en datauppsättning med namnet InputDataset som pekar på en blobfil (emp.ext) i rotmappen i en blobbehållare (adftutorial) i Azure Storage som representeras av den länkade tjänsten AzureStorageLinkedService. Om du inte anger ett värde för filnamnet (eller hoppar över det), kommer data från alla blobbar i indatamappen att kopieras till målet. I den här kursen anger du ett värde för filnamnet. 

1. I **redigeringsprogrammet** för Data Factory klickar du på **... More (Mer)**, **Ny datauppsättning** och sedan på **Azure Blob Storage** i listrutan. 
   
    ![Menyn Ny datauppsättning](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment: 
   
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

    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

    | Egenskap | Beskrivning |
    |:--- |:--- |
    | typ | Typegenskapen har angetts till **AzureBlob** eftersom det finns data i Azure Blob-lagringen. |
    | linkedServiceName | Refererar till **AzureStorageLinkedService** som du skapade tidigare. |
    | folderPath | Anger vilken **blobbehållare** och **mapp** som innehåller indatablobbar. I den här självstudiekursen adftutorial är blob-behållaren och -mappen rotmappen. | 
    | fileName | Den här egenskapen är valfri. Om du tar bort egenskapen kommer alla filer från folderPath hämtas. I den här självstudiekursen har angetts **emp.txt** som filnamn så att endast den filen hämtas för bearbetning. |
    | format -> typ |Indatafilen är i textformat, så vi använder **TextFormat**. |
    | columnDelimiter | Kolumner i loggfilerna avgränsas med **kommatecken (`,`)**. |
    | frekvens/intervall | Frekvensen är **timme** och intervallet är **1**, vilket innebär att indatasektorerna är tillgängliga en gång i **timmen**. Det betyder att tjänsten Data Factory söker efter indata varje timme i rotmappen för den angivna blobbehållaren (**adftutorial**). Den söker data i pipelinens start- och sluttider och inte före eller efter dessa tider.  |
    | extern | Den här egenskapen anges som **true** om indatan inte skapades av denna pipeline. Inkommande data i den här självstudien finns i filen emp.txt som genereras av denna pipeline, så vi ställer in den här egenskapen på true. |

    Mer information om de här JSON-egenskaperna finns i artikeln [Azure Blob-anslutningsapp](data-factory-azure-blob-connector.md#dataset-properties).      
3. Klicka på **Distribuera** i verktygsfältet för att distribuera **InputDataset**. Kontrollera att du ser **InputDataset** i trädvyn.

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
Den länkade tjänsten Azure SQL Database anger anslutningssträngen som används av tjänsten Data Factory vid körningstiden för att ansluta till din Azure SQL-databas. Och utdatauppsättningen (OutputDataset) för SQL-tabellen som du skapade i det här steget anger tabellen i databasen som data kopieras till från blob-lagringen.

1. I **redigeringsprogrammet** för Data Factory klickar du på **... More (Mer)**, **Ny datauppsättning** och sedan på **Azure SQL** i listrutan. 
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment:

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

    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

    | Egenskap | Beskrivning |
    |:--- |:--- |
    | typ | Typegenskapen är **AzureSqlTable** eftersom data kopieras till en tabell i en Azure SQL-databas. |
    | linkedServiceName | Refererar till **AzureSqlLinkedService** som du skapade tidigare. |
    | tableName | Ange **tabellen** dit data kopieras. | 
    | frekvens/intervall | Frekvensen är inställd på **timme** och intervallet är **1**, vilket innebär att utdatasegment produceras **varje timme** mellan pipelinens start- och sluttider, inte före eller efter dessa tider.  |

    Det finns tre kolumner – **ID**, **FirstName** och **LastName** – i emp-tabellen i databasen. ID är en identitetskolumn, så du anger bara **FirstName** och **LastName** här.

    Mer information om de här JSON-egenskaperna finns i artikeln [Azure SQL-anslutningsapp](data-factory-azure-sql-connector.md#dataset-properties).
3. Klicka på **Distribuera** i verktygsfältet för att distribuera **OutputDataset**. Kontrollera att du ser **OutputDataset** i trädvyn under **Datasets**. 

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget ska du skapa en pipeline med en **kopieringsaktivitet** som använder **InputDataset** som indata och **OutputDataset** som utdata.

Schemat styrs för närvarande av utdatamängd. I den här självstudiekursen är datamängden för utdata konfigurerad för att skapa ett segment en gång i timmen. Pipelinen har en starttid och sluttid som är en dag från varandra, vilket är 24 timmar. Därför produceras 24 segment för utdatauppsättningen av pipeline. 

1. I **redigeringsprogrammet** för Data Factory klickar du på **... More (Mer)** och sedan på **Ny pipeline**. Alternativt kan du högerklicka på **Pipelines** i trädvyn och klicka på **Ny pipeline**.
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment: 

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
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```   
    
    Observera följande punkter:
   
    - I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten. I Data Factory-lösningar, kan du också använda [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md).
    - Indata för aktiviteten är inställd på **InputDataset** och utdata för aktiviteten är inställd på **OutputDataset**. 
    - I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp. En fullständig lista över datakällor som stöds av kopieringsaktiviteten som källor och mottagare finns i [Datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Klicka på länken i tabellen om du vill veta hur du använder ett visst datalager som stöds som källa/mottagare.
    - Både start- och slutdatum måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2016-10-14T16:32:41Z. **Sluttiden** är valfri, men vi använder den i den här självstudiekursen. Om du inte anger värdet för **slut**egenskapen, beräknas det som ”**start + 48 timmar**”. Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut**egenskapen.
     
    I det föregående exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.

    Beskrivningar av JSON-egenskaper i en pipeline-definition finns i artikeln [skapa pipelines](data-factory-create-pipelines.md). Beskrivningar av JSON-egenskaper i en kopieringsaktivitet-definition finns i artikeln [aktiviteter för dataflyttning](data-factory-data-movement-activities.md). Beskrivningar av JSON-egenskaper som stöds av BlobSource finns i artikeln [Azure Blob-anslutningsapp](data-factory-azure-blob-connector.md). Beskrivningar av JSON-egenskaper som stöds av SqlSink finns i artikeln [Azure SQL Database-anslutningsapp](data-factory-azure-sql-connector.md).
3. Klicka på **Distribuera** i verktygsfältet för att distribuera **ADFTutorialPipeline**. Kontrollera att du ser pipelinen i trädvyn. 
4. Stäng nu bladet **Redigerare** genom att klicka på **X**. Klicka på **X** igen för att se startsidan för **Data Factory** för **ADFTutorialDataFactory**.

**Grattis!** Du har skapat en Azure-datafabrik med en pipeline för att kopiera data från en Azure blob-lagring till en Azure SQL-databas. 


## <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget ska du använda Azure-portalen för att övervaka vad som händer i en Azure-datafabrik.    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Övervaka pipeline med övervaknings- och hanteringsappen
Följande steg visar hur du övervakar pipelines i din datafabrik med hjälp av övervaknings- och hanteringsappen: 

1. Klicka på ikonen **Övervaka och hantera** på datafabrikens startsida.
   
    ![Ikonen Övervaka och hantera](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. Du bör se programmet **Övervaka och hantera** i en separat flik. 

    > [!NOTE]
    > Om du ser att webbläsaren har fastnat på ”Auktoriserar...” ska du göra något av följande: avmarkera kryssrutan **Blockera cookies från tredje part och platsdata** (eller) behåll den aktiverad och skapa ett undantag för **login.microsoftonline.com**. Försök sedan starta guiden igen.

    ![Appen Övervaka och hantera](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. Ändra **Starttid** och **Sluttid** så att de inkluderar starttid (2017-05-11) och sluttid (2017-05-12) i pipelinen. Klicka sedan på **Tillämpa**.       
3. Du ser det **aktivitetsfönster** som är associerat med varje timme mellan pipelinens start- och sluttider i listan i den mellersta rutan. 
4. Välj ett aktivitetsfönster i listan **Aktivitetsfönster** om du vill se information om det. 
    ![Aktivitetsfönsterinformation](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    I aktivitetsfönsterutforskaren till höger ser du att segment upp till den aktuella UTC-tiden (20:12) bearbetas (med grön färg). Segmenten 20: 00-21:00, 21:00-22:00, 22:00-23:00, 23:00-24:00 bearbetas inte ännu.

    Området **Försök** i den högra rutan innehåller information om aktivitetskörningar för datasektorn. Om ett fel uppstod, visas information om felet. Till exempel, om indatamappen eller behållaren inte finns och segmentbearbetningen misslyckades visas ett felmeddelande om att behållaren eller mappen inte finns.

    ![Aktivitetskörningsförsök](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. Starta **SQL Server Management Studio**, anslut till Azure SQL Database och kontrollera att raderna infogas i **emp**-tabellen i databasen.
    
    ![sql-frågeresultat](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

Se [Övervaka och hantera Azure Data Factory-pipelines med övervaknings- och hanteringsappen](data-factory-monitor-manage-app.md) för mer information om att använda programmet.

### <a name="monitor-pipeline-using-diagram-view"></a>Övervaka pipeline med diagramvyn
Du kan också övervaka datapipelines genom att använda diagramvyn.  

1. På **Data Factory**-bladet klickar du på **Diagram**.
   
    ![Data Factory-bladet – Diagramikon](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Du bör se ett diagram som liknar följande bild: 
   
    ![Diagramvy](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. I diagramvyn dubbelklickar du på **InputDataset** för att visa datauppsättningens segment.  
   
    ![Datauppsättningar med InputDataset valt](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Klicka på **Visa mer** för att se alla datasegment. Du ser 24 timsegment mellan pipelinens start- och sluttider. 
   
    ![Alla indatasektorer](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Observera att alla datasegment upp till aktuell tid är **klara** eftersom filen **emp.txt** alltid finns i blobbehållaren: **adftutorial\input**. Segmenten för framtida tider ännu inte klara. Kontrollera att inga sektorer visas i avsnittet **Nyligen misslyckade sektorer** längst ned.
6. Stäng bladen tills du ser diagramvyn (eller) rulla åt vänster för att visa diagramvyn. Dubbelklicka sedan på **OutputDataset**. 
8. Klicka på länken **Visa mer** på bladet **Tabell** för **OutputDataset** för att se alla segment.

    ![datasektorblad](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. Observera att alla segment upp till den aktuella UTC-tiden flyttar från **väntande körning** tillstånd = > **pågår** ==> **klar** tillstånd. Segment från tidigare (före aktuell tid) bearbetas från senaste till äldsta som standard. Till exempel om den aktuella tiden är 20:12 UTC, bearbetas segmentet för 19:00 - 20:00 för 18:00 - 19:00. Segmentet 20:00 - 21 00 bearbetas i slutet av tidsintervallet som standard, det vill säga efter 21:00.  
10. Klicka på någon av datasektorerna i listan. Bladet **Datasektor** visas. En datauppsättning som associeras med ett aktivitetsfönster kallas ett segment. Ett segment kan ha en eller flera filer.  
    
     ![datasektorblad](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Om sektorn inte har statusen **Klar** kan du se sektorer uppströms som inte är klara och som blockerar aktuell sektor från att köras i listan **Sektorer uppströms som inte är klara**.
11. På bladet **DATASEKTOR** bör du se alla aktivitetskörningar i listan längst ned. Öppna bladet **Aktivitetskörningsinformation** genom att klicka på en **aktivitetskörning**. 
    
    ![Aktivitetskörningsinformation](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    I det här bladet ser di hur lång tid kopieringen tog, genomflödet, hur många byte data har lästs och skrivits, körningens starttid och sluttid osv.  
12. Klicka på **X** för att stänga alla blad tills du kommer tillbaka till startbladet för **ADFTutorialDataFactory**.
13. (valfritt) Klicka på panelen **Datauppsättningar** eller rutan **Pipelines** för att hämta blad som du har sett i föregående steg. 
14. Starta **SQL Server Management Studio**, anslut till Azure SQL Database och kontrollera att raderna infogas i **emp**-tabellen i databasen.
    
    ![sql-frågeresultat](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>Sammanfattning
I den här självstudien har du skapat en Azure-datafabrik som kopierar data från en Azure-blobb till en Azure SQL-databas. Du använde Azure-portalen för att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Här är de avancerade steg som du utförde i självstudien:  

1. Du skapade en Azure **Data Factory**.
2. Du skapade **länkade tjänster**:
   1. En länkad **Azure Storage-**tjänst som länkar Azure Storage-kontot som innehåller indata.     
   2. En länkad **Azure SQL**-tjänst som länkar din Azure SQL-databas som innehåller utdata. 
3. Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4. Du skapade en **pipeline** med en **kopieringsaktivitet** med **BlobSource** som källa och **SqlSink** som mottagare.  

## <a name="next-steps"></a>Nästa steg
I den här kursen används Azure blob storage som ett datalager för källa och en Azure SQL-databas som ett dataarkiv som mål i en kopieringsåtgärd. Följande tabell innehåller en lista över datalager som stöds som källor och mål av kopieringsaktiviteten: 

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

För mer information om hur du kopierar data till/från ett datalager klickar du på länken för datalagret i tabellen.

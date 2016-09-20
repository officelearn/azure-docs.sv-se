<properties 
    pageTitle="Självstudier: Skapa en pipeline med en kopieringsaktivitet med hjälp av Visual Studio | Microsoft Azure" 
    description="I de här självstudierna skapar du en Azure Data Factory-pipeline med en kopieringsaktivitet genom att använda Visual Studio." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/01/2016" 
    ms.author="spelluru"/>

# Självstudie: Skapa en pipeline med en kopieringsaktivitet med hjälp av Visual Studio
> [AZURE.SELECTOR]
- [Självstudier – översikt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Använda Data Factory-redigeraren](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Använda PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Använda Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Använda REST-API:et](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Använda .NET-API:et](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Använda guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)

I de här självstudierna gör du följande med hjälp av Visual Studio 2013:

1. Skapa två länkade tjänster: **AzureStorageLinkedService1** och **AzureSqlLinkedService1**. AzureStorageLinkedService1 länkar en Azure-lagring och AzureSqlLinkedService1 länkar en Azure SQL-databas till datafabriken: **ADFTutorialDataFactoryVS**. Indatan för pipelinen finns i en blob-behållare i Azure blob-lagring och utdata lagras i en tabell i Azure SQL-databasen. Därför kan du lägga till dessa två datalager som länkade tjänster i datafabriken.
2. Skapa två datafabrikstabeller: **EmpTableFromBlob** och **EmpSQLTable**, som visar de in- och utdata som lagras i datalagren. För EmpTableFromBlob anger du den blob-behållare som innehåller en blob med källdata. För EmpSQLTable anger du den SQL-tabell som lagrar utdata. Du kan också ange andra egenskaper som struktur, tillgänglighet och så vidare.
3. Skapa en pipeline med namnet **ADFTutorialPipeline** i ADFTutorialDataFactoryVS. Pipelinen ska ha en **kopieringsaktivitet** som kopierar indata från Azure-bloben till utdata i Azure SQL-tabellen. Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten. 
4. Skapa en datafabrik och distribuera länkade tjänster, tabeller och pipelinen.    

## Krav

1. Läs igenom [I de här självstudieöversikten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
    
    > [AZURE.IMPORTANT] Slutför de nödvändiga förutsättningarna innan du fortsätter. 
2. Du måste vara **administratör för Azure-prenumerationen** för att kunna publicera Data Factory-enheter i Azure Data Factory.  
3. Du måste ha följande installerat på datorn: 
    - Visual Studio 2013 eller Visual Studio 2015
    - Hämta Azure SDK för Visual Studio 2013 eller Visual Studio 2015. Gå till [Azures hämtningssida](https://azure.microsoft.com/downloads/) och klicka på **VS 2013** eller **VS 2015** i **.NET**-avsnittet.
    - Hämta det senaste Azure Data Factory-plugin-programmet för Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Om du använder Visual Studio 2013 kan du även uppdatera plugin-programmet genom att göra följande: I menyn klickar du på **Verktyg** -> **Tillägg och uppdateringar** -> **Online** -> **Visual Studio-galleriet** -> **Microsoft Azure Data Factory-verktyg för Visual Studio** -> **Uppdatera**. 
 


## Skapa Visual Studio-projekt 
1. Starta **Visual Studio 2013**. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**. Dialogrutan **Nytt projekt** visas.  
2. I dialogrutan **Nytt projekt** väljer du mallen **DataFactory** och klickar på **Tomt Data Factory-projekt**. Om du inte ser DataFactory-mallen stänger du Visual Studio, installerar Azure SDK för Visual Studio 2013 och öppnar Visual Studio på nytt.  

    ![Dialogrutan Nytt projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Ange ett **namn** för projektet, en **plats** och ett namn för **lösningen**. Klicka på **OK**.

    ![Solution Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png) 

## Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. Ett dataarkiv kan vara en Azure-lagring, Azure SQL Database eller en lokal SQL Server-databas.

I det här steget skapar du två länkade tjänster: **AzureStorageLinkedService1** och **AzureSqlLinkedService1**. Den länkade AzureStorageLinkedService1-tjänsten länkar ett Azure-lagringskonto och AzureSqlLinkedService länkar en Azure SQL-databas till datafabriken: **ADFTutorialDataFactory**. 

### Skapa den länkade Azure-lagringstjänsten

4. Högerklicka på **Länkade tjänster** i Solution Explorer, peka på **Lägg till** och klicka på **Nytt objekt**.      
5. I dialogrutan **Lägg till nytt objekt** väljer du **Länkad Azure-lagringstjänst** i listan och klickar på **Lägg till**. 

    ![Ny länkad tjänst](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
 
3. Ersätt **accountname** och **accountkey** med namnet på ditt Azure-lagringskonto och dess nyckel. 

    ![Länkad Azure-lagringstjänst](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Spara filen **AzureStorageLinkedService1.json**.

### Skapa den länkade Azure SQL-tjänsten

5. Högerklicka på noden **Länkade tjänster** i **Solution Explorer** igen, peka på **Lägg till** och klicka på **Nytt objekt**. 
6. Den här gången väljer du **Länkad Azure SQL-tjänst** och klickar på **Lägg till**. 
7. I **filen AzureSqlLinkedService1.json** ersätter du **servername**, **databasename**, **username@servername** och **password** med namnen på din Azure SQL-server, databas, ditt användarkonto och lösenord.    
8.  Spara filen **AzureSqlLinkedService1.json**. 


## Skapa datauppsättningar
I föregående steg skapade du de länkade tjänsterna **AzureStorageLinkedService1** och **AzureSqlLinkedService1** för att länka ett Azure-lagringskonto och en Azure SQL-databas till datafabriken: **ADFTutorialDataFactory**. I det här steget definierar du två datafabrikstabeller – **EmpTableFromBlob** och **EmpSQLTable** – som visar in- och utdata som lagras i de datalager som refereras till av AzureStorageLinkedService1 och AzureSqlLinkedService1. För EmpTableFromBlob anger du den blob-behållare som innehåller en blob med källdata. För EmpSQLTable anger du den SQL-tabell som lagrar utdata.

### Skapa indatauppsättning

9. Högerklicka på **Tabeller** i **Solution Explorer**, peka på **Lägg till** och klicka på **Nytt objekt**.
10. I dialogrutan **Lägg till nytt objekt** väljer du **Azure-blobb** och klickar på **Lägg till**.   
10. Ersätt texten JSON med följande text och spara filen **AzureBlobLocation1.json**. 

        {
          "name": "EmpTableFromBlob",
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

### Skapa datauppsättning för utdata

11. Högerklicka på **Tabeller** i **Solution Explorer** igen, peka på **Lägg till** och klicka på **Nytt objekt**.
12. I dialogrutan **Lägg till nytt objekt** väljer du **Azure SQL** och klickar på **Lägg till**. 
13. Ersätt texten JSON med följande JSON och spara filen **AzureSqlTableLocation1.json**.

        {
          "name": "EmpSQLTable",
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

## Skapa pipeline 
Du har skapat länkade in-/utdatatjänster och tabeller hittills. Nu skapar du en pipeline med en **kopieringsaktivitet** som kopierar data från Azure-bloben till Azure SQL-databasen. 


1. Högerklicka på **Pipelines** i **Solution Explorer**, peka på **Lägg till** och klicka på **Nytt objekt**.  
15. Välj **Kopiera datapipeline** i dialogrutan **Lägg till nytt objekt** och klicka på **Lägg till**. 
16. Ersätt JSON med följande JSON och spara filen **CopyActivity1.json**.
            
        {
          "name": "ADFTutorialPipeline",
          "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
              {
                "name": "CopyFromBlobToSQL",
                "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "EmpTableFromBlob"
                  }
                ],
                "outputs": [
                  {
                    "name": "EmpSQLTable"
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

## Publicera/distribuera Data Factory-entiteter
  
18. I Solution Explorer högerklickar du på projektet och klickar sedan på **Publicera**. 
19. Om du ser dialogrutan **Logga in på ditt Microsoft-konto**, anger du dina autentiseringsuppgifter för det konto som har Azure-prenumerationen. Klicka sedan på **Logga in**.
20. Du bör se följande dialogruta:

    ![Dialogrutan Publicera](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)

21. På sidan Konfigurera datafabrik gör du följande: 
    1. välj alternativet **Skapa ny Data Factory**.
    2. Ange **VSTutorialFactory** som **namn**.  
    
        > [AZURE.NOTE]  
        > Namnet på Azure Data Factory måste vara globalt unikt. Ändra namnet på datafabriken (till exempel yournameVSTutorialFactory) och försök att publicera igen, om du får ett felmeddelande om namnet på datafabriken när du publicerar. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
        
    3. Välj rätt prenumeration i fältet **Prenumeration**. 
    4. Välj **resursgrupp** för datafabriken som ska skapas. 
    5. Välj **region** för datafabriken. 
    6. Klicka på **Nästa** för att växla till sidan **Publicera objekt**. 
23. På sidan **Publicera objekt** kontrollerar du att alla datafabriksentiteter har valts. Klicka på **Nästa** för att växla till sidan **Sammanfattning**.     
24. Granska sammanfattningen och klicka på **Nästa** för att starta distributionsprocessen och visa **Distributionsstatus**.
25. På sidan **Distributionsstatus** bör du se statusen för distributionen. Klicka på Slutför när distributionen är klar. 

Observera följande: 

- Om du får felet: ”**Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory**” gör du något av följande och försöker att publicera igen: 

    - I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad. 
    
            Get-AzureRmResourceProvider
    - Logga in med Azure-prenumerationen i [Azure-portalen](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure-portalen. Den här åtgärden registrerar automatiskt providern åt dig.
-   Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
-   Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen

## Sammanfattning
I den här självstudien har du skapat en Azure-datafabrik som kopierar data från en Azure-blobb till en Azure SQL-databas. Du använde Visual Studio till att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Här är de avancerade steg som du utförde i självstudierna:  

1.  Du skapade en Azure **Data Factory**.
2.  Du skapade **länkade tjänster**:
    1. En länkad **Azure-lagrings**tjänst som länkar Azure-lagringskontot som innehåller indata.    
    2. En länkad **Azure SQL**-tjänst som länkar din Azure SQL-databas som innehåller utdata. 
3.  Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4.  Du skapade en **pipeline** med en **kopieringsaktivitet** med **BlobSource** som källa och **SqlSink** som mottagare. 


## Använda Server Explorer för att visa datafabriker

1. I **Visual Studio** klickar du på **Visa** i menyn och därefter på **Server Explorer**.
2. I fönstret Server Explorer expanderar du **Azure** och **Data Factory**. Om du ser **Logga in till Visual Studio** anger du det **konto** som är associerat med din Azure-prenumeration och klickar på **Fortsätt**. Ange **lösenordet** och klicka på **Logga in**. Visual Studio försöker hämta information om alla Azure-datafabriker i din prenumeration. Du ser statusen för den här åtgärden i fönstret **Uppgiftslista för Data Factory**.
    ![Server Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Du kan också högerklicka på en datafabrik och välja Exportera Data Factory till nytt projekt, för att skapa ett Visual Studio-projekt som baseras på en befintlig datafabrik.
    ![Exportera datafabrik till ett VS-projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## Uppdatera Data Factory-verktyg för Visual Studio
Om du vill uppdatera Azure Data Factory-verktyg för Visual Studio gör du följande:

1. Klicka på **Verktyg** i menyn och välj **Tillägg och uppdateringar**. 
2. Välj **Uppdateringar** i den vänstra rutan och välj sedan **Visual Studio-galleriet**.
4. Välj **Azure Data Factory-verktyg för Visual Studio** och klicka på **Uppdatera**. Om du inte ser den här posten har du redan den senaste versionen av verktygen. 

Se [Övervaka datauppsättningar och pipeline](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) för instruktioner om hur du använder Azure Portal till att övervaka pipeline och datauppsättningar som du har skapat i den här självstudien.

## Se även
| Avsnitt | Beskrivning |
| :---- | :---- |
| [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) | Den här artikeln innehåller detaljerad information om kopieringsaktiviteten som du använde i självstudien. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) | I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Pipelines](data-factory-create-pipelines.md) | Den här artikeln beskriver pipelines och aktiviteter i Azure Data Factory |
| [Datauppsättningar](data-factory-create-datasets.md) | I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory.
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) | Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. 



<!--HONumber=sep16_HO1-->



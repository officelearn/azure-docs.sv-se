<properties 
    pageTitle="Självstudiekurs: Skapa en pipeline med en kopieringsaktivitet med hjälp av Data Factory-redigeraren | Microsoft Azure" 
    description="I den här självstudien skapar du en Azure Data Factory-pipeline med en kopieringsaktivitet med hjälp av Data Factory-redigeraren på Azure-portalen." 
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

# Självstudie: Skapa en pipeline med en kopieringsaktivitet med hjälp av Data Factory-redigeraren
> [AZURE.SELECTOR]
- [Självstudier – översikt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Använda Data Factory-redigeraren](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Använda PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Använda Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Använda REST-API:et](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Använda .NET-API:et](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Använda guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)


Den här självstudien innehåller följande steg:

Steg | Beskrivning
-----| -----------
[Skapa en Azure Data Factory](#create-data-factory) | I det här steget skapar du en Azure-datafabrik med namnet **ADFTutorialDataFactory**.  
[Skapa länkade tjänster](#create-linked-services) | I det här steget skapar du två länkade tjänster: **AzureStorageLinkedService** och **AzureSqlLinkedService**. AzureStorageLinkedService länkar Azure-lagringen och AzureSqlLinkedService länkar Azure SQL-databasen till ADFTutorialDataFactory. Indata för pipelinen finns i en blobbehållare i Azure Blob Storage och utdata lagras i en tabell i Azure SQL-databasen. Därför kan du lägga till dessa två datalager som länkade tjänster i datafabriken.      
[Skapa datauppsättningar för indata och utdata ](#create-datasets) | I det föregående steget skapade du länkade tjänster som refererar till datalager som innehåller in- och utdata. I det här steget definierar du två datafabrikstabeller – **EmpTableFromBlob** och **EmpSQLTable** – som representerar de in- och utdata som lagras i datalagren. För EmpTableFromBlob anger du blobbehållaren som innehåller en blobb med källdata, och för EmpSQLTable anger du SQL-tabellen som lagrar utdata. Du kan också ange andra egenskaper som struktur, tillgänglighet och så vidare. 
[Skapa en pipeline](#create-pipeline) | I det här steget skapar du en pipeline med namnet **ADFTutorialPipeline** i ADFTutorialDataFactory. Pipelinen ska ha en **kopieringsaktivitet** som kopierar indata från Azure-blobben till utdata i Azure SQL-tabellen. Kopieringsaktiviteten utför dataflyttningen i Azure Data Factory. Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten. 
[Övervaka pipeline](#monitor-pipeline) | I det här steget ska du övervaka sektorer från indata- och utdatatabeller med hjälp av Azure-portalen.

> [AZURE.IMPORTANT] 
> Gå igenom [kursöversikten](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) och se till att du uppfyller kraven innan du går den här självstudiekursen.

## Skapa en datafabrik
I det här steget använder du Azure-portalen för att skapa en Azure-datafabrik med namnet **ADFTutorialDataFactory**.

1.  När du har loggat in på [Azure-portalen][azure-portal] klickar du på **Nytt** i det nedre vänstra hörnet. Välj **Dataanalys** på bladet **Skapa** och klicka på **Data Factory** på bladet **Dataanalys**. 

    ![Nytt->DataFactory][image-data-factory-new-datafactory-menu]    

6. På bladet **Ny datafabrik**:
    1. Ange **ADFTutorialDataFactory** som **namn**. 
    
        ![Bladet Ny datafabrik][image-data-factory-getstarted-new-data-factory-blade]
    2. Klicka på **RESURSGRUPPENS NAMN** och gör följande:
        1. Klicka på **Skapa en ny resursgrupp**.
        2. På bladet **Skapa resursgrupp** anger du **ADFTutorialResourceGroup** som **namn** på resursgruppen och klickar på **OK**. 

            ![Skapa resursgrupp][image-data-factory-create-resource-group]

        Vissa av stegen i den här självstudien förutsätter att du använder namnet: **ADFTutorialResourceGroup** på resursgruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../resource-group-overview.md).  
7. På bladet **Ny datafabrik** ser du att **Lägg till på startsidan** har valts.
8. Klicka på **Skapa** på bladet **Ny datafabrik**.

    Namnet på Azure Data Factory måste vara globalt unikt. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
    
        Data factory name “ADFTutorialDataFactory” is not available  
     
    ![Datafabriksnamnet är inte tillgängligt][image-data-factory-name-not-available]
    
    > [AZURE.NOTE] Namnet på datafabriken kan komma att registreras som ett DNS-namn i framtiden och blir då synligt offentligt.  
    > 
    > Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen

9. Klicka på hubben **MEDDELANDEN** till vänster och se om det finns några meddelanden från processen. Klicka på **X** för att stänga bladet **MEDDELANDEN** om det är öppet. 
10. När datafabriken har skapats visas bladet **Datafabrik** (se nedan).

    ![Datafabrikens startsida][image-data-factory-get-stated-factory-home-page]

## Skapa länkade tjänster
Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. Ett datalager kan vara Azure Storage, Azure SQL Database eller en lokal SQL Server-databas.

I det här steget skapar du två länkade tjänster: **AzureStorageLinkedService** och **AzureSqlLinkedService**. Den länkade AzureStorageLinkedService-tjänsten länkar ett Azure-lagringskonto och AzureSqlLinkedService länkar en Azure SQL-databas till **ADFTutorialDataFactory**. Du ska skapa en pipeline senare i den här självstudiekursen som kopierar data från en blobbehållare i AzureStorageLinkedService till en SQL-tabell i AzureSqlLinkedService.

### Skapa en länkad tjänst för Azure-lagringskontot
1.  På bladet **DATAFABRIK** klickar du på **Författare och distribution** för att starta **Redigeraren** för datafabriken.

    ![Ikonen Författare och distribution][image-author-deploy-tile] 

     
5. I **Redigeraren** klickar du på knappen **Nytt datalager** i verktygsfältet och väljer **Azure-lagring** på den nedrullningsbara menyn. Du bör se JSON-mallen för att skapa en länkad Azure-lagringstjänst i den högra rutan. 

    ![Redigerarens knapp Nytt datalager][image-editor-newdatastore-button]
    
6. Ersätt **accountname** och **accountkey** med namnet på ditt Azure-lagringskonto och dess nyckelvärden. 

    ![JSON för redigerarens blobblagring](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
    
    Se [Referens för JSON-skript](http://go.microsoft.com/fwlink/?LinkId=516971) för information om JSON-egenskaper.

6. Klicka på **Distribuera** i verktygsfältet för att distribuera AzureStorageLinkedService. Kontrollera att du ser meddelandet **DEN LÄNKADE TJÄNSTEN HAR SKAPATS** i namnlisten.

    ![Distribuera redigerarens blobblagring][image-editor-blob-storage-deploy]

### Skapa en länkad tjänst för Azure SQL Database
1. I **Data Factory-redigeraren** klickar du på knappen **Nytt datalager** i verktygsfältet och väljer **Azure SQL Database** på den nedrullningsbara menyn. Du bör se JSON-mallen för att skapa en länkad Azure SQL-tjänst i den högra rutan.

    ![Redigerare för Azure SQL-inställningar][image-editor-azure-sql-settings]

2. Ersätt **servername**, **databasename**, **username@servername** och **password** med namnen på din Azure SQL-server, din databas, ditt användarkonto och ditt lösenord. 
3. Klicka på **Distribuera** i verktygsfältet för att skapa och distribuera AzureSqlLinkedService. 
   

## Skapa datauppsättningar
I föregående steg skapade du de länkade tjänsterna **AzureStorageLinkedService** och **AzureSqlLinkedService** för att länka ett Azure-lagringskonto och en Azure SQL-databas till datafabriken: **ADFTutorialDataFactory**. I det här steget definierar du två datafabrikstabeller, **EmpTableFromBlob** och **EmpSQLTable**, som representerar in- och utdata som lagras i datalager som AzureStorageLinkedService och AzureSqlLinkedService refererar till. För EmpTableFromBlob anger du blobbehållaren som innehåller en blobb med källdata, och för EmpSQLTable anger du SQL-tabellen som lagrar utdata. 

### Skapa indatauppsättning 
En tabell är en rektangulär datauppsättning med ett schema. I det här steget skapar du en tabell med namnet **EmpBlobTable** som pekar på en blobbehållare i Azure-lagringen som representeras av den länkade tjänsten **AzureStorageLinkedService**.

1. I **redigeraren** för Data Factory klickar du på **Ny datauppsättning** i verktygsfältet och sedan på knappen **Blobbtabell** på den nedrullningsbara menyn. 
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment: 

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

        
     Observera följande: 
    
    - datauppsättningens **typ** anges till **AzureBlob**.
    - **linkedServiceName** anges till **AzureStorageLinkedService**. Du skapade den här länkade tjänsten i steg 2.
    - **folderPath** anges till behållaren **adftutorial**. Du kan också ange namnet på en blobb i mappen. Eftersom du inte anger namnet på någon blobb, anses data från alla blobbar i behållaren vara indata.  
    - formatet **typ** anges till **TextFormat**
    - Det finns två fält i textfilen – **FirstName** och **LastName** – som avgränsas med ett kommatecken (**columnDelimiter**) 
    - **availability** är inställt på **hourly** (**frequency** är inställt på **hour** och **interval** är inställt på **1**). Det betyder att Data Factory söker efter indata varje timme i rotmappen för den angivna blobbehållaren (**adftutorial**). 
    

    Om du inte anger något **fileName** för en **indata****tabell**, anses alla filer/blobbar från indatamappen (**folderPath**) vara indata. Om du anger ett fileName i JSON, anses endast den angivna filen/blobben vara indata.
 
    Om du inte anger något **fileName** för en **utdatatabell**, genereras filerna i **folderPath** och namnges i följande format: Data.&lt;GUID\&gt;.txt (exempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Ange **folderPath** och **fileName** dynamiskt utifrån **SliceStart**-tiden med hjälp av **partitionedBy**-egenskapen. I följande exempel använder folderPath Year, Month och Day från SliceStart (starttiden för den sektor som bearbetas) och fileName använder Hour från SliceStart. Om exempelvis en sektor produceras 2014-10-20T08:00:00, anges folderName till wikidatagateway/wikisampledataout/2014/10/20 och fileName anges till 08.csv. 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

    Se [Referens för JSON-skript](http://go.microsoft.com/fwlink/?LinkId=516971) för information om JSON-egenskaper.

2. Klicka på **Distribuera** i verktygsfältet för att skapa och distribuera tabellen **EmpTableFromBlob**. Kontrollera att du ser meddelandet **TABELLEN HAR SKAPATS** i namnlisten i redigeraren.

### Skapa datauppsättning för utdata
I den här delen av steget ska du skapa en utdatauppsättning med namnet **EmpSQLTable**. Den här datauppsättningen pekar på en SQL-tabell i Azure SQL-databasen som representeras av **AzureSqlLinkedService**. 

1. I **redigeraren** för Data Factory klickar du på knappen **Ny datauppsättning** i verktygsfältet och sedan på **Azure SQL-tabell** på den nedrullningsbara menyn. 
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment:

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

        
     Observera följande: 
    
    * datauppsättningens **typ** anges till **AzureSQLTable**.
    * **linkedServiceName** har angetts till **AzureSqlLinkedService** (du skapade den här länkade tjänsten i steg 2).
    * **tablename** anges till **emp**.
    * Det finns tre kolumner – **ID**, **FirstName** och **LastName** – i emp-tabellen i databasen. ID är en identitetskolumn, så du anger bara **FirstName** och **LastName** här.
    * **Tillgängligheten** anges till **varje timme** (**frekvens** inställd på **timme** och **intervall** inställd på **1**).  Data Factory-tjänsten genererar en utdatasektor varje timme i **emp**-tabellen i Azure SQL-databasen.


3. Klicka på **Distribuera** i verktygsfältet för att skapa och distribuera tabellen **EmpSQLTable**.


## Skapa pipeline
I det här steget skapar du en pipeline med en **kopieringsaktivitet** som använder **EmpTableFromBlob** som indata och **EmpSQLTable** som utdata.

1. I **redigeraren** för Data Factory klickar du på knappen **Ny pipeline** i verktygsfältet. Klicka på **... (tre punkter)** i verktygsfältet om du inte ser knappen. Alternativt kan du högerklicka på **Pipelines** i trädvyn och klicka på **Ny pipeline**.

    ![Redigerarens knapp Ny pipeline][image-editor-newpipeline-button]
 
2. Ersätt JSON i den högra rutan med följande JSON-kodfragment: 
        
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
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2015-07-12T00:00:00Z",
            "end": "2015-07-13T00:00:00Z"
          }
        } 

    Observera följande:

    - I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **CopyActivity**.
    - Indata för aktiviteten är inställd på **EmpTableFromBlob** och utdata för aktiviteten är inställd på **EmpSQLTable**.
    - I avsnittet för **transformering** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp.

    Ersätt värdet i **start**egenskapen med den aktuella dagen och **slut**värdet med nästa dag. Du kan ange endast datumdelen och hoppa över tidsvärdet. Till exempel ”2015-02-03” som motsvarar ”2015-02-03T00:00:00Z”
    
    Både start- och slutdatum måste vara i [ISO-format](http://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. **Sluttiden** är valfri, men vi använder den i den här självstudiekursen. 
    
    Om du inte anger värdet för **slut**egenskapen, beräknas det som ”**start + 48 timmar**”. Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut**egenskapen.
    
    I det föregående exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.
    
    Se [Referens för JSON-skript](http://go.microsoft.com/fwlink/?LinkId=516971) för information om JSON-egenskaper.

4. Klicka på **Distribuera** i verktygsfältet för att distribuera **ADFTutorialPipeline**. Kontrollera att du ser meddelandet **PIPELINE HAR SKAPATS**.
5. Stäng nu bladet **Redigerare** genom att klicka på **X**. Klicka på **X** igen för att stänga ADFTutorialDataFactory-bladet i verktygsfältet och trädvyn. Om du ser meddelandet **Dina osparade ändringar kommer att tas bort** klickar du på **OK**.
6. Du bör vara tillbaka på bladet **DATAFABRIK** i **ADFTutorialDataFactory**.

**Grattis!** Du har skapat en Azure-datafabrik, länkade tjänster, tabeller och en pipeline som du har schemalagt.   
 
### Visa datafabriken i en diagramvy 
1. På bladet **DATAFABRIK** klickar du på **Diagram**.

    ![Data Factory-bladet – Diagramikon][image-datafactoryblade-diagramtile]

2. Du bör se ett diagram som liknar följande: 

    ![Diagramvy][image-data-factory-get-started-diagram-blade]

    Du kan zooma in, zooma ut, zooma till 100 %, anpassa zoomningen, placera pipelines och tabeller automatiskt samt visa härkomstinformation (markerar överordnade och underordnade objekt för de valda objekten).  Du kan dubbelklicka på ett objekt (in-/utdatatabell eller pipeline) för att se dess egenskaper. 
3. Högerklicka på **ADFTutorialPipeline** i diagramvyn och klicka på **Öppna pipeline**. Du bör se aktiviteterna i pipelinen tillsammans med indata och utdata i datauppsättningar för aktiviteterna. I den här självstudien får du endast ha en aktivitet i pipelinen (kopieringsaktiviteten) med EmpTableBlob som indatauppsättning och EmpSQLTable som utdatauppsättning.   

    ![Öppna pipeline](./media/data-factory-copy-activity-tutorial-using-azure-portal/DiagramView-OpenPipeline.png)

4. Klicka på **Datafabrik** i länken i det övre vänstra hörnet för att återgå till diagramvyn. I diagramvyn visas alla pipelines. I det här exemplet har du bara skapat en pipeline.   
 

## Övervaka pipeline
I det här steget ska du använda Azure-portalen för att övervaka vad som händer i en Azure-datafabrik. 

1. Gå till [Azure Portal (förhandsversion)][azure-portal] om portalen inte redan är öppen. 
2. Om bladet för **ADFTutorialDataFactory** inte är öppet, öppnar du det genom att klicka på **ADFTutorialDataFactory** på **startsidan**. 
3. Du bör se antal och namn på de tabeller och pipelines som du skapade i det här bladet.

    ![startsida med namn][image-data-factory-get-started-home-page-pipeline-tables]

4. Klicka på ikonen **Datauppsättningar**.
5. På bladet **Datauppsättningar** klickar du på **EmpTableFromBlob**. Den här datauppsättningen är indatauppsättningen för **ADFTutorialPipeline**.

    ![Datauppsättningar med EmpTableFromBlob är valt][image-data-factory-get-started-datasets-emptable-selected]   
5. Observera att datasektorer upp till aktuell tid redan har skapats och är **klara** eftersom filen **emp.txt** alltid finns i blobbehållaren: **adftutorial\input**. Kontrollera att inga sektorer visas i avsnittet **Nyligen misslyckade sektorer** längst ned.

    Båda listorna **Nyligen uppdaterade sektorer** och **Nyligen misslyckade sektorer** sorteras efter **SENASTE UPPDATERINGSTID**. Uppdateringstiden för en sektor ändras i följande situationer. 
    
    Klicka på rubriken i listorna eller på **... (tre punkter)** om du vill visa en mer omfattande sektorlista. Klicka på **Filter** i verktygsfältet för att filtrera sektorerna.  
    
    Om du vill visa datasektorer sorterade efter sektorns start- och sluttider i stället, klickar du på ikonen **Datasektorer (efter sektortid)**.   

    ![Datasektorer efter sektortid][DataSlicesBySliceTime]   

6. På bladet **Datauppsättningar** klickar du nu på **EmpSQLTable**. Den här datauppsättningen är utdatauppsättningen för **ADFTutorialPipeline**.

    ![bladet datauppsättningar][image-data-factory-get-started-datasets-blade]

6. Bladet **EmpSQLTable** bör visas, som du ser i följande bild:

    ![tabellblad][image-data-factory-get-started-table-blade]
 
7. Observera att datasektorerna upp till den aktuella tiden redan har skapats och är **klara**. Inga sektorer visas i avsnittet **Problemsektorer** längst ned.
8. Klicka på **... (tre punkter)** för att se alla sektorer.

    ![datasektorblad][image-data-factory-get-started-dataslices-blade]

9. Klicka på någon av datasektorerna i listan. Bladet **DATASEKTOR** visas.

    ![datasektorblad][image-data-factory-get-started-dataslice-blade]
  
    Om sektorn inte har statusen **Klar** kan du se sektorer uppströms som inte är klara och som blockerar aktuell sektor från att köras i listan **Sektorer uppströms som inte är klara**. 

11. På bladet **DATASEKTOR** bör du se alla aktivitetskörningar i listan längst ned. Öppna bladet **AKTIVITETSKÖRNINGSINFORMATION** genom att klicka på en **aktivitetskörning**. 

    ![Aktivitetskörningsinformation][image-data-factory-get-started-activity-run-details]

    
12. Klicka på **X** för att stänga alla blad tills du kommer tillbaka till startbladet för **ADFTutorialDataFactory**.
14. (valfritt) Klicka på **Pipelines** på startsidan för **ADFTutorialDataFactory**, klicka på **ADFTutorialPipeline** på bladet **Pipelines** och sök i indatatabellerna (**Förbrukning**) eller utdatatabellerna (**Producerat**).
15. Starta **SQL Server Management Studio**, anslut till Azure SQL Database och kontrollera att raderna infogas i **emp**-tabellen i databasen.

    ![sql-frågeresultat][image-data-factory-get-started-sql-query-results]


## Sammanfattning 
I den här självstudien har du skapat en Azure-datafabrik som kopierar data från en Azure-blobb till en Azure SQL-databas. Du använde Azure-portalen för att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Här är de avancerade steg som du utförde i självstudien:  

1.  Du skapade en Azure **Data Factory**.
2.  Du skapade **länkade tjänster**:
    1. En länkad **Azure-lagrings**tjänst som länkar Azure-lagringskontot som innehåller indata.    
    2. En länkad **Azure SQL**-tjänst som länkar din Azure SQL-databas som innehåller utdata. 
3.  Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4.  Du skapade en **pipeline** med en **kopieringsaktivitet** med **BlobSource** som källa och **SqlSink** som mottagare.  


## Se även
| Avsnitt | Beskrivning |
| :---- | :---- |
| [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) | Den här artikeln innehåller detaljerad information om kopieringsaktiviteten som du använde i självstudien. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) | I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Pipelines](data-factory-create-pipelines.md) | Den här artikeln beskriver pipelines och aktiviteter i Azure Data Factory. |
| [Datauppsättningar](data-factory-create-datasets.md) | Den här artikeln förklarar hur datauppsättningar fungerar i Azure Data Factory.
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) | Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. 

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account



[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-deploy]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-diagram-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/CreateNewResourceGroup.png


[image-data-factory-new-datafactory-menu]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png


[image-data-factory-name-not-available]: ./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png
 


<!--HONumber=sep16_HO1-->



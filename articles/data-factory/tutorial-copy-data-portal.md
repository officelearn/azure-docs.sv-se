---
title: "Använda Azure Portal för att skapa Data Factory-pipeline"
description: "Den här självstudien innehåller stegvisa instruktioner för att skapa en datafabrik med en pipeline i Azure-portalen. Pipelinen använder kopieringsaktiviteten för att kopiera data från Azure Blob Storage till en Azure SQL-databas. "
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 8b5211e9c932221c6b6134e7e0627f4d7f964123
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Kopiera data från en Azure-blob till Azure SQL Database med Data Factory
I den här självstudiekursen skapar du en datafabrik med Azure Data Factory-användargränssnittet. Pipelinen i den här datafabriken kopierar data från Azure Blob Storage till Azure SQL Database. Konfigurationsmönstret i den här självstudien gäller kopiering av ett filbaserat datalager till ett relationsdatalager. En lista över datakällor som stöds som källor och mottagare finns i tabellen över [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).
>
> - Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Läs [get started with Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (kom igång med Data Factory version 1) om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig.

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en kopieringsaktivitet.
> * Testkör pipelinen
> * Utlös pipelinen manuellt
> * Utlös pipelinen enligt ett schema
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Nödvändiga komponenter
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
* **Azure Storage-konto**. Du kan använda blob-lagringen som **källa** för datalagringen. Om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) .
* **Azure SQL Database**. Du använder databasen som **mottagare** för datalagringen. Om du inte har någon Azure SQL Database kan du läsa om hur du skapar en i [Skapa en Azure SQL Database](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered nu Azure-blobblagringen och Azure SQL-databasen för den här självstudien genom att utföra följande steg:

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Öppna Anteckningar. Kopiera följande text och spara den som en **emp.txt**-fil på din disk.

    ```
    John,Doe
    Jane,Doe
    ```

2. Skapa en behållare med namnet **adftutorial** i Azure Blob Storage. Skapa en mapp som heter **input** i den här behållaren. Ladda sedan upp filen **emp.txt** till mappen **input**. Använd Azure-portalen eller verktyg som [Azure Storage Explorer](http://storageexplorer.com/) när du gör uppgifterna.

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

2. Ge Azure-tjänster åtkomst till SQL-servern. Kontrollera att inställningen **Tillåt åtkomst till Azure-tjänster** är **PÅ** för din Azure SQL-server så att tjänsten Data Factory kan skriva data till din Azure SQL-server. Gör så här för att kontrollera och aktivera den här inställningen:

    1. Klicka på hubben **Fler tjänster** till vänster och klicka på **SQL-servrar**.
    2. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
    3. På sidan **Brandväggsinställningar** väljer du **På** för **Tillåt åtkomst till Azure-tjänster**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget skapar du en datafabrik och startar sedan användargränssnittet för Azure Data Factory för att skapa en pipeline i datafabriken. 

1. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sida för ny datafabrik](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
     ![Sida för ny datafabrik](./media/tutorial-copy-data-portal/name-not-available-error.png)
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
        Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.      
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-copy-data-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
   ![Datafabrikens startsida](./media/tutorial-copy-data-portal/data-factory-home-page.png)
10. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory i en separat flik.

## <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en kopieringsaktivitet i datafabriken. Kopieringsaktiviteten kopierar data från Azure Blob Storage till en Azure SQL-databas. I [snabbstartssjälvstudien](quickstart-create-data-factory-portal.md) skapade du en pipeline med följande steg:

1. Skapa den länkade tjänsten. 
2. Skapa datauppsättningar för indata och utdata.
3. Skapa sedan en pipeline.

I den här självstudien börjar du med att skapa pipelinen och skapar länkade tjänster och datauppsättningar när du behöver dem för att konfigurera pipelinen. 

1. Klicka på panelen **Skapa pipeline** på sidan Kom igång. 

   ![Skapa en pipelinepanel](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
3. I fönstret **Egenskaper** för pipelinen anger du **pipelinenamnet** till **CopyPipeline**.

    ![Namn på pipeline](./media/tutorial-copy-data-portal/pipeline-name.png)
4. I verktygslådan **Aktiviteter** visar du kategorin **Dataflöde** och drar och släpper aktiviteten **Kopiera** från verktygslådan till pipelinedesignerytan. 

    ![Dra och släpp kopieringsaktivitet](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
5. På fliken **General** (Allmänt) i fönstret **Egenskaper** anger du **CopyFromBlobToSql** som aktivitetens namn.

    ![Aktivitetsnamn](./media/tutorial-copy-data-portal/activity-name.png)
6. Växla till fliken **Källa**. Klicka på **+ Ny** för att skapa en källdatauppsättning. 

    ![Meny för ny källdatauppsättning](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
7. Välj **Azure Blob Storage** i fönstret **Ny datauppsättning** och klicka på **Slutför**. Källdata är i Azure Blob Storage, så du väljer Azure Blob Storage för källdatauppsättningen. 

    ![Välj Azure Blob Storage](./media/tutorial-copy-data-portal/select-azure-storage.png)
8. Du ser att en ny **flik** öppnas i programmet med rubriken **AzureBlob1**.

    ![Fliken Azure Blob1 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
9. På fliken **General** (Allmänt) i fönstret **Egenskaper** längst ned anger du **SourceBlobDataset** som **namn**.

    ![Namn på datauppsättning](./media/tutorial-copy-data-portal/dataset-name.png)
10. Välj fliken **Anslutning** i egenskapsfönstret. Klicka på **+ Ny** bredvid textrutan **Länkad tjänst**. En länkad tjänst länkar ett datalager eller en beräkning till datafabriken. I det här fallet skapar du en länkad Azure Storage-tjänst för att länka ditt Azure Storage-konto till datalagret. Den länkade tjänsten har anslutningsinformationen som Data Factory-tjänsten använder för att ansluta till bloblagringen vid körning. Datauppsättningen anger behållaren, mappen och filen (valfritt) som innehåller källdata. 

    ![Knapp för ny länkad tjänst](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
12. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureStorageLinkedService** i fältet **Namn**. 
    2. Välj ditt Azure Storage-konto i fältet **Lagringskontonamn**.
    3. Testa anslutningen till Azure Storage-kontot genom att klicka på **Testa anslutning**.  
    4. Klicka på **Spara** för att spara den länkade tjänsten.

        ![Ny länkad Azure Storage-tjänst](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
13. Klicka på **Bläddra** för fältet **Filsökväg**.  

    ![Bläddringsknapp för fil](./media/tutorial-copy-data-portal/file-browse-button.png)
14. Navigera till mappen **adftutorial/input**, välj filen **emp.txt** och klicka på **Slutför**. Du kan också dubbelklicka på emp.txt. 

    ![Välja indatafil](./media/tutorial-copy-data-portal/select-input-file.png)
15. Bekräfta att **Filformat** är inställt på **Textformat** och att **kolumnavgränsare** är inställt på **Komma (`,`)**. Om källfilen använder olika avgränsare för rad och kolumn kan du klicka på **Detect Text Format** (Identifiera textformat) i fältet **Filformat**. Verktyget Kopiera data identifierar filformatet och avgränsarna automatiskt. Du kan fortfarande åsidosätta dessa värden. Du kan förhandsvisa data på den här sedan om du klickar på **Förhandsgranska data**.

    ![Identifiera textformat](./media/tutorial-copy-data-portal/detect-text-format.png)
17. Välj fliken **Schema** i fönstret Egenskaper och klicka på **Importera schema**. Lägg märke till att programmet identifierade två kolumner i källfilen. Du importerar schemat hit så att du kan mappa kolumner från källdatalagret till datalagret för mottagare. Om du inte behöver mappa kolumner kan du hoppa över det här steget. Importera schemat för den här självstudien.

    ![Identifiera källans schema](./media/tutorial-copy-data-portal/detect-source-schema.png)  
19. Gå till fliken  **med pipeline**  eller klicka på pipelinen i **trädvyn** till vänster.  

    ![Fliken Pipeline](./media/tutorial-copy-data-portal/pipeline-tab.png)
20. Bekräfta att **SourceBlobDataset** är valt för Source Dataset (Källdatauppsättning) i egenskapsfönstret. Du kan förhandsvisa data på den här sedan om du klickar på **Förhandsgranska data**. 
    
    ![Källdatauppsättning](./media/tutorial-copy-data-portal/source-dataset-selected.png)
21. Växla till fliken **Sink** (Mottagare) och klicka på **New** (Nytt) för att skapa en datauppsättning för mottagare. 

    ![Meny för ny datauppsättning för mottagare](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
22. Välj **Azure SQL Database** i fönstret **Ny datauppsättning** och klicka på **Slutför**. Du kopierar data till en Azure SQL-databas i den här självstudien. 

    ![Välja Azure SQL Database](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
23. På fliken **General** (Allmänt) i egenskapsfönstret anger du namnet **OutputSqlDataset**. 
    
    ![Namn på datauppsättning för utdata](./media/tutorial-copy-data-portal/output-dataset-name.png)
24. Välj fliken **Anslutning** och klicka på **New** (Nytt) för **Länkad tjänst**. En datauppsättning måste associeras med en länkad tjänst. De länkade tjänsterna har anslutningssträngen som Data Factory-tjänsten använder för att ansluta till Azure SQL-databasen vid körning. Datauppsättningen anger den behållare, mapp och fil (valfritt) som data kopieras till. 
    
    ![Knapp för ny länkad tjänst](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
25. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureSqlDatabaseLinkedService** i fältet **Namn**. 
    2. Välj din Azure SQL-server i fältet **Servernamn**.
    4. Välj din Azure SQL-databas i fältet **Databasnamn**. 
    5. Ange användarens namn i fältet **Användarnamn**. 
    6. Ange användarens lösenord i fältet **Lösenord**. 
    7. Testa anslutningen genom att klicka på **Testa anslutning**.
    8. Klicka på **Spara** för att spara den länkade tjänsten. 
    
        ![Ny länkad Azure SQL Database-tjänst](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

26. Välj **[dbo].[emp]** för **Tabell**. 

    ![Välja tabellen emp](./media/tutorial-copy-data-portal/select-emp-table.png)
27. Växla till fliken **Schema** och klicka på Import Schema (Importera schema). 

    ![Importera målschema](./media/tutorial-copy-data-portal/import-destination-schema.png)
28. Välj kolumnen **ID** och klicka på **Ta bort**. ID-kolumnen är en identitetskolumn i SQL-databasen, så kopieringsaktiviteten måste inte infoga data i den här kolumnen.

    ![Ta bort kolumn-ID](./media/tutorial-copy-data-portal/delete-id-column.png)
30. Växla till fliken med **pipelinen** och bekräfta att **OutputSqlDataset** är valt för **Sink Dataset** (Datauppsättning för mottagare).

    ![Fliken Pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
31. Växla till fliken **Mappning** i egenskapsfönstret längst ned och klicka på **Import Schemas** (Importera scheman). Lägg märke till att den första och andra kolumnen i källfilen är mappade till fälten **FirstName** och **LastName** i SQL-databasen.

    ![Mappa scheman](./media/tutorial-copy-data-portal/map-schemas.png)
33. Verifiera pipelinen genom att klicka på knappen **Verifiera**. Klicka på **högerpilen** för att stänga verifieringsfönstret.

    ![Utdatarapport för pipeline](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
34. Klicka på **Kod** i det högra hörnet. Du ser JSON-koden som är associerad med pipelinen. 

    ![Knappen Kod](./media/tutorial-copy-data-portal/code-button.png)
35. Du ser JSON-koden som liknar följande fragment:  

    ```json
    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToSql",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 20
                    },
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "SqlSink",
                            "writeBatchSize": 10000
                        },
                        "enableStaging": false,
                        "parallelCopies": 0,
                        "cloudDataMovementUnits": 0,
                        "translator": {
                            "type": "TabularTranslator",
                            "columnMappings": "Prop_0: FirstName, Prop_1: LastName"
                        }
                    },
                    "inputs": [
                        {
                            "referenceName": "SourceBlobDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputSqlDataset",
                            "type": "DatasetReference",
                            "parameters": {}
                        }
                    ]
                }
            ]
        }
    }
    ```

## <a name="test-run-the-pipeline"></a>Testkör pipelinen
Du kan testköra en pipeline innan du publicerar artefakter (länkade tjänster, datauppsättningar och pipeline) till Data Factory (eller) din egen VSTS GIT-lagringsplats. 

1. För att testköra pipelinen klickar du på **Test Run** (Testkörning) i verktygsfältet. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret. 

    ![Knappen Testkörning](./media/tutorial-copy-data-portal/test-run-output.png)
2. Kontrollera att data från källfilen infogas i SQL-måldatabasen. 

    ![Kontrollera SQL-utdata](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. Klicka på **Publicera alla** i rutan till vänster. Med den här åtgärden publicerar du enheter (länkade tjänster, datauppsättningar och pipeliner) som du skapade tidigare i Azure Data Factory.

    ![Knappen Publicera](./media/tutorial-copy-data-portal/publish-button.png)
4. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill visa meddelanden klickar du på **Show Notifications** (Visa meddelanden) i den vänstra sidorutan. Stäng meddelandefönstret genom att klicka på **X**.

    ![Visa meddelanden](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>Konfigurera kodlagringsplats
Du kan publicera koden som är kopplad till artefakterna för datafabriken till en kodlagringsplats för Visual Studio Team System (VSTS). I det här steget skapar du koddatabasen. 

Om du inte vill arbeta med VSTS-kodlagringsplatsen kan du hoppa över det här steget och fortsätta att publicera till Data Factory som i föregående steg. 

1. Klicka på **Data Factory** i det vänstra hörnet (eller) nedåtpilen bredvid och klicka på **Configure Code Repository** (Konfigurera kodlagringsplats). 

    ![Knappen Kod](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. På sidan **Inställningar för lagringsplats** utför du följande steg: 
    1. Välj **Visual Studio Team Services Git** i fältet **Typ av lagringsplats**.
    2. Välj ditt VSTS-konto i fältet för **Visual Studio Team Services-konto**.
    3. Välj ett projekt i VSTS-kontot i fältet **Projektnamn**.
    4. Skriv **Tutorial2** som **namn på Git-lagringsplatsen** som ska associeras till din datafabrik. 
    5. Kontrollera att alternativet **Import existing Data Factory resources to repository** (Importera befintliga Data Factory-resurser till lagringsplats) är valt. 
    6. Spara inställningarna genom att klicka på **Spara**. 

        ![Inställningar för lagringsplats](./media/tutorial-copy-data-portal/repository-settings.png)
3. Bekräfta att **VSTS GIT** är valt för lagringsplatsen.

    ![VSTS GIT valt](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. I en separat flik i webbläsaren går du till lagringsplatsen **Tutorial2**. Du ser två grenar: **master** och **adf_publish**.

    ![Grenarna master och adf_publish](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Kontrollera att **JSON-filerna** för Data Factory-entiteterna är i grenen **master**.

    ![Filer i huvudgrenen](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Bekräfta att **JSON-filerna** inte är i grenen **adf_publish** ännu. 

    ![Filer i grenen adf_publish](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. Lägg till en **beskrivning** för **pipelinen** och klicka på knappen **Spara** i verktygsfältet. 

    ![Lägga till beskrivning för pipelinen](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Nu bör du se en **gren** med ditt användarnamn på lagringsplatsen **Tutorial2**. Ändringen du gjorde är i din egen gren, inte i huvudgrenen. Du kan endast publicera entiteter från huvudgrenen.

    ![Din gren](./media/tutorial-copy-data-portal/your-branch.png)
9. Flytta markören över knappen **Synkronisera** (klicka inte ännu), välj alternativet **Commit Changes** (Verkställ ändringar) och klicka på knappen **Synkronisera** för att synkronisera dina ändringar med grenen **master**. 

    ![Verkställa och synkronisera ändringar](./media/tutorial-copy-data-portal/commit-and-sync.png)
9. I fönstret Sync your changes (Synkronisera ändringar) gör du följande: 

    1. Bekräfta att **CopyPipeline** visas i den uppdaterade listan över pipeliner.
    2. Bekräfta att **Publish changes after sync** (Publicera ändringar efter synkronisering) är markerat. Om du avmarkerar det här alternativet synkroniserar du endast ändringarna i din gren med huvudgrenen, men du publicerar dem inte till Data Factory-tjänsten. Du kan publicera dem senare med knappen **Publicera**. Om du markerar det här alternativet synkroniseras ändringarna till huvudgrenen först, och sedan till Data Factory-tjänsten.
    3. Klicka på **Sync** (Synkronisera). 

    ![Fönstret Synkronisera ändringar](./media/tutorial-copy-data-portal/sync-your-changes.png)
10. Nu kan du se filer i grenen **adf_publish** för lagringsplatsen **Tutorial2**. Du ser även Azure Resource Manager-mallen för din Data Factory-lösning i den här grenen.  

    ![Filer i grenen adf_publish](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>Utlös pipelinen manuellt
I det här steget utlöser du manuellt pipelinen du publicerade i föregående steg. 

1. Klicka på **Utlösare** i verktygsfältet och klicka på **Trigger Now** (Utlös nu). På sidan **Pipelinekörning** klickar du på **Slutför**.  

    ![Menyn Trigger now (Utlös nu)](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Växla till fliken **Övervaka** till vänster. Du ser en pipelinekörning som är utlöst av en manuell utlösare. Du kan använda länkar i kolumnen Åtgärder för att visa aktivitetsinformation och köra pipelinen på nytt.

    ![Övervaka pipelinekörning](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningarna klickar du på länken **View activity runs** (Visa aktivitetskörningar) i kolumnen **Action** (Åtgärd). Det finns bara en aktivitet i det här exemplet. Därför visas bara en post i listan. Om du vill se mer information om kopieringsåtgärden klickar du på länken **Details** (glasögonikonen) i kolumnen **Åtgärder**. Om du vill växla tillbaka till vyn med **pipelinekörningar** klickar du på **Pipeliner** högst upp. Om du vill uppdatera vyn klickar du på **Uppdatera**.

    ![Visa aktivitetskörningar](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Kontrollera att två rader till har lagts till i tabellen **emp** i Azure SQL-databasen. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Utlös pipelinen enligt ett schema
I det här schemat skapar du en schemautlösare för pipelinen. Utlösaren kör pipelinen enligt det angivna schemat (varje timme, varje dag osv.). I det här exemplet ställer du in att utlösaren ska köras varje minut fram till angiven sluttid. 

1. Välj fliken **Redigera** till vänster. 

    ![Fliken Redigera](./media/tutorial-copy-data-portal/edit-tab.png)
2. Klicka på **Utlösare** och välj **New/Edit** (Nytt/Redigera). Om pipelinen inte är den aktiva växlar du till den. 

    ![Utlösarens meny för nytt/redigera](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. I fönstret **Add Triggers** (Lägg till utlösare) klickar du på **Choose trigger...** (Välj utlösare...) och klickar på **New** (Nytt). 

    ![Lägg till utlösare – ny utlösare](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. Utför följande steg i fönstret **Ny utlösare**: 

    1. Som **namn** anger du **RunEveryMinute**.
    2. Välj **On Date** (Datum) i **End** (Slut). 
    3. Klicka på listrutan för **End On** (Slut den).
    4. Välj den **aktuella dagen**. Som standard är slutdagen inställd på nästa dag. 
    5. Uppdatera **minutdelen** så att den är några minuter efter den aktuella datumet/tiden. Utlösaren aktiveras enbart när du har publicerat ändringarna. Om du ställer in den bara med ett par minuters skillnad och du inte publicerar den förrän då ser du därför ingen utlösarkörning.  
    6. Klicka på **Använd**. 

        ![Ställa in egenskaper för utlösare](./media/tutorial-copy-data-portal/set-trigger-properties.png)
    7. Kontrollera alternativet **Aktiverad**. Du kan inaktivera och aktivera senare med den här kryssrutan.
    8. Klicka på **Nästa**.

        ![Utlösare aktiverad – nästa](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Det finns ingen associerad kostnad till varje pipelinekörning. Ställ därför in slutdatumet korrekt. 
6. På sidan **Trigger Run Parameters** (Parametrar för utlösarkörning) läser du varningen och klickar på **Slutför**. Pipelinen i det här exemplet tar inga parametrar. 

    ![Pipeline-parametrar](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
7. Klicka på **Synkronisera** för att synkronisera ändringar i din gren med huvudgrenen. Som standard är **Publish changes after sync** (Publicera ändringar efter synkronisering) markerat. När du väljer **Synkronisera** publiceras därför även uppdaterade entiteter till Azure Data Factory-tjänsten från huvudgrenen. Utlösaren aktiveras inte förrän publiceringen genomförs.

    ![Publicera utlösare](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
9. Växla till fliken **Övervakare** till vänster för att se de utlösta pipelinekörningarna. 

    ![Utlösta pipelinekörningar](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
9. Om du vill växla från pipelinekörningsvyn till vyn med utlösarkörningar klickar du på Pipeline Runs (Pipelinekörningar) och väljer Trigger Runs (Utlösarkörningar).
    
    ![Menyn Trigger Runs (Utlösarkörningar)](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
10. Utlösarkörningarna visas i en lista. 

    ![Listan med utlösarkörningar](./media/tutorial-copy-data-portal/trigger-runs-list.png)
11. Verifiera att två rader per minut (för varje pipelinekörning) infogas i tabellen **emp** tills den angivna sluttiden. 

## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en kopieringsaktivitet.
> * Testkör pipelinen
> * Utlös pipelinen manuellt
> * Utlös pipelinen enligt ett schema
> * Övervaka pipelinen och aktivitetskörningarna.


Fortsätt till följande självstudie och lär dig att kopiera data från en lokal plats till molnet: 

> [!div class="nextstepaction"]
>[Kopiera data från en lokal plats till molnet](tutorial-hybrid-copy-portal.md)

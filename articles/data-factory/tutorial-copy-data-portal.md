---
title: Använda Azure Portal för att skapa en datafabrikspipeline | Microsoft Docs
description: Den här självstudien innehåller stegvisa instruktioner för att skapa en datafabrik med en pipeline i Azure-portalen. Pipelinen använder kopieringsaktiviteten för att kopiera data från Azure Blob Storage till en SQL-databas.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/09/2018
ms.author: jingwang
ms.openlocfilehash: 34c78a114c1d106c400a94941aa113153383e206
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
ms.locfileid: "30173346"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Kopiera data från en Azure Blob Storage till en SQL-databas med Azure Data Factory
I den här självstudiekursen skapar du en datafabrik med Azure Data Factory-användargränssnittet. Pipelinen i den här datafabriken kopierar data från Azure Blob Storage till en SQL-databas. Konfigurationsmönstret i den här självstudien gäller kopiering av ett filbaserat datalager till ett relationsdatalager. En lista över datakällor som stöds som källor och mottagare finns i tabellen över [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Om du inte har använt datafabriken tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).
>
> - Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory, som är allmänt tillgänglig, se [Kom igång med Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en kopieringsaktivitet.
> * Testkör pipelinen.
> * Utlös pipelinen manuellt.
> * Utlös pipelinen enligt ett schema.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Nödvändiga komponenter
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
* **Azure Storage-konto**. Du kan använda Blob Storage som *källa* för datalagringen. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* **Azure SQL Database**. Du använder databasen som *mottagare* för datalagringen. Om du inte har en SQL-databas kan du läsa om hur du skapar en i [Skapa en SQL-databas](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered nu Blob Storage och SQL-databasen för den här självstudien genom att utföra nedanstående steg.

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Öppna Anteckningar. Kopiera följande text och spara den som en **emp.txt**-fil på din disk:

    ```
    John,Doe
    Jane,Doe
    ```

2. Skapa en behållare med namnet **adftutorial** i Blob Storage. Skapa en mapp som heter **input** i den här behållaren. Ladda sedan upp filen **emp.txt** till mappen **input**. Använd Azure-portalen eller verktyg som [Azure Storage Explorer](http://storageexplorer.com/) när du gör uppgifterna.

#### <a name="create-a-sink-sql-table"></a>Skapa en SQL-mottagartabell

1. Använd följande SQL-skript för att skapa tabellen **dbo.emp** i din SQL-databas:

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

2. Ge Azure-tjänster åtkomst till SQL Server. Kontrollera att inställningen **Tillåt åtkomst till Azure-tjänster** är **PÅ** för din SQL Server så att datafabriken kan skriva data till din SQL Server. Gör så här för att kontrollera och aktivera inställningen:

    a. Till vänster väljer du **Fler tjänster** > **SQL-servrar**.

    b. Välj din server och under **INSTÄLLNINGAR** väljer du **Brandvägg**.

    c. På sidan **Brandväggsinställningar** väljer du **PÅ** för **Tillåt åtkomst till Azure-tjänster**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget skapar du en datafabrik och startar sedan användargränssnittet för Data Factory för att skapa en pipeline i datafabriken. 

1. Öppna webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. I den vänstra menyn väljer du **Nytt** > **Data och analys** > **Data Factory**. 
  
   ![Skapa ny datafabrik](./media/tutorial-copy-data-portal/new-azure-data-factory-menu.png)
3. I fönstret **Ny datafabrik**, under **Namn**, anger du **ADFTutorialDataFactory**. 
      
     ![Ny datafabrik](./media/tutorial-copy-data-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara *globalt unikt*. Om följande felmeddelande visas för namnfältet ändrar du namnet på datafabriken (t.ex. dittnamnADFTutorialDataFactory). Se artikeln [om namnregler för datafabriker](naming-rules.md) för namnregler för datafabriksartefakter.
  
   ![Felmeddelande](./media/tutorial-copy-data-portal/name-not-available-error.png)
4. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
5. Gör något av följande för **Resursgrupp**:
     
    a. Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

    b. Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md). 
6. Under **Version** väljer du **V2 (förhandsversion)**.
7. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas i andra regioner.
8. Välj **fäst till instrumentpanelen**. 
9. Välj **Skapa**. 
10. Du ser följande panel på instrumentpanelen med statusen **Distribuerar datafabrik**: 

    ![Panelen Distribuerar datafabrik](media/tutorial-copy-data-portal/deploying-data-factory.png)
11. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
    ![Datafabrikens startsida](./media/tutorial-copy-data-portal/data-factory-home-page.png)
12. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en kopieringsaktivitet i datafabriken. Kopieringsaktiviteten kopierar data från Blob Storage till en SQL-databas. I [snabbstartssjälvstudien](quickstart-create-data-factory-portal.md) skapade du en pipeline med följande steg:

1. Skapa den länkade tjänsten. 
2. Skapa datauppsättningar för indata och utdata.
3. Skapa en pipeline.

I denna självstudie börjar du med att skapa pipelinen. Sedan skapar du länkade tjänster och datauppsättningar när du behöver dem för att konfigurera pipelinen. 

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**. 

   ![Skapa pipeline](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
2. I fönstret **Egenskaper** för pipelinen anger du i **Namn** värdet **CopyPipeline** som namn på pipelinen.

    ![Namn på pipeline](./media/tutorial-copy-data-portal/pipeline-name.png)
3. I verktygslådan **Aktiviteter** visar du kategorin **Dataflöde** och drar och släpper aktiviteten **Kopiera** från verktygslådan till pipelinedesignerytan. 

    ![Kopieringsaktivitet](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)
4. På fliken **Allmänt** i fönstret **Egenskaper** anger du **CopyFromBlobToSql** som aktivitetens namn.

    ![Aktivitetsnamn](./media/tutorial-copy-data-portal/activity-name.png)
5. Gå till fliken **Källa**. Välj **+ Nytt** för att skapa en källdatauppsättning. 

    ![Fliken Källa](./media/tutorial-copy-data-portal/new-source-dataset-button.png)
6. I fönstret **Ny datauppsättning** väljer du **Azure Blob Storage** och klickar på **Slutför**. Dina källdata finns i Blob Storage, så du väljer **Azure Blob Storage** för källdatauppsättningen. 

    ![Val av lagringsutrymme](./media/tutorial-copy-data-portal/select-azure-storage.png)
7. Du ser att en ny flik öppnas i programmet med rubriken **AzureBlob1**.

    ![Fliken AzureBlob1 ](./media/tutorial-copy-data-portal/new-tab-azure-blob1.png)        
8. På fliken **Allmänt** nedtill i fönstret **Egenskaper** skriver du för **Namn** värdet **SourceBlobDataset**.

    ![Namn på datauppsättning](./media/tutorial-copy-data-portal/dataset-name.png)
9. Gå till fliken **Anslutning** i fönstret **Egenskaper**. Vid textrutan **Länkad tjänst** väljer du **+ Nytt**. 

    En länkad tjänst länkar ett datalager eller en beräkning till datafabriken. I det här fallet skapar du en länkad Storage-tjänst för att länka ditt lagringskonto till datalagret. Den länkade tjänsten har anslutningsinformationen som Data Factory använder för att ansluta till Blob Storage vid körning. Datauppsättningen anger behållaren, mappen och filen (valfritt) som innehåller källdata. 

    ![Knapp för ny länkad tjänst](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)
10. Utför följande steg i fönstret **Ny länkad tjänst**: 

    a. Under **Namn** anger du **AzureStorageLinkedService**. 

    b. Under **Lagringskontonamn** väljer du ditt lagringskonto.

    c. Välj **Testa anslutningen** för att testa anslutningen till lagringskontot.

    d. Välj **Spara** för att spara den länkade tjänsten.

    ![Ny länkad tjänst](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)
11. Vid **Filsökväg** väljer du **Bläddra**.

    ![Bläddringsknapp för filsökväg](./media/tutorial-copy-data-portal/file-browse-button.png)
12. Gå till mappen **adftutorial/input**, välj filen **emp.txt** och klicka på **Slutför**. Du kan också dubbelklicka på **emp.txt**. 

    ![Välja indatafil](./media/tutorial-copy-data-portal/select-input-file.png)
13. Bekräfta att **Filformat** är satt till **Textformat** och att **Kolumnavgränsare** är satt till **Komma (`,`)**. Om källfilen använder olika avgränsare för rad och kolumn kan du välja **Detect Text Format** (Identifiera textformat) i fältet **Filformat**. Verktyget Kopiera data identifierar filformatet och avgränsarna automatiskt. Du kan fortfarande åsidosätta dessa värden. Om du vill förhandsgranska data på den här sidan väljer du **Förhandsgranska data**.

    ![Identifiera textformat](./media/tutorial-copy-data-portal/detect-text-format.png)
14. Gå till fliken **Schema** i fönstret **Egenskaper** och välj **Importera schema**. Lägg märke till att programmet identifierade två kolumner i källfilen. Importera schemat hit så att du kan mappa kolumner från källdatalagret till datalagret för mottagare. Om du inte behöver mappa kolumner kan du hoppa över det här steget. Importera schemat för den här självstudien.

    ![Identifiera källans schema](./media/tutorial-copy-data-portal/detect-source-schema.png)  
15. Gå sedan till fliken med pipelinen eller välj pipeline till vänster.

    ![Fliken Pipeline](./media/tutorial-copy-data-portal/pipeline-tab.png)
16. I **Källdatauppsättning** i fönstret **Egenskaper** kontrollerar du att **SourceBlobDataset** är valt. Om du vill förhandsgranska data på den här sidan väljer du **Förhandsgranska data**. 
    
    ![Källdatauppsättning](./media/tutorial-copy-data-portal/source-dataset-selected.png)
17. Gå till fliken **Mottagare** och välj **+ Nytt** för att skapa en datauppsättning för mottagare. 

    ![Datauppsättning för mottagare](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
18. I fönstret **Ny datauppsättning** väljer du **Azure SQL Database** och sedan **Slutför**. I dessa självstudier kopierar du data till en SQL-databas. 

    ![Val av SQL-databas](./media/tutorial-copy-data-portal/select-azure-sql-database.png)
19. På fliken **Allmänt** i fönstret **Egenskaper** anger du för **Namn** värdet **OutputSqlDataset**. 
    
    ![Namn på datauppsättning för utdata](./media/tutorial-copy-data-portal/output-dataset-name.png)
20. Gå till fliken **Anslutning** och välj **Länkad tjänst** och sedan **+ Nytt**. En datauppsättning måste associeras med en länkad tjänst. Den länkade tjänsten har anslutningssträngen som Data Factory använder för att ansluta till SQL-databasen vid körning. Datauppsättningen anger den behållare, mapp och fil (valfritt) som data kopieras till. 
    
    ![Länkad tjänst](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
21. Utför följande steg i fönstret **Ny länkad tjänst**: 

    a. Under **Namn** anger du **AzureSqlDatabaseLinkedService**.

    b. Under **Servernamn** väljer du din SQL Server-instans.

    c. Under **Databasnamn** väljer du din SQL-databas.

    d. Under **Användarnamn** anger du namnet på användaren.

    e. Under **Lösenord** anger du användarens lösenord.

    f. Välj **Testanslutning** för att testa anslutningen.

    g. Välj **Spara** för att spara den länkade tjänsten. 
    
    ![Spara ny länkad tjänst](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

22. Under **Tabell** väljer du **[dbo].[emp]**. 

    ![Tabell](./media/tutorial-copy-data-portal/select-emp-table.png)
23. Gå till fliken **Schema** och välj **Importera schema**. 

    ![Välj importschema](./media/tutorial-copy-data-portal/import-destination-schema.png)
24. Välj kolumnen **ID** och sedan **Ta bort**. Kolumnen **ID** är en identitetskolumn i SQL-databasen, så kopieringsaktiviteten måste inte infoga data i den här kolumnen.

    ![Ta bort kolumn-ID](./media/tutorial-copy-data-portal/delete-id-column.png)
25. Gå till fliken med pipelinen och kontrollera i **Sink Dataset** (Datauppsättning för mottagare) att **OutputSqlDataset** har valts.

    ![Fliken Pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        
26. Gå till fliken **Mappning** längst ned i fönstret **Egenskaper** och välj **Importera scheman**. Lägg märke till att den första och andra kolumnen i källfilen är mappade till **FirstName** och **LastName** i SQL-databasen.

    ![Mappa scheman](./media/tutorial-copy-data-portal/map-schemas.png)
27. Verifiera pipelinen genom att välja **Verifiera**. Klicka på högerpilen i det övre högra hörnet för att stänga verifieringsfönstret.

    ![Utdatarapport för pipeline](./media/tutorial-copy-data-portal/pipeline-validation-output.png)   
28. I det övre högra hörnet väljer du **Kod**. Du ser JSON-koden som är associerad med pipelinen. 

    ![Knappen Kod](./media/tutorial-copy-data-portal/code-button.png)
29. Du ser JSON-kod som liknar följande kodavsnitt: 

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
Du kan testköra en pipeline innan du publicerar artefakter (länkade tjänster, datauppsättningar och pipeline) till datafabriken eller din egen GIT-lagringsplats i Visual Studio Team Services. 

1. För att testköra pipelinen väljer du **Testkörning** i verktygsfältet. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret. 

    ![Testa pipeline](./media/tutorial-copy-data-portal/test-run-output.png)
2. Kontrollera att data från källfilen infogas i SQL-måldatabasen. 

    ![Kontrollera SQL-utdata](./media/tutorial-copy-data-portal/verify-sql-output.png)
3. Klicka på **Publicera alla** i rutan till vänster. Med den här åtgärden publicerar du enheter (länkade tjänster, datauppsättningar och pipeliner) som du skapat i datafabriken.

    ![Publicera](./media/tutorial-copy-data-portal/publish-button.png)
4. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill visa meddelanden klickar du på fliken **Visa meddelanden**. Stäng meddelandefönstret genom att klicka på **Stäng**.

    ![Visa meddelanden](./media/tutorial-copy-data-portal/show-notifications.png)

## <a name="configure-code-repository"></a>Konfigurera kodlagringsplats
Du kan publicera koden som är kopplad till artefakterna för datafabriken till en kodlagringsplats för Visual Studio Team Services. I det här steget skapar du koddatabasen.  Mer information om visuell redigering med VSTS-integrering finns i [Skapa med VSTS Git-integrering](author-visually.md#author-with-vsts-git-integration).

Om du inte vill arbeta med koddatabasen i Visual Studio Team Services kan du hoppa över det här steget. Du kan fortsätta publicera till datafabriken som du gjorde i föregående steg. 

1. Klicka på **Datafabrik** i det övre vänstra hörnet eller använd nedåtpilen bredvid och välj **Configure Code Repository** (Konfigurera kodlagringsplats). 

    ![Konfigurera kodlagringsplats](./media/tutorial-copy-data-portal/configure-code-repository-button.png)
2. På sidan **Inställningar för lagringsplats** utför du följande steg:

    a. Under **Typ av lagringsplats** väljer du **Visual Studio Team Services Git**.

    b. Under **Visual Studio Team Services-konto** väljer du ditt Visual Studio Team Services-konto.

    c. Under **Projektnamn** väljer du ett projekt i ditt Visual Studio Team Services-konto.

    d. Som **namn på lagringsplatsen i Git** anger du **Tutorial2** för den Git-lagringsplats som ska kopplas till din datafabrik.

    e. Kontrollera att kryssrutan **Import existing Data Factory resources to repository** (Importera befintliga datafabriksresurser till lagringsplats) är förkryssad.

    f. Spara inställningarna genom att klicka på **Spara**. 

    ![Inställningar för lagringsplats](./media/tutorial-copy-data-portal/repository-settings.png)
3. Bekräfta att **VSTS GIT** är valt för lagringsplatsen.

    ![Välj VSTS GIT](./media/tutorial-copy-data-portal/vsts-git-selected.png)
4. I en separat flik i webbläsaren går du till lagringsplatsen **Tutorial2**. Du ser två grenar: **adf_publish** och **master**.

    ![Grenarna master och adf_publish](./media/tutorial-copy-data-portal/initial-branches-vsts-git.png)
5. Kontrollera att JSON-filerna för datafabriksentiteterna är i grenen **master**.

    ![Filer i huvudgrenen](./media/tutorial-copy-data-portal/master-branch-files.png)
6. Kontrollera att JSON-filerna inte är i grenen **adf_publish** än. 

    ![Filer i grenen adf_publish](./media/tutorial-copy-data-portal/adf-publish-files.png)
7. I **Beskrivning** lägger du till en beskrivning för pipelinen och väljer **Spara** i verktygsfältet. 

    ![Pipeline-beskrivning](./media/tutorial-copy-data-portal/pipeline-description.png)
8. Nu ser du en gren med ditt användarnamn på lagringsplatsen **Tutorial2**. Ändringen du gjorde är i din egen gren, inte i huvudgrenen. Du kan endast publicera entiteter från huvudgrenen.

    ![Din gren](./media/tutorial-copy-data-portal/your-branch.png)
9. Flytta markören över knappen **Synkronisera** (klicka inte ännu), välj alternativet **Commit Changes** (Verkställ ändringar) och klicka på knappen **Synkronisera** för att synkronisera dina ändringar med mastergrenen. 

    ![Verkställa och synkronisera ändringar](./media/tutorial-copy-data-portal/commit-and-sync.png)
10. I fönstret **Synkronisera ändringar** gör du följande: 

    a. Bekräfta att **CopyPipeline** visas i den uppdaterade listan **Pipeliner**.

    b. Bekräfta att **Publish changes after sync** (Publicera ändringar efter synkronisering) är markerat. Om du avmarkerar den här kryssrutan, kommer bara ändringar i din gren att synkroniseras med mastergrenen. De publiceras inte till datafabriken än. Du kan publicera dem senare med knappen **Publicera**. Om du markerar den här kryssrutan synkroniseras ändringarna till huvudgrenen först, sedan publiceras de till datafabriken.

    c. Välj **Synkronisera**. 

    ![Synkronisera dina ändringar](./media/tutorial-copy-data-portal/sync-your-changes.png)
11. Nu kan du se filer i grenen **adf_publish** för lagringsplatsen **Tutorial2**. Du ser även Azure Resource Manager-mallen för din datafabrikslösning i den här grenen. 

    ![Fillista i grenen adf_publish](./media/tutorial-copy-data-portal/adf-publish-files-after-publish.png)


## <a name="trigger-the-pipeline-manually"></a>Utlös pipelinen manuellt
I det här steget utlöser du manuellt pipelinen du publicerade i föregående steg. 

1. Välj **Utlös** i verktygsfältet och sedan **Trigger Now** (Utlös nu). På sidan **Pipeline Run** (Pipelinekörning) väljer du **Slutför**.  

    ![Utlösarpipeline](./media/tutorial-copy-data-portal/trigger-now-menu.png)
2. Gå till fliken **Övervaka** till vänster. Du ser en pipelinekörning som är utlöst av en manuell utlösare. Du kan använda länkar i kolumnen **Åtgärder** för att visa aktivitetsinformation och köra pipelinen på nytt.

    ![Övervaka pipelinekörningar](./media/tutorial-copy-data-portal/monitor-pipeline.png)
3. Om du vill se aktivitetskörningar som är associerade med pipelinekörningen, väljer du länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder). Det finns bara en aktivitet i det här exemplet. Därför visas bara en post i listan. Om du vill se mer information om kopieringsåtgärden väljer du länken **Information** (glasögonikonen) i kolumnen **Åtgärder**. Välj **Pipelines** högst upp för att gå tillbaka till vyn **Pipeline Runs** (Pipelinekörning). Välj **Uppdatera** för att uppdatera vyn.

    ![Övervaka aktivitetskörningar](./media/tutorial-copy-data-portal/view-activity-runs.png)
4. Kontrollera att två rader har lagts till i tabellen **emp** i SQL-databasen. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Utlös pipelinen enligt ett schema
I det här schemat skapar du en schemautlösare för pipelinen. Utlösaren kör pipelinen enligt det angivna schemat, t.ex. varje timme eller dagligen. I det här exemplet ställer du in att utlösaren ska köras varje minut fram till angiven sluttid. 

1. Gå till fliken **Redigera** till vänster. 

    ![Fliken Redigera](./media/tutorial-copy-data-portal/edit-tab.png)
2. Välj **Utlösare** och sedan **Ny/Redigera**. Gå till pipelinen om den inte är aktiv. 

    ![Utlösaralternativ](./media/tutorial-copy-data-portal/trigger-new-edit-menu.png)
3. På sidan **Add Triggers** (Lägg till utlösare) väljer du **Choose trigger** (Välj utlösare) och sedan **+ Ny**. 

    ![Knappen Ny](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
4. Utför följande steg i fönstret **Ny utlösare**: 

    a. Under **Namn** anger du **RunEveryMinute**.

    b. Under **Slut**  väljer du **På datum**.

    c. Under **Slutar den** väljer du i listrutan.

    d. Välj den **aktuella dagen**. Som standard är slutdagen inställd på nästa dag.

    e. Uppdatera **minutdelen** så att den är några minuter efter den aktuella datumet/tiden. Utlösaren aktiveras enbart när du har publicerat ändringarna. Om du ställer in den med bara ett par minuters skillnad och du inte publicerar innan dess ser du ingen utlösarkörning.

    f. Välj **Använd**. 

    ![Egenskaper för utlösare](./media/tutorial-copy-data-portal/set-trigger-properties.png)

    g. Välj alternativet **Aktiverad**. Du kan inaktivera och aktivera senare med den här kryssrutan.

    h. Välj **Nästa**.

    ![Knappen Aktiverad](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Det är kostnader associerade med varje pipelinekörning, så var noga med att ställa in slutdatum korrekt. 
5. På sidan **Trigger Run Parameters** (Parametrar för utlösarkörning) läser du varningen och klickar på **Slutför**. Pipelinen i det här exemplet tar inga parametrar. 

    ![Parametrar för utlösarkörning](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)
6. Klicka på **Synkronisera** för att synkronisera ändringar i din gren med huvudgrenen. Som standard är **Publish changes after sync** (Publicera ändringar efter synkronisering) markerat. När du väljer **Synkronisera** publiceras även uppdaterade entiteter till datafabriken från huvudgrenen. Utlösaren aktiveras inte förrän publiceringen genomförs.

    ![Synkronisera dina ändringar](./media/tutorial-copy-data-portal/sync-your-changes-with-trigger.png) 
7. Växla till fliken **Övervakare** till vänster för att se de utlösta pipelinekörningarna. 

    ![Utlösta pipelinekörningar](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
8. För att växla från vyn **Pipeline Runs** (Pipelinekörningar) till vyn **Trigger Runs** (Utlösarkörning) väljer du **Pipeline Runs** och sedan **Trigger Runs**.
    
    ![Utlösarkörning](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
9. Utlösarkörningarna visas i en lista. 

    ![Listan med utlösarkörningar](./media/tutorial-copy-data-portal/trigger-runs-list.png)
10. Verifiera att två rader per minut (för varje pipelinekörning) infogas i tabellen **emp** tills den angivna sluttiden. 

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Blob Storage. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en kopieringsaktivitet.
> * Testkör pipelinen.
> * Utlös pipelinen manuellt.
> * Utlös pipelinen enligt ett schema.
> * Övervaka pipelinen och aktivitetskörningarna.


Fortsätt till nästa självstudie om du vill lära dig att kopiera data från en lokal plats till molnet: 

> [!div class="nextstepaction"]
>[Kopiera data från en lokal plats till molnet](tutorial-hybrid-copy-portal.md)

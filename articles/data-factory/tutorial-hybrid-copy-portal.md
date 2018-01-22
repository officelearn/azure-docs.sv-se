---
title: "Kopiera data från SQL Server till Blob Storage med hjälp av Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kopierar data från ett lokalt datalager till Azure-molnet med hjälp av en lokal Integration Runtime i Azure Data Factory."
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
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: 7b734a76545dbcbddac3c7ad7beae60d662a9129
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Självstudie: Kopiera data från en lokal SQL Server-databas till Azure Blob Storage
I den här självstudien använder du användargränssnittet för Azure Data Factory för att skapa en Data Factory-pipeline som kopierar data från en lokal SQL Server-databas till Azure Blob Storage. Du skapar och använder en lokal installation av Integration Runtime som flyttar data mellan lokala datalager och datalager i molnet. 

> [!NOTE]
> Den här artikeln gäller för version 2 av Azure Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Den här artikeln ger inte någon detaljerad introduktion till Data Factory-tjänsten. Mer information finns i [Introduktion till Azure Data Factory](introduction.md). 

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage. 
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

## <a name="prerequisites"></a>Nödvändiga komponenter
### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte redan har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara tilldelad en *deltagare* eller *ägare*, eller vara en *administratör* för Azure-prenumerationen. 

Om du vill visa vilka behörigheter du har i prenumerationen öppnar du Azure-portalen, väljer användarnamnet i det övre högra hörnet och sedan väljer du **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. För exempel på instruktioner om hur du lägger till en användare till en roll läser du artikeln [Lägg till roller](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 och 2017
I den här självstudien använder du en lokal SQL Server-databas som *källdatalager*. Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den här lokala SQL Server-databasen (källa) till Azure Blob Storage (mottagare). Skapa sedan en tabell med namnet **emp** i SQL Server-databasen och infoga ett par exempelposter i tabellen. 

1. Starta SQL Server Management Studio. Om det inte redan är installerat på datorn öppnar du [Ladda ner SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Anslut till SQL Server-instansen med hjälp av dina autentiseringsuppgifter. 

3. Skapa en exempeldatabas. I trädvyn högerklickar du på **Databaser** och sedan väljer du **Ny databas**. 
 
4. I fönstret **Ny databas** anger du ett namn för databasen och sedan väljer du **OK**. 

5. Skapa tabellen **emp** och infoga lite exempeldata i den genom att köra följande frågeskript mot databasen:

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

6. I trädvyn högerklickar du på databasen du skapade och sedan väljer du **Ny fråga**.

### <a name="azure-storage-account"></a>Azure-lagringskonto
I den här självstudien använder du ett allmänt Azure Storage-konto (Azure Blob Storage för att vara exakt) som datalager för destination/mottagare. Om du inte har något allmänt Azure Storage-konto kan du läsa [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account). Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den här lokala SQL Server-databasen (källa) till Azure Blob Storage (mottagare). 

#### <a name="get-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för Azure Storage-kontot i den här självstudien. Hämta namnet och nyckeln till lagringskontot genom att göra följande: 

1. Logga in på [Azure Portal](https://portal.azure.com) med användarnamnet och lösenordet för Azure. 

2. I det vänstra fönstret väljer du **Fler tjänster**, filtrerar genom att använda nyckelordet **Lagring** och sedan väljer du **Lagringskonton**.

    ![Sök efter lagringskontot](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton och välj sedan ditt lagringskonto. 

4. I fönstret **Lagringskonto** väljer du **Åtkomstnycklar**.

    ![Hämta lagringskontots namn och nyckel](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. I rutorna **Lagringskontonamn** och **key1** kopierar du värdena och klistrar sedan in dem i Anteckningar eller annat redigeringsprogram så att du har dem när du behöver dem senare i självstudien. 

#### <a name="create-the-adftutorial-container"></a>Skapa behållaren adftutorial 
I det här avsnittet skapar du en blobbehållare med namnet **adftutorial** i Azure Blob Storage. 

1. I fönstret **Lagringskonto** växlar du till **Översikt** och klickar sedan på **Blobar**. 

    ![Alternativet Välj blobar](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. I fönstret **Blobtjänst** väljer du **Behållare**. 

    ![Lägga till behållarknapp](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. I fönstret **Ny behållare**, i rutan **Namn**, anger du **adftutorial** och väljer **OK**. 

    ![Ange namn på behållare](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Välj **adftutorial** i listan över behållare.  

    ![Välja behållaren](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Låt **behållarfönstret** för **adftutorial** vara öppet. Du kommer att använda den för att bekräfta utdata i slutet av självstudien. Data Factory skapar automatiskt utdatamappen i den här behållaren, så du behöver inte skapa en.

    ![Behållarfönster](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget skapar du en datafabrik och startar sedan användargränssnittet för Azure Data Factory för att skapa en pipeline i datafabriken. 

1. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sida för ny datafabrik](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
     ![Sida för ny datafabrik](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
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

    ![panelen distribuerar datafabrik](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
   ![Datafabrikens startsida](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
10. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik. 


## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Klicka på **Skapa pipeline** på sidan **Kom igång**. En pipeline skapas automatiskt åt dig. Pipelinen visas i trädvyn och dess redigerare är öppen. 

   ![Sidan Kom igång](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. Välj fliken **Allmänt** i fönstret **Egenskaper** och skriv **SQLServerToBlobPipeline** som **namn**.

   ![Namn på pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
2. I verktygslådan **Aktiviteter** visar du **Dataflöde** och drar och släpper **kopieringsaktiviteten** till pipelinedesignytan. Ge aktiviteten namnet **CopySqlServerToAzureBlobActivity**.

   ![Aktivitetsnamn](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
3. I fönstret Egenskaper växlar du till fliken **Källa** och klickar på **+ Ny**.

   ![Knapp för ny källdatauppsättning](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
4. I fönstret **Ny datauppsättning** letar du reda på **SQL Server**, väljer **SQL Server** och klickar på **Slutför**. En ny flik med namnet **SqlServerTable1** visas. Datauppsättningen **SqlServerTable1** visas också i trädvyn till vänster. 

   ![Välj SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
5. På fliken **Allmänt** i fönstret Egenskaper skriver du **SqlServerDataset** som **namn**.
    
   ![Namn på källdatauppsättning](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
6. Växla till fliken **Anslutningar** och klicka på **+ Ny**. I det här steget skapar du en anslutning till källdatalagret (SQL Server-databasen). 

   ![Källdatauppsättning – knapp för ny anslutning](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
7. I fönstret **Ny länkad tjänst** klickar du på **Ny integreringskörning**. I det här avsnittet kan du skapa en lokal Integration Runtime och koppla den till en lokal dator med SQL Server-databasen. En lokal Integration Runtime är den komponent som kopierar data från SQL Server-databasen på din dator till Azure Blob Storage. 

   ![Ny integreringskörning](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
8. I installationsfönstret för **Integration Runtime** väljer du **Privat nätverk** och klickar på **Nästa**. 

   ![Välj privat nätverk](./media/tutorial-hybrid-copy-portal/select-private-network.png)
9. Ange ett namn på integreringskörningen och klicka på **Nästa**.  
    
   ![Namn på integreringskörning](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
10. Klicka på **Click here to launch the express setup for this computer** (Klicka här för att starta expressinstallation för den här datorn) i avsnittet **Option 1: Express setup** (Alternativ 1: Expressinstallation). 

   ![Klicka på länken för expressinstallation](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
11. Klicka på **Stäng** i fönstret **Snabbinstallation av Integration Runtime (lokal installation)**. 

   ![Integration Runtime har installerats](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
12. Klicka på **Slutför** i webbläsaren för att stänga installationsfönstret för **Integration Runtime**. Nu ska du normalt vara tillbaka i fönstret **New Linked Service** (Ny länkad tjänst).

   ![Installationen av Integration Runtime slutförs](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
13. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):

    1. Ange **SqlServerLinkedService** som **namn**.
    2. Bekräfta att den lokala integreringskörningen som du skapade tidigare visas i **Connect via integration runtime** (Anslut via Integration Runtime).
    3. Ange namnet på din SQL Server i **Servernamn**. 
    4. Ange namnet på databasen med **emp**-tabellen för fältet **Databasnamn**. 
    5. Välj lämplig **autentiseringstyp** som Data Factory ska använda för att ansluta till SQL Server-databasen. 
    6. Ange **användarnamnet** och **lösenordet**. Om du behöver använda ett omvänt snedstreck (\\) i ditt användarkonto eller servernamn infogar du escape-tecknet framför det (\\). Använd till exempel *mydomain\\\\myuser*. 
    7. Klicka på **Testa anslutning**. Utför det här steget för att bekräfta att Data Factory-tjänsten kan ansluta till SQL Server-databasen med den lokala integreringskörningen som du har skapat. 
    8. Klicka på **Spara** för att spara den länkade tjänsten.

       ![Inställningar för länkad SQL Server-tjänst](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
14. Nu visas normalt fönstret med källdatauppsättningen öppen. Gör följande i **Anslutning** i fönstret **Egenskaper**: 

    1. Bekräfta att du ser **SqlServerLinkedService** för **Länkad tjänst**. 
    2. Välj **[dbo].[emp]** för **Tabell**.

        ![Källdatauppsättning – anslutningsinformation](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
15. Växla till fliken med SQLServerToBlobPipeline, eller klicka på **SQLServerToBlobPipeline** i trädvyn. 

    ![Fliken Pipeline](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
16. Välj fliken **Mottagare** i fönstret **Egenskaper** och klicka på **+ Ny**. 

    ![Datauppsättning för mottagare – knappen Ny](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
17. Välj **Azure Blob Storage** i fönstret **Ny datauppsättning** och klicka på **Slutför**. En ny flik öppnas för datauppsättningen. Du kan också se datauppsättningen i trädvyn. 

    ![Välj Azure Blob Storage](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
18. Ange **AzureBlobDataset** som **namn**.

    ![Namn på datauppsättning för mottagare](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
19. Välj fliken **Anslutning** i fönstret **Egenskaper** och klicka på **+ Ny** för **Länkad tjänst**. 

    ![Knapp för ny länkad tjänst](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
20. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):

    1. Ange **AzureStorageLinkedService** som **namn**.
    2. Välj ditt Azure-lagringskonto i **Lagringskontonamn**. 
    3. Testa anslutningen till ditt Azure-lagringskonto genom att klicka på **Testa anslutning**.
    4. Klicka på **Spara**.

        ![Länkad Azure Storage-tjänst – inställningar](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
21.  Nu visas normalt fönstret med datauppsättningen för mottagare öppen. Gör följande på fliken **Anslutning**: 

        1. Bekräfta att **AzureStorageLinkedService** har valts för **Länkad tjänst**.
        2. Ange **adftutorial/fromonprem** för **mappdelen** i **filsökvägen**. Om utdatamappen inte finns i behållaren adftutorial skapas den automatiskt av Data Factory-tjänsten.
        3. Ange `@CONCAT(pipeline().RunId, '.txt')` för **filnamnsdelen** i **filsökvägen**.

            ![Datauppsättning för mottagare – anslutning](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
22. Gå till fliken med öppen pipeline, eller klicka på **pipelinen** i **trädvyn**. Bekräfta att **AzureBlobDataset** har valts för **Sink Dataset** (Datauppsättning för mottagare). 

    ![Vald datauppsättning för mottagare ](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
23. Verifiera pipelineinställningarna genom att klicka på Verifiera i verktygsfältet för pipelinen. Stäng **pipelineverifieringsrapporten** genom att klicka på **X** i det högra hörnet. 

    ![Verifiera pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
1. Publicera entiteterna som du skapade i Azure Data Factory-tjänsten genom att klicka på **Publicera**.

    ![Knappen Publicera](./media/tutorial-hybrid-copy-portal/publish-button.png)
24. Vänta tills du ser popup-fönstret som meddelar att **publiceringen är klar**. Du kan också kontrollera status för publiceringen genom att klicka på länken **Visa meddelanden** till vänster. Stäng meddelandefönstret genom att klicka på **X**. 

    ![Publiceringen är klar](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Utlösa en pipelinekörning
Klicka på **Utlösare** i verktygsfältet för pipelinen och klicka på **Trigger Now** (Utlös nu).

![Utlös nu](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Välj fliken **Övervaka**. Du kan se pipelinen som du utlöste manuellt i föregående steg. 

    ![Pipelinekörningar](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningarna klickar du på länken **View activity runs** (Visa aktivitetskörningar) i kolumnen **Action** (Åtgärd). Du ser bara aktivitetskörningar eftersom det bara finns en aktivitet i pipelinen. Om du vill se mer information om kopieringsåtgärden klickar du på länken **Details** (glasögonikonen) i kolumnen **Actions** (Åtgärder). Du kan gå tillbaka till vyn över pipelinekörningar genom att klicka på **Pipeliner** högst upp.

    ![Aktivitetskörningar](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verifiera utdata
Pipelinen skapar automatiskt utdatamappen med namnet *fromonprem* i `adftutorial`-blobbehållaren. Bekräfta att filen *dbo.emp.txt* finns i utdatamappen. 

1. I behållarfönstret **adftutorial** i Azure-portalen väljer du **Uppdatera** för att se utdatamappen.

    ![Utdatamapp skapad](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. Välj `fromonprem` i listan över mappar. 
3. Bekräfta att du ser en fil med namnet `dbo.emp.txt`.

    ![Utdatafil](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage. 
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

Se [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) för att få en lista över datalager som stöds av datafabriken.

Fortsätt till följande självstudie för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data i molnet](tutorial-bulk-copy-portal.md)

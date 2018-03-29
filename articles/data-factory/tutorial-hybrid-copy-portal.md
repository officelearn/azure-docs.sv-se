---
title: Kopiera data från SQL Server till Blob Storage med hjälp av Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från ett lokalt datalager till molnet med hjälp av en lokal Integration Runtime i Azure Data Factory.
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
ms.date: 01/11/2018
ms.author: jingwang
ms.openlocfilehash: e21c08d418022430400ff14baedc1759d2d16069
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Kopiera data från en lokal SQL Server-databas till Azure Blob Storage
I den här självstudien använder du användargränssnittet för Azure Data Factory för att skapa en Data Factory-pipeline som kopierar data från en lokal SQL Server-databas till Azure Blob Storage. Du skapar och använder en lokal installation av Integration Runtime som flyttar data mellan lokala datalager och datalager i molnet.

> [!NOTE]
> Den här artikeln gäller för version 2 av Azure Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory, som är allmänt tillgänglig, läser du i [Dokumentation för Data Factory, version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Den här artikeln ger inte någon detaljerad introduktion till Data Factory. Mer information finns i [Introduktion till Data Factory](introduction.md). 

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage. 
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

## <a name="prerequisites"></a>Krav
### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte redan har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara tilldelad en *deltagare* eller *ägare*, eller vara en *administratör* för Azure-prenumerationen. 

Gå till Azure Portal om du vill se vilka behörigheter du har i prenumerationen. Välj användarnamnet längst upp till höger och välj sedan **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. För exempel på instruktioner om hur du lägger till en användare till en roll läser du i [Lägg till roller](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 och 2017
I den här självstudien använder du en lokal SQL Server-databas som *källdatalager*. Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den här lokala SQL Server-databasen (källa) till Blob Storage (mottagare). Skapa sedan en tabell med namnet **emp** i SQL Server-databasen och infoga ett par exempelposter i tabellen. 

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

### <a name="azure-storage-account"></a>Azure Storage-konto
I den här självstudien använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för destination eller mottagare. Om du inte har något allmänt Azure Storage-konto kan du läsa [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account). Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den lokala SQL Server-databasen (källa) till Blob Storage (mottagare). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för lagringskontot i den här självstudien. Gör så här för att hämta namnet och nyckeln till lagringskontot: 

1. Logga in på [Azure Portal](https://portal.azure.com) med användarnamnet och lösenordet för Azure. 

2. Välj **Fler tjänster** i vänster fönster. Filtrera genom att använda nyckelordet **Lagring** och välj sedan **Lagringskonton**.

    ![Lagringskontosökning](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton. Välj sedan ditt lagringskonto. 

4. I fönstret **Lagringskonto** väljer du **Åtkomstnycklar**.

    ![Åtkomstnycklar](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. I rutorna **Lagringskontonamn** och **key1** kopierar du värdena och klistrar sedan in dem i Anteckningar eller annat redigeringsprogram så att du har dem när du behöver dem senare i självstudien. 

#### <a name="create-the-adftutorial-container"></a>Skapa behållaren adftutorial 
I det här avsnittet skapar du en blobbehållare med namnet **adftutorial** i Blob Storage. 

1. I fönstret **Lagringskonto** går du till **Översikt** och klickar sedan på **Blobar**. 

    ![Alternativet Välj blobar](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. I fönstret **Blobtjänst** väljer du **Behållare**. 

    ![Behållarknapp](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. I fönstret **Ny behållare**, under **Namn** anger du **adftutorial**. Välj sedan **OK**. 

    ![Fönstret Ny behållare](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Välj **adftutorial** i listan över behållare.

    ![Behållarval](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Låt **behållarfönstret** för **adftutorial** vara öppet. Du kommer att använda den för att bekräfta utdata i slutet av självstudien. Data Factory skapar automatiskt utdatamappen i den här behållaren, så du behöver inte skapa en.

    ![Behållarfönster](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget skapar du en datafabrik och startar sedan användargränssnittet för Data Factory för att skapa en pipeline i datafabriken. 

1. Öppna webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. I den vänstra menyn väljer du **Nytt** > **Data och analys** > **Data Factory**.
   
   ![Skapa ny datafabrik](./media/tutorial-hybrid-copy-portal/new-azure-data-factory-menu.png)
3. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**. 
      
     ![Sida för ny datafabrik](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)
 
   Namnet på datafabriken måste vara *globalt unikt*. Om följande felmeddelande visas för namnfältet ändrar du namnet på datafabriken (t.ex. dittnamnADFTutorialDataFactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.
  
   ![Namn på ny datafabrik](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
4. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
5. Gör något av följande för **Resursgrupp**:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

      - Välj **Skapa ny** och ange namnet på en resursgrupp.
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).
6. Under **Version** väljer du **V2 (förhandsversion)**.
7. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. lagring och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i Data Factory kan finnas i andra regioner.
8. Välj **fäst till instrumentpanelen**. 
9. Välj **Skapa**.
10. Du ser följande panel på instrumentpanelen med statusen **Distribuerar datafabrik**:

    ![Panelen Distribuerar datafabrik](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
11. När datafabriken har skapats visas sidan **Data Factory** som på bilden:
   
    ![Datafabrikens startsida](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
12. Välj panelen **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik. 


## <a name="create-a-pipeline"></a>Skapa en pipeline

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**. En pipeline skapas automatiskt åt dig. Pipelinen visas i trädvyn och dess redigerare öppnas. 

   ![Sidan Nu sätter vi igång](./media/tutorial-hybrid-copy-portal/get-started-page.png)
2. På fliken **Allmänt** nedtill i fönstret **Egenskaper** skriver du för **Namn** värdet **SQLServerToBlobPipeline**.

   ![Namn på pipeline](./media/tutorial-hybrid-copy-portal/pipeline-name.png)
3. Gå till verktygsfältet **Aktiviteter** och expandera **Dataflöde**. Dra och släpp aktiviteten **Kopiera** på pipelinedesignytan. Ge aktiviteten namnet **CopySqlServerToAzureBlobActivity**.

   ![Aktivitetsnamn](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)
4. I fönstret **Egenskaper** går du till fliken **Källa** och väljer **+ Ny**.

   ![Fliken Källa](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)
5. I fönstret **Ny datauppsättning** söker du efter **SQL Server**. Välj **SQL Server** och välj sedan **Slutför**. En ny flik med namnet **SqlServerTable1** visas. Datauppsättningen **SqlServerTable1** visas också i trädvyn till vänster. 

   ![Val av SQL Server](./media/tutorial-hybrid-copy-portal/select-sql-server.png)
6. På fliken **Allmänt** nedtill i fönstret **Egenskaper** skriver du för **Namn** värdet **SqlServerDataset**.
    
   ![Namn på källdatauppsättning](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)
7. Gå till fliken **Anslutningar** och välj **+ Ny**. I det här steget skapar du en anslutning till källdatalagret (SQL Server-databasen). 

   ![Anslutning till källdatauppsättning](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)
8. I fönstret **Ny länkad tjänst** väljer du **Ny Integration Runtime**. I det här avsnittet kan du skapa en lokal Integration Runtime och koppla den till en lokal dator med SQL Server-databasen. En lokal Integration Runtime är den komponent som kopierar data från SQL Server-databasen på din dator till Blob Storage. 

   ![Ny integreringskörning](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)
9. I installationsfönstret för **Integration Runtime** väljer du **Privat nätverk** och väljer sedan **Nästa**. 

   ![Val av privat nätverk](./media/tutorial-hybrid-copy-portal/select-private-network.png)
10. Ange ett namn på integreringskörningen och välj **Nästa**.
    
    ![Namn på integreringskörning](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)
11. Under **Option 1: Express setup** (Alternativ 1: Expressinstallation) väljer du **Click here to launch the express setup for this computer** (Klicka här för att starta expressinstallation för den här datorn). 

    ![Länk för expressinstallation](./media/tutorial-hybrid-copy-portal/click-exress-setup.png)
12. Välj **Stäng** i fönstret **Snabbinstallation av Integration Runtime (lokal installation)**. 

    ![Snabbinstallation av Integration Runtime (lokal installation)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)
13. Välj **Slutför** i webbläsaren i fönstret **Integration Runtime-installation**. 

    ![Installation av Integration Runtime](./media/tutorial-hybrid-copy-portal/click-finish-integration-runtime-setup.png)
14. Utför följande steg i fönstret **Ny länkad tjänst**:

    a. Under **Namn** anger du **SqlServerLinkedService**.

    b. Under **Connect via integration runtime** (Anslut via Integration Runtime) bekräftar du att den lokala integreringskörningen som du skapade tidigare visas.

    c. Under **Servernamn** anger du namnet på SQL Server-instansen. 

    d. Under **Databasnamn** anger du namnet på databasen med **emp**-tabellen.

    e. Under **Autentiseringstyp** väljer du den autentiseringstyp som Data Factory ska använda för att ansluta till SQL Server-databasen.

    f. Under **Användarnamn** och **Lösenord** anger du användarnamnet och lösenordet. Om du behöver använda ett omvänt snedstreck (\\) i ditt användarkonto eller servernamn infogar du escape-tecknet framför det (\\). Använd till exempel *mydomain\\\\myuser*.

    g. Välj **Testanslutning**. Utför det här steget för att bekräfta att Data Factory kan ansluta till SQL Server-databasen med den lokala integreringskörningen som du har skapat.

    h. Välj **Spara** för att spara den länkade tjänsten.

       ![Inställningar för ny länkad tjänst](./media/tutorial-hybrid-copy-portal/sql-server-linked-service-settings.png)
15. Nu visas normalt fönstret med källdatauppsättningen öppen. Gör följande i fliken **Anslutning** i fönstret **Egenskaper**: 

    a. Bekräfta att du ser **SqlServerLinkedService** i **Länkad tjänst**.

    b. Under **Tabell** väljer du **[dbo].[emp]**.

    ![Källdatauppsättning – anslutningsinformation](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)
16. Gå till fliken med **SQLServerToBlobPipeline** eller välj **SQLServerToBlobPipeline** i trädvyn. 

    ![Fliken Pipeline](./media/tutorial-hybrid-copy-portal/pipeliene-tab.png)
17. Gå till fliken **Mottagare** längst ned i fönstret **Egenskaper** och välj **+ Ny**. 

    ![Fliken Mottagare](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)
18. Välj **Azure Blob Storage** i fönstret **Ny datauppsättning**. Välj sedan **Slutför**. En ny flik öppnas för datauppsättningen. Du kan också se datauppsättningen i trädvyn. 

    ![Val av Blob Storage](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)
19. I **Namn** anger du **AzureBlobDataset**.

    ![Namn på datauppsättning för mottagare](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)
20. Gå till fliken **Anslutning** längst ned i fönstret **Egenskaper**. Intill **Länkad tjänst** väljer du **+ Ny**. 

    ![Knapp för ny länkad tjänst](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)
21. Utför följande steg i fönstret **Ny länkad tjänst**:

    a. Under **Namn** anger du **AzureStorageLinkedService**.

    b. Under **Lagringskontonamn** väljer du ditt lagringskonto.

    c. Välj **Testanslutning** för att testa anslutningen till lagringskontot.

    d. Välj **Spara**.

    ![Inställningar för länkad lagringstjänst](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 
22.  Nu visas normalt fönstret med datauppsättningen för mottagare öppen. Gör följande på fliken **Anslutning**: 

        a. I **Länkad tjänst** bekräftar du att **AzureStorageLinkedService** är vald.

        b. I **mappdelen** av **filsökvägen** anger du **adftutorial/fromonprem**. Om utdatamappen inte finns i behållaren adftutorial skapas den automatiskt av Data Factory.

        c. I **filnamnsdelen** av **filsökvägen** anger du `@CONCAT(pipeline().RunId, '.txt')`.

     ![Anslutning till datauppsättning för mottagare](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)
23. Gå till fliken med pipelinen öppen eller välj pipelinen i trädvyn. I **Sink Dataset** (Datauppsättning för mottagare) bekräftar du att **AzureBlobDataset** är vald. 

    ![Vald datauppsättning för mottagare](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)
24. Verifiera pipelineinställningarna genom att välja **Verifiera** i verktygsfältet för pipelinen. Om du vill stänga **verifieringsrapporten för pipeline** väljer du **Stäng**. 

    ![Verifiera pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
25. Om du vill publicera entiteter du skapat till Data Factory väljer du **Publicera alla**.

    ![Knappen Publicera](./media/tutorial-hybrid-copy-portal/publish-button.png)
26. Vänta tills du ser popup-fönstret som meddelar att **publiceringen är klar**. Du kan kontrollera status för publiceringen genom att välja länken **Visa meddelanden** till vänster. Stäng meddelandefönstret genom att klicka på **Stäng**. 

    ![Publiceringen är klar](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)
    

## <a name="trigger-a-pipeline-run"></a>Utlös en pipelinekörning
Välj **Utlös** i verktygsfältet för pipelinen och välj sedan **Trigger Now** (Utlös nu).

![Utlös pipelinekörning](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Gå till fliken **Övervaka**. Du kan se pipelinen som du utlöste manuellt i föregående steg. 

    ![Övervaka pipelinekörningar](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
2. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningen, väljer du länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Åtgärder**. Du ser bara aktivitetskörningar eftersom det bara finns en aktivitet i pipelinen. Om du vill se information om kopieringsoperationen väljer du länken **information** (glasögonikonen) i kolumnen **Åtgärder**. Gå tillbaka till vyn **Pipeline Runs** (Pipelinekörningar) genom att välja **Pipelines** högst upp.

    ![Övervaka aktivitetskörningar](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verifiera utdata
Pipelinen skapar automatiskt utdatamappen med namnet *fromonprem* i `adftutorial`-blobbehållaren. Bekräfta att filen *dbo.emp.txt* finns i utdatamappen. 

1. I behållarfönstret **adftutorial** i Azure-portalen väljer du **Uppdatera** för att se utdatamappen.

    ![Utdatamapp skapad](media/tutorial-hybrid-copy-portal/fromonprem-folder.png)
2. Välj `fromonprem` i listan över mappar. 
3. Bekräfta att du ser en fil med namnet `dbo.emp.txt`.

    ![Utdatafil](media/tutorial-hybrid-copy-portal/fromonprem-file.png)


## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Blob Storage. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och lagring. 
> * Skapa datauppsättningar för SQL Server och Blob Storage.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

Se [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) för att få en lista över datalager som stöds av Data Factory.

Fortsätt till följande självstudiekurs för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data i molnet](tutorial-bulk-copy-portal.md)

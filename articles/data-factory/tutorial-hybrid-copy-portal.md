---
title: Kopiera data från SQL Server till Blob-lagring med Azure-portalen
description: Lär dig hur du kopierar data från ett lokalt datalager till molnet med hjälp av en lokal Integration Runtime i Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 03/12/2020
ms.openlocfilehash: c073d3e51234e0ed8e524c2ae557d4158ad9e7d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80065053"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Kopiera data från en lokal SQL Server-databas till Azure Blob Storage
I den här självstudien använder du användargränssnittet för Azure Data Factory för att skapa en Data Factory-pipeline som kopierar data från en lokal SQL Server-databas till Azure Blob Storage. Du skapar och använder en lokal installation av Integration Runtime som flyttar data mellan lokala datalager och datalager i molnet.

> [!NOTE]
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
För att skapa datafabriksinstanser måste användarkontot som du använder för att logga in på Azure tilldelas en *deltagar-* eller *ägarroll* eller måste vara *administratör för* Azure-prenumerationen.

Gå till Azure Portal om du vill se vilka behörigheter du har i prenumerationen. Välj användarnamnet längst upp till höger och välj sedan **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. Exempelinstruktioner för hur du lägger till en användare till en roll finns i [Hantera åtkomst med RBAC och Azure-portalen](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 och 2017
I den här självstudien använder du en lokal SQL Server-databas som *källdatalager*. Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den här lokala SQL Server-databasen (källa) till Blob Storage (mottagare). Skapa sedan en tabell med namnet **emp** i SQL Server-databasen och infoga ett par exempelposter i tabellen.

1. Starta SQL Server Management Studio. Om det inte redan är installerat på datorn öppnar du [Ladda ner SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. Anslut till SQL Server-instansen med hjälp av dina autentiseringsuppgifter.

1. Skapa en exempeldatabas. I trädvyn högerklickar du på **Databaser** och sedan väljer du **Ny databas**.
1. I fönstret **Ny databas** anger du ett namn för databasen och sedan väljer du **OK**.

1. Skapa tabellen **emp** och infoga lite exempeldata i den genom att köra följande frågeskript mot databasen. I trädvyn högerklickar du på databasen du skapade och sedan väljer du **Ny fråga**.

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

### <a name="azure-storage-account"></a>Azure Storage-konto
I den här självstudien använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för destination eller mottagare. Om du inte har något allmänt Azure Storage-konto kan du läsa [Skapa ett lagringskonto](../storage/common/storage-account-create.md). Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den lokala SQL Server-databasen (källa) till Blob Storage (mottagare). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för lagringskontot i den här självstudien. Gör så här för att hämta namnet och nyckeln till lagringskontot:

1. Logga in på [Azure Portal](https://portal.azure.com) med användarnamnet och lösenordet för Azure.

1. Välj **Alla tjänster** i rutan till vänster. Filtrera genom att använda nyckelordet **Lagring** och välj sedan **Lagringskonton**.

    ![Lagringskontosökning](media/doc-common-process/search-storage-account.png)

1. Filtrera för ditt lagringskonto i listan över lagringskonton om det behövs. Välj sedan ditt lagringskonto.

1. I fönstret **Lagringskonto** väljer du **Åtkomstnycklar**.

1. I rutorna **Lagringskontonamn** och **key1** kopierar du värdena och klistrar sedan in dem i Anteckningar eller annat redigeringsprogram så att du har dem när du behöver dem senare i självstudien.

#### <a name="create-the-adftutorial-container"></a>Skapa containern adftutorial
I det här avsnittet skapar du en blobcontainer med namnet **adftutorial** i Blob Storage.

1. Gå till **Översikt**i fönstret **Lagringskonto** och välj sedan **Behållare**.

    ![Alternativet Välj blobar](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. I fönstret **Behållare** väljer du **+ Behållare** för att skapa en ny.

1. I fönstret **Ny container**, under **Namn** anger du **adftutorial**. Välj sedan **Skapa**.

1. I listan över behållare väljer du **adftutorial** som du just skapade.

1. Låt **containerfönstret** för **adftutorial** vara öppet. Du använder den för att verifiera utdata i slutet av självstudien. Data Factory skapar automatiskt utdatamappen i den här containern, så du behöver inte skapa en.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget skapar du en datafabrik och startar sedan användargränssnittet för Data Factory för att skapa en pipeline i datafabriken.

1. Öppna webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. På den vänstra menyn väljer du **Skapa en resurs** > **Analytics** > **Data Factory:**

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

1. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på datafabriken måste vara *globalt unikt*. Om följande felmeddelande visas för namnfältet ändrar du namnet på datafabriken (t.ex. dittnamnADFTutorialDataFactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.

   ![Namn på ny datafabrik](./media/doc-common-process/name-not-available-error.png)

1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
1. Gör något av följande för **Resursgrupp**:

   - Välj **Använd befintlig**och välj en befintlig resursgrupp i listrutan.

   - Välj **Skapa ny**och ange namnet på en resursgrupp.
        
     Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).
1. Under **Version** väljer du **V2**.
1. Under **Plats**väljer du plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. lagring och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i Data Factory kan finnas i andra regioner.
1. Välj **Skapa**.

1. När datafabriken har skapats visas sidan **Data Factory** som på bilden:

    ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)
1. Välj panelen **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.


## <a name="create-a-pipeline"></a>Skapa en pipeline

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**. En pipeline skapas automatiskt åt dig. Pipelinen visas i trädvyn och dess redigerare öppnas.

   ![Sidan Nu sätter vi igång](./media/doc-common-process/get-started-page.png)

1. Ange **SQLServerToBlobPipeline**för **Namn**på fliken **Allmänt** längst ned i **fönstret Egenskaper** .

1. Expandera **Flytta & Transformera i**verktygsrutan **Aktiviteter** . Dra och släpp aktiviteten **Kopiera** på pipelinedesignytan. Ge aktiviteten namnet **CopySqlServerToAzureBlobActivity**.

1. I fönstret **Egenskaper** går du till fliken **Källa** och väljer **+ Ny**.

1. Sök efter **SQL Server**i dialogrutan **Ny datauppsättning** . Välj **SQL Server**och välj sedan **Fortsätt**.
    ![Ny SqlServer-datauppsättning](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. Ange **SqlServerDataset**under **Namn**i dialogrutan **Ange egenskaper.** Under **Länkad tjänst**väljer du **+ Nytt**. I det här steget skapar du en anslutning till källdatalagret (SQL Server-databasen).

1. Lägg till **Namn** som **SqlServerLinkedService**i dialogrutan **Ny länkad** tjänst . Under **Anslut via integrationskörning**väljer du **+Nytt**.  I det här avsnittet kan du skapa en lokal Integration Runtime och koppla den till en lokal dator med SQL Server-databasen. En lokal Integration Runtime är den komponent som kopierar data från SQL Server-databasen på din dator till Blob Storage.

1. I dialogrutan **Inställningar för integrering körtid** väljer du **Självvärd och**väljer sedan **Fortsätt**.

1. Under namn anger du **TutorialIntegrationRuntime**. Välj sedan **Skapa**.

1. För Inställningar väljer **du Klicka här för att starta expressinställningen för den här datorn**. Med den här åtgärden installeras integreringskörningen på datorn och registreras med Data Factory. Alternativt kan du använda det manuella installationsalternativet för att ladda ned installationsfilen, köra den och använda nyckeln för att registrera integreringskörning.
    ![Installation av Integration Runtime](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. I fönstret **Integration Runtime (Självvärdförd) Express Setup** väljer du **Stäng** när processen är klar.

    ![Snabbinstallation av Integration Runtime (lokal installation)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. I dialogrutan **Ny länkad tjänst (SQL Server)** bekräftar du att **TutorialIntegrationRuntime** är markerat under **Anslut via integrationskörning**. Gör sedan följande:

    a. Under **Namn** anger du **SqlServerLinkedService**.

    b. Under **Servernamn** anger du namnet på SQL Server-instansen.

    c. Under **Databasnamn** anger du namnet på databasen med **emp**-tabellen.

    d. Under **Autentiseringstyp** väljer du den autentiseringstyp som Data Factory ska använda för att ansluta till SQL Server-databasen.

    e. Under **Användarnamn** och **Lösenord** anger du användarnamnet och lösenordet. Om du behöver använda ett omvänt snedstreck (\\) i ditt användarkonto eller servernamn infogar du escape-tecknet framför det (\\). Till exempel, använd *mydomain\\\\myuser*.

    f. Välj **Testanslutning**. Det här steget är att bekräfta att Data Factory kan ansluta till DIN SQL Server-databas med hjälp av den självvärderade integrationskörningen som du skapade.

    g. Om du vill spara den länkade tjänsten väljer du **Skapa**.
 
    ![Ny länkad tjänst (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. När den länkade tjänsten har skapats är du tillbaka på sidan **Ange egenskaper** för SqlServerDataset. Utför följande steg:

    a. Bekräfta att du ser **SqlServerLinkedService** i **Länkad tjänst**.

    b. Under **Tabellnamn**väljer du **[dbo].[ emp]**.
    
    c. Välj **OK**.

1. Gå till fliken med **SQLServerToBlobPipeline** eller välj **SQLServerToBlobPipeline** i trädvyn.

1. Gå till fliken **Mottagare** längst ned i fönstret **Egenskaper** och välj **+ Ny**.

1. Välj **Azure Blob Storage**i dialogrutan **Ny datauppsättning** . Välj sedan **Fortsätt**.

1. Välj formattyp för data i dialogrutan **Välj format.** Välj sedan **Fortsätt**.

    ![Val av dataformat](./media/doc-common-process/select-data-format.png)

1. Ange **AzureBlobDataset** för namn i dialogrutan **Ange egenskaper.** Vid textrutan **Länkad tjänst** väljer du **+ Nytt**.

1. I dialogrutan **Ny länkad tjänst (Azure Blob Storage)** anger du **AzureStorageLinkedService** som namn och väljer ditt lagringskonto i listan **För lagringskontonamn.** Testa anslutningen och välj sedan **Skapa** för att distribuera den länkade tjänsten.

1. När den länkade tjänsten har skapats är du tillbaka på sidan **Ange egenskaper.** Välj **OK**.

1. Öppna diskhon-datauppsättningen. Gör följande på fliken **Anslutning**:

    a. I **Länkad tjänst** bekräftar du att **AzureStorageLinkedService** är vald.

    b. Ange **adftutorial/fromonprem** för delningen **Behållare/katalog** i **Sökvägen.** Om utdatamappen inte finns i containern adftutorial skapas den automatiskt av Data Factory.

    c. För **fildelen** väljer du **Lägg till dynamiskt innehåll**.
    ![dynamiska uttryck för lösning av filnamn](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Lägg `@CONCAT(pipeline().RunId, '.txt')`till och välj sedan **Slutför**. Den här åtgärden byter namn på filen till PipelineRunID.txt.

1. Gå till fliken med pipelinen öppen eller välj pipelinen i trädvyn. I **Sink Dataset** (Datauppsättning för mottagare) bekräftar du att **AzureBlobDataset** är vald.

1. Verifiera pipelineinställningarna genom att välja **Verifiera** i verktygsfältet för pipelinen. Om du vill stänga **pipe-valideringsutgången**markerar du **>>** ikonen.
    ![validera pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. Om du vill publicera entiteter som du har skapat i Data Factory väljer du **Publicera alla**.

1. Vänta tills popup-programmet **Publicera har slutförts.** Om du vill kontrollera publiceringsstatus väljer du länken **Visa meddelanden** högst upp i fönstret. Stäng meddelandefönstret genom att klicka på **Stäng**.


## <a name="trigger-a-pipeline-run"></a>Utlösa en pipelinekörning
Välj **Lägg till utlösare** i verktygsfältet för pipelinen och välj sedan **Utlösare nu**.

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Gå till fliken **Bildskärm.** Du ser pipelinen som du manuellt utlöste i föregående steg.

1. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningen väljer du länken **SQLServerToBlobPipeline** under *PIPELINE NAME*. 
    ![Övervaka pipelinekörningar](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. På sidan **Aktivitetskörning** väljer du länken Information (glasögonbild) för att se information om kopieringen. Om du vill gå tillbaka till vyn Pipeline runs väljer du **Alla pipeline-körningar** högst upp.

## <a name="verify-the-output"></a>Verifiera utdata
Pipelinen skapar automatiskt utdatamappen med namnet *fromonprem* i `adftutorial`-blobcontainern. Bekräfta att du ser filen *[pipeline().RunId].txt* i utdatamappen.


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

En lista över datalager som stöds av Data Factory finns i [Data stores som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Fortsätt till följande självstudiekurs för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data](tutorial-bulk-copy-portal.md)

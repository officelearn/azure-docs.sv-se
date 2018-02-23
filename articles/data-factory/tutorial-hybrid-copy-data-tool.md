---
title: Kopiera lokala data med Azure-verktyget Kopiera data | Microsoft Docs
description: "Skapa en Azure-datafabrik och kopiera sedan data från en lokal SQL Server-databas till Azure Blob Storage med hjälp av verktyget Kopiera data."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: 77090d9a61945c9edc42cde7d647c75e91f54dd6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Kopiera data från en lokal SQL Server-databas till Azure Blob Storage med hjälp av verktyget Kopiera data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 – förhandsversion](tutorial-hybrid-copy-data-tool.md)

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från en lokal SQL Server-databas till Azure Blob Storage.

> [!NOTE]
> - Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Data Factory](introduction.md).
>
> - Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory, som är allmänt tillgänglig, se [Kom igång med Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Nödvändiga komponenter
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

5. Skapa tabellen **emp** och infoga lite exempeldata i den genom att köra följande frågeskript mot databasen. I trädvyn högerklickar du på databasen du skapade och sedan väljer du **Ny fråga**.

    ```sql
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
I den här självstudien använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för destination/mottagare. Om du inte har något allmänt lagringskonto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account). Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den här lokala SQL Server-databasen (källa) till Blob Storage (mottagare). 

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

1. I fönstret **Lagringskonto** växlar du till **Översikt** och klickar sedan på **Blobar**. 

    ![Alternativet Välj blobar](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. I fönstret **Blobtjänst** väljer du **Behållare**. 

    ![Behållarknapp](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. I fönstret **Ny behållare**, i rutan **Namn**, anger du **adftutorial** och väljer **OK**. 

    ![Ny behållare](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Välj **adftutorial** i listan över behållare.

    ![Val av behållare](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Låt **behållarfönstret** för **adftutorial** vara öppet. Du kommer att använda den för att bekräfta utdata i slutet av självstudien. Data Factory skapar automatiskt utdatamappen i den här behållaren, så du behöver inte skapa en.

    ![Behållarfönster](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. I menyn till vänster väljer du **Nytt** > **Data och analys** > **Data Factory**. 
   
   ![Skapa ny datafabrik](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. I fönstret **Ny datafabrik**, under **Namn**, anger du **ADFTutorialDataFactory**. 
      
     ![Ny datafabrik](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   Namnet på datafabriken måste vara *globalt unikt*. Om följande felmeddelande visas för namnfältet ändrar du namnet på datafabriken (t.ex. dittnamnADFTutorialDataFactory). Se artikeln [om namnregler för datafabriker](naming-rules.md) för namnregler för datafabriksartefakter.
  
   ![Namn på ny datafabrik](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. Gör något av följande för **Resursgrupp**:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

      - Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
      Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).
5. För **Version** väljer du **V2 (förhandsversion)**.
6. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i Data Factory kan finnas på andra platser/i andra regioner.
7. Välj **fäst till instrumentpanelen**. 
8. Välj **Skapa**.
9. Du ser följande panel på instrumentpanelen med statusen **Distribuerar datafabrik**:

    ![Panelen Distribuerar datafabrik](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
10. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
    ![Datafabrikens startsida](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
11. Välj **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. Sidan **Nu sätter vi igång** visas. Välj **Kopiera data** för att starta verktyget Kopiera data. 

   ![Panel för verktyget Kopiera data](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. På sidan **Egenskaper** för verktyget Kopiera data går du till **Uppgiftsnamn** och anger **CopyFromOnPremSqlToAzureBlobPipeline**. Välj sedan **Nästa**. Verktyget Kopiera data skapar en pipeline med det namn som du anger i det här fältet. 
    
   ![Uppgiftsnamn](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. Välj **SQL Server** på sidan **Källdatalager** och välj sedan **Nästa**. Du kan behöva rulla ned för att se **SQL Server** i listan. 

   ![Val av SQL Server](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. Under **Anslutningsnamn** anger du **SqlServerLinkedService**. Välj länken **Skapa Integration Runtime**. Du måste skapa en lokal integreringskörning, ladda ned den på din dator och registrera den med Data Factory. Den lokala integreringskörningen kopierar data mellan din lokala miljö och molnet.

   ![Skapa Integration Runtime med lokal installation](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. I dialogrutan **Skapa Integration Runtime** går du till **Namn** och anger **TutorialIntegration Runtime**. Välj sedan **Skapa**. 

   ![Namn på Integration Runtime](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Välj **Starta expressinstallation på den här datorn**. Med den här åtgärden installeras integreringskörningen på datorn och registreras med Data Factory. Alternativt kan du använda det manuella installationsalternativet för att ladda ned installationsfilen, köra den och använda nyckeln för att registrera integreringskörning. 

    ![Länken Starta expressinstallation på den här datorn](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Kör programmet som laddats ned. I fönstret visas status för expressinstallationen. 

    ![Status för expressinstallation](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Bekräfta att **TutorialIntegrationRuntime** har valts i fältet **Integration Runtime**.

    ![Vald integreringskörning](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. Gör följande i **Ange lokal SQL Server-databas**: 

    a. Under **Anslutningsnamn** anger du **OnPremSqlLinkedService**.

    b. Under **Servernamn** anger du namnet på din lokala SQL Server-instans.

    c. Under **Databasnamn** anger du namnet på din lokala databas.

    d. Under **Autentiseringstyp** väljer du lämplig autentisering.

    e. Under **Användarnamn** anger du namnet på en användare med åtkomst till lokal SQL Server.

    f. Ange **lösenordet** för användaren. 
10. På sidan **Välj tabellen som data ska kopieras från eller använd en anpassad fråga**, välj tabellen **[dbo].[emp]** i listan och välj **Nästa**. 

    ![Val av tabellen emp](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. Sidan **Destination data store** (Måldatalager) visas. Välj **Azure Blob Storage** och sedan **Nästa**.

    ![Val av Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. Gör följande på sidan **Ange Azure Blob Storage-konto**: 

    a. Under **Anslutningsnamn** anger du **AzureStorageLinkedService**.

    b. Under **Lagringskontonamn** väljer du ditt lagringskonto i listrutan. 

    c. Välj **Nästa**.

    ![Ange lagringskontot](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. På sidan **Välj utdatafil eller mapp** under **Mappsökväg** anger du **adftutorial/fromonprem**. Du skapade behållaren **adftutorial** som en del av förutsättningarna. Om utdatamappen inte finns skapas den automatiskt av Data Factory. Du kan också använda knappen **Bläddra** för att bläddra i bloblagringen och dess behållare/mappar. Observera att namnet på utdatafilen anges till **dbo.emp** som standard.
        
    ![Välj utdatafil eller mapp](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. Sidan med **filformatsinställningar** visas. Välj **Nästa**. 

    ![Sida för filformatinställningar](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. Sidan **Settings** (Inställningar) visas. Välj **Nästa**. 

    ![Sidan Inställningar](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. Granska värdena för alla inställningar på sidan **Sammanfattning** och välj **Nästa**. 

    ![Sammanfattningssida](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen eller aktiviteten du skapade.

    ![Distributionssida](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. På fliken **Övervaka** kan du visa status för den pipeline som du skapade. Med länkarna i kolumnen **Åtgärd** kan du visa de aktivitetskörningar som är associerade med pipelinekörningar och köra pipelinen på nytt. 

    ![Övervaka pipelinekörningar](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Välj länken **Visa aktivitetskörningar** i kolumnen **Åtgärder** om du vill se aktivitetskörningar som är associerade med pipelinekörningen. Om du vill se mer information om kopieringsåtgärden väljer du länken **Information** (glasögonikonen) i kolumnen **Åtgärder**. Välj **Pipelines** högst upp för att gå tillbaka till vyn **Pipelinekörning**.

    ![Övervaka aktivitetskörningar](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Bekräfta att utdatafilen visas i mappen **fromonprem** för behållaren **adftutorial**. 
 
    ![Utdatablob](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. Klicka på fliken **Redigera** till vänster för att växla till redigeringsläget. Du kan uppdatera de länkade tjänster, datauppsättningar och pipeliner som skapats med verktyget med hjälp av redigeraren. Klicka på **Kod** för att visa JSON-koden som är associerad med den entitet som har öppnats i redigeraren. Mer information om hur du redigerar dessa entiteter i användargränssnittet för Data Factory finns i [Azure Portal-versionen av den här självstudiekursen](tutorial-copy-data-portal.md).

    ![Fliken Redigera](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en lokal SQL Server-databas till Blob Storage. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

Se [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) för att få en lista över datalager som stöds av Data Factory.

Fortsätt till följande självstudie för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data i molnet](tutorial-bulk-copy-portal.md)

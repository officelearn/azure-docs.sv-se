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
ms.openlocfilehash: aba8b13d47b2b9236a0d5cc868e53780e894c406
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-copy-data-tool"></a>Kopiera data från en lokal SQL Server-databas till Azure Blob Storage med hjälp av verktyget Kopiera data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 – förhandsversion](tutorial-hybrid-copy-data-tool.md)

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från en lokal SQL Server-databas till Azure Blob Storage.

> [!NOTE]
> - Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).
>
> - Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Läs [get started with Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (kom igång med Data Factory version 1) om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig.

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline
> * Övervaka pipelinen och aktivitetskörningarna.

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

5. För att skapa tabellen **emp** och infoga några exempeldata i den kör du följande frågeskript mot databasen: I trädvyn höger klickar du på databasen som du har skapat och väljer sedan **Ny fråga**.

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

### <a name="azure-storage-account"></a>Azure-lagringskonto
I den här självstudien använder du ett allmänt Azure Storage-konto (Azure Blob Storage för att vara exakt) som datalager för destination/mottagare. Om du inte har något allmänt Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account). Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den här lokala SQL Server-databasen (källa) till Azure Blob Storage (mottagare). 

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

1. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sida för ny datafabrik](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
     ![Sida för ny datafabrik](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
      Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (Azure Storage, Azure SQL Database o.s.v.) och beräkningarna (HDInsight o.s.v.) som används i Data Factory kan finnas på andra platser/regioner.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
   ![Datafabrikens startsida](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
10. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. Klicka på panelen **Kopiera data** för att starta verktyget Kopiera data. 

   ![Panel för verktyget Kopiera data](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. På sidan **Egenskaper** för verktyget Kopiera data anger du **CopyFromOnPremSqlToAzureBlobPipeline** för **Task name** (Aktivitetsnamn) och klickar på **Nästa**. Verktyget Kopiera data skapar en pipeline med det namn som du anger i det här fältet. 
    
   ![Sidan Egenskaper](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. Välj **SQL Server** på sidan **Källdatalager** och klicka på **Nästa**. Du kan behöva rulla ned för att se **SQL Server** i listan. 

   ![Sidan Källdatalager](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. Ange **SqlServerLinkedService** som **anslutningsnamn** och klicka på länken för att **skapa integreringskörning**. Du måste skapa en lokal integreringskörning, ladda ned den på din dator och registrera den med Data Factory-tjänsten. Den lokala integreringskörningen kopierar data mellan din lokala miljö och Azure-molnet.

   ![Länk för att skapa integreringskörning](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. I dialogrutan för att **skapa integreringskörning** anger du **TutorialIntegration Runtime** i fältet **Namn** och klickar på **Skapa**. 

   ![Dialogruta för att skapa integreringskörning](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Klicka på **Launch express setup on this computer** (Starta expressinstallation på den här datorn). Med den här åtgärden installeras integreringskörningen på datorn och registreras med Data Factory-tjänsten. Alternativt kan du använda det manuella installationsalternativet för att ladda ned installationsfilen, köra den och använda nyckeln för att registrera integreringskörning. 

    ![Länk för att skapa integreringskörning](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Kör programmet som laddats ned. I fönstret visas **status** för expressinstallationen. 

    ![Status för expressinstallation](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Bekräfta att **TutorialIntegrationRuntime** har valts i fältet **Integration Runtime**.

    ![Vald integreringskörning](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. Gör följande i **Specify the on-premises SQL Server database** (Ange lokal SQL Server-databas): 

    1. Ange **OnPremSqlLinkedService** som **anslutningsnamn**.
    2. Ange namnet på din lokala SQL Server som **servernamn**.
    3. Ange namnet på din lokala databas som **databasnamn**. 
    4. Välj lämplig autentisering för **autentiseringstyp**.
    5. Ange namnet på användaren med åtkomst till lokal SQL Server som **användarnamn**.
    6. Ange **lösenordet** för användaren. 
10. På sidan **Select tables from which to copy the data or use a custom query** (Välj tabellen som data ska kopieras från eller använd en anpassad fråga), välj tabellen **[dbo].[emp]** i listan och klicka på **Nästa**. 

    ![Välj tabellen emp](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. Välj **Azure Blob Storage** på sidan **Måldatalager** och klicka på **Nästa**.

    ![Välj Azure Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. Gör följande på sidan **Specify the Azure Blob storage account** (Ange Azure Blob Storage-konto): 

    1. Ange **AzureStorageLinkedService** som **anslutningsnamn**.
    2. Välj ditt Azure Storage-konto i listrutan i **Lagringskontonamn**. 
    3. Klicka på **Nästa**.

        ![Välj Azure Blob Storage](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. Gör följande på sidan **Välj utdatafil eller mapp**: 
    
    1. Ange **adftutorial/fromonprem** för **mappsökväg**. Du skapade behållaren **adftutorial** som en del av förutsättningarna. Om utdatamappen inte finns skapas den automatiskt av Data Factory-tjänsten. Du kan också använda knappen **Bläddra** för att gå till bloblagringen och dess behållare/mappar. Observera att namnet på utdatafilen anges till **dbo.emp** som standard.
        
        ![Välj utdatafil eller mapp](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. Klicka på **Nästa** på sidan för **filformatinställningar**. 

    ![Sida för filformatinställningar](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. Klicka på **Nästa** på sidan **Inställningar**. 

    ![Sidan Inställningar](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. Granska värdena för alla inställningar på sidan **Summary** (Sammanfattning) och klicka på **Nästa**. 

    ![Sammanfattningssida](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. Klicka på **Övervaka** på sidan **Distribution** för att övervaka pipelinen eller aktiviteten du skapade.

    ![Distributionssida](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. På fliken **Övervaka** kan du visa status för den pipeline som du skapade. Med länkarna i kolumnen **Action** (Åtgärd) kan du visa de aktivitetskörningar som är associerade med pipelinekörningar och köra pipelinen på nytt. 

    ![Pipelinekörningar](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Klicka på länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder) om du vill se aktivitetskörningar som är associerade med pipelinekörningen. Om du vill se mer information om kopieringsåtgärden klickar du på länken för **detaljer** (glasögonikonen) i kolumnen **Actions** (Åtgärder). Du kan gå tillbaka till vyn över pipelinekörningar genom att klicka på **Pipeliner** högst upp.

    ![Aktivitetskörningar](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Bekräfta att utdatafilen visas i mappen **fromonprem** för behållaren **adftutorial**.   
 
    ![Utdatablob](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. Klicka på fliken **Redigera** till vänster för att växla till redigeringsläget. Du kan uppdatera de länkade tjänster, datauppsättningar och pipeliner som skapats med verktyget med hjälp av redigeraren. Klicka på **Code** (Kod) för att visa JSON-koden som är associerad med den entitet som har öppnats i redigeraren. Mer information om hur du redigerar dessa entiteter i användargränssnittet för Data Factory finns i [Azure Portal-versionen av den här självstudiekursen](tutorial-copy-data-portal.md).

    ![Fliken Redigera](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en lokal SQL Server-databas till en Azure Blob Storage. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline
> * Övervaka pipelinen och aktivitetskörningarna.

Se [Datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats) för att få en lista över datalager som stöds av datafabriken.

Fortsätt till följande självstudie för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data i molnet](tutorial-bulk-copy-portal.md)
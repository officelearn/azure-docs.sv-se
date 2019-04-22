---
title: Kopiera lokala data med Azure-verktyget Kopiera data | Microsoft Docs
description: Skapa en Azure-datafabrik och kopiera sedan data från en lokal SQL Server-databas till Azure Blob Storage med hjälp av verktyget Kopiera data.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 04/09/2018
ms.author: abnarain
ms.openlocfilehash: 26bc6861602cae349c8ebaafefe070c119a93e87
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261535"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Kopiera data från en lokal SQL Server-databas till Azure Blob Storage med hjälp av verktyget Kopiera data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](tutorial-hybrid-copy-data-tool.md)

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från en lokal SQL Server-databas till Azure Blob Storage.

> [!NOTE]
> - Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Data Factory](introduction.md).

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

Gå till Azure Portal om du vill se vilka behörigheter du har i prenumerationen. Välj användarnamnet längst upp till höger och välj sedan **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. Exempelinstruktioner för hur du lägger till en användare till en roll finns i [Hantera åtkomst med RBAC och Azure-portalen](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 och 2017
I den här självstudien använder du en lokal SQL Server-databas som *källdatalager*. Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den här lokala SQL Server-databasen (källa) till Blob Storage (mottagare). Skapa sedan en tabell med namnet **emp** i SQL Server-databasen och infoga ett par exempelposter i tabellen. 

1. Starta SQL Server Management Studio. Om det inte redan är installerat på datorn öppnar du [Ladda ner SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Anslut till SQL Server-instansen med hjälp av dina autentiseringsuppgifter. 

1. Skapa en exempeldatabas. I trädvyn högerklickar du på **Databaser** och sedan väljer du **Ny databas**. 

1. I fönstret **Ny databas** anger du ett namn för databasen och sedan väljer du **OK**. 

1. Skapa tabellen **emp** och infoga lite exempeldata i den genom att köra följande frågeskript mot databasen. I trädvyn högerklickar du på databasen du skapade och sedan väljer du **Ny fråga**.

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
I den här självstudien använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för destination eller mottagare. Om du inte har något allmänt lagringskonto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md). Pipelinen i datafabriken du skapar i den här självstudien kopierar data från den här lokala SQL Server-databasen (källa) till Blob Storage (mottagare). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för lagringskontot i den här självstudien. Gör så här för att hämta namnet och nyckeln till lagringskontot: 

1. Logga in på [Azure Portal](https://portal.azure.com) med användarnamnet och lösenordet för Azure. 

1. Välj **Fler tjänster** i vänster fönster. Filtrera genom att använda nyckelordet **Lagring** och välj sedan **Lagringskonton**.

    ![Lagringskontosökning](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton. Välj sedan ditt lagringskonto. 

1. I fönstret **Lagringskonto** väljer du **Åtkomstnycklar**.

    ![Åtkomstnycklar](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

1. I rutorna **Lagringskontonamn** och **key1** kopierar du värdena och klistrar sedan in dem i Anteckningar eller annat redigeringsprogram så att du har dem när du behöver dem senare i självstudien. 

#### <a name="create-the-adftutorial-container"></a>Skapa containern adftutorial 
I det här avsnittet skapar du en blobcontainer med namnet **adftutorial** i Blob Storage. 

1. I fönstret **Lagringskonto** växlar du till **Översikt** och klickar sedan på **Blobar**. 

    ![Alternativet Välj blobar](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. I fönstret **Blobtjänst** väljer du **Container**. 

    ![Containerknapp](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. I fönstret **Ny container**, i rutan **Namn**, anger du **adftutorial** och väljer **OK**. 

    ![Ny container](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Välj **adftutorial** i listan över containrar.

    ![Val av container](media/tutorial-hybrid-copy-powershell/select-adftutorial-container.png)

1. Låt **containerfönstret** för **adftutorial** vara öppet. Du kommer att använda den för att bekräfta utdata i slutet av självstudien. Data Factory skapar automatiskt utdatamappen i den här containern, så du behöver inte skapa en.

    ![Containerfönster](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. I menyn till vänster väljer du **Nytt** > **Data och analys** > **Data Factory**. 
  
   ![Skapa ny datafabrik](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
1. I fönstret **Ny datafabrik**, under **Namn**, anger du **ADFTutorialDataFactory**. 
   
     ![Ny datafabrik](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)

   Namnet på datafabriken måste vara *globalt unikt*. Om följande felmeddelande visas för namnfältet ändrar du namnet på datafabriken (t.ex. dittnamnADFTutorialDataFactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.

   ![Namn på ny datafabrik](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
1. Gör något av följande för **Resursgrupp**:
  
   - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

   - Välj **Skapa ny** och ange namnet på en resursgrupp. 
        
     Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).
1. Under **Version** väljer du **V2**.
1. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i Data Factory kan finnas på andra platser/i andra regioner.
1. Välj **fäst till instrumentpanelen**. 
1. Välj **Skapa**.
1. Du ser följande panel på instrumentpanelen med statusen **Distribuerar datafabrik**:

    ![Panelen Distribuerar datafabrik](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
1. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
  
     ![Datafabrikens startsida](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
1. Välj **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. Sidan **Nu sätter vi igång** visas. Välj **Kopiera data** för att starta verktyget Kopiera data. 

   ![Panel för verktyget Kopiera data](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)

1. På sidan **Egenskaper** för verktyget Kopiera data går du till **Uppgiftsnamn** och anger **CopyFromOnPremSqlToAzureBlobPipeline**. Välj sedan **Nästa**. Verktyget Kopiera data skapar en pipeline med det namn som du anger i det här fältet. 

   ![Uppgiftsnamn](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. På sidan **Källdatalager** klickar du på **Skapa ny anslutning**. 

   ![Skapa ny länkad tjänst](./media/tutorial-hybrid-copy-data-tool/create-new-source-data-store.png)

1. Under **New Linked Service** (Ny länkad tjänst) letar du upp **SQL Server** och väljer **Nästa**. 

   ![Val av SQL Server](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)

1. Under ny länkad tjänst (SQL Server) **namn**, ange **SqlServerLinkedService**. Välj **+Ny** under **Connect via integration runtime** (Anslut via Integration Runtime). Du måste skapa en lokal integreringskörning, ladda ned den på din dator och registrera den med Data Factory. Den lokala integreringskörningen kopierar data mellan din lokala miljö och molnet.

   ![Skapa Integration Runtime med lokal installation](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)

1. I dialogrutan för **installation av Integration Runtime** väljer du **Privat nätverk**. Välj sedan **Nästa**. 

   ![](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog0.png)

1. I dialogrutan **inställning av Integration Runtime** går du till **Namn** och anger **TutorialIntegrationRuntime**. Välj sedan **Nästa**. 

   ![Namn på integreringskörning](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)

1. Välj **Click here to launch the express setup for this computer** (Klicka här för att starta expressinstallation för den här datorn). Med den här åtgärden installeras integreringskörningen på datorn och registreras med Data Factory. Alternativt kan du använda det manuella installationsalternativet för att ladda ned installationsfilen, köra den och använda nyckeln för att registrera integreringskörning. 

    ![Starta expressinstallation på den här datorn](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)

1. Kör programmet som laddats ned. I fönstret visas status för expressinstallationen. 

    ![Status för expressinstallation](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Bekräfta att **TutorialIntegrationRuntime** har valts i fältet **Integration Runtime**.

      ![Vald integreringskörning](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Gör följande i **Ange lokal SQL Server-databas**: 

    a. Under **Namn** anger du **SqlServerLinkedService**.

    b. Under **Servernamn** anger du namnet på din lokala SQL Server-instans.

    c. Under **Databasnamn** anger du namnet på din lokala databas.

    d. Under **Autentiseringstyp** väljer du lämplig autentisering.

    e. Under **Användarnamn** anger du namnet på en användare med åtkomst till lokal SQL Server.

    f. Ange **lösenordet** för användaren. Välj **Slutför**. 

1. Välj **Nästa**.

   ![](./media/tutorial-hybrid-copy-data-tool/select-source-linked-service.png)

1. På sidan **Välj tabellen som data ska kopieras från eller använd en anpassad fråga**, välj tabellen **[dbo].[emp]** i listan och välj **Nästa**. Du kan välja andra tabeller baserade på din databas.

   ![Markering i produkttabellen](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)

1. På sidan **Måldatalager** väljer du **Skapa ny anslutning**

   ![Skapa länkad tjänst för mål](./media/tutorial-hybrid-copy-data-tool/create-new-sink-connection.png)

1. I **New Linked Service** (Ny länkad tjänst) letar du upp och väljer **Azure-blobb**. Välj sedan **Fortsätt**. 

   ![Val av Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. Utför följande steg i dialogrutan **New Linked Service (Azure Blob Storage)** (Ny länkad tjänst): 

       a. Under **Name****, enter **AzureStorageLinkedService**.

       b. Under **Connect via integration runtime**, select **TutorialIntegrationRuntime**

       c. Under **Storage account name**, select your storage account from the drop-down list. 

       d. Select **Next**.

   ![Ange lagringskontot](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)

1. I **Måldatalager** väljer du **Nästa**. I **Anslutningsegenskaper** väljer du **Azure Storage-tjänst** som **Azure Blob Storage**. Välj **Nästa**. 

   ![anslutningsegenskaper](./media/tutorial-hybrid-copy-data-tool/select-connection-properties.png)

1. I dialogrutan **Välj utdatafil eller mapp** under **Mappsökväg** anger du **adftutorial/fromonprem**. Du skapade containern **adftutorial** som en del av förutsättningarna. Om utdatamappen inte finns (i det här fallet **fromonprem**) skapas den automatiskt av Data Factory. Du kan också använda knappen **Bläddra** för att bläddra i bloblagringen och dess containrar/mappar. Om du inte anger något värde under **Filnamn** används som standard namnet från källan (i det här fallet **dbo.emp**).
           
   ![Välj utdatafil eller mapp](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. I dialogrutan **Filformatsinställningar** väljer du **Nästa**. 

   ![Sida för filformatinställningar](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)

1. I dialogrutan **Inställningar** väljer du **Nästa**. 

   ![Sidan Inställningar](./media/tutorial-hybrid-copy-data-tool/settings-page.png)

1. Granska värdena för alla inställningar i dialogrutan **Sammanfattning** och välj **Nästa**. 

   ![Sammanfattningssida](./media/tutorial-hybrid-copy-data-tool/summary-page.png)

1. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen eller aktiviteten du skapade.

   ![Distributionssida](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)

1. På fliken **Övervaka** kan du visa status för den pipeline som du skapade. Med länkarna i kolumnen **Åtgärd** kan du visa de aktivitetskörningar som är associerade med pipelinekörningar och köra pipelinen på nytt. 

   ![Övervaka pipelinekörningar](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)

1. Välj länken **Visa aktivitetskörningar** i kolumnen **Åtgärder** om du vill se aktivitetskörningar som är associerade med pipelinekörningen. Om du vill se mer information om kopieringsåtgärden väljer du länken **Information** (glasögonikonen) i kolumnen **Åtgärder**. Välj **Pipelines** högst upp för att gå tillbaka till vyn **Pipelinekörning**.

   ![Övervaka aktivitetskörningar](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)

1. Bekräfta att utdatafilen visas i mappen **fromonprem** för containern **adftutorial**. 

   ![Utdatablob](./media/tutorial-hybrid-copy-data-tool/output-blob.png)

1. Klicka på fliken **Redigera** till vänster för att växla till redigeringsläget. Du kan uppdatera de länkade tjänster, datauppsättningar och pipeliner som skapats med verktyget med hjälp av redigeraren. Klicka på **Kod** för att visa JSON-koden som är associerad med den entitet som har öppnats i redigeraren. Mer information om hur du redigerar dessa entiteter i användargränssnittet för Data Factory finns i [Azure Portal-versionen av den här självstudiekursen](tutorial-copy-data-portal.md).

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

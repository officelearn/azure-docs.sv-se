---
title: Använd privata slut punkter för att skapa en Azure Data Factory pipeline
description: Den här självstudien innehåller stegvisa instruktioner för att skapa en datafabrik med en pipeline i Azure-portalen. Pipelinen använder kopierings aktiviteten för att kopiera data från Azure Blob Storage till en Azure SQL-databas.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: 9458c89922fab8450b7cb8e202491d4c47e250e8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537662"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Kopiera data säkert från Azure Blob Storage till en SQL-databas med hjälp av privata slut punkter

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudiekursen skapar du en datafabrik med Azure Data Factory-användargränssnittet. **Pipelinen i den här data fabriken kopierar data säkert från Azure Blob Storage till en Azure SQL-databas (som både tillåter åtkomst till valda nätverk) med hjälp av privata slut punkter i [Azure Data Factory hanterade Virtual Network](managed-virtual-network-private-endpoint.md).** Konfigurationsmönstret i den här självstudien gäller kopiering av ett filbaserat datalager till ett relationsdatalager. En lista över datakällor som stöds som källor och mottagare finns i tabellen över [datalager som stöds](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).

> [!NOTE]
>
> - Om du inte har använt datafabriken tidigare kan du läsa [Introduktion till Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction).

I den här självstudien får du göra följande:

> * Skapa en datafabrik
> * Skapa en pipeline med en kopierings aktivitet


## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du kan använda Blob Storage som *källa* för datalagringen. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). **Se till att lagrings kontot endast tillåter åtkomst från valda nätverk.** 
* **Azure SQL Database**. Du använder databasen som *mottagare* för datalagringen. Om du inte har en Azure SQL-databas, se [skapa en SQL-databas](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) för att skapa en. **Se till att det Azure SQL Database kontot endast tillåter åtkomst från valda nätverk.** 

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered nu Blob Storage och SQL-databasen för den här självstudien genom att utföra nedanstående steg.

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Öppna Anteckningar. Kopiera följande text och spara den som en **emp.txt**-fil på din disk:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Skapa en container med namnet **adftutorial** i Blob Storage. Skapa en mapp som heter **input** i den här containern. Ladda sedan upp filen **emp.txt** till mappen **input**. Använd Azure-portalen eller verktyg som [Azure Storage Explorer](https://storageexplorer.com/) när du gör uppgifterna.

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

1. Ge Azure-tjänster åtkomst till SQL Server. Kontrollera att inställningen **Tillåt åtkomst till Azure-tjänster** är **PÅ** för din SQL Server så att datafabriken kan skriva data till din SQL Server. Om du vill kontrol lera och aktivera den här inställningen går du till Azure SQL Server > översikt > ange Server brand vägg> inställningen **Tillåt åtkomst till Azure-tjänster** är **på**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget skapar du en datafabrik och startar sedan användargränssnittet för Data Factory för att skapa en pipeline i datafabriken.

1. Öppna **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.


2. På den vänstra menyn väljer du **skapa en resurs**  >  **analys**  >  **Data Factory**.

3. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på Azure Data Factory måste vara *globalt unikt*. Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. (till exempel Dittnamnadftutorialdatafactory). Se artikeln [Namnregler för Data Factory](https://docs.microsoft.com/azure/data-factory/naming-rules) för namnregler för Data Factory-artefakter.

4. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.

5. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig**och välj en befintlig resurs grupp i den nedrullningsbara listan.

    b. Välj **Skapa ny**och ange namnet på en resurs grupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](https://docs.microsoft.com/azure/azure-resource-manager/management/overview). 

6. Under **Version** väljer du **V2**.

7. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas i andra regioner.


8. Välj **Skapa**.


9. När du har skapat meddelandet visas meddelandet i Notifications Center. Välj **gå till resurs** för att navigera till sidan data fabrik.

10. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Skapa en Azure Integration Runtime i ADF-hanterade Virtual Network
I det här steget skapar du en Azure Integration Runtime och aktiverar hanterade Virtual Network.

1. I ADF-portalen går du till **Hantera hubb** och klickar på **nytt** för att skapa en ny Azure integration Runtime.
   ![Skapa nytt Azure Integration Runtime](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Välj att skapa en Azure * *-Integration Runtime.
   ![Ny Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. Aktivera **Virtual Network**.
   ![Ny Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Välj **Skapa**.

## <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en kopieringsaktivitet i datafabriken. Kopieringsaktiviteten kopierar data från Blob Storage till en SQL-databas. I [snabbstartssjälvstudien](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) skapade du en pipeline med följande steg:

1. Skapa den länkade tjänsten.
1. Skapa datauppsättningar för indata och utdata.
1. Skapa en pipeline.

I denna självstudie börjar du med att skapa pipelinen. Sedan skapar du länkade tjänster och datauppsättningar när du behöver dem för att konfigurera pipelinen.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skapa pipeline](./media/doc-common-process/get-started-page.png)
1. I rutan **Egenskaper** för pipelinen anger du **CopyPipeline** som **namn** på pipelinen.

1. I rutan **aktiviteter** , expanderar du kategorin **flytta och transformera** och drar och släpper **Kopiera data** -aktiviteten från verktygs rutan till pipelinens design yta. Ange **CopyFromBlobToSql** som **Namn**.

    ![Kopieringsaktivitet](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurera källan

>[!TIP]
>I den här självstudien använder du *konto nyckel* som autentiseringstyp för ditt käll data lager, men du kan välja andra autentiseringsmetoder som stöds: *SAS-URI*,*tjänstens huvud namn* och *hanterad identitet* om det behövs. Mer information finns i motsvarande avsnitt i [den här artikeln](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties) .
>För att lagra hemligheter för data lager säkert rekommenderar vi också att du använder en Azure Key Vault. Se [den här artikeln](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) för detaljerade illustrationer.

#### <a name="create-source-dataset-and-linked-service"></a>Skapa käll data uppsättning och länkad tjänst

1. Gå till fliken **källa** . Välj **+ ny** för att skapa en käll data uppsättning.

1. I dialog rutan **ny data uppsättning** väljer du **Azure Blob Storage**och väljer sedan **Fortsätt**. Dina källdata finns i Blob Storage, så du väljer **Azure Blob Storage** för källdatauppsättningen.

1. I dialog rutan **Välj format** väljer du format typ för dina data och väljer sedan **Fortsätt**.

1. I dialog rutan **Ange egenskaper** anger du **SourceBlobDataset** som namn. Markera kryss rutan för **första raden som rubrik**. Under text rutan **länkad tjänst** väljer du **+ ny**.

1. I dialog rutan **ny länkad tjänst (Azure Blob Storage)** anger du **AzureStorageLinkedService** som namn, väljer ditt lagrings konto i listan **lagrings konto namn** . 

1. Se till att aktivera **interaktiv redigering**. Det kan ta cirka 1 minut att aktive ras.

    ![Interaktiv redigering](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Välj **test anslutning**. det fungerar inte när lagrings kontot tillåter åtkomst enbart från det valda nätverket och kräver Azure Data Factory för att skapa en privat slut punkt till det som ska godkännas innan det används. I fel meddelandet bör du se en länk för att skapa en **privat slut punkt** som du kan följa för att skapa en hanterad privat slut punkt. *Ett alternativ är att gå direkt till fliken Hantera och följa anvisningarna i [Nästa avsnitt](#create-a-managed-private-endpoint) för att skapa en hanterad privat slut punkt*
> [!NOTE]
> Fliken Hantera är kanske inte tillgänglig för alla data Factory-instanser. Om du inte ser det kan du fortfarande komma åt privata slut punkter via fliken**Author**-->**Connections**-->**privata slut punkt**
1. Håll dialog rutan öppen och gå sedan till ditt lagrings konto som valts ovan.

1. Följ anvisningarna i [det här avsnittet](#approval-of-a-private-link-in-storage-account) om du vill godkänna den privata länken.

1. Gå tillbaka till dialog rutan. **Testa anslutningen** igen och välj **skapa** för att distribuera den länkade tjänsten.

1. När den länkade tjänsten har skapats går den tillbaka till sidan **Ange egenskaper** . Vid **Filsökväg** väljer du **Bläddra**.

1. Navigera till mappen **adftutorial/indata** , Välj **emp.txt** -filen och välj sedan **OK**.

1. Välj **OK**. Den navigerar automatiskt till sidan pipelines. På fliken **källa** bekräftar du att **SourceBlobDataset** har valts. Om du vill förhandsgranska data på den här sidan väljer du **Förhandsgranska data**.

    ![Källdatauppsättning](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slut punkt

Om du inte klickade på hyperlänken när du testar anslutningen ovan följer du följande sökväg. Nu måste du skapa en hanterad privat slut punkt som du ska ansluta till den länkade tjänsten som skapats ovan.

1. Gå till fliken Hantera.
> [!NOTE]
> Fliken Hantera är kanske inte tillgänglig för alla data Factory-instanser. Om du inte ser det kan du fortfarande komma åt privata slut punkter via fliken**Author**-->**Connections**-->**privata slut punkt**

1. Gå till avsnittet hanterade privata slut punkter.

1. Välj **+ ny** under hanterade privata slut punkter.

    ![Ny hanterad privat slut punkt](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Välj panelen Azure-Blob Storage i listan och välj **Fortsätt**.

1. Ange namnet på det lagrings konto som du skapade ovan.

1. Välj **Skapa**.

1. Du bör se efter att ha väntat några sekunder på att den privata länken som skapas behöver ett godkännande.

1. Välj den privata slut punkt som du skapade ovan. Du kan se en hyperlänk som kommer att leda till att du godkänner den privata slut punkten på lagrings konto nivån.

    ![Hantera privat slut punkt](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Godkännande av en privat länk i lagrings kontot
1. I lagrings kontot går du till **privata slut punkts anslutningar** under avsnittet Inställningar.

1. Kryssa för den privata slut punkt som du skapade ovan och välj **Godkänn**.

    ![Godkänn privat slut punkt](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Lägg till en beskrivning och klicka på **Ja**
1. Gå tillbaka till avsnittet **hanterade privata slut punkter** på fliken **Hantera** i Azure Data Factory.
1. Det bör ta cirka 1-2 minuter att få godkännandet för din privata slut punkt att återspegla i Azure Data Factory användar gränssnitt.


### <a name="configure-sink"></a>Konfigurera kanalmottagare
>[!TIP]
>I den här självstudien använder du *SQL-autentisering* som autentiseringstyp för ditt data lager för mottagare, men du kan välja andra autentiseringsmetoder som stöds: *tjänstens huvud namn* och *hanterad identitet* om det behövs. Mer information finns i motsvarande avsnitt i [den här artikeln](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) .
>För att lagra hemligheter för data lager säkert rekommenderar vi också att du använder en Azure Key Vault. Se [den här artikeln](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) för detaljerade illustrationer.

#### <a name="create-sink-dataset-and-linked-service"></a>Skapa data uppsättning för mottagare och länkad tjänst
1. Gå till fliken **Mottagare** och välj **+ Nytt** för att skapa en datauppsättning för mottagare.

1. I dialog rutan **ny data uppsättning** anger du "SQL" i sökrutan för att filtrera anslutningarna, väljer **Azure SQL Database**och väljer sedan **Fortsätt**. I dessa självstudier kopierar du data till en SQL-databas.

1. I dialog rutan **Ange egenskaper** anger du **OutputSqlDataset** som namn. I list rutan **länkad tjänst** väljer du **+ ny**. En datauppsättning måste associeras med en länkad tjänst. De länkade tjänsterna har anslutningssträngen som datafabriken använder för att ansluta till SQL-databasen vid körning. Datauppsättningen anger den container, mapp och fil (valfritt) som data kopieras till.

1. Utför följande steg i dialog rutan **ny länkad tjänst (Azure SQL Database)** :

    1. Under **Namn** anger du **AzureSqlDatabaseLinkedService**.
    1. Under **Servernamn** väljer du din SQL Server-instans.
    1. Se till att aktivera **interaktiv redigering**.
    1. Under **Databasnamn** väljer du din SQL-databas.
    1. Under **Användarnamn** anger du namnet på användaren.
    1. För **Lösenord** anger du användarens lösenord.
    1. Välj **Testanslutning**. Det bör inte utföras eftersom SQL Server endast tillåter åtkomst från valda nätverk och kräver Azure Data Factory för att skapa en privat slut punkt till den, vilket bör godkännas innan det används. I fel meddelandet bör du se en länk för att skapa en **privat slut punkt** som du kan följa för att skapa en hanterad privat slut punkt. *Ett alternativ är att gå direkt till fliken Hantera och följa anvisningarna i nästa avsnitt för att skapa en hanterad privat slut punkt*
    1. Låt dialog rutan vara öppen och gå sedan till den SQL Server som valdes ovan.    
    1. Följ anvisningarna i [det här avsnittet](#approval-of-a-private-link-in-sql-server) om du vill godkänna den privata länken.
    1. Gå tillbaka till dialog rutan. **Testa anslutningen** igen och välj **skapa** för att distribuera den länkade tjänsten.

1. Den navigerar automatiskt till dialog rutan **Ange egenskaper** . Under **Tabell** väljer du **[dbo].[emp]**. Välj sedan **OK**.

1. Gå till fliken med pipelinen och kontrollera i **Sink Dataset** (Datauppsättning för mottagare) att **OutputSqlDataset** har valts.

    ![Fliken Pipeline](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)       

Du kan också mappa schemat för källan till motsvarande mål schema genom att följa [schema mappningen i kopierings aktiviteten](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping).

#### <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slut punkt

Om du inte klickade på hyperlänken när du testar anslutningen ovan följer du följande sökväg. Nu måste du skapa en hanterad privat slut punkt som du ska ansluta till den länkade tjänsten som skapats ovan.

1. Gå till fliken Hantera.
1. Gå till avsnittet hanterade privata slut punkter.
1. Välj **+ ny** under hanterade privata slut punkter.

    ![Ny hanterad privat slut punkt](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Välj panelen Azure SQL Database i listan och välj **Fortsätt**.
1. Ange namnet på den SQL Server som du valde ovan.
1. Välj **Skapa**.
1. Du bör se efter att ha väntat några sekunder på att den privata länken som skapas behöver ett godkännande.
1. Välj den privata slut punkt som du skapade ovan. Du kan se en hyperlänk som kommer att leda till att du godkänner den privata slut punkten på SQL Server-nivån.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Godkännande av en privat länk i SQL Server
1. I SQL Server går du till **privata slut punkts anslutningar** under avsnittet Inställningar.
1. Kryssa för den privata slut punkt som du skapade ovan och välj **Godkänn**.
1. Lägg till en beskrivning och klicka på **Ja**.
1. Gå tillbaka till avsnittet **hanterade privata slut punkter** på fliken **Hantera** i Azure Data Factory.
1. Det bör ta cirka 1-2 minuter att få godkännandet återspeglas för din privata slut punkt.

#### <a name="debug-and-publish-the-pipeline"></a>Felsöka och publicera en pipeline

Du kan felsöka en pipeline innan du publicerar artefakter (länkade tjänster, datauppsättningar och pipelines) till datafabriken eller din egen Azure Repos Git-lagringsplats.

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret.
2. När pipelinen har körts kan du gå till det övre verktygsfältet och välja **publicera alla**. Med den här åtgärden publicerar du enheter (datauppsättningar och pipelines) som du skapat i datafabriken.
3. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill visa meddelanden klickar du på **Visa meddelanden** överst till höger (klock knappen).


#### <a name="summary"></a>Sammanfattning
Pipelinen i det här exemplet kopierar data från Blob Storage till Azure SQL DB med privat slut punkt i hanterade Virtual Network. Du har lärt dig att:

> * Skapa en datafabrik
> * Skapa en pipeline med en kopierings aktivitet


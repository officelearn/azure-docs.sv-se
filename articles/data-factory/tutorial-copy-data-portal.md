---
title: Använd Azure Portal för att skapa en Data Factory-pipeline
description: Den här självstudien innehåller stegvisa instruktioner för att skapa en datafabrik med en pipeline i Azure-portalen. Pipelinen använder kopieringsaktiviteten för att kopiera data från Azure Blob Storage till en SQL-databas.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: ba348cdd478b1d66d7b7286ba0a54adfd98137e2
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683615"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-azure-data-factory"></a>Kopiera data från en Azure Blob Storage till en SQL-databas med Azure Data Factory
I den här självstudiekursen skapar du en datafabrik med Azure Data Factory-användargränssnittet. Pipelinen i den här datafabriken kopierar data från Azure Blob Storage till en SQL-databas. Konfigurationsmönstret i den här självstudien gäller kopiering av ett filbaserat datalager till ett relationsdatalager. En lista över datakällor som stöds som källor och mottagare finns i tabellen över [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Om du inte har använt datafabriken tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en kopieringsaktivitet.
> * Testkör pipelinen.
> * Utlös pipelinen manuellt.
> * Utlös pipelinen enligt ett schema.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Nödvändiga komponenter
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du kan använda Blob Storage som *källa* för datalagringen. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-quickstart-create-account.md).
* **Azure SQL Database**. Du använder databasen som *mottagare* för datalagringen. Om du inte har en SQL-databas kan du läsa om hur du skapar en i [Skapa en SQL-databas](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered nu Blob Storage och SQL-databasen för den här självstudien genom att utföra nedanstående steg.

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Öppna Anteckningar. Kopiera följande text och spara den som en **emp.txt**-fil på din disk:

    ```
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

1. Ge Azure-tjänsterna åtkomst till SQL Server. Kontrollera att inställningen **Tillåt åtkomst till Azure-tjänster** är **PÅ** för din SQL Server så att datafabriken kan skriva data till din SQL Server. Om du vill kontrol lera och aktivera den här inställningen går du till Azure SQL Server > Översikt > Ange server brand vägg > inställningen **Tillåt åtkomst till Azure-tjänster** är **på**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget skapar du en datafabrik och startar sedan användargränssnittet för Data Factory för att skapa en pipeline i datafabriken. 

1. Öppna **Microsoft Edge** eller **Google Chrome**. Just nu är det bara webbläsarna Microsoft Edge och Google Chrome som har stöd för Data Factory UI.
2. På den vänstra menyn väljer du **skapa en resurs** > **Analytics** > **Data Factory**: 
  
   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

3. I fönstret **Ny datafabrik**, under **Namn**, anger du **ADFTutorialDataFactory**. 
 
   Namnet på Azure Data Factory måste vara *globalt unikt*. Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. (till exempel Dittnamnadftutorialdatafactory). Se artikeln [om namnregler för datafabriker](naming-rules.md) för namnregler för datafabriksartefakter.
        
     ![Ny datafabrik](./media/doc-common-process/name-not-available-error.png)
4. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
5. Gör något av följande för **Resursgrupp**:
     
    a. Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

    b. Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md). 
6. Under **Version** väljer du **V2**.
7. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas i andra regioner.
8. Välj **Skapa**. 
9. När du har skapat meddelandet visas meddelandet i Notifications Center. Välj **gå till resurs** för att navigera till sidan data fabrik.
10. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.


## <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en kopieringsaktivitet i datafabriken. Kopieringsaktiviteten kopierar data från Blob Storage till en SQL-databas. I [snabbstartssjälvstudien](quickstart-create-data-factory-portal.md) skapade du en pipeline med följande steg:

1. Skapa den länkade tjänsten. 
1. Skapa datauppsättningar för indata och utdata.
1. Skapa en pipeline.

I denna självstudie börjar du med att skapa pipelinen. Sedan skapar du länkade tjänster och datauppsättningar när du behöver dem för att konfigurera pipelinen. 

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**. 

   ![Skapa pipeline](./media/doc-common-process/get-started-page.png)
1. I fliken **Allmänt** för pipeline anger du **CopyPipeline** för **Namn** på pipeline.

1. I rutan **aktiviteter** , expanderar du kategorin **flytta och transformera** och drar och släpper **Kopiera data** -aktiviteten från verktygs rutan till pipelinens design yta. Ange **CopyFromBlobToSql** som **Namn**.

    ![Kopieringsaktivitet](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurera källan

1. Gå till fliken **källa** . Välj **+ ny** för att skapa en käll data uppsättning. 

1. I dialog rutan **ny data uppsättning** väljer du **Azure Blob Storage**och väljer sedan **Fortsätt**. Dina källdata finns i Blob Storage, så du väljer **Azure Blob Storage** för källdatauppsättningen. 

1. I dialog rutan **Välj format** väljer du format typ för dina data och väljer sedan **Fortsätt**.

    ![Typ av data format](./media/doc-common-process/select-data-format.png)

1. I dialog rutan **Ange egenskaper** anger du **SourceBlobDataset** som namn. Vid textrutan **Länkad tjänst** väljer du **+ Nytt**. 
    
1. I dialog rutan **ny länkad tjänst (Azure Blob Storage)** anger du **AzureStorageLinkedService** som namn, väljer ditt lagrings konto i listan **lagrings konto namn** . Testa anslutningen och välj sedan **Slutför** för att distribuera den länkade tjänsten.

1. När den länkade tjänsten har skapats går den tillbaka till sidan **Ange egenskaper** . Vid **Filsökväg** väljer du **Bläddra**.

1. Navigera till mappen **adftutorial/input**, välj filen **emp.txt** och klicka på **Slutför**.

1. Den navigerar automatiskt till sidan pipelines. På fliken **källa** bekräftar du att **SourceBlobDataset** har valts. Om du vill förhandsgranska data på den här sidan väljer du **Förhandsgranska data**. 
    
    ![Källdatauppsättning](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Konfigurera kanalmottagare

1. Gå till fliken **Mottagare** och välj **+ Nytt** för att skapa en datauppsättning för mottagare. 

1. I dialog rutan **ny data uppsättning** anger du "SQL" i sökrutan för att filtrera anslutningarna, väljer **Azure SQL Database**och väljer sedan **Fortsätt**. I dessa självstudier kopierar du data till en SQL-databas. 

1. I dialog rutan **Ange egenskaper** anger du **OutputSqlDataset** som namn. Vid textrutan **Länkad tjänst** väljer du **+ Nytt**. En datauppsättning måste associeras med en länkad tjänst. De länkade tjänsterna har anslutningssträngen som datafabriken använder för att ansluta till SQL-databasen vid körning. Datauppsättningen anger den container, mapp och fil (valfritt) som data kopieras till. 
      
1. Utför följande steg i dialog rutan **ny länkad tjänst (Azure SQL Database)** : 

    a. Under **Namn** anger du **AzureSqlDatabaseLinkedService**.

    b. Under **Servernamn** väljer du din SQL Server-instans.

    c. Under **Databasnamn** väljer du din SQL-databas.

    d. Under **Användarnamn** anger du namnet på användaren.

    e. För **Lösenord** anger du användarens lösenord.

    f. Välj **Testanslutning** för att testa anslutningen.

    g. Klicka på **Slutför** för att distribuera den länkade tjänsten. 
    
    ![Spara ny länkad tjänst](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Den navigerar automatiskt till dialog rutan **Ange egenskaper** . Under **Tabell** väljer du **[dbo].[emp]** . Välj sedan **Slutför**.

1. Gå till fliken med pipelinen och kontrollera i **Sink Dataset** (Datauppsättning för mottagare) att **OutputSqlDataset** har valts.

    ![Fliken Pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Du kan också mappa schemat för källan till motsvarande mål schema genom att följa [schema mappning i kopierings aktiviteten](copy-activity-schema-and-type-mapping.md)
    
## <a name="validate-the-pipeline"></a>Verifiera pipeline
Verifiera pipelinen genom att välja **Verifiera** i verktygsfältet.
 
Du kan se den JSON-kod som är associerad med pipelinen genom att klicka på **kod** i det övre högra hörnet.

## <a name="debug-and-publish-the-pipeline"></a>Felsöka och publicera en pipeline
Du kan felsöka en pipeline innan du publicerar artefakter (länkade tjänster, datauppsättningar och pipelines) till datafabriken eller din egen Azure Repos Git-lagringsplats. 

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret. 

1. När pipelinen har körts kan du gå till det övre verktygsfältet och välja **publicera alla**. Med den här åtgärden publicerar du enheter (datauppsättningar och pipelines) som du skapat i datafabriken.

1. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. För att visa meddelanden klickar du på **Visa meddelanden** uppe till vänster (klockknappen). 

## <a name="trigger-the-pipeline-manually"></a>Utlös pipelinen manuellt
I det här steget utlöser du manuellt pipelinen du publicerade i föregående steg. 

1. Välj **Lägg till utlösare** i verktygsfältet och välj sedan **Utlös nu**. På sidan **Pipeline Run** (Pipelinekörning) väljer du **Slutför**.  

1. Gå till fliken **Övervaka** till vänster. Du ser en pipelinekörning som är utlöst av en manuell utlösare. Du kan använda länkar i kolumnen **Åtgärder** för att visa aktivitetsinformation och köra pipelinen på nytt.

    ![Övervaka pipelinekörningar](./media/tutorial-copy-data-portal/monitor-pipeline.png)

1. Om du vill se aktivitetskörningar som är associerade med pipelinekörningen, väljer du länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder). I det här exemplet finns det bara en aktivitet, så du ser bara en post i listan. Om du vill se mer information om kopieringsåtgärden väljer du länken **Information** (glasögonikonen) i kolumnen **Åtgärder**. Välj **pipeline-körningar** överst för att gå tillbaka till vyn pipelines-körningar. Välj **Uppdatera** för att uppdatera vyn.

    ![Övervaka aktivitetskörningar](./media/tutorial-copy-data-portal/view-activity-runs.png)

1. Kontrollera att två rader har lagts till i tabellen **emp** i SQL-databasen. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Utlös pipelinen enligt ett schema
I det här schemat skapar du en schemautlösare för pipelinen. Utlösaren kör pipelinen enligt det angivna schemat, t.ex. varje timme eller dagligen. Här ställer du in utlösaren så att den körs varje minut tills angivet Slutdatum/tid-värde. 

1. Gå till fliken **Författare** uppe till vänster på övervakningsfliken. 

1. Gå till din pipeline, klicka på **Lägg till utlösare** i verktygsfältet och välj **nytt/redigera**. 

1. I dialog rutan **Lägg till utlösare** väljer du **+ nytt** för avsnittet **Välj utlösare** .

1. Utför följande steg i fönstret **Ny utlösare**: 

    a. Under **Namn** anger du **RunEveryMinute**.

    b. Under **Slut**  väljer du **På datum**.

    c. Under **Slutar den** väljer du i listrutan.

    d. Välj den **aktuella dagen**. Som standard är slutdagen inställd på nästa dag.

    e. Uppdatera **slut tids** delen så att den är några minuter efter aktuellt datum/tid. Utlösaren aktiveras enbart när du har publicerat ändringarna. Om du ställer in det på bara några minuter, och du inte publicerar det, så ser du inte någon Utlös ande körning.

    f. Välj **Använd**. 

    g. För **aktiverat** alternativ väljer du **Ja**. 

    h. Välj **Nästa**.

    ![Knappen Aktiverad](./media/tutorial-copy-data-portal/trigger-activiated-next.png)

    > [!IMPORTANT]
    > Det är kostnader associerade med varje pipelinekörning, så var noga med att ställa in slutdatum korrekt. 
1. På sidan **Trigger Run Parameters** (Parametrar för utlösarkörning) läser du varningen och klickar på **Slutför**. Pipelinen i det här exemplet tar inga parametrar. 

1. Klicka på **Publicera alla** för att publicera ändringen. 

1. Växla till fliken **Övervakare** till vänster för att se de utlösta pipelinekörningarna. 

    ![Utlösta pipelinekörningar](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)   
 
1. Om du vill växla från **pipeline-körningar** till vyn **Utlösare körs** väljer du **utlösare som körs** längst upp i fönstret.

1. Utlösarkörningarna visas i en lista. 

1. Verifiera att två rader per minut (för varje pipelinekörning) infogas i tabellen **emp** tills den angivna sluttiden. 

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

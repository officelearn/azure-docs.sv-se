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
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: jingwang
ms.openlocfilehash: 1aca53c876b6cc982c141d74cdf727f9c966adfe
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56233871"
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

1. Skapa en container med namnet **adftutorial** i Blob Storage. Skapa en mapp som heter **input** i den här containern. Ladda sedan upp filen **emp.txt** till mappen **input**. Använd Azure-portalen eller verktyg som [Azure Storage Explorer](http://storageexplorer.com/) när du gör uppgifterna.

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

1. Ge Azure-tjänster åtkomst till SQL Server. Kontrollera att inställningen **Tillåt åtkomst till Azure-tjänster** är **PÅ** för din SQL Server så att datafabriken kan skriva data till din SQL Server. Gör så här för att kontrollera och aktivera inställningen:

    a. Till vänster väljer du **Fler tjänster** > **SQL-servrar**.

    b. Välj din server och under **INSTÄLLNINGAR** väljer du **Brandvägg**.

    c. På sidan **Brandväggsinställningar** väljer du **PÅ** för **Tillåt åtkomst till Azure-tjänster**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget skapar du en datafabrik och startar sedan användargränssnittet för Data Factory för att skapa en pipeline i datafabriken. 

1. Öppna **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. Välj **Skapa en resurs** > **Analys** > **Data Factory** i menyn till vänster. 
  
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
6. Under **Version** väljer du **V2**.
7. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas i andra regioner.
8. Välj **fäst till instrumentpanelen**. 
9. Välj **Skapa**. 
10. Du ser följande panel på instrumentpanelen med statusen **Distribuerar datafabrik**: 

    ![Panelen Distribuerar datafabrik](media/tutorial-copy-data-portal/deploying-data-factory.png)
1. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
    ![Datafabrikens startsida](./media/tutorial-copy-data-portal/data-factory-home-page.png)
1. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en kopieringsaktivitet i datafabriken. Kopieringsaktiviteten kopierar data från Blob Storage till en SQL-databas. I [snabbstartssjälvstudien](quickstart-create-data-factory-portal.md) skapade du en pipeline med följande steg:

1. Skapa den länkade tjänsten. 
1. Skapa datauppsättningar för indata och utdata.
1. Skapa en pipeline.

I denna självstudie börjar du med att skapa pipelinen. Sedan skapar du länkade tjänster och datauppsättningar när du behöver dem för att konfigurera pipelinen. 

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**. 

   ![Skapa pipeline](./media/tutorial-copy-data-portal/create-pipeline-tile.png)
1. I fliken **Allmänt** för pipeline anger du **CopyPipeline** för **Namn** på pipeline.

1. I verktygslådan **Aktiviteter** expanderar du kategorin **Flytta och omvandla** och drar och släpper aktiviteten **Kopiera data** från verktygslådan till pipelinedesignytan. Ange **CopyFromBlobToSql** som **Namn**.

    ![Kopieringsaktivitet](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurera källan

1. Gå till fliken **Källa**. Välj **+ Nytt** för att skapa en källdatauppsättning. 

1. I fönstret **Ny datauppsättning** väljer du **Azure Blob Storage** och klickar på **Slutför**. Dina källdata finns i Blob Storage, så du väljer **Azure Blob Storage** för källdatauppsättningen. 

    ![Val av lagringsutrymme](./media/tutorial-copy-data-portal/select-azure-blob-dataset.png)

1. En ny flik öppnas för blobbdatauppsättningen. På fliken **Allmänt** nedtill i fönstret **Egenskaper** anger du **SourceBlobDataset** som **Namn**.

    ![Namn på datauppsättning](./media/tutorial-copy-data-portal/dataset-name.png)

1. Gå till fliken **Anslutning** i fönstret **Egenskaper**. Vid textrutan **Länkad tjänst** väljer du **+ Nytt**. 

    ![Knapp för ny länkad tjänst](./media/tutorial-copy-data-portal/source-dataset-new-linked-service-button.png)

1. I fönstret **New Linked Service** (Ny länkad tjänst) anger du **AzureStorageLinkedService** som namn och väljer ditt lagringskonto från listan **Lagringskontonamn**. Välj sedan **Spara** för att distribuera den länkade tjänsten.

    ![Ny länkad tjänst](./media/tutorial-copy-data-portal/new-azure-storage-linked-service.png)

1. När du har skapat den länkade tjänsten kommer du tillbaka till inställningarna för datauppsättningen. Vid **Filsökväg** väljer du **Bläddra**.

    ![Bläddringsknapp för filsökväg](./media/tutorial-copy-data-portal/file-browse-button.png)

1. Navigera till mappen **adftutorial/input**, välj filen **emp.txt** och klicka på **Slutför**. 

    ![Välja indatafil](./media/tutorial-copy-data-portal/select-input-file.png)

1. Bekräfta att **Filformat** är satt till **Textformat** och att **Kolumnavgränsare** är satt till **Komma (`,`)**. Om källfilen använder olika avgränsare för rad och kolumn kan du välja **Detect Text Format** (Identifiera textformat) i fältet **Filformat**. Verktyget Kopiera data identifierar filformatet och avgränsarna automatiskt. Du kan fortfarande åsidosätta dessa värden. Om du vill förhandsgranska data på den här sidan väljer du **Förhandsgranska data**.

    ![Identifiera textformat](./media/tutorial-copy-data-portal/detect-text-format.png)

1. Gå till fliken **Schema** i fönstret **Egenskaper** och välj **Importera schema**. Lägg märke till att programmet identifierade två kolumner i källfilen. Importera schemat hit så att du kan mappa kolumner från källdatalagret till datalagret för mottagare. Om du inte behöver mappa kolumner kan du hoppa över det här steget. Importera schemat för den här självstudien.

    ![Identifiera källans schema](./media/tutorial-copy-data-portal/detect-source-schema.png)  

1. Nu kan gå tillbaka till pipelinen -> fliken **Källa** och bekräfta att **SourceBlobDataset** har valts. Om du vill förhandsgranska data på den här sidan väljer du **Förhandsgranska data**. 
    
    ![Källdatauppsättning](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Konfigurera kanalmottagare

1. Gå till fliken **Mottagare** och välj **+ Nytt** för att skapa en datauppsättning för mottagare. 

    ![Datauppsättning för mottagare](./media/tutorial-copy-data-portal/new-sink-dataset-button.png)
1. I fönstret **Ny datauppsättning** anger du ”SQL” i sökrutan för att filtrera anslutningar. Välj sedan **Azure SQL Database** och till sist **Slutför**. I dessa självstudier kopierar du data till en SQL-databas. 

    ![Val av SQL-databas](./media/tutorial-copy-data-portal/select-azure-sql-dataset.png)
1. På fliken **Allmänt** i fönstret **Egenskaper** anger du för **Namn** värdet **OutputSqlDataset**. 
    
    ![Namn på datauppsättning för utdata](./media/tutorial-copy-data-portal/output-dataset-name.png)
1. Gå till fliken **Anslutning** och välj **Länkad tjänst** och sedan **+ Nytt**. En datauppsättning måste associeras med en länkad tjänst. Den länkade tjänsten har anslutningssträngen som Data Factory använder för att ansluta till SQL-databasen vid körning. Datauppsättningen anger den container, mapp och fil (valfritt) som data kopieras till. 
    
    ![Länkad tjänst](./media/tutorial-copy-data-portal/new-azure-sql-database-linked-service-button.png)       
1. Utför följande steg i fönstret **Ny länkad tjänst**: 

    a. Under **Namn** anger du **AzureSqlDatabaseLinkedService**.

    b. Under **Servernamn** väljer du din SQL Server-instans.

    c. Under **Databasnamn** väljer du din SQL-databas.

    d. Under **Användarnamn** anger du namnet på användaren.

    e. Under **Lösenord** anger du användarens lösenord.

    f. Välj **Testanslutning** för att testa anslutningen.

    g. Välj **Spara** för att spara den länkade tjänsten. 
    
    ![Spara ny länkad tjänst](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Under **Tabell** väljer du **[dbo].[emp]**. 

    ![Tabell](./media/tutorial-copy-data-portal/select-emp-table.png)
1. Gå till fliken **Schema** och välj **Importera schema**. 

    ![Välj importschema](./media/tutorial-copy-data-portal/import-destination-schema.png)
1. Välj kolumnen **ID** och sedan **Ta bort**. Kolumnen **ID** är en identitetskolumn i SQL-databasen, så kopieringsaktiviteten måste inte infoga data i den här kolumnen.

    ![Ta bort kolumn-ID](./media/tutorial-copy-data-portal/delete-id-column.png)
1. Gå till fliken med pipelinen och kontrollera i **Sink Dataset** (Datauppsättning för mottagare) att **OutputSqlDataset** har valts.

    ![Fliken Pipeline](./media/tutorial-copy-data-portal/pipeline-tab-2.png)        

### <a name="configure-mapping"></a>Konfigurera mappning

Gå till fliken **Mappning** längst ned i fönstret **Egenskaper** och välj **Importera scheman**. Lägg märke till att den första och andra kolumnen i källfilen är mappade till **FirstName** och **LastName** i SQL-databasen.

![Mappa scheman](./media/tutorial-copy-data-portal/map-schemas.png)

## <a name="validate-the-pipeline"></a>Verifiera pipeline
Verifiera pipelinen genom att välja **Verifiera** i verktygsfältet.
 
Du kan se JSON-kod som är kopplat till pipelinen genom att klicka på **Kod** uppe till höger.

## <a name="debug-and-publish-the-pipeline"></a>Felsöka och publicera en pipeline
Du kan felsöka en pipeline innan du publicerar artefakter (länkade tjänster, datauppsättningar och pipelines) till datafabriken eller din egen Azure Repos Git-lagringsplats. 

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret. 

1. När en pipeline körs med lyckat resultat väljer du **Publicera alla** i det översta verktygsfältet. Med den här åtgärden publicerar du enheter (datauppsättningar och pipelines) som du skapat i datafabriken.

    ![Publicera](./media/tutorial-copy-data-portal/publish-button.png)

1. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. För att visa meddelanden klickar du på **Visa meddelanden** uppe till vänster (klockknappen). 

## <a name="trigger-the-pipeline-manually"></a>Utlös pipelinen manuellt
I det här steget utlöser du manuellt pipelinen du publicerade i föregående steg. 

1. Välj **Utlös** i verktygsfältet och sedan **Trigger Now** (Utlös nu). På sidan **Pipeline Run** (Pipelinekörning) väljer du **Slutför**.  

1. Gå till fliken **Övervaka** till vänster. Du ser en pipelinekörning som är utlöst av en manuell utlösare. Du kan använda länkar i kolumnen **Åtgärder** för att visa aktivitetsinformation och köra pipelinen på nytt.

    ![Övervaka pipelinekörningar](./media/tutorial-copy-data-portal/monitor-pipeline.png)
1. Om du vill se aktivitetskörningar som är associerade med pipelinekörningen, väljer du länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder). Det finns bara en aktivitet i det här exemplet. Därför visas bara en post i listan. Om du vill se mer information om kopieringsåtgärden väljer du länken **Information** (glasögonikonen) i kolumnen **Åtgärder**. Välj **Pipelines** högst upp för att gå tillbaka till vyn **Pipeline Runs** (Pipelinekörning). Välj **Uppdatera** för att uppdatera vyn.

    ![Övervaka aktivitetskörningar](./media/tutorial-copy-data-portal/view-activity-runs.png)
1. Kontrollera att två rader har lagts till i tabellen **emp** i SQL-databasen. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Utlös pipelinen enligt ett schema
I det här schemat skapar du en schemautlösare för pipelinen. Utlösaren kör pipelinen enligt det angivna schemat, t.ex. varje timme eller dagligen. I det här exemplet ställer du in att utlösaren ska köras varje minut fram till angiven sluttid. 

1. Gå till fliken **Författare** uppe till vänster på övervakningsfliken. 

1. Gå till din pipeline, klicka på **Utlösare** i verktygsfältet och välj **Ny/Redigera**. 

1. På sidan **Add Triggers** (Lägg till utlösare) väljer du **Choose trigger** (Välj utlösare) och sedan **+ Ny**. 

    ![Knappen Ny](./media/tutorial-copy-data-portal/add-trigger-new-button.png)
1. Utför följande steg i fönstret **Ny utlösare**: 

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
1. På sidan **Trigger Run Parameters** (Parametrar för utlösarkörning) läser du varningen och klickar på **Slutför**. Pipelinen i det här exemplet tar inga parametrar. 

    ![Parametrar för utlösarkörning](./media/tutorial-copy-data-portal/trigger-pipeline-parameters.png)

1. Klicka på **Publicera alla** för att publicera ändringen. 

1. Växla till fliken **Övervakare** till vänster för att se de utlösta pipelinekörningarna. 

    ![Utlösta pipelinekörningar](./media/tutorial-copy-data-portal/triggered-pipeline-runs.png)    
1. För att växla från vyn **Pipeline Runs** (Pipelinekörningar) till vyn **Trigger Runs** (Utlösarkörning) väljer du **Pipeline Runs** och sedan **Trigger Runs**.
    
    ![Utlösarkörning](./media/tutorial-copy-data-portal/trigger-runs-menu.png)
1. Utlösarkörningarna visas i en lista. 

    ![Listan med utlösarkörningar](./media/tutorial-copy-data-portal/trigger-runs-list.png)
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

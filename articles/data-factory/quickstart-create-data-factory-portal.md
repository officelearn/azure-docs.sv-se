---
title: Skapa en Azure-datafabrik med gränssnittet i Azure Data Factory | Microsoft Docs
description: Skapa en datafabrik med en pipeline som en kopierar data från en plats till en annan i Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 6f5a4e04c0d135e85624b04dbcdcda6b7d15a427
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55989397"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Snabbstart: Skapa en datafabrik med hjälp av gränssnittet i Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](quickstart-create-data-factory-portal.md)

Den här snabbstarten beskriver hur du använder Azure Data Factory-användargränssnittet till att skapa och övervaka en datafabrik. Den pipeline du skapar i den här datafabriken *kopierar* data från en mapp till en annan mapp i Azure Blob Storage. En självstudie om hur du *omvandlar* data med hjälp av Azure Data Factory finns i [Självstudie: Transformera data med hjälp av Spark](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](data-factory-introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Om du tittar på den här videon får du hjälp med att förstå Data Factory-användargränssnittet: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. Gå till [Azure-portalen](https://portal.azure.com). 
1. Välj **Skapa en resurs** på den vänstra menyn, välj **Analys** och välj sedan **Data Factory**. 
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
1. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **Namn**. 
      
   ![Sidan Ny datafabrik](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara *globalt unikt*. Om du ser följande fel ändrar du namnet på datafabriken (till exempel **&lt;dittnamn&gt;ADFTutorialDataFactory**) och provar att skapa fabriken igen. Namngivningsregler för Data Factory-artefakter finns i artikeln [Data Factory – namnregler](naming-rules.md).
  
   ![Fel när ett namn inte är tillgängligt](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
1. Välj den Azure-prenumeration där du vill skapa den nya datafabriken för **Prenumeration**. 
1. För **Resursgrupp** utför du något av följande steg:
     
   - Välj **Använd befintlig** och välj en befintlig resursgrupp från listan. 
   - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
   Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
1. För **Version** väljer du **V2**.
1. För **Plats** väljer du en plats för datafabriken.

   I listan visas endast platser som Data Factory har stöd för och var dina Azure Data Factory-metadata kommer att lagras. Observera att de associerade datalagren (såsom Azure Storage och Azure SQL Database) och beräkningar (som HDInsight) som används i Data Factory kan köras i andra regioner.

1. Välj **Skapa**.

1. När datafabriken har skapats visas sidan **Datafabrik**. Välj panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik.
   
   ![Startsidan för datafabriken med panelen Författare och övervakare](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
1. På sidan **Nu sätter vi igång** växlar du till fliken **Författare** i den vänstra panelen. 

    ![Sidan ”Nu sätter vi igång”](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst
I den här proceduren skapar du en länkad tjänst för att länka ditt Azure Storage-konto till datafabriken. Den länkade tjänsten har anslutningsinformationen som Data Factory-tjänsten använder vid körning för att ansluta till den.

1. Klicka på **Anslutningar** och sedan på knappen **Nytt** i verktygsfältet. 

   ![Knappar för att skapa en ny anslutning](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
1. På sidan **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**. 

   ![Välj panelen ”Azure Blob Storage”](./media/quickstart-create-data-factory-portal/select-azure-blob-linked-service.png)
1. Utför följande steg: 

   a. Som **Namn** anger du **AzureStorageLinkedService**.

   b. Som **Lagringskontonamn** väljer du namnet på ditt Azure Storage-konto.

   c. Välj **Testanslutning** och bekräfta att Data Factory-tjänsten kan ansluta till lagringskontot. 

   d. Välj **Slutför** för att spara den länkade tjänsten. 

   ![Inställningar för länkad Azure Storage-tjänst](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 

## <a name="create-datasets"></a>Skapa datauppsättningar
I den här proceduren skapar du två datamängder: **InputDataset** och **OutputDataset**. Dessa datauppsättningar är av typen **AzureBlob**. De refererar till den länkade Azure Storage-tjänst du skapade i föregående avsnitt. 

Datauppsättningen för indata representerar källdata i indatamappen. I definitionen av datauppsättningen för indata anger du blobcontainern (**adftutorial**), mappen (**input**) och filen (**emp.txt**) som innehåller källdata. 

Datauppsättningen för utdata representerar de data som kopieras till målet. I definitionen av datauppsättningen för utdata anger du blobcontainern (**adftutorial**), mappen (**output**) och filen som data ska kopieras till. Varje pipelinekörning har ett unikt ID tilldelat. Du kan komma åt detta ID via systemvariabeln **RunId**. Namnet på utdatafilen utvärderas dynamiskt baserat på pipelinens körnings-ID.   

I den länkade tjänstinställningen angav du Azure-lagringskontot som innehåller källdata. I inställningarna för källdatauppsättningen anger du exakt var källdata finns (blobcontainer, mapp och fil). I inställningarna för mottagaruppsättningen anger du var du vill kopiera data (blobcontainer, mapp och fil). 
 
1. Klicka på knappen **+** (plus) och välj **Datauppsättning**.

   ![Meny för att skapa en datauppsättning](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
1. I fönstret **Ny datauppsättning** väljer du **Azure Blob Storage** och klickar på **Slutför**. 

   ![Välj ”Azure Blob Storage”](./media/quickstart-create-data-factory-portal/select-azure-blob-dataset.png)
1. I fliken **Allmänt** för datauppsättningen anger du **InputDataset** som **namn**. 

1. Växla till fliken **Anslutning** och utför följande steg: 

    a. För **Länkad tjänst** väljer du **AzureStorageLinkedService**.

    b. För **Filsökväg** väljer du knappen **Bläddra**.

    ![Fliken ”Anslutning” och knappen ”Bläddra”](./media/quickstart-create-data-factory-portal/file-path-browse-button.png) c. I fönstret för att **välja en fil eller mapp** navigerar du till mappen **input** i containern **adftutorial**, väljer filen **emp.txt** och väljer sedan **Slutför**.

    ![Bläddra efter indatafilen](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    
    d. (valfritt) Välj **Förhandsgranska data** om du vill förhandsgranska data i filen emp.txt.     

1. Upprepa stegen för att skapa datauppsättningen för utdata:  

   a. Klicka på knappen **+** (plus) och välj **Datauppsättning**.

   b. I fönstret **Ny datauppsättning** väljer du **Azure Blob Storage** och klickar på **Slutför**.

   c. Ange**OutputDataset** i tabellen **Allmänt** som namn.

   d. I fliken **Anslutning** väljer du **AzureStorageLinkedService** som länkad tjänsten och anger **adftutorial/utdata** för mappen, i katalogfältet. Om **utdatamappen** inte finns skapas kopieringsaktiviteten vid körningen.

## <a name="create-a-pipeline"></a>Skapa en pipeline 
I den här proceduren skapar och verifierar du en pipeline med en kopieringsaktivitet som använder uppsättningar för indata och utdata. Kopieringsaktiviteten kopierar data från filen som anges i inställningarna för datauppsättningen för indata till filen som anges i inställningarna för datauppsättningen för utdata. Om datauppsättningen för indata endast anger en mapp (inte filnamnet) kopierar kopieringsaktiviteten alla filer i källmappen till målet. 

1. Välj knappen **+** (plus) och välj sedan **Pipeline**. 

   ![Meny för att skapa en ny pipeline](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
1. In fliken **Allmänt** anger du **CopyPipeline** som **Name**. 

1. Gå till verktygsfältet **Aktiviteter** och expandera **Flytta och transformera**. Dra aktiviteten **Kopiera** från verktygslådan **Aktiviteter** till pipelinedesignytan. Du kan också söka efter aktiviteter i verktygslådan **Aktiviteter**. Ange **CopyFromBlobToBlob** som **Namn**.

   ![Allmänna inställningar för kopieringsaktivitet](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
1. Växla till fliken **Källa** i inställningarna för kopieringsaktiviteten och välj **InputDataset** som **Källdatauppsättning**.

1. Växla till fliken **Mottagare** i inställningarna för kopieringsaktiviteten och välj **OutputDataset** som **Datauppsättning för mottagare**.

1. Verifiera pipelineinställningarna genom att klicka på **Verifiera** i verktygsfältet för pipelinen. Bekräfta att pipelinen har verifierats. Du stänger utdata från verifieringen genom att välja **>>** (högerpil). 

## <a name="debug-the-pipeline"></a>Felsöka pipeline
I det här steget felsöker du pipelinen innan du distribuerar den till Data Factory. 

1. Klicka på **Felsök** i Pipeline-verktygsfältet över arbetsytan för att starta en testkörning. 
    
1. Bekräfta att du ser status för pipelinekörningen på fliken **Utdata** i pipelineinställningarna längst ner. 

1. Bekräfta att du ser en utdatafil i **outputfolder** för containern **adftutorial**. Om utdatamappen inte finns skapas den automatiskt av Data Factory-tjänsten. 

## <a name="trigger-the-pipeline-manually"></a>Utlös pipelinen manuellt
I den här proceduren distribuerar du entiteter (länkade tjänster, datauppsättningar, pipeliner) till Azure Data Factory. Sedan utlöser du en pipelinekörning manuellt. 

1. Innan du utlöser en pipeline måste du publicera entiteter i Data Factory. Välj **Publicera alla** i rutan längs upp för att publicera. 

   ![Knappen Publicera](./media/quickstart-create-data-factory-portal/publish-button.png)
1. Om du vill utlösa pipelinen manuellt väljer du **Utlös** i verktygsfältet och sedan **Trigger Now** (Utlös nu). 

## <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Växla till fliken **Övervaka** till vänster. Du kan uppdatera listan med knappen **Uppdatera**.

   ![Flik för att övervaka pipelinekörningar med knapp för att uppdatera](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Välj länken **View Activity Runs** (Visa aktivitetskörningar) under **Åtgärder**. Du kan se status för kopieringsaktiviteten på den här sidan. 

   ![Pipeline-aktivitetskörningar](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
1. Om du vill se mer information om kopieringsåtgärden väljer du länken **Detaljer** (glasögonbilden) i kolumnen **Actions** (Åtgärder). Mer information om egenskaperna finns i [Copy Activity overview](copy-activity-overview.md) (Översikt över kopieringsaktivitet). 

   ![Detaljerad information om kopieringsåtgärden](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Bekräfta att du ser en ny fil i **utdatamappen**. 
1. Du kan gå tillbaka till vyn med **pipelinekörningar** från vyn med **aktivitetskörningar** genom att välja länken **Pipeliner**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Utlös pipelinen enligt ett schema
Den här proceduren är valfri i den här självstudien. Du kan skapa en *schemautlösare* för att schemalägga pipelinen så att den körs regelbundet (varje timme, varje dag och så vidare). I den här proceduren skapar du en utlösare som ska köras varje minut tills det slutdatum och den sluttid du anger. 

1. Växla till fliken **Författare**. 

1. Gå till din pipeline, välj **Utlösare** i pipeline-verktygsfältet och välj **Ny/redigera**. 

1. På sidan **Add Triggers** (Lägg till utlösare) väljer du **Choose trigger** (Välj utlösare) och sedan **Ny**. 

1. På sidan **Ny utlösare** går du till fältet **Slut**, väljer **På datumet**, anger en sluttid några minuter senare och väljer sedan **Använd**. 

   Den tillkommer en kostnad för varje pipelinekörning, så ange sluttiden bara några minuter efter starttiden. Kontrollera att det är samma dag. Men se till att det finns tillräckligt med tid att köra pipelinen mellan publiceringstiden och sluttiden. Utlösaren träder endast i kraft när du har publicerat lösningen till Data Factory, och inte när du sparar utlösaren i användargränssnittet. 

   ![Inställningar för utlösare](./media/quickstart-create-data-factory-portal/trigger-settings.png)
1. På den **Ny utlösare** markerar du kryssrutan **Aktiverad** och väljer sedan **Nästa**. 

   ![Kryssrutan ”Aktivera” och knappen ”Nästa”](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Läs varningsmeddelandet och välj **Slutför**.

   ![Varningsmeddelande och knappen ”Slutför”](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
1. Välj **Publicera alla** för att publicera ändringarna till Data Factory. 

1. Växla till fliken **Övervaka** till vänster. Om du vill uppdatera listan väljer du **Uppdatera**. Du ser att pipelinen körs varje minut från publiceringstiden till sluttiden. 

   Observera värdena i kolumnen **Aktiverad av**. Den manuella körningen av utlösaren var från steget (**Trigger Now**) (Utlös nu) du gjorde tidigare. 

   ![Lista med utlösta körningar](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
1. Växla till vyn **Trigger Runs** (Utlösarkörningar). 

   ![Växla till vyn ”Trigger Runs” (Utlösarkörningar)](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
1. Bekräfta att en utdatafil har skapats för varje pipelinekörning fram till det angivet slutdatum och angiven sluttid i **utdatamappen**. 

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-portal.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
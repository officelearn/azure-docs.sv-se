---
title: "Skapa en Azure-datafabrik med gränssnittet i Azure Data Factory | Microsoft Docs"
description: "I den här självstudien lär du dig att skapa en datafabrik med en pipeline som kopierar data från en mapp till en annan i Azure Blob Storage."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: ebce4e0d137d2e56cc914efad357593af7abb255
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-data-factory-using-the-azure-data-factory-ui"></a>Skapa en datafabrik med hjälp av gränssnittet i Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 – förhandsversion](quickstart-create-data-factory-portal.md)

Den här snabbstarten beskriver hur du använder Azure Data Factory-användargränssnittet till att skapa och övervaka en datafabrik. Den pipeline du skapar i den här datafabriken **kopierar** data från en mapp till en annan mapp i Azure Blob Storage. Om du vill se en självstudie som visar hur du **omvandlar** data med Azure Data Factory går du till [Tutorial: Transform data using Spark](tutorial-transform-data-spark-portal.md) (Självstudie: Omvandla data med Spark). 


> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](data-factory-introduction.md). 
>
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory, som är allmänt tillgänglig, går du igenom [självstudien om Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Om du tittar på den här videon får du hjälp med att förstå Data Factory-användargränssnittet: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Navigera till [Azure-portalen](https://portal.azure.com). 
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sidan Ny datafabrik](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
     ![Namnet är inte tillgängligt – fel](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
    Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. Endast platser som stöds av Data Factory visas i listrutan. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas på andra platser.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
    ![Datafabrikens startsida](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. Klicka på rutan **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik. 
11. På sidan kom igång växlar du till fliken **Redigera** på den vänstra panelen som på följande bild: 

    ![Sidan Kom igång](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
I det här steget skapar du en länkad tjänst för att länka ditt Azure Storage-konto till datafabriken. Den länkade tjänsten har anslutningsinformationen som Data Factory-tjänsten använder vid körning för att ansluta till den.

2. Klicka på **Anslutningar** och sedan på knappen **New** (Nytt) i verktygsfältet. 

    ![Ny anslutning](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
3. På sidan **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**. 

    ![Välj Azure Storage](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
4. Utför följande steg på sidan **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureStorageLinkedService** som **namn**.
    2. Välj namnet på ditt Azure-lagringskonto som **lagringskontonamn**.
    3. Klicka på **Testanslutning** för att bekräfta att Data Factory-tjänsten kan ansluta till lagringskontot. 
    4. Klicka på **Spara** för att spara den länkade tjänsten. 

        ![Inställningar för länkad Azure Storage-tjänst](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
5. Bekräfta att du kan se **AzureStorageLinkedService** i listan över länkade tjänster. 

    ![Länkad Azure-lagringstjänst i listan](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du två datauppsättningar: **InputDataset** och **OutputDataset**. Dessa datauppsättningar är av typen **AzureBlob**. De refererar till den **Azure Storage-länkade tjänst** som du skapade i föregående steg. 

Datauppsättningen för indata representerar källdata i indatamappen. I datauppsättningen för indata anger du blobbehållaren (**adftutorial**), mappen (**input**) och filen (**emp.txt**) som innehåller källdata. 

Datauppsättningen för utdata representerar de data som kopieras till målet. I datauppsättningen för utdata anger du blobbehållaren (**adftutorial**), mappen (**output**) och filen som data kopieras till. Varje pipelinekörning har ett unikt ID som är kopplat till den, som kan nås med hjälp av systemvariabeln **RunId**. Namnet på utdatafilen utvärderas dynamiskt baserat på pipelinens körnings-ID.   

I den länkade tjänstinställningen angav du Azure-lagringskontot som innehåller källdata. I inställningarna för källdatauppsättningen anger du exakt var källdata finns (blobbehållare, mapp och fil). I inställningarna för mottagaruppsättningen anger du var du vill kopiera data (blobbehållare, mapp och fil). 
 
1. Klicka på knappen **+ (plus)** och välj **Datauppsättning**.

    ![Menyn Ny datauppsättning](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Slutför**. 

    ![Välj Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. I datauppsättningens **egenskapsfönster** anger du **InputDataset** som **namn**. 

    ![Allmänna inställningar för datauppsättning](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. Välj fliken **Anslutning** och gör följande: 

    1. Välj **AzureStorageLinkedService** för länkad tjänst. 
    2. Klicka på **Bläddra** för **Filsökväg**. 
        ![Bläddra efter indatafilen](./media/quickstart-create-data-factory-portal/file-path-browse-button.png)
    3. I fönstret **Choose a file or folder** (Välj en fil eller mapp) navigerar du till mappen **input** i behållaren **adftutorial** och väljer filen **emp.txt**. Klicka sedan på **Slutför**.

        ![Bläddra efter indatafilen](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    4. (valfritt) Klicka på **Förhandsgranska data** om du vill förhandsgranska data i emp.txt-filen.     
5. Upprepa stegen för att skapa datauppsättningen för utdata.  

    1. Klicka på knappen **+ (plus)** i den vänstra rutan och välj **Datauppsättning**.
    2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Slutför**.
    3. Ange**OutputDataset** som namn.
    4. Ange **adftutorial/output** för mappen. Kopieringsaktiviteten skapar utdatamappen om den inte finns. 
    5. Ange `@CONCAT(pipeline().RunId, '.txt')` för filnamnet. Varje gång du kör en pipeline har pipelinekörningen ett unikt ID kopplat till sig. Uttrycket sammanfogar körnings-ID:t för pipelinen med **.txt** för att utvärdera utdatafilnamnet. En lista över systemvariabler och uttryck som stöds finns i [Systemvariabler](control-flow-system-variables.md) och [Uttrycksspråk](control-flow-expression-language-functions.md).

        ![Inställningar för utdatauppsättningen](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>Skapa en pipeline 
I det här steget ska du skapa och verifiera en pipeline med en **kopieringsaktivitet** som använder uppsättningar för indata och utdata. Kopieringsaktiviteten kopierar data från filen som anges i inställningarna för datauppsättningen för indata till inställningarna för datauppsättningen för utdata. Om datauppsättningen för indata endast anger en mapp (inte filnamnet) kopierar kopieringsaktiviteten alla filer i källmappen till målet. 

1. Klicka på knappen **+ (plus)** och välj **Pipeline**. 

    ![Meny för ny pipeline](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. Ange **CopyPipeline** som **namn** i fönstret **Egenskaper**. 

    ![Allmänna inställningar för pipeline](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. I verktygslådan **Aktiviteter** visar du **Dataflöde** och drar och släpper aktiviteten **Kopiera** från verktygslådan **Aktiviteter** till pipelinedesignerytan. Du kan också söka efter aktiviteter i verktygslådan **Aktiviteter**. Ange **CopyFromBlobToBlob** som **namn**.

    ![Allmänna inställningar för kopieringsaktivitet](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. Växla till fliken **Källa** i inställningarna för kopieringsaktiviteten och välj **InputDataset** som **källdatauppsättning**.

    ![Källinställningar för kopieringsaktivitet](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. Växla till fliken **Mottagare** i inställningarna för kopieringsaktiviteten och välj **OutputDataset** som **datauppsättning för mottagare**.

    ![Inställningar för mottagare för kopieringsaktivitet](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. Verifiera pipelineinställningarna genom att klicka på **Verifiera**. Bekräfta att pipelinen har verifierats. Klicka på **högerpilen** (>>) för att stänga verifieringsutdata. 

    ![Verifiera pipeline](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>Testkör pipelinen
I det här steget måste du tstköra pipelinen innan du distribuerar den till Data Factory. 

1. I pipelinens verktygsfält klickar du på **Test Run** (Testkörning). 
    
    ![Testkörning av pipeline](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. Bekräfta att du ser status för pipelinekörningen på fliken **Utdata** för pipelineinställningarna. 

    ![Utdata för testkörning](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. Bekräfta att du ser en utdatafil i **outputfolder** för behållaren **adftutorial**. Om utdatamappen inte finns skapar Data Factory-tjänsten den automatiskt. 
    
    ![Verifiera utdata](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>Utlös pipelinen manuellt
I det här steget distribuerar du entiteter (länkade tjänster, datauppsättningar, pipeliner) till Azure Data Factory. Sedan utlöser du en pipelinekörning manuellt. Du kan också publicera entiteter i din egen VSTS GIT-lagringsplats. Det förklaras i [en annan självstudie](tutorial-copy-data-portal.md?#configure-code-repository).

1. Innan du utlöser en pipeline måste du publicera entiteter till Data Factory. Klicka på **Publicera** i rutan till vänster för att publicera. 

    ![Knappen Publicera](./media/quickstart-create-data-factory-portal/publish-button.png)
2. För att utlösa pipelinen manuellt klickar du på **Utlösa** i verktygsfältet och väljer **Trigger Now** (Utlös nu). 
    
    ![Utlös nu](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Växla till fliken **Övervaka** till vänster. Du kan uppdatera listan med knappen **Uppdatera**.

    ![Övervaka pipeline](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. Klicka på länken **View Activity Runs** (Visa aktivitetskörningar) under **Åtgärder**. Du kan se status för kopieringsaktivitetskörningen på den här sidan. 

    ![Pipeline-aktivitetskörningar](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. Om du vill se mer information om kopieringsåtgärden klickar du på länken **Details** (glasögonbilden) i kolumnen **Actions** (Åtgärder). Mer information om egenskaperna finns i [Copy Activity overview](copy-activity-overview.md) (Översikt över kopieringsaktivitet). 

    ![Detaljerad information om kopieringsåtgärden](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. Bekräfta att du ser en ny fil i **utdatamappen**. 
5. Du kan gå tillbaka till **vyn över pipelinekörningar** från vyn med **aktivitetskörningar** och klicka på länken **Pipeliner**. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Utlös pipelinen enligt ett schema
Det här steget är valfritt i den här självstudien. Du kan skapa en **schemautlösare** för att schemalägga pipelinen för så att den körs regelbundet (varje timme, varje dag osv.). I det här steget skapar du en utlösare som ska köras varje minut tills datumet/tiden som du har angivit som sluttid. 

1. Växla till fliken **Redigera**. 

    ![Växla till fliken Redigera](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. Klicka på **Utlösare** på menyn och klicka på **New/Edit** (Nytt/Redigera). 

    ![Menyn Ny utlösare](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. På sidan **Add Triggers** (Lägg till utlösare) klickar du på **Choose trigger...** (Välj utlösare...) och klickar på **New** (Nytt). 

    ![Lägg till utlösare – ny utlösare](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. På sidan **Ny utlösare** går du till fältet **End** (Slut), väljer **On Date** (Datum), anger sluttiden några minuter efter den aktuella tiden och klickar på **Apply** (Använd). Den finns en associerad kostnad till varje pipelinekörning, så ange sluttiden bara några minuter efter starttiden. Kontrollera att det är samma dag. Men se till att det finns tillräckligt med tid att köra pipelinen mellan publiceringstiden och sluttiden. Utlösaren träder endast i kraft när du har publicerat lösningen till Data Factory, och inte när du sparar utlösaren i användargränssnittet. 

    ![Inställningar för utlösare](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. Kontrollera alternativet **Aktiverad** på sidan **Ny utlösare** och klicka på **Nästa** 

    ![Inställningar för utlösare – knappen Nästa](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. På sidan **Ny utlösare** läser du varningsmeddelandet och klickar på **Slutför**.

    ![Inställningar för utlösare – knappen Slutför](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. Klicka på **Publicera** för att publicera Data Factory-ändringar. 

    ![Knappen Publicera](./media/quickstart-create-data-factory-portal/publish-2.png)
8. Växla till fliken **Övervaka** till vänster. Om du vill uppdatera listan klickar du på **Uppdatera**. Du ser pipelinekörningarna varje minut från publiceringstiden till sluttiden. Observera värdena i kolumnen **Aktiverad av**. Den manuella utlösarkörningen var från det föregående steget (**Trigger Now**) (Utlös nu). 

    ![Övervaka utlösta körningar](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. Klicka på nedåtpilen bredvid **Pipeline Runs** (Pipelinekörningar) för att växla till vyn **Trigger Runs** (Utlösarkörningar). 

    ![Övervaka utlösarkörningar](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. Bekräfta att en **utdatafil** har skapats för varje pipelinekörning fram till det angivna slutdatumtiden i **utdatamappen**. 

## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-portal.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
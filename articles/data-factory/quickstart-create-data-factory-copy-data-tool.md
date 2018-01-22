---
title: Kopiera data med Azure-verktyget Kopiera data | Microsoft Docs
description: "Skapa en Azure-datafabrik och kopiera sedan data från en mapp i en Azure Blob Storage till en annan mapp med hjälp av verktyget Kopiera data."
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
ms.openlocfilehash: c17e738e3db18503f7cdda24a5f01ceb78e4e6a3
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="use-copy-data-tool-to-copy-data"></a>Kopiera data med verktyget Kopiera data 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Version 2 – förhandsversion](quickstart-create-data-factory-copy-data-tool.md)

I den här snabbstarten använder du Azure-portalen för att skapa en datafabrik. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från en mapp i en Azure Blob Storage till en annan mapp. 

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](data-factory-introduction.md). 
>
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Läs [get started with Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (kom igång med Data Factory version 1) om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig.


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sida för ny datafabrik](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Försök sedan att skapa datafabriken på nytt. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
     ![Namnet är inte tillgängligt – fel](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
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

    ![panelen distribuerar datafabrik](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
   ![Datafabrikens startsida](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)
10. Klicka på rutan **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik. 

## <a name="launch-copy-data-tool"></a>Starta verktyget Kopiera data

1. Klicka på panelen **Kopiera data** för att starta verktyget Kopiera data. 

   ![Panel för verktyget Kopiera data](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. Klicka på **Nästa** på sidan **Egenskaper** för verktyget Kopiera data. Du kan ange ett namn och en beskrivning för pipelinen på den här sidan. 

    ![Verktyget Kopiera data – sidan Egenskaper](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. Välj **Azure Blob Storage** på sidan **Källdatalager** och klicka på **Nästa**.

    ![Sidan Källdatalager](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. Välj ditt **lagringskontonamn** i listrutan på sidan för att **ange Azure Blob Storage-konto** och klicka på Nästa. 

    ![Ange Blob Storage-konto](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. Gör följande på sidan **Välj indatafil eller mapp**:

    1. Navigera till mappen **adftutorial/input**. 
    2. Välj filen **emp.txt**.
    3. Klicka på **Välj**. Du kan hoppa över det här steget genom att dubbelklicka på **emp.txt**. 
    4. Klicka på **Nästa**. 

    ![Välj indatafil eller mapp](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. Observera att verktyget automatiskt identifierar kolumn- och radavgränsare på sidan för **filformatinställningar** och klicka på **Nästa**. Du kan också förhandsgranska data och visa schemat för indata på den här sidan. 

    ![Sidan för filformatinställningar](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. Välj **Azure Blob Storage** på sidan **Måldatalager** och klicka på **Nästa**. 

    ![Sidan Måldatalager](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. Välj ditt Azure Blob Storage-konto på sidan för att **ange Azure Blob Storage-konto** och klicka på **Nästa**. 

    ![Sida för att ange datalager för mottagare](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. Gör följande på sidan **Välj utdatafil eller mapp**: 

    1. Ange **adftutorial/output** som **mappsökväg**.
    2. Ange **emp.txt** som **filnamn**. 
    3. Klicka på **Nästa**. 

    ![Välj utdatafil eller mapp](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. Klicka på **Nästa** på sidan för **filformatinställningar**. 

    ![Sida för filformatinställningar](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. Klicka på **Nästa** på sidan **Inställningar**. 

    ![Sida för avancerade inställningar](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Granska alla inställningar på sidan **Sammanfattning** och klicka på Nästa. 

    ![Sammanfattningssida](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. Klicka på **Övervaka** på sidan **Deployment complete** (Distribution klar) för att övervaka pipelinen du har skapat. 

    ![Distributionssida](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. Programmet växlar till fliken **Övervaka**. På den här sidan visas status för pipelinen. Om du vill uppdatera listan klickar du på **Uppdatera**. 
    
    ![Sida för att övervaka pipelinekörningar](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Klicka på länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen Åtgärder. Pipelinen har endast en aktivitet av typen **Kopiera**. 

    ![Sida för aktivitetskörningar](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. Om du vill se mer information om kopieringsåtgärden klickar du på länken för **detaljer** (glasögonbilden) i kolumnen **Actions** (Åtgärder). Mer information om egenskaperna finns i [Copy Activity overview](copy-activity-overview.md) (Översikt över kopieringsaktivitet). 

    ![Detaljerad information om kopieringsåtgärden](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Verifiera att filen **emp.txt** har skapats i mappen **output** för behållaren **adftutorial**. Om utdatamappen inte finns skapas den automatiskt av Data Factory-tjänsten. 
18. Välj fliken **Redigera** så att du kan redigera länkade tjänster, datauppsättningar och pipeliner. Mer information om hur du redigerar dessa i användargränssnittet för Data Factory finns i [Skapa en datafabrik med hjälp av Azure-portalen](quickstart-create-data-factory-portal.md).

    ![Fliken Redigera](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>Nästa steg
Pipeline i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-portal.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
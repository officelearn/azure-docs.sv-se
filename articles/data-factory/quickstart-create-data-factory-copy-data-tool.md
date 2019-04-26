---
title: Kopiera data med Azure-verktyget Kopiera data | Microsoft Docs
description: Skapa en Azure-datafabrik och kopiera sedan data från en plats i Azure Blob Storage till en annan plats med hjälp av verktyget för att kopiera data.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: yexu
ms.openlocfilehash: a77041b063f043c065df9a2955e9d07b5692f592
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60314569"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Snabbstart: Kopiera data med verktyget Kopiera data

> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](quickstart-create-data-factory-copy-data-tool.md)

I den här snabbstarten använder du Azure-portalen för att skapa en datafabrik. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från en mapp i en Azure Blob Storage till en annan mapp. 

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](data-factory-introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Välj **Nytt** på den vänstra menyn, välj **Data och analys** och välj sedan **Data Factory**. 
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
1. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **Namn**. 
      
   ![Sidan Ny datafabrik](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
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
   
   ![Startsidan för datafabriken med panelen Författare och övervakare](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Starta verktyget Kopiera data

1. Sidan **Nu sätter vi igång** visas. Välj panelen **Kopiera data** för att starta verktyget kopieringsdata. 

   ![Panelen Kopiera data](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)

1. På sidan **Egenskaper** sida i verktyget Kopiera data kan du ange ett namn för din pipeline och dess beskrivning och sedan välja **Nästa**. 

   ![Sidan Egenskaper](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Gör följande på sidan **Källdatalager**:

    a. Klicka på **+ Skapa ny anslutning** för att lägga till en anslutning.

    ![Sidan om källdatalager](./media/quickstart-create-data-factory-copy-data-tool/new-source-linked-service.png)

    b. Välj **Azure Blob Storage** från galleriet och välj sedan **Nästa**.

    ![Välj blobblagring från galleriet](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. På sidan där du **anger Azure Blob Storage-konto** väljer du ditt lagringskonto i listan med **lagringskontonamn**. Välj sedan **Avsluta**. 

   ![Konfigurera Azure Blob Storage-konto](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

   d. Välj den nyligen skapade länkade tjänsten som källa och klicka sedan på **Nästa**.

   ![Välj källa för den länkade tjänsten](./media/quickstart-create-data-factory-copy-data-tool/select-source-linked-service.png)


1. Gör följande på sidan för att **välja indatafil eller -mapp**:

   a. Klicka på **Bläddra** för att gå till mappen **adftutorial/input**, välj filen **emp.txt** och klicka på **Välj**. 

   ![Sidan för att välja indatafil eller -mapp](./media/quickstart-create-data-factory-copy-data-tool/configure-source-path.png)

   d. Markera alternativet **binär kopia** för att kopiera filen i befintligt skick och välj **nästa**. 

   ![Sidan för att välja indatafil eller -mapp](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Sidan **Destination data store** (Måldatalager) visas. Välj den länkade **Azure Blob Storage**-tjänsten som du skapade och sedan **Nästa**. 

   ![Sidan om måldatalager](./media/quickstart-create-data-factory-copy-data-tool/select-sink-linked-service.png)

1. På sidan **Välj utdatafil eller mapp** anger du **adftutorial/fromonprem** och klickar på **Nästa**. 

   ![Sidan för att välja utdatafil eller -mapp](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. På sidan **Inställningar** väljer du **Nästa** för att använda standardkonfigurationerna. 

1. Granska alla inställningar på sidan **Summary** (Sammanfattning) och klicka på **Nästa**. 

    ![Sidan med sammanfattning](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)

1. Sidan om att **distributionen har slutförts** visas. Välj **Övervaka** om du vill övervaka den pipeline du har skapat. 

    ![Sidan om att distributionen har slutförts](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Programmet växlar till fliken **Övervaka**. På den här fliken visas status för pipelinen. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). 
    
    ![Övervaka pipelinekörning](./media/quickstart-create-data-factory-copy-data-tool/pipeline-monitoring.png)

1. Välj länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Actions** (Åtgärder). Pipelinen har endast en aktivitet av typen **Kopiera**. 

    ![Övervaka aktivitetskörningar](./media/quickstart-create-data-factory-copy-data-tool/activity-monitoring.png)
    
1. Om du vill se mer information om kopieringsåtgärden väljer du länken **Detaljer** (glasögonbilden) i kolumnen **Actions** (Åtgärder). Mer information om egenskaperna finns i [Copy Activity overview](copy-activity-overview.md) (Översikt över kopieringsaktivitet).

    ![Detaljerad information om kopieringsåtgärden](./media/quickstart-create-data-factory-copy-data-tool/activity-execution-details.png)

1. Verifiera att filen **emp.txt** har skapats i mappen **output** för containern **adftutorial**. Om utdatamappen inte finns skapas den automatiskt av Data Factory-tjänsten. 

1. Växla till fliken **Fönster** över fliken **Övervakare** på den vänstra panelen så att du kan redigera länkade tjänster, datauppsättningar och piplines. Mer information om hur du redigerar dessa i användargränssnittet för Data Factory finns i [Skapa en datafabrik med hjälp av Azure-portalen](quickstart-create-data-factory-portal.md).

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-portal.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
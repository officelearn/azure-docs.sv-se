---
title: Med Azure Data Factory du inkrementellt kopierar nya och ändrade filer endast baserat på LastModifiedDate | Microsoft Docs
description: Skapa en Azure-datafabrik och sedan använda verktyget kopieringsdata inkrementellt läsa in nya filer endast baserat på LastModifiedDate.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: d79b44d0123d64d6280939767e5df7b5f64a5fcb
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445960"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Kopiera nya och ändrade filer baserat på LastModifiedDate med hjälp av verktyget kopieringsdata stegvis

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du verktyget kopieringsdata för att skapa en pipeline som kopierar stegvis nya och ändrade filer endast baserat på deras ”LastModifiedDate” från Azure Blob storage till Azure Blob storage. 

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure-lagringskonto**: Använda Blob storage som den _källa_ och _mottagare_ datalager. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Skapa två behållare i Blob storage

Förbered din Blob-lagring för den här självstudien genom att utföra dessa steg.

1. Skapa en behållare med namnet **källa**. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

2. Skapa en behållare med namnet **mål**. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På menyn till vänster väljer **skapa en resurs** > **Data och analys** > **Data Factory**: 
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**. 
      
     ![Ny datafabrik](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:
   
   ![Felmeddelande för ny datafabrik](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. Gör något av följande för **Resursgrupp**:
     
    a. Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

    b. Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).

5. För **version** väljer du **V2**.
6. Under **plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL Database) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas på andra platser och i andra regioner.
7. Välj **fäst till instrumentpanelen**. 
8. Välj **Skapa**.
9. På instrumentpanelen visar panelen **Distribuera Data Factory** processens status.

    ![Panelen Distribuera datafabrik](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. När den har skapats visas startsidan för **Data Factory**.
   
    ![Datafabrikens startsida](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory i en separat flik. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. På den **nu sätter vi igång** väljer den **kopieringsdata** rubrik för att starta verktyget kopieringsdata. 

   ![Panel för verktyget Kopiera data](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. På den **egenskaper** utför följande steg:

    a. Under **aktivitetsnamn**, ange **DeltaCopyFromBlobPipeline**.

    b. Under **uppgift takt eller schemalägga**väljer **körs regelbundet enligt schema**.

    c. Under **utlöser typ**väljer **rullande fönster**.
    
    d. Under **upprepning**, ange **15 minuters intervall**. 
    
    e. Välj **Nästa**. 
    
    Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn. 

    ![Sidan Egenskaper](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Gör följande på sidan **Källdatalager**:

    a. Klicka på **+ Skapa ny anslutning**, för att lägga till en anslutning.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Välj **Azure Blob Storage** från galleriet och klicka sedan på **Fortsätt**.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. På den **ny länkad tjänst** väljer du ditt lagringskonto från den **lagringskontonamn** listan och klicka sedan på **Slutför**.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Den nya länkade tjänsten och sedan klicka på **nästa**. 
    
   ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Gör följande på sidan **Välj indatafil eller mapp**:
    
    a. Bläddra och välj den **källa** mappen, klicka sedan på **Välj**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Under **fil som läser in beteendet**väljer **stegvis inläsning: LastModifiedDate**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Kontrollera **binär kopia** och klicka på **nästa**.
    
     ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. På den **måldatalager** väljer den **AzureBlobStorage** som är samma lagringsutrymme kontot som datakällagring och klicka sedan på **nästa**.

    ![Sidan Måldatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. På den **Välj utdatafil eller mapp** gör du följande steg:
    
    a. Bläddra och välj den **mål** mappen, klicka sedan på **Välj**.
    
    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Klicka på **Nästa**.
    
     ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**. 

    ![Sidan Inställningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Granska inställningarna på sidan **Sammanfattning** och klicka på **Nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).

    ![Distributionssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Observera att fliken **Övervaka** till vänster väljs automatiskt. I kolumnen **Åtgärder** finns länkar som visar information om aktivitetskörningen och för att köra pipelinen igen. Välj **uppdatera** att uppdatera listan och välj den **visa Aktivitetskörningar** länken i den **åtgärder** kolumn. 

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill se mer information om kopieringsåtgärden väljer du länken **Information** (glasögonikonen) i kolumnen **Åtgärder**. 

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Med hänsyn till att det finns ingen fil i **källa** behållare i blob storage-kontot, så inte visas alla filer som har kopierats till **mål** behållare i blob storage-kontot.
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Skapa en tom textfil och ge den namnet som file1.txt. Överför file1.txt-filen till den **källa** behållare i ditt storage-konto. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Gå tillbaka till den **Pipelinekörningar** väljer **alla Pipelines körningar**, medan samma pipeline utlöses igen automatiskt.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Välj **visa aktivitet köras** för den andra kör pipeline-om du ser det kommer och gör samma sak för att få mer information.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Du ser en fil (file1.txt) har kopierats från den **källa** behållaren till den **mål** behållare på ditt lagringskonto.
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Skapa en annan tom textfil och ge den namnet som file2.txt. Överför file2.txt-filen till den **källa** behållare i ditt storage-konto. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).  
    
16. Gör samma sak som steg 13 och 14 och du ser bara den nya filen (file2.txt) har kopierats från den **källa** behållaren till den **mål** behållare på ditt lagringskonto i nästa pipelinekörning.  
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Du kan också kontrollera samma med hjälp av Azure Storage Explorer (https://storageexplorer.com/) söker igenom filer.
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Transformera data med Apache Spark i molnet](tutorial-transform-data-spark-portal.md)
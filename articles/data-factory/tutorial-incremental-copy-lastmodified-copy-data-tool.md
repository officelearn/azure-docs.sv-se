---
title: Kopiera nya och ändrade filer baserat på LastModifiedDate med hjälp av verktyget kopieringsdata stegvis | Microsoft Docs
description: Skapa en Azure-datafabrik och sedan använda verktyget kopieringsdata inkrementellt läsa in nya filer baserat på LastModifiedDate.
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
ms.openlocfilehash: 8308190e0e68365343fb50ca33f9bea75c3e4e66
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544491"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Kopiera nya och ändrade filer baserat på LastModifiedDate med hjälp av verktyget kopieringsdata stegvis

I den här självstudien ska du använda Azure-portalen för att skapa en datafabrik. Sedan använder du verktyget kopieringsdata för att skapa en pipeline som kopierar stegvis nya och ändrade filer, baserat på deras **LastModifiedDate** från Azure Blob storage till Azure Blob storage.

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får utföra du följande uppgifter:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **Azure-prenumeration**: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure-lagringskonto**: Använda Blob storage som den _källa_ och _mottagare_ datalager. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Skapa två behållare i Blob storage

Förbered din Blob-lagring för den här självstudien genom att utföra dessa steg.

1. Skapa en behållare med namnet **källa**. Du kan använda olika verktyg för att utföra den här uppgiften som [Azure Storage Explorer](https://storageexplorer.com/).

2. Skapa en behållare med namnet **mål**. 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På menyn till vänster väljer **skapa en resurs** > **Data och analys** > **Data Factory**: 
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**. 
      
     ![Ny datafabrik](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:
   
   ![Felmeddelande för ny datafabrik](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
3. Välj Azure **prenumeration** i som du skapar den nya datafabriken. 
4. Gör något av följande för **Resursgrupp**:
     
    * Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

    * Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).

5. Under **version**väljer **V2**.
6. Under **plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalager (till exempel Azure Storage och SQL-databas) och beräkningarna (till exempel Azure HDInsight) som data factory använder kan vara i andra platser och regioner.
7. Välj **fäst till instrumentpanelen**. 
8. Välj **Skapa**.
9. På instrumentpanelen, referera till den **distribuera Data Factory** ikonen för att visa processens status.

    ![Panelen distribuerar Datafabrik](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. När den har skapats visas startsidan för **Data Factory**.
   
    ![Datafabrikens startsida](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Öppna Azure Data Factory-användargränssnittet (UI) på en separat flik genom att välja den **författare och Övervakare** panelen. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. På den **nu sätter vi igång** väljer den **kopieringsdata** rubrik för att öppna verktyget kopieringsdata. 

   ![Panel för verktyget Kopiera data](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. På den **egenskaper** utför följande steg:

    a. Under **aktivitetsnamn**, ange **DeltaCopyFromBlobPipeline**.

    b. Under **uppgift takt** eller **schemalägga**väljer **körs regelbundet enligt schema**.

    c. Under **Utlösartyp**väljer **rullande fönster**.
    
    d. Under **upprepning**, ange **15 minuters intervall**. 
    
    e. Välj **Nästa**. 
    
    Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn. 

    ![Sidan Egenskaper](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Gör följande på sidan **Källdatalager**:

    a. Välj **+ Skapa ny anslutning**, för att lägga till en anslutning.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Välj **Azure Blob Storage** från galleriet och välj sedan **Fortsätt**.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. På den **ny länkad tjänst** väljer du ditt lagringskonto från den **lagringskontonamn** listan och välj sedan **Slutför**.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Välj den nyligen skapade länkade tjänsten och välj sedan **nästa**. 
    
   ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Gör följande på sidan för att **välja indatafil eller -mapp**:
    
    a. Bläddra och välj den **källa** mapp och välj sedan **Välj**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Under **fil som läser in beteendet**väljer **stegvis inläsning: LastModifiedDate**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Kontrollera **binär kopia** och välj **nästa**.
    
     ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. På den **måldatalager** väljer **AzureBlobStorage**. Det här är samma lagringskonto som källa för datalagringen. Välj sedan **Nästa**.

    ![Sidan Måldatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Gör följande på sidan **Choose the output file or folder** (Välj utdatafil eller -mapp):
    
    a. Bläddra och välj den **mål** mapp och välj sedan **Välj**.
    
    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Välj **Nästa**.
    
     ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**. 

    ![Sidan Inställningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. På den **sammanfattning** granskar du inställningarna och välj sedan **nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).

    ![Distributionssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Observera att fliken **Övervaka** till vänster väljs automatiskt. I kolumnen **Åtgärder** finns länkar som visar information om aktivitetskörningen och för att köra pipelinen igen. Välj **uppdatera** att uppdatera listan och välj den **visa Aktivitetskörningar** länken i den **åtgärder** kolumn. 

    ![Uppdatera listan med och välj Visa Aktivitetskörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen, därför visas bara en post. Om du vill se mer information om kopieringsåtgärden väljer du länken **Information** (glasögonikonen) i kolumnen **Åtgärder**. 

    ![Kopieringsaktivitet finns i pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Eftersom det inte finns någon fil i den **källa** behållare i Blob storage-kontot visas inte alla filer som kopieras till den **mål** behållare i Blob storage-kontot.
    
    ![Ingen fil i källbehållare eller Målbehållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Skapa en tom textfil och ge den namnet **file1.txt**. Ladda upp den här filen till den **källa** behållare i ditt storage-konto. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Skapa file1.txt och överför till källbehållare](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Gå tillbaka till den **Pipelinekörningar** väljer **alla Pipelinekörningar**, medan samma pipelinen ska utlösas igen automatiskt.  

    ![Välj alla Pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Välj **visa aktivitet köras** för den andra pipelinekörningen när du ser det. Granska informationen på samma sätt som du gjorde för den första pipelinekörningen.  

    ![Välj Visa aktivitet köras och detaljer för recensionen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Kommer du att detta finns i en fil (file1.txt) har kopierats från den **källa** behållaren till den **mål** behållare för Blob storage-kontot.
    
    ![File1.txt har kopierats från källbehållare till Målbehållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Skapa en annan tom textfil och ge den namnet **file2.txt**. Ladda upp den här filen till den **källa** behållare i Blob storage-kontot.   
    
16. Upprepa steg 13 och 14 för den här andra textfilen. Ser du att endast den nya filen (file2.txt) har kopierats från den **källa** behållaren till den **mål** behållare på ditt lagringskonto i nästa pipelinekörning.  
    
    ![File2.txt har kopierats från källbehållare till Målbehållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Du kan också bekräfta det genom att använda [Azure Storage Explorer](https://storageexplorer.com/) söker igenom filer.
    
    ![Sök igenom filer med Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie och lär dig mer om hur du transformerar data med hjälp av en Apache Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Transformera data i molnet med hjälp av en Apache Spark-kluster](tutorial-transform-data-spark-portal.md)
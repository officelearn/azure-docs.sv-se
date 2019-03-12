---
title: Med Azure Data Factory du inkrementellt kopierar nya filer endast baserat på tid partitionerade filnamn | Microsoft Docs
description: Skapa en Azure-datafabrik och sedan använda verktyget kopieringsdata inkrementellt läsa in nya filer endast baserat på tid partitionerade filnamn.
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
ms.openlocfilehash: df1542d6d20120a9b1e087fadf3743479ecebf07
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533841"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Kopiera nya filer baserat på tid partitionerade filnamn med hjälp av verktyget kopieringsdata stegvis

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du verktyget kopieringsdata för att skapa en pipeline som inkrementellt kopierar nya filer baserat på tid partitionerade filnamn från Azure Blob storage till Azure Blob storage. 

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

1. Skapa en behållare med namnet **källa**.  Skapa en mappsökväg som **2019/02/26/14** i din behållare. Skapa en tom textfil och ger den namnet **file1.txt**. Ladda upp file1.txt till sökvägen till mappen **källa/2019/02/26/14** i ditt lagringskonto.  Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).
    
    ![Ladda upp filer](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Justera mappnamn med UTC-tid.  Om den aktuella UTC-tiden är 14:03:00 den 26 februari 2019 du till exempel skapa sökvägen till mappen som **källa/2019/02/26/14/** av regeln för **källa / {Year} / {Month} / {Day} / {Hour} /**.

2. Skapa en behållare med namnet **mål**. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. I den vänstra menyn väljer du **+ Nytt** > **Data och analys** > **Data Factory**: 
   
   ![Skapa ny datafabrik](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. I fönstret **Ny datafabrik**, under **Namn**, anger du **ADFTutorialDataFactory**. 
      
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
    
    d. Under **upprepning**, ange **1 timme/timmar**. 
    
    e. Välj **Nästa**. 
    
    Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn. 

    ![Sidan Egenskaper](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Gör följande på sidan **Källdatalager**:

    a. Klicka på **+ Skapa ny anslutning**, för att lägga till en anslutning.

    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Välj **Azure Blob Storage** från galleriet och klicka sedan på **Fortsätt**.

    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. På den **ny länkad tjänst** väljer du ditt lagringskonto från den **lagringskontonamn** listan och klicka sedan på **Slutför**.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Den nya länkade tjänsten och sedan klicka på **nästa**. 
    
   ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Gör följande på sidan **Välj indatafil eller mapp**:
    
    a. Bläddra och välj den **källa** behållare, välj sedan **Välj**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. Under **fil som läser in beteendet**väljer **stegvis inläsning: time-partitionerad mappen/filen namn**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Skriva dynamisk mappsökvägen som **källa / {year} / {month} / {day} / {hour} /**, och ändra formatet som följande avsnitt:
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Kontrollera **binär kopia** och klicka på **nästa**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. På den **måldatalager** väljer den **AzureBlobStorage**, vilket är samma lagringsutrymme kontot som datakällagring och klicka sedan på **nästa**.

    ![Sidan Måldatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. På den **Välj utdatafil eller mapp** gör du följande steg:
    
    a. Bläddra och välj den **mål** mappen, klicka sedan på **Välj**.
    
    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Skriva dynamisk mappsökvägen som **källa / {year} / {month} / {day} / {hour} /**, och ändra formatet som följande avsnitt:
    
    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Klicka på **Nästa**.
    
    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**. 

    ![Sidan Inställningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Granska inställningarna på sidan **Sammanfattning** och klicka på **Nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).
    ![Distributionssida](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Observera att fliken **Övervaka** till vänster väljs automatiskt.  Du måste vänta tills den pipeline som körs när den utlöses automatiskt (om efter en timme).  När den körs den **åtgärder** -kolumnen innehåller länkar för att visa information om aktivitetskörningar och köra pipelinen på nytt. Välj **uppdatera** att uppdatera listan och välj den **visa Aktivitetskörningar** länken i den **åtgärder** kolumn. 

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Du kan se källfilen (file1.txt) har kopierats från **källa/2019/02/26/14/** till **mål/2019/02/26/14/** med samma namn.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Du kan också kontrollera samma med hjälp av Azure Storage Explorer (https://storageexplorer.com/) söker igenom filer.
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Skapa en annan tom textfil med det nya namnet som **file2.txt**. Överföra filen file2.txt i sökvägen till mappen **källa/2019/02/26/15** i ditt lagringskonto.   Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).   
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Du kan känna att det krävs en ny sökväg som ska skapas. Justera mappnamn med UTC-tid.  Om den aktuella UTC-tiden är 15:20:00 den 26 februari 2019 du till exempel skapa sökvägen till mappen som **källa/2019/02/26/15/** av regeln för **{Year} / {Month} / {Day} / {Hour} /**.
    
13. Gå tillbaka till den **Pipelinekörningar** väljer **alla Pipelines körningar**, medan samma pipeline utlöses igen automatiskt efter en annan en timme.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Välj **visa aktivitet köras** för den andra pipelinen att köras när det kommer och gör samma sak för att få mer information.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Du kan se källfilen (file2.txt) har kopierats från **källa/2019/02/26/15/** till **mål/2019/02/26/15/** med samma namn.
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Du kan också kontrollera samma med hjälp av Azure Storage Explorer (https://storageexplorer.com/) söker igenom filer i **mål** behållare
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Transformera data med Apache Spark i molnet](tutorial-transform-data-spark-portal.md)
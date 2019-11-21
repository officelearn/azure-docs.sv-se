---
title: Incrementally copy new files based on time partitioned file name
description: Create an Azure data factory and then use the Copy Data tool to incrementally load new files only based on time partitioned file name.
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
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: 746b5cbcc58f6c722623446227417e6c94dd0a80
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217453"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Incrementally copy new files based on time partitioned file name by using the Copy Data tool

I den här självstudien skapar du en datafabrik i Azure Portal. Then, you use the Copy Data tool to create a pipeline that incrementally copies new files based on time partitioned file name from Azure Blob storage to Azure Blob storage. 

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure storage account**: Use Blob storage as the _source_  and _sink_ data store. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Create two containers in Blob storage

Prepare your Blob storage for the tutorial by performing these steps.

1. Create a container named **source**.  Create a folder path as **2019/02/26/14** in your container. Create an empty text file, and name it as **file1.txt**. Upload the file1.txt to the folder path **source/2019/02/26/14** in your storage account.  Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).
    
    ![upload files](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Please adjust the folder name with your UTC time.  For example, if the current UTC time is 2:03 PM on Feb 26th, 2019, you can create the folder path as **source/2019/02/26/14/** by the rule of **source/{Year}/{Month}/{Day}/{Hour}/** .

2. Create a container named **destination**. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. On the left menu, select **Create a resource** > **Data + Analytics** > **Data Factory**: 
   
   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

2. I fönstret **Ny datafabrik**, under **Namn**, anger du **ADFTutorialDataFactory**. 
    
    Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:
   
   ![Felmeddelande för ny datafabrik](./media/doc-common-process/name-not-available-error.png)
   
   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_ **ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
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
   
    ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)
11. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory i en separat flik. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. On the **Let's get started** page, select the **Copy Data** title to launch the Copy Data tool. 

   ![Panel för verktyget Kopiera data](./media/doc-common-process/get-started-page.png)
   
2. On the **Properties** page, take the following steps:

    a. Under **Task name**, enter **DeltaCopyFromBlobPipeline**.

    b. Under **Task cadence or Task schedule**, select **Run regularly on schedule**.

    c. Under **Trigger type**, select **Tumbling Window**.
    
    d. Under **Recurrence**, enter **1 Hour(s)** . 
    
    e. Välj **Nästa**. 
    
    Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn. 

    ![Sidan Egenskaper](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Gör följande på sidan **Källdatalager**:

    a. Click  **+ Create new connection**, to add a connection.

    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Select **Azure Blob Storage** from the gallery, and then click **Continue**.

    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. On the **New Linked Service** page, select your storage account from the **Storage account name** list, and then click **Finish**.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Select the newly created linked service, then click **Next**. 
    
   ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Gör följande på sidan **Välj indatafil eller mapp**:
    
    a. Browse and select the **source** container, then select **Choose**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. Under **File loading behavior**, select **Incremental load: time-partitioned folder/file names**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Write the dynamic folder path as **source/{year}/{month}/{day}/{hour}/** , and change the format as followings:
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Check **Binary copy** and click **Next**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. On the **Destination data store** page, select the **AzureBlobStorage**, which is the same storage account as data source store, and then click **Next**.

    ![Sidan Måldatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. On the **Choose the output file or folder** page, do the following steps:
    
    a. Browse and select the **destination** folder, then click **Choose**.
    
    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Write the dynamic folder path as **source/{year}/{month}/{day}/{hour}/** , and change the format as followings:
    
    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Klicka på **Next**.
    
    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**. 

    ![Sidan Inställningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Granska inställningarna på sidan **Sammanfattning** och klicka på **Nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).
    ![Deployment page](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Observera att fliken **Övervaka** till vänster väljs automatiskt.  You need wait for the pipeline run when it is triggered automatically (about after one hour).  When it runs, the **Actions** column includes links to view activity run details and to rerun the pipeline. Select **Refresh** to refresh the list, and select the **View Activity Runs** link in the **Actions** column. 

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. You can see the source file (file1.txt) has been copied from  **source/2019/02/26/14/**  to **destination/2019/02/26/14/** with the same file name.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    You can also verify the same by using Azure Storage Explorer (https://storageexplorer.com/) to scan the files.
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Create another empty text file with the new name as **file2.txt**. Upload the file2.txt file to the folder path **source/2019/02/26/15** in your storage account.   Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).   
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > You might be aware that a new folder path is required to be created. Please adjust the folder name with your UTC time.  For example, if the current UTC time is 3:20 PM on Feb 26th, 2019, you can create the folder path as **source/2019/02/26/15/** by the rule of **{Year}/{Month}/{Day}/{Hour}/** .
    
13. To go back to the **Pipeline Runs** view, select **All Pipelines Runs**, and wait for the same pipeline being triggered again automatically after another one hour.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Select **View Activity Run** for the second pipeline run when it comes, and do the same to review details.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    You can see the source file (file2.txt) has been copied from  **source/2019/02/26/15/**  to **destination/2019/02/26/15/** with the same file name.
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    You can also verify the same by using Azure Storage Explorer (https://storageexplorer.com/) to scan the files in **destination** container
    
    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Transformera data med Apache Spark i molnet](tutorial-transform-data-spark-portal.md)
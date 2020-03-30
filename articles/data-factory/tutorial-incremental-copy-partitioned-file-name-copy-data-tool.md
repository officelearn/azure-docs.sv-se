---
title: Kopiera nya filer stegvis baserat på tidspartiat filnamn
description: Skapa en Azure-datafabrik och använd sedan verktyget Kopiera data för att stegvis läsa in nya filer endast baserat på tidspartitionerat filnamn.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/17/2020
ms.openlocfilehash: 50e82362f39da2d3f3ecfad2de1ed07b68f8a6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501642"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Kopiera nya filer stegvis baserat på tidspartiat filnamn med hjälp av verktyget Kopiera data

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du verktyget Kopiera data för att skapa en pipeline som stegvis kopierar nya filer baserat på tidsbe partitionerat filnamn från Azure Blob-lagring till Azure Blob-lagring.

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure storage-konto:** Använd Blob-lagring som _källa_ och _sink_ data store. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Skapa två behållare i Blob-lagring

Förbered din Blob-lagring för självstudien genom att utföra dessa steg.

1. Skapa en behållare med namnet **källa**.  Skapa en mappsökväg som **2020/03/17/03** i behållaren. Skapa en tom textfil och namnge den som **file1.txt**. Ladda upp filen1.txt till **mappsökvägen källa/2020/03/17/03** i ditt lagringskonto.  Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

    ![ladda upp filer](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Justera mappnamnet med UTC-tiden.  Om den aktuella UTC-tiden till exempel är 03:38 den 17 mars 2020 kan du skapa mappsökvägen som **källa/2020/03/17/03/** enligt **källregeln/{Year}/{Month}/{Day}/{Hour}/**.

2. Skapa en behållare med namnet **destination**. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **Skapa en resursData** > **+ Analytics** > **Data Factory:**

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

2. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

    Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:

   ![Felmeddelande för ny datafabrik](./media/doc-common-process/name-not-available-error.png)

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
4. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig**och välj en befintlig resursgrupp i listrutan.

    b. Välj **Skapa ny**och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).

5. För **version** väljer du **V2**.
6. Under **plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL Database) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas på andra platser och i andra regioner.
7. Välj **Skapa**.
8. När den har skapats visas startsidan för **Data Factory**.
9. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory i en separat flik.

    ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. På sidan **Låt oss komma igång** väljer du rubriken Kopiera **data** för att starta verktyget Kopiera data.

   ![Panel för verktyget Kopiera data](./media/doc-common-process/get-started-page.png)

2. Gör följande på sidan **Egenskaper:**

    a. Under **Aktivitetsnamn**anger du **DeltaCopyFromBlobPipeline**.

    b. Under **Aktivitetskadens eller Aktivitetsschema**väljer du **Kör regelbundet enligt schemat**.

    c. Under **Utlösartyp**väljer du **Tumlande fönster**.

    d. Under **Återkommande**anger du **1 timme( s)**.

    e. Välj **Nästa**.

    Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn.

    ![Sidan Egenskaper](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Gör följande på sidan **Källdatalager**:

    a. Klicka på **+ Skapa ny anslutning** för att lägga till en anslutning
    
    b. Välj Azure Blob Storage i galleriet och välj sedan Fortsätt.
    
    c. På sidan **Ny länkad tjänst (Azure Blob Storage)** anger du ett namn för den länkade tjänsten. Välj din Azure-prenumeration och välj ditt lagringskonto i listan **För lagringskontonamn.** Testa anslutningen och välj sedan **Skapa**.

    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Markera den nyligen skapade länkade tjänsten på sidan **Källa-datalager** och klicka sedan på **Nästa**.

4. Gör följande på sidan **Välj indatafil eller mapp**:

    a. Bläddra och markera **källbehållaren** och välj sedan **Välj**.

    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. Under **Filinläsning**väljer du **Inkrementell inläsning: tidspartitionerade mapp-/filnamn**.

    c. Skriv sökvägen till den dynamiska mappen som **källa/{year}/{month}/{day}/{hour}/** och ändra formatet enligt följande skärmbild. Markera **Binär kopia** och klicka på **Nästa**.

    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. På sidan **Måldatalager** väljer du **AzureBlobStorage**, som är samma lagringskonto som datakällarkivet, och klickar sedan på **Nästa**.

    ![Sidan Måldatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Gör följande på sidan **Välj utdatafil eller mapp:**

    a. Bläddra och markera **målmappen** och klicka sedan på **Välj**.

    b. Skriv sökvägen till den dynamiska mappen som **mål/{year}/{month}/{day}/{hour}/** och ändra formatet enligt följande:

    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Klicka på **Nästa**.

    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**.

8. Granska inställningarna på sidan **Sammanfattning** och klicka på **Nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).
    ![Distributionssida](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Observera att fliken **Övervaka** till vänster väljs automatiskt.  Du måste vänta på pipeline-körningen när den utlöses automatiskt (ungefär efter en timme). När den körs klickar du på pipelinenamnslänken **DeltaCopyFromBlobPipeline** för att visa information om aktivitetskörning eller köra pipelinen igen. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera).

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Justera kolumnbredden för **käll-** och målkolumnerna (om det behövs) för att visa mer information, du kan se källfilen (file1.txt) har kopierats från *källa/2020/03/17/03/* till *destination/2020/03/17/03/* med samma filnamn. **destination** 

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Du kan också verifiera samma sakhttps://storageexplorer.com/) med hjälp av Azure Storage Explorer ( för att söka igenom filerna.

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Skapa en annan tom textfil med det nya namnet som **file2.txt**. Ladda upp filen file2.txt till **mappsökvägen källa/2020/03/17/04** i ditt lagringskonto. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

    > [!NOTE]
    > Du kanske är medveten om att en ny mappsökväg måste skapas. Justera mappnamnet med UTC-tiden.  Om den aktuella UTC-tiden till exempel är 04:20 den 17 mars 2020 kan du skapa mappsökvägen som **källa/2020/03/17/04/** enligt regeln **{Year}/{Month}/{Day}/{Hour}/**.

13. Om du vill gå tillbaka till vyn **Pipeline runs** väljer du **Alla pipelines körs**och väntar på att samma pipeline ska utlösas igen automatiskt efter ytterligare en timme.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Välj den nya **länken DeltaCopyFromBlobPipeline** för den andra pipelinekörningen när den kommer och gör samma sak för att granska detaljer. Källfilen (file2.txt) har kopierats från **source/2020/03/17/04/** till **destination/2020/03/17/04/** med samma filnamn. Du kan också verifiera samma sakhttps://storageexplorer.com/) med hjälp av Azure Storage Explorer ( för att söka igenom filerna i **målbehållaren.**


## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Transformera data med Apache Spark i molnet](tutorial-transform-data-spark-portal.md)

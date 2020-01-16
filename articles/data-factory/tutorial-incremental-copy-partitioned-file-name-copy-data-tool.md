---
title: Kopiera nya filer stegvis baserat på tidspartitionerat fil namn
description: Skapa en Azure-datafabrik och Använd sedan Kopiera data-verktyget för att stegvis läsa in nya filer baserat på tidspartitionerat fil namn.
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
ms.date: 1/24/2019
ms.openlocfilehash: 38e0028efd7fbda8aa5bee497836ce1161109e03
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982615"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Kopiera nya filer stegvis baserat på partitionerat fil namn med hjälp av Kopiera data-verktyget

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du Kopiera data-verktyget för att skapa en pipeline som stegvis kopierar nya filer baserat på tidspartitionerat fil namn från Azure Blob Storage till Azure Blob Storage.

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**: Använd Blob Storage som data lager för _källa_ och _mottagare_ . Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Skapa två behållare i Blob Storage

Förbered blob-lagringen för självstudien genom att utföra dessa steg.

1. Skapa en behållare med namnet **Source**.  Skapa en mappsökväg som **2019/02/26/14** i din behållare. Skapa en tom textfil och ge den namnet **fil1. txt**. Överför filen Fil1. txt till mappsökvägen **Source/2019/02/26/14** i ditt lagrings konto.  Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

    ![Ladda upp filer](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Justera mappnamnet med din UTC-tid.  Om den aktuella UTC-tiden till exempel är 2:03 PM på Feb 26, 2019, kan du skapa mappsökvägen som **källa/2019/02/26/14/** av regeln för **källa/{year}/{month}/{Day}/{Hour}/** .

2. Skapa en behållare med namnet **mål**. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **skapa en resurs** > **data och analys** > **Data Factory**:

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

2. I fönstret **Ny datafabrik**, under **Namn**, anger du **ADFTutorialDataFactory**.

    Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:

   ![Felmeddelande för ny datafabrik](./media/doc-common-process/name-not-available-error.png)

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_ **ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
4. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

    b. Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).

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

1. På sidan **nu sätter vi igång** väljer du **Kopiera data** titeln för att starta verktyget kopiera data.

   ![Panel för verktyget Kopiera data](./media/doc-common-process/get-started-page.png)

2. Utför följande steg på sidan **Egenskaper** :

    a. Under **uppgifts namn**, anger du **DeltaCopyFromBlobPipeline**.

    b. Under **aktivitets takt eller aktivitets schema**väljer du **kör regelbundet enligt schema**.

    c. Under **utlösnings typ**väljer du **rullande-fönster**.

    d. Under **upprepning**anger du **1 timme (ar)** .

    e. Välj **Nästa**.

    Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn.

    ![Sidan Egenskaper](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Gör följande på sidan **Källdatalager**:

    a. Klicka på **+ Skapa ny anslutning**för att lägga till en anslutning.

    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)

    b. Välj **Azure Blob Storage** från galleriet och klicka sedan på **Fortsätt**.

    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)

    c. På sidan **ny länkad tjänst** väljer du ditt lagrings konto i listan **lagrings konto namn** och klickar sedan på **Slutför**.

    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Välj den nyligen skapade länkade tjänsten och klicka sedan på **Nästa**.

   ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Gör följande på sidan **Välj indatafil eller mapp**:

    a. Bläddra och välj **käll** behållaren och välj sedan **Välj**.

    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. Under **fil inläsnings beteende**väljer du **stegvis inläsning: tidspartitionade mappar/fil namn**.

    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)

    c. Skriv sökvägen till den dynamiska mappen som **källa/{year}/{month}/{Day}/{Hour}/** och ändra formatet enligt följande:

    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)

    d. Kontrol lera **binär kopia** och klicka på **Nästa**.

    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. På sidan **mål data lager** väljer du **AzureBlobStorage**, som är samma lagrings konto som data källans lager och klickar sedan på **Nästa**.

    ![Sidan Måldatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Utför följande steg på sidan **Välj utdatafil eller mapp** :

    a. Bläddra och välj **målmappen** och klicka sedan på **Välj**.

    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)

    b. Skriv sökvägen till den dynamiska mappen som **källa/{year}/{month}/{Day}/{Hour}/** och ändra formatet enligt följande:

    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)

    c. Klicka på **Next**.

    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**.

    ![Sidan Inställningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)
8. Granska inställningarna på sidan **Sammanfattning** och klicka på **Nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).
    ![distributions sidan](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Observera att fliken **Övervaka** till vänster väljs automatiskt.  Du måste vänta på att pipelinen ska köras när den utlöses automatiskt (ungefär en timme).  När den körs innehåller kolumnen **åtgärder** Länkar för att Visa aktivitets körnings information och köra pipelinen på nytt. Välj **Uppdatera** för att uppdatera listan och välj länken **Visa aktivitet kör** i kolumnen **åtgärder** .

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Du kan se käll filen (fil1. txt) har kopierats från **källan/2019/02/26/14/** till **destinationen/2019/02/26/14/** med samma fil namn.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Du kan också kontrol lera att du använder Azure Storage Explorer (https://storageexplorer.com/) för att genomsöka filerna.

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Skapa en annan tom textfil med det nya namnet som **fil2. txt**. Ladda upp filen fil2. txt till mappsökvägen **Source/2019/02/26/15** i ditt lagrings konto.   Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)

    > [!NOTE]
    > Du kan vara medveten om att en ny mappsökväg krävs för att kunna skapas. Justera mappnamnet med din UTC-tid.  Om den aktuella UTC-tiden till exempel är 3:20 PM på Feb 26, 2019, kan du skapa mappsökvägen som **källa/2019/02/26/15/** med regeln **{year}/{month}/{Day}/{Hour}/** .

13. Gå tillbaka till vyn **pipeline-körningar** genom att välja **alla pipelines körs**och vänta tills samma pipeline utlöses igen automatiskt efter en halvtimme.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Välj **Visa aktivitets körning** för den andra pipelinen som körs när den kommer och gör samma sak för att granska information.  

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)

    Du kan se käll filen (fil2. txt) har kopierats från **källan/2019/02/26/15/** till **destinationen/2019/02/26/15** /med samma fil namn.

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png)

    Du kan också kontrol lera att du använder Azure Storage Explorer (https://storageexplorer.com/) för att genomsöka filerna i **mål** behållaren

    ![Övervaka pipelinekörningar](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Transformera data med Apache Spark i molnet](tutorial-transform-data-spark-portal.md)

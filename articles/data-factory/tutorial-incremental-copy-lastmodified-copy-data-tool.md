---
title: Data verktyg för att kopiera nya och uppdaterade filer stegvis
description: Skapa en Azure-datafabrik och Använd sedan Kopiera data-verktyget för att stegvis läsa in nya filer baserat på LastModifiedDate.
services: data-factory
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
ms.openlocfilehash: 05696ae2fed31c267aa37013fac32db0916e24bf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439321"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Kopiera nya och ändrade filer stegvis baserat på LastModifiedDate med hjälp av verktyget Kopiera data

I den här självstudien använder du Azure Portal för att skapa en data fabrik. Sedan använder du Kopiera data-verktyget för att skapa en pipeline som stegvis kopierar nya och ändrade filer, baserat på deras **LastModifiedDate** från Azure Blob Storage till Azure Blob Storage.

Genom att göra det genomsöker ADF alla filerna från käll arkivet, använder fil filtret efter deras LastModifiedDate och kopierar den nya och uppdaterade filen först sedan den senaste gången till mål lagret.  Observera att om du låter ADF Skanna enorma mängder filer, men bara kopiera några få filer till målet, förväntar du dig fortfarande den långa varaktigheten på grund av att fil genomsökningen också tar lång tid.   

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien ska du utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**: Använd Blob Storage som data lager för _källa_ och _mottagare_ . Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Skapa två behållare i Blob Storage

Förbered blob-lagringen för självstudien genom att utföra dessa steg.

1. Skapa en behållare med namnet **Source**. Du kan använda olika verktyg för att utföra den här uppgiften, t. ex. [Azure Storage Explorer](https://storageexplorer.com/).

2. Skapa en behållare med namnet **mål**. 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **skapa en resurs** > **data och analys** > **Data Factory**: 
   
   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

2. I fönstret **Ny datafabrik**, under **Namn**, anger du **ADFTutorialDataFactory**. 
 
   Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:
   
   ![Felmeddelande för ny datafabrik](./media/doc-common-process/name-not-available-error.png)

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_ **ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
3. Välj den Azure- **prenumeration** där du vill skapa den nya data fabriken. 
4. Gör något av följande för **Resursgrupp**:
     
    * Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

    * Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).

5. Under **version**väljer du **v2**.
6. Under **plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Data lag ren (till exempel Azure Storage och SQL Database) och beräkningarna (till exempel Azure HDInsight) som din Data Factory använder kan finnas på andra platser och regioner.
7. Välj **fäst till instrumentpanelen**. 
8. Välj **Skapa**.
9. På instrument panelen, se sidan **distribuera Data Factory** för att se process statusen.

    ![Distribuera Data Factory panel](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. När den har skapats visas startsidan för **Data Factory**.
   
    ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)
11. Om du vill öppna användar gränssnittet för Azure Data Factory på en separat flik väljer du panelen **författare & Monitor** . 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. På sidan **nu sätter vi igång** väljer du **Kopiera data** titeln för att öppna kopiera datas verktyget. 

   ![Panel för verktyget Kopiera data](./media/doc-common-process/get-started-page.png)
   
2. Utför följande steg på sidan **Egenskaper** :

    a. Under **uppgifts namn**, anger du **DeltaCopyFromBlobPipeline**.

    b. Under **aktivitets takt** eller **aktivitets schema**väljer du **kör regelbundet enligt schema**.

    c. Under **utlösnings typ**väljer du **rullande-fönster**.
    
    d. Under **upprepning**anger du **15 minuter**. 
    
    e. Välj **Nästa**. 
    
    Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn. 

    ![Sidan Egenskaper](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Gör följande på sidan **Källdatalager**:

    a. Välj **+ Skapa ny anslutning**för att lägga till en anslutning.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Välj **Azure Blob Storage** från galleriet och välj sedan **Fortsätt**.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. På sidan **ny länkad tjänst** väljer du ditt lagrings konto i listan **lagrings konto namn** och väljer sedan **Slutför**.
    
    ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Välj den nyligen skapade länkade tjänsten och välj sedan **Nästa**. 
    
   ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Gör följande på sidan för att **välja indatafil eller -mapp**:
    
    a. Bläddra och välj **källmappen** och välj sedan **Välj**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Under **fil inläsnings beteende**väljer du **stegvis belastning: LastModifiedDate**.
    
    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Markera **binär kopia** och välj **Nästa**.
    
     ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. På sidan **mål data lager** väljer du **AzureBlobStorage**. Det här är samma lagrings konto som käll data lagret. Välj sedan **Nästa**.

    ![Sidan Måldatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Gör följande på sidan **Choose the output file or folder** (Välj utdatafil eller -mapp):
    
    a. Bläddra och välj **målmappen** och välj sedan **Välj**.
    
    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Välj **Nästa**.
    
     ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**. 

    ![Sidan Inställningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. På sidan **Sammanfattning** granskar du inställningarna och väljer sedan **Nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).

    ![Distributionssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Observera att fliken **Övervaka** till vänster väljs automatiskt. I kolumnen **Åtgärder** finns länkar som visar information om aktivitetskörningen och för att köra pipelinen igen. Välj **Uppdatera** för att uppdatera listan och välj länken **Visa aktivitet kör** i kolumnen **åtgärder** . 

    ![Uppdatera listan och välj Visa aktivitets körningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Det finns bara en aktivitet (kopierings aktiviteten) i pipelinen, så att du bara ser en post. Om du vill se mer information om kopieringsåtgärden väljer du länken för **detaljer** (glasögonikonen) i kolumnen **Actions** (Åtgärder). 

    ![Kopierings aktiviteten är i pipeline](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Eftersom det inte finns någon fil i **käll** behållaren i ditt Blob Storage-konto, visas ingen fil som kopierats till **mål** behållaren i ditt Blob Storage-konto.
    
    ![Ingen fil i käll containern eller mål behållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Skapa en tom textfil och ge den namnet **fil1. txt**. Ladda upp text filen till **käll** behållaren i ditt lagrings konto. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Skapa fil1. txt och ladda upp till käll behållare](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Gå tillbaka till vyn **pipeline-körningar** genom att välja **alla pipelines-körningar**och vänta tills samma pipeline utlöses igen automatiskt.  

    ![Välj alla pipeline-körningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Välj **Visa aktivitets körning** för den andra pipeline-körningen när den visas. Granska sedan informationen på samma sätt som du gjorde för den första pipeline-körningen.  

    ![Välj Visa aktivitets körning och granska information](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Du kommer att se en fil (fil1. txt) har kopierats från **käll** behållaren till **mål** behållaren för ditt Blob Storage-konto.
    
    ![Fil1. txt har kopierats från käll containern till mål behållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Skapa en annan tom textfil och ge den namnet **fil2. txt**. Ladda upp text filen till **käll** behållaren i ditt Blob Storage-konto.   
    
16. Upprepa steg 13 och 14 för den här andra text filen. Du kommer att se att endast den nya filen (fil2. txt) har kopierats från **käll** behållaren till **mål** behållaren för ditt lagrings konto i nästa pipeline-körning.  
    
    ![Fil2. txt har kopierats från käll containern till mål behållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Du kan också kontrol lera detta genom att använda [Azure Storage Explorer](https://storageexplorer.com/) för att genomsöka filerna.
    
    ![Genomsök filer med Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Nästa steg
Gå vidare till följande självstudie för att lära dig hur du omvandlar data med hjälp av ett Apache Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Transformera data i molnet med hjälp av ett Apache Spark kluster](tutorial-transform-data-spark-portal.md)
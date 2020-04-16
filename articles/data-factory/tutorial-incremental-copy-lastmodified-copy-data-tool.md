---
title: Dataverktyg för att kopiera nya och uppdaterade filer stegvis
description: Skapa en Azure-datafabrik och använd sedan verktyget Kopiera data för att stegvis läsa in nya filer baserat på LastModifiedDate.
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
ms.date: 3/18/2020
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399493"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Kopiera nya och ändrade filer stegvis baserat på LastModifiedDate med hjälp av verktyget Kopiera data

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien använder du Azure-portalen för att skapa en datafabrik. Du ska sedan använda verktyget Kopiera data för att skapa en pipeline som stegvis kopierar nya och ändrade filer, från Azure Blob-lagring till Azure Blob-lagring. Den `LastModifiedDate` använder för att avgöra vilka filer som ska kopieras.

När du har slutfört stegen här söker Azure Data Factory igenom alla filer `LastModifiedDate`i källarkivet, använder filfiltret efter och kopierar endast filer som är nya eller har uppdaterats till målarkivet sedan förra gången. Observera att om Data Factory skannar ett stort antal filer bör du fortfarande förvänta dig långa varaktigheter. Filskanning är tidskrävande, även när mängden data som kopieras minskas.

> [!NOTE]
> Om du inte har använt datafabriken tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**: Använd Blob-lagring för käll- och sinkdatalager. Om du inte har ett Azure Storage-konto följer du instruktionerna i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Skapa två behållare i Blob-lagring

Förbered blob-lagringen för självstudien genom att slutföra följande steg:

1. Skapa en behållare med namnet **källa**. Du kan använda olika verktyg för att utföra den här uppgiften, till exempel [Azure Storage Explorer](https://storageexplorer.com/).

2. Skapa en behållare med namnet **destination**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Välj **Skapa en resurs** i fönstret till vänster. Välj > **Analysdatafabrik:** **Analytics**

   ![Välj datafabrik](./media/doc-common-process/new-azure-data-factory-menu.png)

2. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på datafabriken måste vara globalt unikt. Det här felmeddelandet kan visas:

   ![Det inte tillgängliga namnet visas](./media/doc-common-process/name-not-available-error.png)

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
3. Under **Prenumeration**väljer du den Azure-prenumeration där du skapar den nya datafabriken.
4. Gör något av följande under **Resursgrupp:**

    * Välj **Använd befintlig** och välj sedan en befintlig resursgrupp i listan.

    * Välj **Skapa nytt** och ange sedan ett namn för resursgruppen.
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).

5. Under **Version** väljer du **V2**.
6. Under **Plats**väljer du plats för datafabriken. Endast platser som stöds visas i listan. Datalager (till exempel Azure Storage och Azure SQL Database) och beräkningar (till exempel Azure HDInsight) som din datafabrik använder kan vara på andra platser och regioner.
8. Välj **Skapa**.
9. När datafabriken har skapats visas startsidan för datafabriken.
10. Om du vill öppna användargränssnittet för Azure Data Factory (UI) på en separat flik väljer du panelen **Författare & Övervakare:**

    ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. På sidan **Låt oss komma igång** väljer du panelen Kopiera **data** för att öppna verktyget Kopiera data:

   ![Kopiera datapanel](./media/doc-common-process/get-started-page.png)

2. Gör följande på sidan **Egenskaper:**

    a. Under **Aktivitetsnamn**anger du **DeltaCopyFromBlobPipeline**.

    b. Under **Aktivitetskadens eller Aktivitetsschema**väljer du **Kör regelbundet enligt schemat**.

    c. Under **Utlösartyp**väljer du **Tumlande fönster**.

    d. Under **Återkommande**anger du **15 minuter.**

    e. Välj **Nästa**.

    Data Factory skapar en pipeline med det angivna aktivitetsnamnet.

    ![Sidan Kopiera dataegenskaper](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Gör så här på sidan **Källdatalager:**

    a. Välj **Skapa ny anslutning** om du vill lägga till en anslutning.

    b. Välj **Azure Blob Storage** från galleriet och välj sedan **Fortsätt:**

    ![Välj Azure-blogglagring](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. På sidan **Ny länkad tjänst (Azure Blob Storage)** väljer du ditt lagringskonto i listan **För lagringskontonamn.** Testa anslutningen och välj sedan **Skapa**.

    d. Välj den nya länkade tjänsten och välj sedan **Nästa:**

   ![Välj den nya länkade tjänsten](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Gör följande på sidan för att **välja indatafil eller -mapp**:

    a. Bläddra efter och välj **källmappen** och välj sedan **Välj**.

    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Under **Filinläsning**väljer du **Inkrementell inläsning: LastModifiedDate**.

    c. Välj **Binär kopia** och välj sedan **Nästa:**

     ![Välj indatafil eller mappsida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. På sidan **Måldatalager** väljer du den **AzureBlobStorage-tjänst** som du skapade. Det här är samma lagringskonto som källdatalagret. Välj sedan **Nästa**.

6. Gör följande på sidan **Choose the output file or folder** (Välj utdatafil eller -mapp):

    a. Bläddra efter och välj **målmappen** och välj sedan **Välj:**

    ![Välj utdatafil eller mappsida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Välj **Nästa**.

7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**.

8. På sidan **Sammanfattning** granskar du inställningarna och väljer sedan **Nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).

    ![Distributionssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Observera att fliken **Övervaka** till vänster väljs automatiskt. Programmet växlar till fliken **Bildskärm.** Du ser status för pipelinen. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). Välj länken under **PIPELINE NAMN** om du vill visa information om aktivitetskörning eller köra pipelinen igen.

    ![Uppdatera information om list- och vyaktivitetskörning](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Det finns bara en aktivitet (kopieringsaktiviteten) på pipelinen, så du ser bara en post. Om du vill ha mer information om kopieringen väljer du länken **Information** (ikonen Glasögon) i kolumnen **AKTIVITETSNAMN.** Mer information om egenskaperna finns i [Kopiera aktivitetsöversikt](copy-activity-overview.md).

    ![Kopiera aktivitet på pipelinen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Eftersom det inte finns några filer i källbehållaren i ditt Blob-lagringskonto visas inga filer som kopierats till målbehållaren i kontot:

    ![Inga filer i källbehållaren eller målbehållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Skapa en tom textfil och namnge den **file1.txt**. Ladda upp den här textfilen till källbehållaren i ditt lagringskonto. Du kan använda olika verktyg för att utföra dessa uppgifter, till exempel [Azure Storage Explorer](https://storageexplorer.com/).

    ![Skapa file1.txt och ladda upp den till källbehållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Om du vill gå tillbaka till vyn **Pipeline-körningar** väljer du **Alla pipeline-körningar**och väntar på att samma pipeline ska utlösas automatiskt igen.  

    ![Välj Alla pipelinekörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. När den andra pipeline-körningen är klar följer du samma steg som nämndes tidigare för att granska information om aktivitetskörningen.  

    Du ser att en fil (file1.txt) har kopierats från källbehållaren till målbehållaren för ditt Blob-lagringskonto:

    ![file1.txt har kopierats från källbehållaren till målbehållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Skapa en annan tom textfil och namnge den **file2.txt**. Ladda upp den här textfilen till källbehållaren i ditt Blob-lagringskonto.

16. Upprepa steg 13 och 14 för den andra textfilen. Du ser att endast den nya filen (file2.txt) kopierades från källbehållaren till målbehållaren för ditt lagringskonto under den här pipeline-körningen.  

    Du kan också kontrollera att endast en fil har kopierats med hjälp av [Azure Storage Explorer](https://storageexplorer.com/) för att söka igenom filerna:

    ![Skanna filer med hjälp av Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Nästa steg
Gå till följande självstudiekurs om du vill lära dig hur du omvandlar data med hjälp av ett Apache Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Omvandla data i molnet med hjälp av ett Apache Spark-kluster](tutorial-transform-data-spark-portal.md)

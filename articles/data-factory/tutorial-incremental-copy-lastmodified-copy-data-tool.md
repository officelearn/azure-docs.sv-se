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
ms.openlocfilehash: 743f9dd8f7998178a75d716f4da22efee2b3bc79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131141"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Kopiera nya och ändrade filer stegvis baserat på LastModifiedDate med hjälp av verktyget Kopiera data

I den här självstudien använder du Azure-portalen för att skapa en datafabrik. Sedan ska du använda verktyget Kopiera data för att skapa en pipeline som stegvis kopierar nya och ändrade filer, baserat på deras **LastModifiedDate** från Azure Blob-lagring till Azure Blob-lagring.

På så sätt kommer ADF att skanna alla filer från källarkivet, tillämpa filfiltret med deras LastModifiedDate och kopiera den nya och uppdaterade filen bara sedan förra gången till målarkivet.  Observera att om du låter ADF skanna enorma mängder filer men bara kopiera några filer till destinationen, skulle du fortfarande förvänta dig den långa varaktigheten på grund av filskanning är tidskrävande också.   

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien ska du utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Krav

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure storage-konto:** Använd Blob-lagring som _källa_ och _sink_ data store. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Skapa två behållare i Blob-lagring

Förbered din Blob-lagring för självstudien genom att utföra dessa steg.

1. Skapa en behållare med namnet **källa**. Du kan använda olika verktyg för att utföra den här uppgiften, till exempel [Azure Storage Explorer](https://storageexplorer.com/).

2. Skapa en behållare med namnet **destination**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **Skapa en resursData** > **+ Analytics** > **Data Factory:**

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

2. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:

   ![Felmeddelande för ny datafabrik](./media/doc-common-process/name-not-available-error.png)

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
3. Välj den **Azure-prenumeration** där du ska skapa den nya datafabriken.
4. Gör något av följande för **Resursgrupp**:

    * Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

    * Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).

5. Under **version**väljer du **V2**.
6. Under **plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager (till exempel Azure Storage och SQL Database) och beräkningar (till exempel Azure HDInsight) som din datafabrik använder kan finnas på andra platser och regioner.
8. Välj **Skapa**.
9. När den har skapats visas startsidan för **Data Factory**.
10. Om du vill öppna användargränssnittet för Azure Data Factory (UI) på en separat flik väljer du panelen **Författare & Övervakare.**

    ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. På sidan **Låt oss komma igång** väljer du rubriken Kopiera **data** för att öppna verktyget Kopiera data.

   ![Panel för verktyget Kopiera data](./media/doc-common-process/get-started-page.png)

2. Gör följande på sidan **Egenskaper:**

    a. Under **Aktivitetsnamn**anger du **DeltaCopyFromBlobPipeline**.

    b. Under **Aktivitetskadens eller** **Aktivitetsschema**väljer du **Kör regelbundet enligt schemat**.

    c. Under **Utlösartyp**väljer du **Tumlande fönster**.

    d. Under **Återkommande**anger du **15 minuter.**

    e. Välj **Nästa**.

    Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn.

    ![Sidan Egenskaper](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Gör följande på sidan **Källdatalager**:

    a. Välj **+ Skapa ny anslutning**om du vill lägga till en anslutning.

    b. Välj **Azure Blob Storage** i galleriet och välj sedan **Fortsätt**.

    ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. På sidan **Ny länkad tjänst(Azure Blob Storage)** väljer du ditt lagringskonto i listan **För lagringskontonamn.** Testa anslutningen och välj sedan **Skapa**.

    d. Välj den nyligen skapade länkade tjänsten och välj sedan **Nästa**.

   ![Sidan Källdatalager](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Gör följande på sidan för att **välja indatafil eller -mapp**:

    a. Bläddra och markera **källmappen** och välj sedan **Välj**.

    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Under **Filinläsning**väljer du **Inkrementell inläsning: LastModifiedDate**.

    c. Markera **Binär kopia** och välj **Nästa**.

     ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. På sidan **Måldatalager** väljer du det **AzureBlobStorage** som du skapade. Det här är samma lagringskonto som källdatalagret. Välj sedan **Nästa**.

6. Gör följande på sidan **Choose the output file or folder** (Välj utdatafil eller -mapp):

    a. Bläddra och markera **målmappen** och välj sedan **Välj**.

    ![Välj utdatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Välj **Nästa**.

7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**.

8. På sidan **Sammanfattning** granskar du inställningarna och väljer sedan **Nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).

    ![Distributionssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Observera att fliken **Övervaka** till vänster väljs automatiskt. Programmet växlar till fliken **Bildskärm.** Du ser status för pipelinen. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). Klicka på länken under **PIPELINE NAMN** om du vill visa information om aktivitetskörning eller köra pipelinen igen. 

    ![Uppdatera lista och välj Visa aktivitetskörningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Det finns bara en aktivitet (kopieringsaktiviteten) på pipelinen, så du ser bara en post. Om du vill ha mer information om kopieringsåtgärden väljer du länken Information (ikonen **För** glasögon) under kolumnen **AKTIVITETSNAMN.** Mer information om egenskaperna finns i [Copy Activity overview](copy-activity-overview.md) (Översikt över kopieringsaktivitet). 

    ![Kopieringsaktivitet är på gång](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Eftersom det inte finns någon fil i **källbehållaren** i ditt Blob-lagringskonto visas ingen fil som kopierats till **målbehållaren** i ditt Blob-lagringskonto.

    ![Ingen fil i källbehållaren eller målbehållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Skapa en tom textfil och namnge den **file1.txt**. Ladda upp den här textfilen till **källbehållaren** i ditt lagringskonto. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

    ![Skapa file1.txt och ladda upp till källbehållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Om du vill gå tillbaka till vyn **Pipeline runs** väljer du **Alla pipeline-körningar**och väntar på att samma pipeline ska utlösas igen automatiskt.  

    ![Välj alla pipeline-körningar](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. När den andra pipeline-körningen är klar följer du samma steg som nämns ovan för att granska information om aktivitetskörningen.  

    Du kommer att se att en fil (file1.txt) har kopierats från **källbehållaren** till **målbehållaren** för ditt Blob-lagringskonto.

    ![File1.txt har kopierats från källbehållare till målbehållare](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Skapa en annan tom textfil och namnge den **file2.txt**. Ladda upp den här textfilen till **källbehållaren** i ditt Blob-lagringskonto.

16. Upprepa steg 13 och 14 för den andra textfilen. Du kommer att se att endast den nya filen (file2.txt) har kopierats från **källbehållaren** till **målbehållaren** för ditt lagringskonto i nästa pipeline-körning.  

    Du kan också verifiera detta genom att använda [Azure Storage Explorer](https://storageexplorer.com/) för att söka igenom filerna.

    ![Skanna filer med Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Nästa steg
Gå vidare till följande självstudiekurs om du vill lära dig mer om hur du omvandlar data med hjälp av ett Apache Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Omvandla data i molnet med hjälp av ett Apache Spark-kluster](tutorial-transform-data-spark-portal.md)

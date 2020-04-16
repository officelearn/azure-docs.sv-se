---
title: Läsa in data i Azure Data Lake Storage Gen2
description: Använda Azure Data Factory för att kopiera data till Azure Data Lake Storage Gen2
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 96674f059e9cbc21c5c8c64eff8c94c810c4aa32
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417784"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Läsa in data i Azure Data Lake Storage Gen2 med Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stordataanalys, inbyggda i [Azure Blob-lagring](../storage/blobs/storage-blobs-introduction.md). Det gör att du kan samverka med dina data med hjälp av både filsystem och objekt lagring paradigm.

Azure Data Factory (ADF) är en fullständigt hanterad molnbaserad dataintegrationstjänst. Du kan använda tjänsten för att fylla sjön med data från en omfattande uppsättning lokala och molnbaserade datalager och spara tid när du skapar dina analyslösningar. En detaljerad lista över kopplingar som stöds finns i tabellen [över datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory erbjuder en skala ut, hanterad dataförflyttningslösning. På grund av ADF:s utskalningsarkitektur kan den inta data med högt dataflöde. Mer information finns i [Kopiera aktivitetsprestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder datafabrikskopieringsdataverktyget för att läsa in data från _Amazon Web Services S3-tjänsten_ i _Azure Data Lake Storage Gen2_. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

>[!TIP]
>För att kopiera data från Azure Data Lake Storage Gen1 till Gen2, se [den här specifika genomgången](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Krav

* Azure-prenumeration: Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Storage-konto med Data Lake Storage Gen2 aktiverat: Om du inte har ett lagringskonto [skapar du ett konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* AWS-konto med en S3-bucket som innehåller data: Den här artikeln visar hur du kopierar data från Amazon S3. Du kan använda andra datalager genom att följa liknande steg.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **Skapa en resursData** > **+ Analytics** > **Data Factory:**
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **Ny datafabrik** anger du värden för de fält som visas i följande bild: 
      
   ![Sidan Ny datafabrik](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure-datafabrik. Om felet "Data fabriksnamn \"LoadADLSDemo\" inte är tillgängligt" anger du ett annat namn för datafabriken. Du kan till exempel använda namnet _**yourname**_**ADFTutorialDataFactory**. Försök att skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa datafabriken. 
    * **Resursgrupp:** Välj en befintlig resursgrupp i listrutan eller välj alternativet **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager som används av datafabriken kan finnas på andra platser och regioner. 

3. Välj **Skapa**.
4. När skapandet är klart går du till din datafabrik. Startsidan för **Data Factory** visas i följande bild: 
   
   ![Datafabrikens startsida](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Välj panelen **Författare och övervakare** för att starta dataintegreringsprogrammet på en separat flik.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Läsa in data i Azure Data Lake Storage Gen2

1. På sidan **Kom igång** väljer du panelen **Kopiera data** för att starta verktyget Kopiera data: 

   ![Panel för verktyget Kopiera data](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. På sidan **Egenskaper** anger du **CopyFromAmazonS3ToADLS** för fältet **Aktivitetsnamn** och väljer **Nästa:**

    ![Sidan Egenskaper](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. På sidan **Källa för datalager** klickar du på **+ Skapa ny anslutning:**

    ![Sidan Källdatalager](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Välj **Amazon S3** i kopplingsgalleriet och välj **Fortsätt**
    
    ![Sidan Källdatalager s3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Gör följande steg på **sidan Ange Amazon S3-anslutning:**

   1. Ange värdet **för Access-nyckel-ID.**
   2. Ange värdet **för den hemliga åtkomstnyckeln.**
   3. Klicka på **Testa anslutning** för att verifiera inställningarna och välj sedan **Slutför**.
   4. Du kommer att se en ny anslutning skapas. Välj **Nästa**.
   
      ![Ange Amazon S3-konto](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. På sidan **Välj indatafil eller mapp** bläddrar du till den mapp och fil du vill kopiera över. Markera mappen/filen, välj **Välj:**

    ![Välj indatafil eller mapp](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Ange kopieringsbeteendet genom att markera alternativen **Kopiera filer rekursivt** och **Binär kopia**. Välj **Nästa:**

    ![Ange utdatamapp](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. På sidan **Måldatalager** klickar du på **+ Skapa ny anslutning**och väljer sedan Azure Data Lake Storage **Gen2**och väljer **Fortsätt:**

    ![Sidan Måldatalager](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Gör följande i **sidan Ange Azure Data Lake Storage-anslutning:**

   1. Välj ditt datasjölagringsgenm2-kompatibla konto i listrutan "Lagringskontonamn".
   2. Välj **Slutför** för att skapa anslutningen. Välj sedan **Nästa**.
   
   ![Ange Azure Data Lake Storage Gen2-konto](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. På sidan **Välj utdatafil eller mapp** anger du **copyfroms3** som utdatamappnamn och väljer **Nästa**. ADF skapar motsvarande ADLS Gen2-filsystem och undermappar under kopieringen om det inte finns.

    ![Ange utdatamapp](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. På sidan **Inställningar** väljer du **Nästa** om du vill använda standardinställningarna:

    ![Sidan Inställningar](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. På sidan **Sammanfattning** granskar du inställningarna och väljer **Nästa:**

    ![Sammanfattningssida](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. På **sidan Distribution**väljer du **Övervaka** för att övervaka pipelinen:

    ![Distributionssida](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Observera att fliken **Övervaka** till vänster väljs automatiskt. Kolumnen **Åtgärder** innehåller länkar för att visa information om aktivitetskörning och för att köra pipelinen igen:

    ![Övervaka pipelinekörningar](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningen väljer du länken **Visa aktivitetskörningar** i kolumnen **Åtgärder.** Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill växla tillbaka till vyn pipelinekörningar väljer du länken **Pipelines** högst upp. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). 

    ![Övervaka aktivitetskörningar](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Om du vill övervaka körningsinformationen för varje kopieringsaktivitet väljer du länken **Information** (glasögonbild) under **Åtgärder** i aktivitetsövervakningsvyn. Du kan övervaka information som mängden data som kopieras från källan till diskhon, datadataflöde, körningssteg med motsvarande varaktighet och använda konfigurationer:

    ![Information om övervakning av aktivitetskörning](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Kontrollera att data kopieras till ditt DataSjölagringsgenm2-konto.

## <a name="next-steps"></a>Nästa steg

* [Kopiera aktivitetsöversikt](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-anslutning](connector-azure-data-lake-storage.md)

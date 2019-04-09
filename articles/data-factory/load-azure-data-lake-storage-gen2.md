---
title: Läs in data till Azure Data Lake Storage Gen2 med Azure Data Factory
description: Använda Azure Data Factory för att kopiera data till Azure Data Lake Storage Gen2
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: jingwang
ms.openlocfilehash: 8e3a23b38dc95f05ed9a6d99d303f3d86eac60ad
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269273"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Läs in data till Azure Data Lake Storage Gen2 med Azure Data Factory

Azure Data Lake Storage Gen2 är en uppsättning funktioner för analys av stordata, inbyggda i [Azure Blob storage](../storage/blobs/storage-blobs-introduction.md). Det kan du samverka med dina data med paradigm för lagring av system- och båda fil.

Azure Data Factory är en fullständigt hanterad molnbaserad dataintegreringstjänst. Du kan använda tjänsten för att fylla i sjön med data från ett stort utbud av lokala och molnbaserade datalager och spara tid när du skapar Analyslösningar. En detaljerad lista över kopplingar som stöds finns i tabellen med [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory är en lösning för flytt av skalbara, hanterade data. På grund av ADF skalbar arkitektur, kan det mata in data med ett högt dataflöde. Mer information finns i [kopiera aktivitet prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget kopieringsdata i Data Factory för att läsa in data från _Amazon Web Services S3-tjänsten och_ till _Azure Data Lake Storage Gen2_. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

>[!TIP]
>Kopierar data från Azure Data Lake Storage Gen1 till Gen2, finns i [den här specifika genomgången](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Storage-konto med Data Lake Storage Gen2 aktiverat: Om du inte har ett lagringskonto [skapa ett konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* AWS-konto med en S3-bucket som innehåller data: Den här artikeln visar hur du kopierar data från Amazon S3. Du kan använda andra datalager genom att följa liknande steg.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På menyn till vänster väljer **skapa en resurs** > **Data och analys** > **Data Factory**:
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. I den **ny datafabrik** anger värden för fälten som visas i följande bild: 
      
   ![Sida för ny datafabrik](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure data factory. Om du får felet ”datafabriksnamnet \"LoadADLSDemo\" är inte tillgänglig”, ange ett annat namn för data factory. Du kan till exempel använda namnet  _**dittnamn**_**ADFTutorialDataFactory**. Försök att skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa data factory. 
    * **Resursgrupp**: Välj en befintlig resursgrupp från den nedrullningsbara listan eller Välj den **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager som används av data factory kan finnas på andra platser och regioner. 

3. Välj **Skapa**.
4. När datafabriken har skapats går du till din datafabrik. Du ser den **Data Factory** startsida, enligt följande bild: 
   
   ![Datafabrikens startsida](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Välj den **författare och Övervakare** att starta Dataintegrationsprogrammet i en separat flik.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Läs in data till Azure Data Lake Storage Gen2

1. I den **börjar** väljer den **kopieringsdata** att starta verktyget kopieringsdata: 

   ![Panel för verktyget Kopiera data](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. I den **egenskaper** anger **CopyFromAmazonS3ToADLS** för den **aktivitetsnamn** och markerar **nästa**:

    ![Sidan Egenskaper](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. I den **källdatalagret** klickar du på **+ Skapa ny anslutning**:

    ![Sidan Källdatalager](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Välj **Amazon S3** i galleriet för anslutningen och välj **Fortsätt**
    
    ![Sidan s3 källdatalager](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. I den **ange Amazon S3 anslutning** gör du följande steg:

   1. Ange den **Åtkomstnyckelns ID** värde.
   2. Ange den **hemliga åtkomstnyckel** värde.
   3. Klicka på **Testanslutning** för att verifiera inställningarna kan sedan välja **Slutför**.
   4. Du ser en ny anslutning skapas. Välj **Nästa**.
   
      ![Ange Amazon S3-konto](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. I den **Välj indatafil eller mapp** sidan, bläddra till mappen och filen som du vill kopiera över. Välj mappen/filen, Välj **Välj**:

    ![Välj indatafil eller mapp](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Ange kopieringsbeteendet genom att markera den **kopiera filer rekursivt** och **binär kopia** alternativ. Välj **nästa**:

    ![Ange Utdatamappen](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. I den **måldatalager** klickar du på **+ Skapa ny anslutning**, och välj sedan **Azure Data Lake Storage Gen2**, och välj **Fortsätt**:

    ![Sidan Måldatalager](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. I den **ange Azure Data Lake Storage anslutning** gör du följande steg:

   1. Välj din Data Lake Storage Gen2 kan kontot från ”lagringskontonamnet” nedrullningsbar listruta.
   2. Välj **Slutför** att skapa anslutningen. Välj sedan **Nästa**.
   
   ![Ange konto för Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. I den **Välj utdatafil eller mapp** anger **copyfroms3** som utdata mappnamn och välj **nästa**. ADF skapar motsvarande ADLS Gen2 filsystemet och undermappar vid kopiering om det inte finns.

    ![Ange Utdatamappen](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. I den **inställningar** väljer **nästa** att använda standardinställningarna för:

    ![Sidan Inställningar](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. I den **sammanfattning** , granskar du inställningarna och välj **nästa**:

    ![Sammanfattningssida](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. I den **distributionssida**väljer **övervakaren** att övervaka pipelinen:

    ![Distributionssida](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Observera att fliken **Övervaka** till vänster väljs automatiskt. Den **åtgärder** -kolumnen innehåller länkar för att visa information om aktivitetskörningar och köra pipelinen på nytt:

    ![Övervaka pipelinekörningar](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Om du vill visa de aktivitetskörningar som är associerade med pipelinekörningen, väljer den **visa Aktivitetskörningar** länken i den **åtgärder** kolumn. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Växla tillbaka till vyn med pipelinekörningar, Välj den **Pipelines** länken längst upp. Om du vill uppdatera listan väljer du **Uppdatera**. 

    ![Övervaka aktivitetskörningar](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. För att övervaka körning-information för varje kopieringsaktiviteten, Välj den **information** länken (glasögonbilden) under **åtgärder** i övervakningsvyn-aktivitet. Du kan övervaka information som mängden data som kopieras från källan till mottagare, dataflöde, utförande med motsvarande tid och används konfigurationer:

    ![Övervaka aktivitetskörningsinformation](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Kontrollera att data har kopierats till ditt Data Lake Storage Gen2-konto.

## <a name="best-practices"></a>Bästa praxis

Kopiera när stora volymer av data från filbaserat datalager, är föreslagna till:

- Partitionera filerna i 10TB till 30TB filuppsättning.
- Utlöses inte av för många samtidiga kopia körs för att undvika begränsning från källan eller mottagaren datalager. Du kan börja med en kopia som kör och övervaka dataflödet och sedan gradvis lägga till fler efter behov.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-anslutningsapp](connector-azure-data-lake-storage.md)

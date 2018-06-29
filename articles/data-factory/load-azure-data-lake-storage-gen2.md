---
title: Läs in data till Azure Data Lake lagring Gen2 (förhandsgranskning) med Azure Data Factory
description: Använd Azure Data Factory för att kopiera data till Azure Data Lake lagring Gen2 (förhandsgranskning)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 961c8dea4dbb6b6600d10b75e84a9a84c34c329b
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036662"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-preview-with-azure-data-factory"></a>Läs in data till Azure Data Lake lagring Gen2 Preview med Azure Data Factory

[Azure Data Lake lagring Gen2 Preview](../storage/data-lake-storage/introduction.md) lägger till ett protokoll med hierarkiskt filsystem namnområde och säkerhet: funktioner i Azure Blob Storage, vilket gör det enkelt att ansluta analytics ramverk till en beständig lagringsskikt. I Data Lake lagring Gen2 (förhandsgranskning) alla egenskaper för objektlagring finns kvar när du lägger till fördelarna med ett gränssnitt för fil-system.

Azure Data Factory är en helt hanterad molnbaserade integration datatjänst. Du kan använda tjänsten för att fylla i lake med data från ett stort utbud av lokala och molnbaserade data lagras och spara tid när du skapar din Analyslösningar. En detaljerad lista över kopplingar som stöds finns i tabellen i [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory ger en skalbar lösning för hanterade flytt till skillnad från AzCopy, en kommandorad data transfer verktyget. På grund av ADF skalbar arkitektur, det mata in data på en hög genomströmning. Mer information finns i [kopiera aktivitet prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget Data Factory kopieringsdata att läsa in data från _Amazon Web Services S3 service_ till _Azure Data Lake lagring Gen2_. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Storage-konto med Data Lake lagring Gen2 aktiverad: Om du inte har ett lagringskonto, klickar du på [här](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) att skapa en.
* AWS-konto med ett S3-bucket som innehåller data: den här artikeln visar hur du kopierar data från Amazon S3. Du kan använda andra dataarkiv genom att följa liknande steg.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer **ny** > **Data + analys** > **Data Factory**:
   
   ![Skapa en ny datafabrik](./media/load-azure-data-lake-storage-gen2/new-azure-data-factory-menu.png)
2. I den **nya data factory** anger värden för fälten som visas i följande bild: 
      
   ![Sida för ny datafabrik](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Namnet**: Ange ett globalt unikt namn för din Azure data factory. Om du får felet ”datafabriksnamnet \"LoadADLSDemo\" är inte tillgängligt” ange ett annat namn för data factory. Du kan till exempel använda namnet  _**dittnamn**_**ADFTutorialDataFactory**. Försök att skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumerationen**: Välj din Azure-prenumeration att skapa datafabriken. 
    * **Resursgruppen**: Välj en befintlig resursgrupp från den nedrullningsbara listan eller Välj den **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Välj **V2(Preview)**.
    * **Plats**: Välj platsen för data factory. Endast platser som stöds visas i listrutan. Datalager som används av datafabriken kan vara på andra platser och regioner. 

3. Välj **Skapa**.
4. När du har skapats, gå till din data factory. Du ser den **Data Factory** startsidan som visas i följande bild: 
   
   ![Datafabrikens startsida](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Välj den **författare & övervakaren** rutan för att starta programmet på Integration Data i en separat flik.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Läs in data till Azure Data Lake lagring Gen2

1. I den **Kom igång** väljer den **kopieringsdata** rutan för att starta verktyget kopieringsdata: 

   ![Panel för verktyget Kopiera data](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. I den **egenskaper** anger **CopyFromAmazonS3ToADLS** för den **aktivitet** och markerar **nästa**:

    ![Sidan Egenskaper](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. I den **källa datalagret** klickar du på **+ Skapa ny anslutning**:

    ![Sidan Källdatalager](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Välj **Amazon S3** connector-galleriet och välj **Fortsätt**
    
    ![Datakälla data store s3 på sidan](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. I den **ange Amazon S3 anslutning** gör du följande:
   1. Ange den **åtkomst nyckel-ID** värde.
   2. Ange den **hemlighet åtkomstnyckel** värde.
   3. Klicka på **Testanslutningen** Välj sedan om du vill verifiera inställningarna för **Slutför**.
   
   ![Ange Amazon S3 konto](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   
   4. Du ser en ny anslutning skapas. Välj **Nästa**.
   
5. I den **Välj inkommande filen eller mappen** sidan, bläddra till mappen och filen som du vill kopiera över. Välj mappen/filen **Välj**:

    ![Välj indatafil eller mapp](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Ange hur kopia genom att kontrollera den **kopiera filer rekursivt** och **binära kopiera** alternativ. Välj **nästa**:

    ![Ange utdatamapp](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. I den **data målarkiv** klickar du på **+ Skapa ny anslutning**, och välj sedan **Azure Data Lake lagring Gen2 (förhandsgranskning)**, och välj **Fortsätt** :

    ![Sidan Måldatalager](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. I den **ange lagring av Azure Data Lake anslutning** gör du följande:

   1. Välj din Data Lake lagring Gen2 kan kontot från ”lagringskontonamnet” i listan.
   2. Välj **Nästa**.
   
   ![Ange Azure Data Lake Store-konto](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. I den **välja filen eller mappen** anger **copyfroms3** som utdata mappnamn och välj **nästa**: 

    ![Ange utdatamapp](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. I den **inställningar** väljer **nästa** att använda standardinställningarna:

    ![Sidan Inställningar](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. I den **sammanfattning** , granskar du inställningarna och väljer **nästa**:

    ![Sammanfattningssida](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. I den **distribution sida**väljer **övervakaren** att övervaka pipelinen:

    ![Distributionssida](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Observera att fliken **Övervaka** till vänster väljs automatiskt. Den **åtgärder** kolumn innehåller länkar för att visa aktivitet kör information och att köra pipelinen:

    ![Övervaka pipelinekörningar](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Om du vill visa aktivitetskörningar som är associerade med pipeline kör, Välj den **visa aktiviteten körs** länken i den **åtgärder** kolumn. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Växla tillbaka till pipelinen körs visa, Välj den **Pipelines** längst upp. Om du vill uppdatera listan väljer du **Uppdatera**. 

    ![Övervaka aktivitetskörningar](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. För att övervaka körning av information för varje kopieringsaktiviteten, Välj den **information** länken (glasögon bild) under **åtgärder** i aktiviteten övervakningsvyn. Du kan övervaka information som mängden data som kopieras från källan till mottagare, överföring av data, utförande med motsvarande varaktighet och används konfigurationer:

    ![Övervakaraktiviteten kör information](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Kontrollera att data kopieras till ditt Data Lake lagring Gen2-konto.

## <a name="best-practice"></a>Bästa praxis

Kopiera när stora volymer av data från filbaserade data store du rekommenderas att:

- Partitionera filerna till 10TB till 20TB filuppsättning.
- För många samtidiga kopia körs för att undvika begränsning från käll- eller sink datalager utlöses inte. Du kan börja med en kopia köra och övervaka genomflödet och sedan gradvis lägga till fler efter behov.

## <a name="next-steps"></a>Nästa steg

* [Aktiviteten-kopia – översikt](copy-activity-overview.md)
* [Azure Data Lake lagring Gen2-koppling](connector-azure-data-lake-storage.md)
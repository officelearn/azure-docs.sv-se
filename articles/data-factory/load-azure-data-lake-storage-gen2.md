---
title: Läsa in data i Azure Data Lake Storage Gen2
description: Använd Azure Data Factory för att kopiera data till Azure Data Lake Storage Gen2
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417784"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Läs in data i Azure Data Lake Storage Gen2 med Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stor data analys, som är inbyggda i [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Det gör att du kan använda gränssnittet med dina data med hjälp av både fil systemet och objekt lagrings paradigm.

Azure Data Factory (ADF) är en helt hanterad molnbaserad data integrerings tjänst. Du kan använda tjänsten för att fylla i Lake med data från en omfattande uppsättning lokala och molnbaserade data lager och spara tid när du skapar dina analys lösningar. En detaljerad lista över anslutningar som stöds finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory erbjuder en skalbar lösning för data förflyttning. På grund av den skalbara arkitekturen i ADF kan den mata in data i ett högt data flöde. Mer information finns i [Kopiera aktivitets prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget Data Factory Kopiera data för att läsa in data från _Amazon Web Services S3-tjänsten_ i _Azure Data Lake Storage Gen2_. Du kan följa liknande steg för att kopiera data från andra typer av data lager.

>[!TIP]
>Information om hur du kopierar data från Azure Data Lake Storage Gen1 till Gen2 finns i [den här detaljerade genom gången](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Krav

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Storage konto med Data Lake Storage Gen2 aktiverat: om du inte har ett lagrings konto [skapar du ett konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* AWS-konto med en S3-Bucket som innehåller data: den här artikeln visar hur du kopierar data från Amazon S3. Du kan använda andra data lager genom att följa liknande steg.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **skapa en resurs** > **data och analys** > **Data Factory**:
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **ny data fabrik** anger du värden för de fält som visas i följande bild: 
      
   ![Sidan Ny datafabrik](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure Data Factory. Om du får felet "Data Factory name \"LoadADLSDemo\" är inte tillgängligt" anger du ett annat namn för data fabriken. Du kan till exempel använda namnet _**dittnamn**_**ADFTutorialDataFactory**. Försök att skapa data fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa data fabriken. 
    * **Resurs grupp**: Välj en befintlig resurs grupp i list rutan eller Välj alternativet för att **Skapa nytt** och ange namnet på en resurs grupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
    * **Version**: Välj **v2**.
    * **Plats**: Välj plats för data fabriken. Endast platser som stöds visas i listrutan. De data lager som används av Data Factory kan finnas på andra platser och regioner. 

3. Välj **Skapa**.
4. När du har skapat filen går du till din data fabrik. Du ser **Data Factory** start sida så som visas i följande bild: 
   
   ![Datafabrikens startsida](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Välj panelen **Författare och övervakare** för att starta dataintegreringsprogrammet på en separat flik.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Läsa in data i Azure Data Lake Storage Gen2

1. På sidan **Kom igång** väljer du panelen **Kopiera data** för att starta kopiera data-verktyget: 

   ![Panel för verktyget Kopiera data](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. På sidan **Egenskaper** anger du **CopyFromAmazonS3ToADLS** för fältet **uppgifts namn** och väljer **Nästa**:

    ![Sidan Egenskaper](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. På sidan **käll data lager** klickar du på **+ Skapa ny anslutning**:

    ![Sidan Källdatalager](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Välj **Amazon S3** i kopplings galleriet och välj **Fortsätt**
    
    ![Käll data lager S3-sida](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. På sidan **Ange Amazon S3-anslutning** utför du följande steg:

   1. Ange **åtkomst nyckelns ID-** värde.
   2. Ange nyckel värde för **hemlig åtkomst** .
   3. Klicka på **Testa anslutning** för att verifiera inställningarna och välj sedan **Slutför**.
   4. Du ser att en ny anslutning skapas. Välj **Nästa**.
   
      ![Ange Amazon S3-konto](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. På sidan **Välj indatafil eller mapp** bläddrar du till den mapp och fil du vill kopiera över. Välj mappen/filen **och välj sedan:**

    ![Välj indatafil eller mapp](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Ange kopieringsbeteendet genom att markera alternativen **Kopiera filer rekursivt** och **Binär kopia**. Välj **Nästa**:

    ![Ange mapp för utdata](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. På sidan **mål data lager** klickar du på **+ Skapa ny anslutning**och välj sedan **Azure Data Lake Storage Gen2**och välj **Fortsätt**:

    ![Sidan Måldatalager](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Utför följande steg på sidan **ange Azure Data Lake Storage anslutning** :

   1. Välj ditt Data Lake Storage Gen2-kapabla konto i list rutan "lagrings konto namn".
   2. Välj **Slutför** för att skapa anslutningen. Välj **Nästa**.
   
   ![Ange Azure Data Lake Storage Gen2 konto](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. På sidan **Välj utdatafil eller mapp** anger du **copyfroms3** som namn på utdata-mappen och väljer **Nästa**. ADF skapar motsvarande ADLS Gen2 fil system och undermappar under kopieringen om den inte finns.

    ![Ange mapp för utdata](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. På sidan **Inställningar** väljer du **Nästa** för att använda standardinställningarna:

    ![Sidan Inställningar](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. På sidan **Sammanfattning** granskar du inställningarna och väljer **Nästa**:

    ![Sammanfattningssida](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. På **sidan distribution**väljer du **övervakare** för att övervaka pipelinen:

    ![Distributionssida](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Observera att fliken **Övervaka** till vänster väljs automatiskt. I kolumnen **åtgärder** finns länkar för att Visa aktivitets körnings information och köra pipelinen igen:

    ![Övervaka pipelinekörningar](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Om du vill visa aktivitets körningar som är associerade med pipeline-körningen väljer du länken **Visa aktivitet kör** i kolumnen **åtgärder** . Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill växla tillbaka till vyn pipeline-körningar väljer du länken **pipelines** överst. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). 

    ![Övervaka aktivitetskörningar](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Om du vill övervaka körnings informationen för varje kopierings aktivitet väljer du länken **information** (glasögon-avbildning) under **åtgärder** i vyn aktivitets övervakning. Du kan övervaka information om data volymen som kopieras från källan till mottagaren, data genom strömning, körnings steg med motsvarande varaktighet och använda konfigurationer:

    ![Övervaka körnings information för aktivitet](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Verifiera att data har kopierats till ditt Data Lake Storage Gen2-konto.

## <a name="next-steps"></a>Nästa steg

* [Översikt över kopierings aktivitet](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-anslutning](connector-azure-data-lake-storage.md)

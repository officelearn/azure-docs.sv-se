---
title: "Läs in data till Azure Data Lake Store med hjälp av Azure Data Factory | Microsoft Docs"
description: "Använd Azure Data Factory för att kopiera data till Azure Data Lake Store"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 3f73cd65b0ceb3148ce8ceb83d7b4e1be1280077
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-data-lake-store-using-azure-data-factory"></a>Läs in data till Azure Data Lake Store med hjälp av Azure Data Factory

[Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) är en företagsomfattande storskalig lagringsplats för analytiska arbetsbelastningar för stordata. Med Azure Data Lake kan du samla in data av olika storlekar, former, typer och inmatningshastighet på en enda plats för drifts- och undersökande analyser.

Azure Data Factory är en helt hanterad molnbaserade data integration tjänst som kan användas för att fylla i lake med data från ditt befintliga system och sparar värdefull tid när du skapar din Analyslösningar. Här är de främsta fördelarna med att läsa in data i Azure Data Lake Store med hjälp av Azure Data Factory:

* **Enkelt att ställa in**: 5 intuitiva guiden med ingen skript som krävs.
* **Omfattande stöd för datalager**: inbyggt stöd för ett stort utbud av lokala och molnbaserade dataarkiv finns detaljerad lista i [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.
* **Skyddade och kompatibla**: data överförs över HTTPS eller ExpressRoute och tjänsten för global närvaro garanterar att dina data lämnar aldrig geografisk gräns
* **Högpresterande**: upp till 1 Gbit/s-datainläsning hastighet i Azure Data Lake Store. Mer information från [kopiera aktivitet prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget Data Factory kopieringsdata till **Läs in data från Amazon S3 till Azure Data Lake Store**. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

> [!NOTE]
>  Allmän information om funktionerna i Data Factory i Kopiera data till/från Azure Data Lake Store finns [kopiera data till och från Azure Data Lake Store med hjälp av Azure Data Factory](connector-azure-data-lake-store.md) artikel.
>
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
* **Azure Data Lake Store**. Om du inte har ett Data Lake Store-konto finns i [skapa ett Data Lake Store-konto](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account) artikel steg för att skapa en.
* **Amazon S3**. Den här artikeln visar hur du kopierar data från Amazon S3. Du kan använda andra dataarkiv genom att följa liknande steg.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Klicka på **NY** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/load-data-into-azure-data-lake-store/new-azure-data-factory-menu.png)
2. I den **nya data factory** anger du värdena som visas i följande skärmbild: 
      
     ![Ny data factory-sida](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
   * **Namn.** Ange ett globalt unikt namn för data factory. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory) och provar att skapa fabriken igen. Se [Data Factory - namnregler](naming-rules.md) artikel för namnregler för Data Factory-artefakter.
  
            `Data factory name "LoadADLSDemo" is not available`

    * **Prenumeration.** Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
    * **Resursgrupp.** Välj en befintlig resursgrupp från den nedrullningsbara listan eller välj **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
    * **Version.** Välj **V2 (förhandsgranskning)**.
    * **Plats.** Välj platsen för data factory. Platser som stöds endast visas i den nedrullningsbara listan. Datalager (Azure Data Lake Store, Azure Storage, Azure SQL Database, etc.) används av datafabriken kan vara i andra platser/regioner.

3. Klicka på **Skapa**.
4. När skapandet är klar går du till din data factory och du ser den **Data Factory** sidan som visas i bilden. Klicka på **författare & övervakaren** rutan för att starta programmet på Integration Data i en separat flik. 
   
   ![Datafabrikens startsida](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

## <a name="load-data-into-azure-data-lake-store"></a>Läs in data till Azure Data Lake Store

1. Klicka på sidan komma igång **kopieringsdata** rutan för att starta verktyget kopieringsdata. 

   ![Kopiera Data verktyget sida vid sida](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. I den **egenskaper** sida i verktyget kopieringsdata ange **CopyFromAmazonS3ToADLS** för den **aktivitet**, och klicka på **nästa**. 

    ![Kopiera Data verktyget-egenskapssidan](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. I den **källa datalagret** väljer **Amazon S3**, och klicka på **nästa**.

    ![Källa data store-sidan](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
4. I den **ange Amazon S3 anslutning** gör du följande: 
    1. Ange den **åtkomst-ID för**.
    2. Ange den **hemliga åtkomstnyckel**.
    3. Klicka på **Nästa**. 

        ![Ange Amazon S3 konto](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
5. I den **Välj inkommande filen eller mappen** sidan, navigera till mappen/filen som du vill kopiera över, markera och på **Välj**, klicka på **nästa**. 

    ![Välj filen eller mappen](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. I den **data målarkiv** väljer **Azure Data Lake Store**, och klicka på **nästa**. 

    ![Målsida data store](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

7. I den här sidan väljer du kopiera beteendet genom att kontrollera **kopiera filer rekursivt** och **binära kopiera** (kopiera filer som-är) alternativ. Klicka på **Nästa**.

    ![Ange utdatamapp](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)

8. I den **ange Data Lake Store anslutning** gör du följande: 

    1. Välj din Data Lake Store för den **Data Lake Store-kontonamnet**.
    2. Ange den service principal information, bland annat **klient**, **tjänsten ägar-ID**, och **Service principal key**.
    3. Klicka på **Nästa**. 

    > [!IMPORTANT]
    > I den här genomgången ska du använda en **tjänstens huvudnamn** att autentisera data lake store. Följ anvisningarna [här](connector-azure-data-lake-store.md#using-service-principal-authentication) och se till att du ger service principal rätt behörighet i Azure Data Lake Store.

    ![Ange Azure Data Lake Store-konto](./media/load-data-into-azure-data-lake-store/specify-adls.png)

9. I den **välja filen eller mappen** anger **copyfroms3**, klicka på **nästa**. 

    ![Ange utdatamapp](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)


10. I den **inställningar** klickar du på **nästa**. 

    ![Inställningssidan](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. I den **sammanfattning** , granskar du inställningarna och klicka på **nästa**.

    ![Sammanfattningssida](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. I den **distribution sida**, klickar du på **övervakaren** att övervaka pipeline (aktivitet).

    ![Distribution sida](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Observera att den **övervakaren** till vänster väljs automatiskt. Du vill visa länkar att visa aktivitet kör information och för att köra pipeline i den **åtgärder** kolumn. 

    ![Övervakaren pipeline körs](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Så här visar aktivitet körs som är kopplade till pipelinen kör **visa aktiviteten körs** länken i den **åtgärder** kolumn. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen, så att du ser endast en post. Växla tillbaka till pipelinen körs vyn klickar du på **Pipelines** längst upp. Klicka på **uppdatera** uppdatera listan. 

    ![Övervaka aktiviteten körs](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Du kan övervaka varje kopieringsaktiviteten körning information ytterligare genom att klicka på den **information** länken under **åtgärder** i aktiviteten övervakningsvyn. Den visar information, inklusive mängden data som kopieras från källan till mottagare, dataflöde, steg den går igenom med motsvarande varaktighet och används konfigurationer.

    ![Övervakaraktiviteten kör information](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Kontrollera att data kopieras till din Azure Data Lake Store. 

    ![Kontrollera Data Lake Store-utdata](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Nästa steg

Gå till följande artikel för att lära dig om Azure Data Lake Store-stöd: 

> [!div class="nextstepaction"]
>[Azure Data Lake Store-koppling](connector-azure-data-lake-store.md)
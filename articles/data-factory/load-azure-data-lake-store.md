---
title: Läsa in data i Azure Data Lake Storage Gen1
description: Använda Azure Data Factory för att kopiera data till Azure Data Lake Storage Gen1
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/17/2018
ms.openlocfilehash: 1b1b19814709451bdbbea97462c459149484e71f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415850"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Läsa in data i Azure Data Lake Storage Gen1 med hjälp av Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) (tidigare känt som Azure Data Lake Store) är en företagsomfattande hyperskala-lagringsplats för stordata analytiska arbetsbelastningar. Med DataSjölagring Gen1 kan du samla in data av valfri storlek, typ och inmatningshastighet. Data samlas in på en enda plats för operativa och undersökande analyser.

Azure Data Factory är en fullständigt hanterad molnbaserad dataintegrationstjänst. Du kan använda tjänsten för att fylla sjön med data från ditt befintliga system och spara tid när du skapar dina analyslösningar.

Azure Data Factory erbjuder följande fördelar för att läsa in data i Data Lake Storage Gen1:

* **Lätt att ställa in:** En intuitiv 5-stegsguide utan skript krävs.
* **Omfattande stöd för datalager**: Inbyggt stöd för en omfattande uppsättning lokala och molnbaserade datalager. En detaljerad lista finns i tabellen [över datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
* **Säker och kompatibel**: Data överförs via HTTPS eller ExpressRoute. Den globala tjänstnärvaron säkerställer att dina data aldrig lämnar den geografiska gränsen.
* **Hög prestanda:** Upp till 1 GB/s datainläsningshastighet i Data Lake Storage Gen1. Mer information finns i [Kopiera aktivitetsprestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder data fabrikskopieringsdataverktyget för att _läsa in data från Amazon S3 i Data Lake Storage Gen1_. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

> [!NOTE]
> Mer information finns i [Kopiera data till eller från DataSjölagring gen1 med hjälp av Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Krav

* Azure-prenumeration: Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* DataSjölagringsgenm1-konto: Om du inte har ett DataSjölagringsgenm1-konto läser du instruktionerna i [Skapa ett DataSjölagringsgenm1-konto](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: Denna artikel visar hur man kopierar data från Amazon S3. Du kan använda andra datalager genom att följa liknande steg.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **Skapa en resurs** > **Analytics** > **Data Factory:**
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **Ny datafabrik** anger du värden för de fält som visas i följande bild: 
      
   ![Sidan Ny datafabrik](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure-datafabrik. Om felet "Data fabriksnamn \"LoadADLSG1Demo\" inte är tillgängligt" anger du ett annat namn för datafabriken. Du kan till exempel använda namnet _**yourname**_**ADFTutorialDataFactory**. Försök att skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa datafabriken. 
    * **Resursgrupp:** Välj en befintlig resursgrupp i listrutan eller välj alternativet **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager som används av datafabriken kan finnas på andra platser och regioner. Dessa datalager inkluderar Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database och så vidare.

3. Välj **Skapa**.
4. När skapandet är klart går du till din datafabrik. Startsidan för **Data Factory** visas i följande bild: 
   
   ![Datafabrikens startsida](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Välj panelen **Författare och övervakare** för att starta dataintegreringsprogrammet på en separat flik.

## <a name="load-data-into-data-lake-storage-gen1"></a>Läsa in data i DataSjölagring Gen1

1. På sidan **Kom igång** väljer du panelen **Kopiera data** för att starta verktyget Kopiera data: 

   ![Panel för verktyget Kopiera data](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. På sidan **Egenskaper** anger du **CopyFromAmazonS3ToADLS** för fältet **Aktivitetsnamn** och väljer **Nästa:**

    ![Sidan Egenskaper](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. På sidan **Källa för datalager** klickar du på **+ Skapa ny anslutning:**

    ![Sidan Källdatalager](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Välj **Amazon S3**och välj **Fortsätt**
    
    ![Sidan Källdatalager s3](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. Gör följande steg på **sidan Ange Amazon S3-anslutning:** 
   1. Ange värdet **för Access-nyckel-ID.**
   2. Ange värdet **för den hemliga åtkomstnyckeln.**
   3. Välj **Slutför**.
   
      ![Ange Amazon S3-konto](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. Du kommer att se en ny anslutning. Välj **Nästa**.
   
   ![Ange Amazon S3-konto](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. På sidan **Välj indatafil eller mapp** bläddrar du till den mapp och fil du vill kopiera över. Markera mappen/filen, välj **Välj**och välj sedan **Nästa:**

    ![Välj indatafil eller mapp](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Välj kopieringsbeteendet genom att välja alternativen **Kopiera filer rekursivt** och **binär kopia** (kopiera filer enligt detta) . Välj **Nästa:**

    ![Ange utdatamapp](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. På sidan **Måldatalager** klickar du på **+ Skapa ny anslutning**och väljer sedan Azure Data Lake Storage **Gen1**och väljer **Fortsätt:**

    ![Sidan Måldatalager](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Gör följande på sidan **Ny länkad tjänst (Azure Data Lake Storage Gen1):** 

   1. Välj ditt DataSjölagringsgenm1-konto för **kontonamnet DataSjölager**.
   2. Ange **klienten**och välj Slutför.
   3. Välj **Nästa**.
   
   > [!IMPORTANT]
   > I den här genomgången använder du en hanterad identitet för Azure-resurser för att autentisera ditt Data Lake Storage Gen1-konto. Var noga med att ge MSI rätt behörigheter i Data Lake Storage Gen1 genom att följa [dessa instruktioner](connector-azure-data-lake-store.md#managed-identity).
   
   ![Ange datasjölagringsgenm1-konto](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. På sidan **Välj utdatafil eller mapp** anger du **copyfroms3** som utdatamappnamn och väljer **Nästa:** 

    ![Ange utdatamapp](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. På sidan **Inställningar** väljer du **Nästa:**

    ![Sidan Inställningar](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. På sidan **Sammanfattning** granskar du inställningarna och väljer **Nästa:**

    ![Sammanfattningssida](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. På **sidan Distribution**väljer du **Övervaka** för att övervaka pipelinen (uppgift):

    ![Distributionssida](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Observera att fliken **Övervaka** till vänster väljs automatiskt. Kolumnen **Åtgärder** innehåller länkar för att visa information om aktivitetskörning och för att köra pipelinen igen:

    ![Övervaka pipelinekörningar](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningen väljer du länken **Visa aktivitetskörningar** i kolumnen **Åtgärder.** Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill växla tillbaka till vyn pipelinekörningar väljer du länken **Pipelines** högst upp. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). 

    ![Övervaka aktivitetskörningar](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Om du vill övervaka körningsinformationen för varje kopieringsaktivitet väljer du länken **Information** under **Åtgärder** i aktivitetsövervakningsvyn. Du kan övervaka information som mängden data som kopieras från källan till diskhon, datadataflöde, körningssteg med motsvarande varaktighet och använda konfigurationer:

    ![Information om övervakning av aktivitetskörning](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Kontrollera att data kopieras till ditt DataSjölagringsgenm1-konto: 

    ![Verifiera datasjölagringgend1-utdata](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Nästa steg

Gå vidare till följande artikel om du vill veta mer om Support för Data Lake Storage Gen1: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1-anslutning](connector-azure-data-lake-store.md)

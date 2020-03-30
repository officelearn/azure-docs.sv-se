---
title: Läs in data till Azure SQL Data Warehouse
description: Använda Azure Data Factory för att kopiera data till Azure SQL Data Warehouse
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/22/2018
ms.openlocfilehash: dc6d8c9da749e7ee60713f4357dc6d46051d89f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131277"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Läsa in data till Azure SQL Data Warehouse med Azure Data Factory

[Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) är en molnbaserad, skalningsdatabas som kan bearbeta stora mängder data, både relationella och icke-relationella. SQL Data Warehouse bygger på den massivt parallella bearbetningsarkitektur (MPP) som är optimerad för arbetsbelastningar för företagsdatalager. Det erbjuder molnelasticitet med flexibiliteten att skala lagring och beräkna oberoende av varandra.

Det är nu enklare än någonsin att komma igång med Azure SQL Data Warehouse när du använder Azure Data Factory. Azure Data Factory är en fullständigt hanterad molnbaserad dataintegrationstjänst. Du kan använda tjänsten för att fylla i ett SQL Data Warehouse med data från ditt befintliga system och spara tid när du skapar dina analyslösningar.

Azure Data Factory erbjuder följande fördelar för inläsning av data i Azure SQL Data Warehouse:

* **Lätt att ställa in:** En intuitiv 5-stegsguide utan skript krävs.
* **Omfattande stöd för datalager**: Inbyggt stöd för en omfattande uppsättning lokala och molnbaserade datalager. En detaljerad lista finns i tabellen [över datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
* **Säker och kompatibel**: Data överförs via HTTPS eller ExpressRoute. Den globala tjänstnärvaron säkerställer att dina data aldrig lämnar den geografiska gränsen.
* **Oöverträffad prestanda med hjälp av PolyBase:** Polybase är det mest effektiva sättet att flytta data till Azure SQL Data Warehouse. Använd mellanlagringsblobfunktionen för att uppnå höga belastningshastigheter från alla typer av datalager, inklusive Azure Blob storage och Data Lake Store. (Polybase stöder Azure Blob storage och Azure Data Lake Store som standard.) Mer information finns i [Kopiera aktivitetsprestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder datafabrikskopieringsdataverktyget för att _läsa in data från Azure SQL Database i Azure SQL Data Warehouse_. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

> [!NOTE]
> Mer information finns i [Kopiera data till eller från Azure SQL Data Warehouse med hjälp av Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Krav

* Azure-prenumeration: Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure SQL Data Warehouse: Datalagret innehåller data som kopieras över från SQL-databasen. Om du inte har ett Azure SQL Data Warehouse läser du instruktionerna i [Skapa ett SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: Den här självstudien kopierar data från en Azure SQL-databas med Exempeldata för Adventure Works LT. Du kan skapa en SQL-databas genom att följa instruktionerna i [Skapa en Azure SQL-databas](../sql-database/sql-database-get-started-portal.md).
* Azure storage-konto: Azure Storage används som _mellanlagringsblob_ i masskopieringsåtgärden. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **Skapa en resursData** > **+ Analytics** > **Data Factory:**

   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **Ny datafabrik** anger du värden för de fält som visas i följande bild:

   ![Sidan Ny datafabrik](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)

    * **Namn**: Ange ett globalt unikt namn för din Azure-datafabrik. Om felet "Data fabriksnamn \"LoadSQLDWDemo\" inte är tillgängligt" anger du ett annat namn för datafabriken. Du kan till exempel använda namnet _**yourname**_**ADFTutorialDataFactory**. Försök att skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa datafabriken. 
    * **Resursgrupp:** Välj en befintlig resursgrupp i listrutan eller välj alternativet **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager som används av datafabriken kan finnas på andra platser och regioner. Dessa datalager inkluderar Azure Data Lake Store, Azure Storage, Azure SQL Database och så vidare.

3. Välj **Skapa**.
4. När skapandet är klart går du till din datafabrik. Startsidan för **Data Factory** visas i följande bild:

   ![Datafabrikens startsida](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Välj panelen **Författare och övervakare** för att starta dataintegreringsprogrammet på en separat flik.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Läs in data till Azure SQL Data Warehouse

1. På sidan **Kom igång** väljer du panelen **Kopiera data** för att starta verktyget Kopiera data:

   ![Panel för verktyget Kopiera data](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. På sidan **Egenskaper** anger du **CopyFromSQLToSQLDW** för fältet **Aktivitetsnamn** och väljer **Nästa:**

    ![Sidan Egenskaper](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Gör så här på sidan **Källdatalager:**

    a. Klicka på **+ Skapa ny anslutning:**

    ![Sidan Källdatalager](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Välj **Azure SQL Database** i galleriet och välj **Fortsätt**. Du kan skriva "SQL" i sökrutan för att filtrera kopplingarna.

    ![Välj Azure SQL DB](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. På sidan **Ny länkad tjänst** väljer du servernamnet och DB-namnet i listrutan och anger användarnamn och lösenord. Klicka på **Testa anslutning** för att verifiera inställningarna och välj sedan **Slutför**.

    ![Konfigurera Azure SQL DB](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Välj den nyligen skapade länkade tjänsten som källa och klicka sedan på **Nästa**.

    ![Välj källa för den länkade tjänsten](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. I de **välj tabeller som data ska kopieras från eller använda en anpassad frågesida** anger du **SalesLT** för att filtrera tabellerna. Välj rutan **(Markera alla)** om du vill använda alla tabeller för kopian och välj sedan **Nästa:**

    ![Välj källtabeller](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Gör så här på sidan **Måldatalager:**

    a. Klicka på **+ Skapa ny anslutning** för att lägga till en anslutning

    ![Sidan Sink-datalager](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Välj **Azure SQL Data Warehouse** i galleriet och välj **Nästa**.

    ![Välj Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. På sidan **Ny länkad tjänst** väljer du servernamnet och DB-namnet i listrutan och anger användarnamn och lösenord. Klicka på **Testa anslutning** för att verifiera inställningarna och välj sedan **Slutför**.

    ![Konfigurera Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Välj den nyligen skapade länkade tjänsten som mottagare och klicka sedan på **Nästa**.

    ![Välj mottagare för länkade tjänster](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. På sidan **Tabellmappning** granskar du innehållet och väljer **Nästa**. En intelligent tabellmappning visas. Källtabellerna mappas till måltabellerna baserat på tabellnamnen. Om det inte finns en källtabell i målet skapar Azure Data Factory en måltabell med samma namn som standard. Du kan också mappa en källtabell till en befintlig måltabell.

   > [!NOTE]
   > Automatisk tabellgenerering för SQL Data Warehouse-diskhonen gäller när SQL Server eller Azure SQL Database är källan. Om du kopierar data från ett annat källdatalager måste du förskapa schemat i diskhon Azure SQL Data Warehouse innan du kör datakopian.

   ![Sidan för tabellmappning](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. På sidan **Schemamappning** granskar du innehållet och väljer **Nästa**. Den intelligenta tabellmappningen baseras på kolumnnamnet. Om du låter Data Factory skapa tabellerna automatiskt kan konvertering av datatyp ske när det finns inkompatibiliteter mellan käll- och målbutikerna. Om det finns en datatypskonvertering som inte stöds mellan käll- och målkolumnen visas ett felmeddelande bredvid motsvarande tabell.

    ![Sidan för schemamappning](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Gör så här på sidan **Inställningar:**

    a. I avsnittet **Mellanlagringsinställningar** klickar du på **+ Nytt** för att nya en mellanlagringslagring ska nysas. Lagringen används för mellanlagring av data innan den läses in i SQL Data Warehouse med hjälp av PolyBase. När kopian är klar rensas interimsdata i Azure Storage automatiskt.

    ![Konfigurera mellanlagring](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. På sidan **Ny länkad tjänst** väljer du ditt lagringskonto och väljer **Slutför**.

    ![Konfigurera Azure Storage](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. Avmarkera **standardalternativet Använd typ** i avsnittet Avancerade **inställningar** och välj sedan **Nästa**.

    ![Konfigurera PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. På sidan **Sammanfattning** granskar du inställningarna och väljer **Nästa:**

    ![Sammanfattningssida](./media/load-azure-sql-data-warehouse/summary-page.png)
1. På **sidan Distribution**väljer du **Övervaka** för att övervaka pipelinen (uppgift):

    ![Distributionssida](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Observera att fliken **Övervaka** till vänster väljs automatiskt. Kolumnen **Åtgärder** innehåller länkar för att visa information om aktivitetskörning och för att köra pipelinen igen:

    ![Övervaka pipelinekörningar](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningen väljer du länken **Visa aktivitetskörningar** i kolumnen **Åtgärder.** Om du vill växla tillbaka till vyn pipelinekörningar väljer du länken **Pipelines** högst upp. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera).

    ![Övervaka aktivitetskörningar](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Om du vill övervaka körningsinformationen för varje kopieringsaktivitet väljer du länken **Information** under **Åtgärder** i aktivitetsövervakningsvyn. Du kan övervaka information som mängden data som kopieras från källan till diskhon, datadataflöde, körningssteg med motsvarande varaktighet och använda konfigurationer:

    ![Information om övervakning av aktivitetskörning](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Nästa steg

Gå vidare till följande artikel om du vill veta mer om Azure SQL Data Warehouse-stöd:

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse-anslutning](connector-azure-sql-data-warehouse.md)

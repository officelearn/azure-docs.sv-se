---
title: Läs in data till Azure SQL Data Warehouse
description: Använd Azure Data Factory för att kopiera data till Azure SQL Data Warehouse
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
ms.openlocfilehash: 05e87258576bceee2e1bbba7ec5ef6ea5ead4924
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440244"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Läs in data i Azure SQL Data Warehouse med Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) är en molnbaserad, skalbar databas som kan bearbeta stora mängder data, både relationella och icke-relationella. SQL Data Warehouse bygger på den minnes trycks arkitektur (massivt parallell bearbetning) som är optimerad för arbets belastningar för företags data lager. Det ger moln elastiskhet med flexibiliteten att skala lagring och beräkning oberoende av varandra.

Att komma igång med Azure SQL Data Warehouse är nu enklare än någonsin när du använder Azure Data Factory. Azure Data Factory är en helt hanterad molnbaserad data integrerings tjänst. Du kan använda tjänsten för att fylla i en SQL Data Warehouse med data från ditt befintliga system och spara tid när du skapar dina analys lösningar.

Azure Data Factory erbjuder följande fördelar för att läsa in data i Azure SQL Data Warehouse:

* **Enkelt att konfigurera**: en intuitiv 5-stegs guide utan skript krävs.
* **Stöd för omfattande data lager**: inbyggt stöd för en omfattande uppsättning lokala och molnbaserade data lager. En detaljerad lista finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
* **Säkert och kompatibelt**: data överförs via https eller ExpressRoute. Med den globala tjänstens närvaro ser du till att dina data aldrig lämnar den geografiska gräns.
* **Oöverträffade prestanda med hjälp av PolyBase**: Polybase är det mest effektiva sättet att flytta data till Azure SQL Data Warehouse. Använd funktionen för mellanlagring av BLOB för att uppnå höga belastnings hastigheter från alla typer av data lager, inklusive Azure Blob Storage och Data Lake Store. (PolyBase stöder Azure Blob Storage och Azure Data Lake Store som standard.) Mer information finns i [Kopiera aktivitets prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget Data Factory Kopiera data för att _läsa in data från Azure SQL Database till Azure SQL Data Warehouse_. Du kan följa liknande steg för att kopiera data från andra typer av data lager.

> [!NOTE]
> Mer information finns i [Kopiera data till eller från Azure SQL Data Warehouse med Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Krav

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure SQL Data Warehouse: data lagret innehåller de data som kopieras från SQL-databasen. Om du inte har ett Azure SQL Data Warehouse kan du läsa anvisningarna i [skapa en SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: den här självstudien kopierar data från en Azure SQL-databas med Adventure Works LT-exempel data. Du kan skapa en SQL-databas genom att följa anvisningarna i [skapa en Azure SQL-databas](../sql-database/sql-database-get-started-portal.md). 
* Azure Storage-konto: Azure Storage används som _mellanlagrings_ -BLOB i Mass kopierings åtgärden. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **skapa en resurs** > **data och analys** > **Data Factory**: 
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **ny data fabrik** anger du värden för de fält som visas i följande bild:
      
   ![Sidan Ny datafabrik](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure Data Factory. Om du får felet "data fabriks namnet \"LoadSQLDWDemo\" inte är tillgängligt" anger du ett annat namn på data fabriken. Du kan till exempel använda namnet _**dittnamn**_ **ADFTutorialDataFactory**. Försök att skapa data fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa data fabriken. 
    * **Resurs grupp**: Välj en befintlig resurs grupp i list rutan eller Välj alternativet för att **Skapa nytt** och ange namnet på en resurs grupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
    * **Version**: Välj **v2**.
    * **Plats**: Välj plats för data fabriken. Endast platser som stöds visas i listrutan. De data lager som används av Data Factory kan finnas på andra platser och regioner. Dessa data lager omfattar Azure Data Lake Store, Azure Storage, Azure SQL Database och så vidare.

3. Välj **Skapa**.
4. När du har skapat filen går du till din data fabrik. Du ser **Data Factory** start sida så som visas i följande bild:
   
   ![Datafabrikens startsida](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Välj panelen **Författare och övervakare** för att starta dataintegreringsprogrammet på en separat flik.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Läs in data till Azure SQL Data Warehouse

1. På sidan **Kom igång** väljer du panelen **Kopiera data** för att starta kopiera data-verktyget:

   ![Panel för verktyget Kopiera data](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. På sidan **Egenskaper** anger du **CopyFromSQLToSQLDW** för fältet **uppgifts namn** och väljer **Nästa**:

    ![Sidan Egenskaper](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Utför följande steg på sidan **käll data lager** :

    a. Klicka på **+ Skapa ny anslutning**:

    ![Sidan Källdatalager](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Välj **Azure SQL Database** i galleriet och välj **Fortsätt**. Du kan skriva "SQL" i sökrutan för att filtrera kopplingarna.

    ![Välj Azure SQL DB](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. På sidan **ny länkad tjänst** väljer du Server namnet och databas namnet i list rutan och anger användar namn och lösen ord. Klicka på **Testa anslutning** för att verifiera inställningarna och välj sedan **Slutför**.
   
    ![Konfigurera Azure SQL DB](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Välj den nyligen skapade länkade tjänsten som källa och klicka sedan på **Nästa**.

    ![Välj källa för den länkade tjänsten](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. I listan **Välj tabeller som data ska kopieras från eller Använd en anpassad fråga** anger du **SalesLT** för att filtrera tabellerna. Välj rutan **(Markera alla)** om du vill använda alla tabeller för kopian och välj sedan **Nästa**: 

    ![Välj käll tabeller](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Utför följande steg på sidan **mål data lager** :

    a. Klicka på **+ Skapa ny anslutning** för att lägga till en anslutning

    ![Sidan data lager för mottagare](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Välj **Azure SQL Data Warehouse** i galleriet och välj **Nästa**.

    ![Välj Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. På sidan **ny länkad tjänst** väljer du Server namnet och databas namnet i list rutan och anger användar namn och lösen ord. Klicka på **Testa anslutning** för att verifiera inställningarna och välj sedan **Slutför**.
   
    ![Konfigurera Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Välj den nyligen skapade länkade tjänsten som mottagare och klicka sedan på **Nästa**.

    ![Välj mottagare för länkade tjänster](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. På sidan **tabell mappning** granskar du innehållet och väljer **Nästa**. En intelligent tabell mappning visas. Käll tabellerna mappas till mål tabellerna baserat på tabell namn. Om en käll tabell inte finns i målet skapar Azure Data Factory en mål tabell med samma namn som standard. Du kan också mappa en käll tabell till en befintlig mål tabell. 

   > [!NOTE]
   > Automatisk tabell skapande för SQL Data Warehouse Sink gäller när SQL Server eller Azure SQL Database är källan. Om du kopierar data från ett annat käll data lager måste du skapa schemat i Sink-Azure SQL Data Warehouse innan du kör data kopieringen.

   ![Sidan för tabellmappning](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. På sidan **schema mappning** granskar du innehållet och väljer **Nästa**. Den intelligenta tabell mappningen baseras på kolumn namnet. Om du låter Data Factory skapa tabeller automatiskt, kan data typ konverteringen ske när det finns inkompatibiliteter mellan käll-och mål arkiven. Om det finns en data typs konvertering som inte stöds mellan kolumnen källa och mål visas ett fel meddelande bredvid motsvarande tabell.

    ![Sidan för schemamappning](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Utför följande steg på sidan **Inställningar** :

    a. I avsnittet **mellanlagrings inställningar** klickar du på **+ ny** för att välja ny mellanlagring av mellanlagring. Lagrings utrymmet används för att mellanlagra data innan de läses in i SQL Data Warehouse med PolyBase. När kopieringen är klar rensas interims data i Azure Storage automatiskt. 

    ![Konfigurera mellanlagring](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. På sidan **ny länkad tjänst** väljer du ditt lagrings konto och väljer **Slutför**.
   
    ![Konfigurera Azure Storage](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. I avsnittet **Avancerade inställningar** avmarkerar du alternativet **Använd typ standard** och väljer sedan **Nästa**.

    ![Konfigurera PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. På sidan **Sammanfattning** granskar du inställningarna och väljer **Nästa**:

    ![Sammanfattningssida](./media/load-azure-sql-data-warehouse/summary-page.png)
1. På **sidan distribution**väljer du **övervakare** för att övervaka pipelinen (aktiviteten):

    ![Distributionssida](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Observera att fliken **Övervaka** till vänster väljs automatiskt. I kolumnen **åtgärder** finns länkar för att Visa aktivitets körnings information och köra pipelinen igen: 

    ![Övervaka pipelinekörningar](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Om du vill visa aktivitets körningar som är associerade med pipeline-körningen väljer du länken **Visa aktivitet kör** i kolumnen **åtgärder** . Om du vill växla tillbaka till vyn pipeline-körningar väljer du länken **pipelines** överst. Om du vill uppdatera listan väljer du **Uppdatera**. 

    ![Övervaka aktivitetskörningar](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Om du vill övervaka körnings informationen för varje kopierings aktivitet väljer du länken **information** under **åtgärder** i vyn aktivitets övervakning. Du kan övervaka information om data volymen som kopieras från källan till mottagaren, data genom strömning, körnings steg med motsvarande varaktighet och använda konfigurationer:

    ![Övervaka körnings information för aktivitet](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Nästa steg

Gå vidare till följande artikel om du vill lära dig mer om Azure SQL Data Warehouse-support: 

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse koppling](connector-azure-sql-data-warehouse.md)

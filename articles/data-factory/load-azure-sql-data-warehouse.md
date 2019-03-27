---
title: Läs in data till Azure SQL Data Warehouse med hjälp av Azure Data Factory | Microsoft Docs
description: Använda Azure Data Factory för att kopiera data till Azure SQL Data Warehouse
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 6a7e0a27d3cda4193a04467d541f851a9e57fa46
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446720"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Läs in data till Azure SQL Data Warehouse med hjälp av Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) är en molnbaserad skalbar databas som kan bearbeta massiva mängder data, både relationsdata och icke-relationsdata. SQL Data Warehouse bygger på arkitektur för massiv parallellbearbetning (MPP) som är optimerad för arbetsbelastningar med informationslager enterprise. Den erbjuder molnelasticitet med flexibiliteten att skalas lagring och beräkning oberoende av varandra.

Komma igång med Azure SQL Data Warehouse är nu enklare än någonsin när du använder Azure Data Factory. Azure Data Factory är en fullständigt hanterad molnbaserad dataintegreringstjänst. Du kan använda tjänsten för att fylla i ett SQL Data Warehouse med data från ditt befintliga system och spara tid när du skapar Analyslösningar.

Azure Data Factory erbjuder följande fördelar vid inläsning av data i Azure SQL Data Warehouse:

* **Enkelt att konfigurera**: En intuitiv 5-steg-guide med inga skript som krävs.
* **Omfattande stöd för datalager**: Inbyggt stöd för ett stort utbud av lokala och molnbaserade datalager. En detaljerad lista finns i tabellen med [datalager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
* **Säker och kompatibel**: Data överförs via HTTPS- eller ExpressRoute. Tjänsten för global närvaro säkerställer att dina data aldrig lämnar geografisk gräns.
* **Oöverträffade prestanda med hjälp av PolyBase**: Polybase är det effektivaste sättet att flytta data till Azure SQL Data Warehouse. Använd funktionen mellanlagring blob för att uppnå hög belastning hastigheter från alla typer av datalager, som Azure Blob storage och Data Lake Store. (Polybase stöder Azure Blob storage och Azure Data Lake Store som standard.) Mer information finns i [kopiera aktivitet prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget kopieringsdata i Data Factory för att _läsa in data från Azure SQL Database till Azure SQL Data Warehouse_. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

> [!NOTE]
> Mer information finns i [kopiera data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory](connector-azure-sql-data-warehouse.md).

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure SQL Data Warehouse: Datalagret innehåller de data som kopieras från SQL-databasen. Om du inte har en Azure SQL Data Warehouse, se anvisningarna i [skapa ett SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: Den här självstudien kopierar data från en Azure SQL database med Adventure Works LT-exempeldata. Du kan skapa en SQL-databas genom att följa instruktionerna i [skapa en Azure SQL database](../sql-database/sql-database-get-started-portal.md). 
* Azure lagringskonto: Azure Storage används som den _mellanlagring_ blob i masskopieringsåtgärden. Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-quickstart-create-account.md).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På menyn till vänster väljer **skapa en resurs** > **Data och analys** > **Data Factory**: 
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. I den **ny datafabrik** anger värden för fälten som visas i följande bild:
      
   ![Sida för ny datafabrik](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure data factory. Om du får felet ”datafabriksnamnet \"LoadSQLDWDemo\" är inte tillgänglig”, ange ett annat namn för data factory. Du kan till exempel använda namnet  _**dittnamn**_**ADFTutorialDataFactory**. Försök att skapa datafabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa data factory. 
    * **Resursgrupp**: Välj en befintlig resursgrupp från den nedrullningsbara listan eller Välj den **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för datafabriken. Endast platser som stöds visas i listrutan. De datalager som används av data factory kan finnas på andra platser och regioner. Dessa datalager omfattar Azure Data Lake Store, Azure Storage, Azure SQL Database och så vidare.

3. Välj **Skapa**.
4. När datafabriken har skapats går du till din datafabrik. Du ser den **Data Factory** startsida, enligt följande bild:
   
   ![Datafabrikens startsida](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Välj den **författare och Övervakare** att starta Dataintegrationsprogrammet i en separat flik.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Läs in data till Azure SQL Data Warehouse

1. I den **börjar** väljer den **kopieringsdata** att starta verktyget kopieringsdata:

   ![Panel för verktyget Kopiera data](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. I den **egenskaper** anger **CopyFromSQLToSQLDW** för den **aktivitetsnamn** och markerar **nästa**:

    ![Sidan Egenskaper](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. I den **källdatalagret** utför du följande steg:

    a. Klicka på **+ Skapa ny anslutning**:

    ![Sidan Källdatalager](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Välj **Azure SQL Database** i galleriet och välj **Fortsätt**. Du kan skriva ”SQL” i sökrutan för att filtrera kopplingarna.

    ![Välj Azure SQL DB](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. I den **ny länkad tjänst** sidan Välj servernamn och DB-namn i listrutan och ange användarnamn och lösenord. Klicka på **Testanslutning** för att verifiera inställningarna kan sedan välja **Slutför**.
   
    ![Konfigurera Azure SQL DB](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Välj den nyligen skapade länkade tjänsten som källa och klicka sedan på **Nästa**.

    ![Välj källa för den länkade tjänsten](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. I den **Välj tabellen som att kopiera data eller använda en anpassad fråga** anger **SalesLT** att filtrera tabellerna. Välj den **(Välj alla)** för att använda alla tabeller för kopian och väljer sedan **nästa**: 

    ![Välj källtabellerna](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. I den **måldatalager** utför du följande steg:

    a. Klicka på **+ Skapa ny anslutning** för att lägga till en anslutning

    ![Sidan för mottagare](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Välj **Azure SQL Data Warehouse** i galleriet och välj **nästa**.

    ![Välj Azure SQL DW](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. I den **ny länkad tjänst** sidan Välj servernamn och DB-namn i listrutan och ange användarnamn och lösenord. Klicka på **Testanslutning** för att verifiera inställningarna kan sedan välja **Slutför**.
   
    ![Konfigurera Azure SQL DW](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Välj den nyligen skapade länkade tjänsten som mottagare och klicka sedan på **Nästa**.

    ![Välj mottagare för länkade tjänster](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. I den **tabellmappning** granskar innehållet, och välj **nästa**. En intelligent tabellmappning visar. Källtabellerna mappas till måltabellerna baserat på tabellnamn. Om en källtabellen inte finns i målet, skapar Azure Data Factory en tabell med destinationer med samma namn som standard. Du kan också mappa en källtabellen till en befintlig måltabellen. 

   > [!NOTE]
   > Automatisk tabell skapas för SQL Data Warehouse-mottagaren gäller när SQL Server eller Azure SQL Database är källan. Om du kopierar data från en annan källdatalager kan behöva du skapa schemat i Azure SQL Data Warehouse-mottagaren innan du kör Datakopieringen.

   ![Sidan för tabellmappning](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. I den **schemamappning** granskar innehållet, och välj **nästa**. Intelligent tabellmappning baseras på kolumnnamnet. Om du låter Data Factory automatiskt skapa tabellerna kan data typkonvertering inträffa när det finns kompatibilitetsrelaterade problem mellan källa och mål. Om det finns en typkonvertering för data som inte stöds mellan källa och mål-kolumnen, visas ett felmeddelande visas bredvid motsvarande register.

    ![Sidan för schemamappning](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. I den **inställningar** utför du följande steg:

    a. I **mellanlagring inställningar** klickar du på **+ ny** till nya en mellanlagrings lagring. Lagringsutrymmet används för att mellanlagra data innan den läser in i SQL Data Warehouse med PolyBase. När kopieringen är klar, rensas automatiskt den tillfälliga data i Azure Storage. 

    ![Konfigurera mellanlagring](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. I den **ny länkad tjänst** väljer du ditt lagringskonto, och välj **Slutför**.
   
    ![Konfigurera Azure Storage](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. I den **avancerade inställningar** avsnittet, avmarkera de **Använd förvald** och sedan välja **nästa**.

    ![Konfigurera PolyBase](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. I den **sammanfattning** , granskar du inställningarna och välj **nästa**:

    ![Sammanfattningssida](./media/load-azure-sql-data-warehouse/summary-page.png)
1. I den **distributionssida**väljer **övervakaren** att övervaka pipelinen (aktiviteten):

    ![Distributionssida](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. Observera att fliken **Övervaka** till vänster väljs automatiskt. Den **åtgärder** -kolumnen innehåller länkar för att visa information om aktivitetskörningar och köra pipelinen på nytt: 

    ![Övervaka pipelinekörningar](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Om du vill visa de aktivitetskörningar som är associerade med pipelinekörningen, väljer den **visa Aktivitetskörningar** länken i den **åtgärder** kolumn. Växla tillbaka till vyn med pipelinekörningar, Välj den **Pipelines** länken längst upp. Om du vill uppdatera listan väljer du **Uppdatera**. 

    ![Övervaka aktivitetskörningar](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. För att övervaka körning-information för varje kopieringsaktiviteten, Välj den **information** länka **åtgärder** i övervakningsvyn-aktivitet. Du kan övervaka information som mängden data som kopieras från källan till mottagare, dataflöde, utförande med motsvarande tid och används konfigurationer:

    ![Övervaka aktivitetskörningsinformation](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Nästa steg

Fortsätt till följande artikel om du vill veta mer om Azure SQL Data Warehouse-stöd: 

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse-anslutningsapp](connector-azure-sql-data-warehouse.md)

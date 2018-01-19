---
title: "Läs in data till Azure SQL Data Warehouse med Azure Data Factory | Microsoft Docs"
description: "Använd Azure Data Factory för att kopiera data till Azure SQL Data Warehouse"
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
ms.openlocfilehash: 36e24da50386d1abc441e2beb09f570a9612a346
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-using-azure-data-factory"></a>Läs in data till Azure SQL Data Warehouse med Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) är en molnbaserad skalbar databas kan bearbeta massiva mängder data, relationell såväl som icke-relationell.  Bygger på arkitektur med massivt parallell bearbetning (MPP) och är SQL Data Warehouse optimerad för arbetsbelastningar på företag data warehouse.  Den erbjuder molntjänster elasticitet möjlighet att skala lagring och beräkning oberoende av varandra.

Komma igång med Azure SQL Data Warehouse är nu enklare än någonsin med **Azure Data Factory**.  Azure Data Factory är en helt hanterad molnbaserade data integration tjänst som kan användas för att fylla i en SQL Data Warehouse med data från ditt befintliga system och sparar värdefull tid när du utvärderar SQL Data Warehouse och skapa din analytics lösningar. Här är de främsta fördelarna med att läsa in data i Azure SQL Data Warehouse med Azure Data Factory:

* **Enkelt att ställa in**: 5 intuitiva guiden med ingen skript som krävs.
* **Omfattande stöd för datalager**: inbyggt stöd för ett stort utbud av lokala och molnbaserade dataarkiv finns detaljerad lista i [stöds datalager](copy-activity-overview.md#supported-data-stores-and-formats) tabell.
* **Skyddade och kompatibla**: data överförs över HTTPS eller ExpressRoute och tjänsten för global närvaro garanterar att dina data lämnar aldrig geografisk gräns
* **Enastående prestanda genom att använda PolyBase**: med Polybase är det mest effektiva sättet att flytta data till Azure SQL Data Warehouse. Funktionen fristående blob kan uppnå du hög belastning hastigheter från alla typer av datalager utöver Azure Blob storage och Data Lake Store där Polybase stöder som standard. Mer information från [kopiera aktivitet prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget Data Factory kopieringsdata till **Läs in data från Azure SQL-databas till Azure SQL Data Warehouse**. Du kan följa liknande steg för att kopiera data från andra typer av datalager.

> [!NOTE]
>  Allmän information om funktionerna i Data Factory i Kopiera data till/från Azure SQL Data Warehouse finns [kopiera data till och från Azure SQL Data Warehouse med hjälp av Azure Data Factory](connector-azure-sql-data-warehouse.md) artikel.
>
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [Kopieringsaktiviteten i V1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
* **Azure SQL Data Warehouse**. Det här datalagret innehåller de data som kopieras från SQL Database. Om du inte har något Azure SQL Database Warehouse kan du läsa om att skapa ett i artikeln [Skapa ett Azure SQL Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* **Azure SQL Database**. Den här kursen kopierar data från en Azure SQL Database med Adventure Works LT exempeldata, kan du skapa en följande den [skapa en Azure SQL database](../sql-database/sql-database-get-started-portal.md) artikel. 
* **Azure Storage-konto**. Azure Storage används som **mellanlagring** blob i masskopieringsåtgärd. Om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-create-storage-account.md#create-a-storage-account) .

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Klicka på **NY** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. I den **nya data factory** anger du värdena som visas i följande skärmbild:
      
     ![Ny data factory-sida](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
   * **Namn.** Ange ett globalt unikt namn för data factory. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory) och provar att skapa fabriken igen. Se [Data Factory - namnregler](naming-rules.md) artikel för namnregler för Data Factory-artefakter.
  
            `Data factory name "LoadSQLDWDemo" is not available`

    * **Prenumeration.** Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
    * **Resursgrupp.** Välj en befintlig resursgrupp från den nedrullningsbara listan eller välj **Skapa nytt** och ange namnet på en resursgrupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
    * **Version.** Välj **V2 (förhandsgranskning)**.
    * **Plats.** Välj platsen för data factory. Platser som stöds endast visas i den nedrullningsbara listan. Datalager (Azure Storage, Azure SQL Database, etc.) används av datafabriken kan vara i andra platser/regioner. 

3. Klicka på **Skapa**.
4. När skapandet är klar går du till din data factory och du ser den **Data Factory** sidan som visas i bilden. Klicka på **författare & övervakaren** rutan för att starta programmet på Integration Data i en separat flik.
   
   ![Datafabrikens startsida](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

## <a name="load-data-into-azure-sql-data-warehouse"></a>Läs in data till Azure SQL Data Warehouse

1. Klicka på sidan komma igång **kopieringsdata** rutan för att starta verktyget kopieringsdata. 

   ![Kopiera Data verktyget sida vid sida](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. I den **egenskaper** sida i verktyget kopieringsdata ange **CopyFromSQLToSQLDW** för den **aktivitet**, och klicka på **nästa**. 

    ![Kopiera Data verktyget-egenskapssidan](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. I den **källa datalagret** väljer **Azure SQL Database**, och klicka på **nästa**.

    ![Källa data store-sidan](./media/load-azure-sql-data-warehouse/specify-source.png)
4. I den **anger Azure SQL-databas** gör du följande: 
    1. Välj din Azure SQL-server för den **servernamn**.
    2. Välj din Azure SQL-databasen för den **databasnamnet**.
    3. Ange namnet på användaren **användarnamn**.
    4. Ange lösenord för användaren **lösenord**.
    5. Klicka på **Nästa**. 

        ![Ange Azure SQL-databas](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. I den **Välj tabeller som du vill kopiera data eller använda en anpassad fråga** sidan, filtrera tabellerna genom att ange **SalesLT** i textrutan, kontrollera den **(Markera allt)** Om du vill markera alla tabeller och klicka sedan på **nästa**. 

    ![Välj filen eller mappen](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. I den **data målarkiv** väljer **Azure SQL Data Warehouse**, och klicka på **nästa**. 

    ![Målsida data store](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. I den **ange Azure SQL Data Warehouse** gör du följande: 

    1. Välj din Azure SQL-server för den **servernamn**.
    2. Välj Azure SQL Data Warehouse för den **databasnamnet**.
    3. Ange namnet på användaren **användarnamn**.
    4. Ange lösenord för användaren **lösenord**.
    5. Klicka på **Nästa**. 

        ![Ange Azure SQL Data Warehouse](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. I den **tabell mappning** , granska och klicka på **nästa**. Mappningen för en intelligent tabellen visas maps källa måltabell baserat på namn. Om tabellen inte finns i skapar målet, som standard Azure Data Factory en med samma namn. Du kan också välja att mappa till en befintlig tabell. 

    > [!NOTE]
    > Automatiskt skapande av tabell för SQL Data Warehouse sink används när SQL Server eller Azure SQL Database är källan. Om du kopierar data från andra dataarkiv källa skapa du pre schemat i kanalmottagare Azure SQL Data Warehouse före kopiering av data.

    ![Tabell mappning sida](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. I den **schemamappning** , granska och klicka på **nästa**. Intelligent mappningen baserat på kolumnnamn. Om du väljer att låta Data Factory automatiskt skapa tabellerna kan rätt datatypkonvertering inträffa om du behöver åtgärda inkompatibilitet mellan käll- och lagrar. Om det finns en typkonvertering för data som inte stöds mellan käll- och kolumnen, visas ett felmeddelande om du tillsammans med motsvarande register.

    ![Sidan för mappning av schemat](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. I den **inställningar** väljer du ett Azure Storage i den **lagringskontonamnet** listrutan. Den används för mellanlagring av data innan den läses in i SQL Data Warehouse med PolyBase. När kopieringen är klar rensas mellanliggande data i lagring automatiskt. 

    Avmarkera alternativet ”Använd standard” under ”avancerade inställningar för”.

    ![Inställningssidan](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. I den **sammanfattning** , granskar du inställningarna och klicka på **nästa**.

    ![Sammanfattningssida](./media/load-azure-sql-data-warehouse/summary-page.png)
13. I den **distribution sida**, klickar du på **övervakaren** att övervaka pipeline (aktivitet).

    ![Distribution sida](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Observera att den **övervakaren** till vänster väljs automatiskt. Du vill visa länkar att visa aktivitet kör information och för att köra pipeline i den **åtgärder** kolumn. 

    ![Övervakaren pipeline körs](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Så här visar aktivitet körs som är kopplade till pipelinen kör **visa aktiviteten körs** länken i den **åtgärder** kolumn. Det finns 10 kopiera aktiviteter i pipelinen, varje kopierar en tabell med data. Växla tillbaka till pipelinen körs vyn klickar du på **Pipelines** längst upp. Klicka på **uppdatera** uppdatera listan. 

    ![Övervaka aktiviteten körs](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. Du kan övervaka varje kopieringsaktiviteten körning information ytterligare genom att klicka på den **information** länken under **åtgärder** i aktiviteten övervakningsvyn. Den visar information, inklusive mängden data som kopieras från källan till mottagare, dataflöde, steg den går igenom med motsvarande varaktighet och används konfigurationer.

    ![Övervakaraktiviteten kör information](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Nästa steg

Gå till följande artikel för att lära dig om Azure SQL Data Warehouse-stöd: 

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse-koppling](connector-azure-sql-data-warehouse.md)
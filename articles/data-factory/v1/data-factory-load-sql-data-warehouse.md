---
title: Läs in terabyte data till SQL Data Warehouse | Microsoft Docs
description: Visar hur 1 TB data kan läsas in i Azure SQL Data Warehouse med Azure Data Factory under 15 minuter
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6a7f31cf541bc1cccd3a5d565a0d3a223ccd3aee
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045175"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns [kopiera data till och från Azure SQL Data Warehouse med hjälp av Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) är en molnbaserad skalbar databas kan bearbeta massiva mängder data, relationell såväl som icke-relationell.  Bygger på arkitektur med massivt parallell bearbetning (MPP) och är SQL Data Warehouse optimerad för arbetsbelastningar på företag data warehouse.  Den erbjuder molntjänster elasticitet möjlighet att skala lagring och beräkning oberoende av varandra.

Komma igång med Azure SQL Data Warehouse är nu enklare än någonsin med **Azure Data Factory**.  Azure Data Factory är en helt hanterad molnbaserade data integration tjänst som kan användas för att fylla i en SQL Data Warehouse med data från ditt befintliga system och sparar värdefull tid när du utvärderar SQL Data Warehouse och skapa din analytics lösningar. Här är de främsta fördelarna med att läsa in data i Azure SQL Data Warehouse med Azure Data Factory:

* **Enkelt att ställa in**: 5 intuitiva guiden med ingen skript som krävs.
* **Omfattande stöd för datalager**: inbyggt stöd för ett stort utbud av lokala och molnbaserade dataarkiv.
* **Skyddade och kompatibla**: data överförs över HTTPS eller ExpressRoute och tjänsten för global närvaro garanterar att dina data lämnar aldrig geografisk gräns
* **Enastående prestanda genom att använda PolyBase** – med Polybase är det mest effektiva sättet att flytta data till Azure SQL Data Warehouse. Funktionen fristående blob kan uppnå du hög belastning hastigheter från alla typer av datalager utöver Azure Blob storage, Polybase stöder som standard.

Den här artikeln visar hur du använder guiden för Data Factory kopiera att läsa in 1 TB data från Azure Blobblagring till Azure SQL Data Warehouse under 15 minuter på över 1,2 Gbit/s genomströmning.

Den här artikeln innehåller stegvisa instruktioner för att flytta data till Azure SQL Data Warehouse med hjälp av guiden Kopiera.

> [!NOTE]
>  Allmän information om funktionerna i Data Factory för att flytta data till/från Azure SQL Data Warehouse finns [flytta data till och från Azure SQL Data Warehouse med Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) artikel.
>
> Du kan också skapa pipelines med hjälp av Azure portal, Visual Studio, PowerShell, osv. Se [Självstudier: kopiera data från Azure Blob till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för en snabb genomgång med stegvisa instruktioner för att använda aktiviteten kopiera i Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Förutsättningar
* Azure Blob Storage: experimentet använder Azure Blob Storage (GRS) för att lagra TPC-H tester dataset.  Lär dig mer om du inte har ett Azure storage-konto [hur du skapar ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account).
* [TPC-H](http://www.tpc.org/tpch/) data: vi ska använda TPC-H som tester dataset.  För att göra det, måste du använda `dbgen` från TPC-H toolkit som hjälper dig att generera datamängden.  Du kan antingen ladda ned källkoden för `dbgen` från [TPC verktyg](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) och kompilera den själv eller hämta kompilerade binärfilen från [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Kör dbgen.exe med följande kommandon för att generera 1 TB flat fil för `lineitem` tabell sprids över 10 filer:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Nu kopiera genererade filer till Azure-Blob.  Referera till [flytta data till och från ett lokalt filsystem med hjälp av Azure Data Factory](data-factory-onprem-file-system-connector.md) för hur du gör med ADF kopia.    
* Azure SQL Data Warehouse: experimentet läser in data i Azure SQL Data Warehouse som skapats med 6 000 dwu: er

    Referera till [skapar ett Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) detaljerade anvisningar om hur du skapar en SQL Data Warehouse-databas.  För att få bästa möjliga belastningen prestanda i SQL Data Warehouse med Polybase kan väljer vi maxantalet Informationslagerenheter (dwu: er) tillåts i inställningen prestanda, som är 6 000 dwu: er.

  > [!NOTE]
  > Prestanda för datainläsning är proportionell mot antalet dwu: er som du konfigurerar i SQL Data Warehouse vid inläsning från Azure Blob:
  >
  > Läsa in 1 TB i 1 000 tar DWU SQL Data Warehouse 87 minuter (~ 200 Mbit/s genomströmning) lästes in 1 TB till 2 000 DWU-informationslager tar 46 minuter (~ 380 Mbit/s genomströmning) lästes in 1 TB till 6 000 DWU SQL Data Warehouse tar 14 minuter (~1.2 Gbit/s genomströmning)
  >
  >

    Flytta skjutreglaget prestanda ända till höger om du vill skapa ett SQL Data Warehouse med 6 000 dwu: er:

    ![Skjutreglaget för prestanda](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    För en befintlig databas som inte är konfigurerad med 6 000 dwu: er kan skala upp med hjälp av Azure portal.  Navigera till databasen i Azure-portalen och det finns en **skala** knappen i den **översikt** panelen visas i följande bild:

    ![Skala knappen](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klicka på den **skala** knappen för att öppna följande panelen, flytta skjutreglaget till det högsta värdet och klicka på **spara** knappen.

    ![Dialogruta](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Experimentet läser in data i Azure SQL Data Warehouse med hjälp av `xlargerc` resursklassen.

    För att uppnå bästa möjliga genomströmningen, kopiera måste utföras med hjälp av en SQL Data Warehouse-användare som hör till `xlargerc` resursklassen.  Lär dig hur du gör det genom att följa [ändra ett exempel på användaren resurs klassen](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Skapa mål tabellschemat i Azure SQL Data Warehouse-databas genom att köra följande DDL-instruktion:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
Med nödvändiga steg för att slutföra, är vi nu redo att konfigurera kopieringsaktiviteten med hjälp av guiden Kopiera.

## <a name="launch-copy-wizard"></a>Använda guiden Kopiera
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **skapar du en resurs** från det övre vänstra hörnet, klickar du på **Intelligence + analys**, och klicka på **Data Factory**.
3. I den **nya data factory** fönstret:

   1. Ange **LoadIntoSQLDWDataFactory** för den **namn**.
       Namnet på Azure Data Factory måste vara globalt unikt. Om du får felmeddelandet: **datafabriksnamnet ”LoadIntoSQLDWDataFactory” är inte tillgänglig**, ändra namnet på data factory (till exempel yournameLoadIntoSQLDWDataFactory) och försök att skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.  
   2. Välj din Azure-**prenumeration**.
   3. För resursgruppen utför du något av följande steg:
      1. Välj **Använd befintlig** och välj en befintlig resursgrupp.
      2. Välj **Skapa nytt** och ange ett namn för en resursgrupp.
   4. Välj **plats** för datafabriken.
   5. Välj kryssrutan **PIN-kod för instrumentpanelen** längst ned på bladet.  
   6. Klicka på **Skapa**.
4. När datafabriken har skapats visas bladet **Datafabrik** enligt nedanstående bild:

   ![Datafabrikens startsida](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. På startsidan Datafabrik klickar du på ikonen **Kopiera data** för att starta **guiden Kopiera**.

   > [!NOTE]
   > Om du ser att webbläsaren har fastnat på ”Auktoriserar...”, inaktiverar/avmarkerar du inställningen **Blockera cookies från tredje part och platsdata** (eller) behåller den aktiverad och skapar ett undantag för **login.microsoftonline.com**. Försök sedan starta guiden igen.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Steg 1: Konfigurera schemat för datainläsning
Det första steget är att konfigurera schemat för datainläsning.  

På sidan **Egenskaper**:

1. Ange **CopyFromBlobToAzureSqlDataWarehouse** för **aktivitet**
2. Välj **kör nu en gång** alternativet.   
3. Klicka på **Nästa**.  

    ![Guiden Kopiera - egenskapssidan](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Steg 2: Konfigurera källan
Det här avsnittet visas hur du konfigurerar källa: Azure Blob som innehåller 1 TB TPC-H radobjekt filer.

1. Välj den **Azure Blob Storage** som du lagrar och klickar på **nästa**.

    ![Kopiera - guidesidan Välj källa](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Fyll i anslutningsinformationen för Azure Blob storage-konto och klicka på **nästa**.

    ![Guiden Kopiera - anslutningsinformationen för datakällan](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Välj den **mappen** som innehåller TPC-H objektet filer och klickar på **nästa**.

    ![Guiden Kopiera – Välj inkommande mapp](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. När du klickar på **nästa**, formatinställningar filen identifieras automatiskt.  Kontrollera att den kolumnavgränsaren är ' | 'i stället för standard kommatecken','.  Klicka på **nästa** när du har förhandsgranskas data.

    ![Guiden Kopiera - inställningar för format](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Steg 3: Konfigurera mål
Det här avsnittet visar hur du konfigurerar mål: `lineitem` tabell i Azure SQL Data Warehouse-databas.

1. Välj **Azure SQL Data Warehouse** som mål att lagra och klickar på **nästa**.

    ![Guiden Kopiera - Välj målserver datalager](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Fyll i anslutningsinformationen för Azure SQL Data Warehouse.  Kontrollera att du anger den användare som är medlem i rollen `xlargerc` (finns i **krav** avsnittet detaljerade anvisningar), och klicka på **nästa**.

    ![Guiden Kopiera - anslutningsinformation för mål](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Välj tabellen och klickar på **nästa**.

    ![Kopiera guidesidan - tabellen mappning](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Schemat mappning på sidan låter ”tillämpa kolumnmappningen” alternativet är avmarkerat och klickar på **nästa**.

## <a name="step-4-performance-settings"></a>Steg 4: Prestandainställningar

**Tillåt polybase** är markerad som standard.  Klicka på **Nästa**.

![Kopiera guidesidan - schemat mappning](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Steg 5: Distribuera och övervaka belastningen resultat
1. Klicka på **Slutför** om du vill distribuera.

    ![Guiden Kopiera - sammanfattningssida](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. När installationen är klar klickar du på `Click here to monitor copy pipeline` att övervaka kopian kör pågår. Välj Kopiera pipeline som du skapade i den **aktivitet Windows** lista.

    ![Guiden Kopiera - sammanfattningssida](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Du kan visa kopian kör information i den **aktivitet fönstret Explorer** i den högra panelen, inklusive datavolym läses från källan och skrivs till målet, varaktighet och den genomsnittliga genomflödet för körning.

    Som du ser i följande skärmbild visar tog kopiera 1 TB från Azure Blob Storage till SQL Data Warehouse 14 minuter, ett effektivt sätt att uppnå 1,22 Gbit/s genomströmning!

    ![Guiden Kopiera - lyckades dialogrutan](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Bästa praxis
Här följer några Metodtips för att köra din Azure SQL Data Warehouse-databas:

* Använd en större resursklassen när inläsning till ett GRUPPERAT COLUMNSTORE-INDEX.
* Överväg att använda hash-distribution genom att markera kolumn i stället för default round robin distribution för effektivare kopplingar.
* Överväg att använda heap för tillfälliga data för högre belastning hastighet.
* Skapa statistik när du är klar läser in Azure SQL Data Warehouse.

Se [Metodtips för Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) mer information.

## <a name="next-steps"></a>Nästa steg
* [Guiden för data Factory kopiera](data-factory-copy-wizard.md) -den här artikeln innehåller information om guiden Kopiera.
* [Kopiera aktivitet prestanda och prestandajustering guide](data-factory-copy-activity-performance.md) -den här artikeln innehåller guiden referens prestandamått och prestandajustering.

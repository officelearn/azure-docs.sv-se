---
title: Läsa in terabyte av data till SQL Data Warehouse | Microsoft Docs
description: Visar hur 1 TB data kan läsas in i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory
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
ms.openlocfilehash: 5fb4034d49982d600fe5b0de17d0b198e3ee653e
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056233"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [kopiera data till och från Azure SQL Data Warehouse med hjälp av Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) är en molnbaserad skalbar databas kan bearbeta massiva mängder data, både relationsdata och icke-relationsdata.  Bygger på arkitektur med massivt parallell bearbetning (MPP) och är SQL Data Warehouse optimerat för enterprise arbetsbelastningar med informationslager.  Den erbjuder molnelasticitet med flexibiliteten att skalas lagring och beräkning oberoende av varandra.

Komma igång med Azure SQL Data Warehouse är nu enklare än någonsin med **Azure Data Factory**.  Azure Data Factory är en fullständigt hanterad molnbaserad dataintegreringstjänst, som kan användas för att fylla i ett SQL Data Warehouse med data från ditt befintliga system och sparar värdefull tid när du utvärderar SQL Data Warehouse och skapa dina analyser lösningar. Här är de främsta fördelarna med att läsa in data i Azure SQL Data Warehouse med Azure Data Factory:

* **Enkelt att konfigurera**: 5 steg intuitiv guide med inga skript som krävs.
* **Utforska data store har stöd för**: inbyggt stöd för ett stort utbud av lokala och molnbaserade datalager.
* **Säker och kompatibel**: data överförs via HTTPS- eller ExpressRoute och tjänsten för global närvaro säkerställer att dina data lämnar aldrig geografiska gränser
* **Oöverträffade prestanda med hjälp av PolyBase** – med hjälp av Polybase är det effektivaste sättet att flytta data till Azure SQL Data Warehouse. Funktionen mellanlagring blob kan uppnå du hög belastning hastigheter från alla typer av datalager förutom Azure Blob storage som Polybase stöder som standard.

Den här artikeln visar hur du använder Data Factory-Kopieringsguide att läsa in 1 TB data från Azure Blob Storage till Azure SQL Data Warehouse på under 15 minuter, med över 1,2 Gbit/s-dataflöde.

Den här artikeln innehåller stegvisa instruktioner för att flytta data till Azure SQL Data Warehouse med hjälp av guiden Kopiera.

> [!NOTE]
>  Allmän information om funktionerna i Data Factory i flytta data till och från Azure SQL Data Warehouse finns i [flytta data till och från Azure SQL Data Warehouse med Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) artikeln.
>
> Du kan också skapa pipelines med Azure portal, Visual Studio, PowerShell, osv. Se [självstudie: kopiera data från Azure-Blob till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) en snabb genomgång med stegvisa instruktioner för att använda Kopieringsaktivitet i Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Förutsättningar
* Azure Blob Storage: det här experimentet använder Azure Blob Storage (GRS) för att lagra TPC-H testning datauppsättning.  Om du inte har ett Azure storage-konto, lär du dig [hur du skapar ett lagringskonto](../../storage/common/storage-quickstart-create-account.md).
* [TPC-H](http://www.tpc.org/tpch/) data: Vi kommer att använda TPC-H som testar datamängd.  Om du vill göra det, måste du använda `dbgen` från TPC-H toolkit, som hjälper dig att skapa datauppsättningen.  Du kan antingen ladda ned källkoden för `dbgen` från [TPC verktyg](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) och kompilera den själv eller hämta kompilerade binärfilen från [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TPCHTools).  Kör dbgen.exe med följande kommandon för att generera 1 TB flat fil för `lineitem` tabellen sprids över 10 filer:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Kopiera nu genererade filerna till Azure Blob.  Referera till [flytta data till och från ett lokalt filsystem med hjälp av Azure Data Factory](data-factory-onprem-file-system-connector.md) för hur du gör med hjälp av ADF kopiering.    
* Azure SQL Data Warehouse: det här experimentet läser in data i Azure SQL Data Warehouse skapas med 6 000 dwu: er

    Referera till [skapar en Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) detaljerade anvisningar om hur du skapar ett SQL Data Warehouse-databas.  För att få bästa möjliga inläsningsprestanda i SQL Data Warehouse med Polybase kan välja vi maximala antalet Informationslagerenheter (dwu: er) tillåts i inställningen för prestanda med 6 000 dwu: er.

  > [!NOTE]
  > När du läser in från Azure Blob, står prestanda för inläsning av data i direkt proportion till antalet dwu: er som du konfigurerar på SQL Data Warehouse:
  >
  > Läsa in 1 TB i 1 000 tar DWU SQL Data Warehouse 87 minuter (~ 200 Mbit/s dataflöde) läser in 1 TB till 2 000 DWU SQL informationslager som tar 46 minuter (~ 380 MB/s genomströmning) läser in 1 TB i 6 000 DWU SQL Data Warehouse tar 14 minuter (dataflöden ~1.2 Gbit/s)
  >
  >

    Flytta skjutreglaget prestanda hela vägen till höger om du vill skapa ett SQL Data Warehouse med 6 000 dwu: er:

    ![Skjutreglaget för prestanda](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Du kan skala upp med hjälp av Azure portal för en befintlig databas som inte är konfigurerad med 6 000 dwu: er, den.  Gå till databasen i Azure-portalen och det finns en **skala** knappen i den **översikt** panel visas i följande bild:

    ![Skala knappen](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klicka på den **skala** knappen för att öppna panelen följande, flytta skjutreglaget till det maximala värdet och klicka på **spara** knappen.

    ![Dialogruta](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Det här experimentet läser in data i Azure SQL Data Warehouse med hjälp av `xlargerc` resursklass.

    För att uppnå bästa möjliga genomflöde, kopiera måste utföras med hjälp av ett SQL Data Warehouse-användare som tillhör `xlargerc` resursklass.  Lär dig hur du gör det genom att följa [ändra en klass för användaren resursexempel](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
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
Nödvändiga steg har slutförts, är vi nu redo att konfigurera kopieringsaktiviteten med hjälp av guiden Kopiera.

## <a name="launch-copy-wizard"></a>Använda guiden Kopiera
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **skapa en resurs** i det övre vänstra hörnet, klickar du på **information + analys**, och klicka på **Data Factory**.
3. I den **ny datafabrik** fönstret:

   1. Ange **LoadIntoSQLDWDataFactory** för den **namn**.
       Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet: **datafabriksnamnet ”LoadIntoSQLDWDataFactory” är inte tillgänglig**, ändra namnet på datafabriken (till exempel yournameLoadIntoSQLDWDataFactory) och försöker skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.  
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

## <a name="step-1-configure-data-loading-schedule"></a>Steg 1: Konfigurera schemat för inläsning av data
Det första steget är att konfigurera schemat för inläsning av data.  

På sidan **Egenskaper**:

1. Ange **CopyFromBlobToAzureSqlDataWarehouse** för **aktivitetsnamn**
2. Välj **kör en gång nu** alternativet.   
3. Klicka på **Nästa**.  

    ![Kopieringsguiden – sidan Egenskaper](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Steg 2: Konfigurera källa
Det här avsnittet visas hur du konfigurerar källan: Azure-Blob som innehåller 1 TB TPC-H radobjekt filer.

1. Välj den **Azure Blob Storage** som de datalagring och klicka på **nästa**.

    ![Kopieringsguiden – sidan Välj källa](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Fyll i anslutningsinformationen för Azure Blob storage-kontot och klicka på **nästa**.

    ![Kopieringsguiden - anslutningsinformationen för datakällan](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Välj den **mappen** som innehåller TPC-H objekt filer och klicka på **nästa**.

    ![Guiden Kopiera – Välj Indatamappen](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. När du klickar på **nästa**, filformatinställningar identifieras automatiskt.  Kontrollera att kolumnavgränsare är ' | 'i stället för standard-kommatecken ””,.  Klicka på **nästa** när du har förhandsgranskat data.

    ![Kopieringsguiden – filformatinställningar](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Steg 3: Konfigurera mål
Det här avsnittet visar hur du konfigurerar mål: `lineitem` tabell i Azure SQL Data Warehouse-databasen.

1. Välj **Azure SQL Data Warehouse** som mål att lagra och klickar på **nästa**.

    ![Kopieringsguiden - Välj måldatalager](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Fyll i anslutningsinformationen för Azure SQL Data Warehouse.  Kontrollera att du anger den användare som är medlem i rollen `xlargerc` (se den **krav** för detaljerade anvisningar), och klicka på **nästa**.

    ![Kopieringsguiden - anslutningsinformation för mål](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Välj tabellen och klicka på **nästa**.

    ![Kopiera guiden - sidan för tabellmappning](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. På sidan för schemamappning, lämnar ”gäller kolumnmappning” alternativet är avmarkerat och klickar på **nästa**.

## <a name="step-4-performance-settings"></a>Steg 4: Prestandainställningar

**Tillåt polybase** är markerad som standard.  Klicka på **Nästa**.

![Kopiera guiden - sidan för schemamappning](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Steg 5: Distribuera och övervaka användarbelastning som medför
1. Klicka på **Slutför** knappen för att distribuera.

    ![Kopieringsguiden - sammanfattningssida](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. När distributionen är klar klickar du på `Click here to monitor copy pipeline` att övervaka kopia som kör pågår. Välj den kopieringspipelinen som du skapade i den **aktivitet Windows** lista.

    ![Kopieringsguiden - sammanfattningssida](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Du kan visa den kopian körningsinformation den **aktivitet fönstret Explorer** i den högra panelen, inklusive datavolymen läses från källan och skrivs till målet, varaktighet och genomsnittlig genomströmning för körningen.

    Som du ser i följande skärmbild visar, tog kopierar 1 TB från Azure Blob Storage till SQL Data Warehouse 14 minuter, effektivt får 1,22 Gbit/s genomströmning!

    ![Kopieringsguiden - lyckades dialogrutan](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Bästa praxis
Här följer några Metodtips för att köra din Azure SQL Data Warehouse-databas:

* Använd en större resursklass när du läser in i ett GRUPPERAT COLUMNSTORE-INDEX.
* Överväg att använda hash-distribution av en väljer kolumn i stället för standard round robin distributionsplatsen för effektivare kopplingar.
* Överväg att använda heap för tillfälliga data för den högre belastningen hastighet.
* Skapa statistik när du har slutfört läser in Azure SQL Data Warehouse.

Se [Metodtips för Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-best-practices.md) mer information.

## <a name="next-steps"></a>Nästa steg
* [Data Factory-Kopieringsguide](data-factory-copy-wizard.md) -den här artikeln innehåller information om guiden Kopiera.
* [Kopiera aktivitet prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md) -den här artikeln innehåller prestandamått för referens- och Justeringsguiden.

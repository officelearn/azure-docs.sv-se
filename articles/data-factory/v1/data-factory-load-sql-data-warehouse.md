---
title: Läsa in terabyte data i SQL Data Warehouse
description: Visar hur 1 TB data kan läsas in i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0bef6b5e87e7f0964989db371014c305b97f1d12
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419314"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>Ladda 1 TB i Azure SQL Data Warehouse under 15 minuter med Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Kopiera data till eller från Azure SQL Data Warehouse med hjälp av Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) är en molnbaserad, skalningsdatabas som kan bearbeta stora mängder data, både relationella och icke-relationella.  SQL Data Warehouse bygger på kraftigt parallell bearbetningsarkitektur (MPP) och är optimerat för arbetsbelastningar för företagsdatalager.  Det erbjuder molnelasticitet med flexibiliteten att skala lagring och beräkna oberoende av varandra.

Det är nu enklare än någonsin **att**komma igång med Azure SQL Data Warehouse.  Azure Data Factory är en fullständigt hanterad molnbaserad dataintegrationstjänst, som kan användas för att fylla ett SQL Data Warehouse med data från ditt befintliga system och spara värdefull tid samtidigt som du utvärderar SQL Data Warehouse och skapar dina analyslösningar. Här är de viktigaste fördelarna med att läsa in data i Azure SQL Data Warehouse med Azure Data Factory:

* **Lätt att ställa in:** 5-stegs intuitiv guide utan skript krävs.
* **Omfattande stöd för datalager**: inbyggt stöd för en omfattande uppsättning lokala och molnbaserade datalager.
* **Säker och kompatibel:** data överförs via HTTPS eller ExpressRoute, och global tjänstnärvaro säkerställer att dina data aldrig lämnar den geografiska gränsen
* **Oöverträffad prestanda med hjälp av PolyBase** – Att använda Polybase är det mest effektiva sättet att flytta data till Azure SQL Data Warehouse. Med hjälp av mellanlagringsbloloben kan du uppnå höga belastningshastigheter från alla typer av datalager förutom Azure Blob-lagring, som Polybase stöder som standard.

Den här artikeln visar hur du använder datafabrikskopieringsguiden för att läsa in 1 TB-data från Azure Blob Storage i Azure SQL Data Warehouse på mindre än 15 minuter, med över 1,2 Gbit/s dataflöde.

Den här artikeln innehåller steg-för-steg-instruktioner för att flytta data till Azure SQL Data Warehouse med hjälp av kopieringsguiden.

> [!NOTE]
>  Allmän information om funktioner för Data Factory vid flyttning av data till/från Azure SQL Data Warehouse finns i [Flytta data till och från Azure SQL Data Warehouse med hjälp av Azure Data](data-factory-azure-sql-data-warehouse-connector.md) Factory-artikeln.
>
> Du kan också bygga pipelines med Visual Studio, PowerShell, etc. Se [självstudiekurs: Kopiera data från Azure Blob till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för en snabb genomgång med steg-för-steg-instruktioner för hur du använder kopieringsaktiviteten i Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Krav
* Azure Blob Storage: det här experimentet använder Azure Blob Storage (GRS) för lagring av TPC-H-testningsdatauppsättning.  Om du inte har ett Azure-lagringskonto kan du läsa om hur du [skapar ett lagringskonto](../../storage/common/storage-account-create.md).
* [TPC-H data:](http://www.tpc.org/tpch/) vi kommer att använda TPC-H som testdatauppsättning.  För att göra det `dbgen` måste du använda från TPC-H-verktygslådan, som hjälper dig att generera datauppsättningen.  Du kan antingen hämta `dbgen` källkoden för från [TPC Tools](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) och kompilera den själv, eller ladda ner den kompilerade binärfilen från [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Kör dbgen.exe med följande kommandon för att generera `lineitem` 1 TB platt fil för tabell spridda över 10 filer:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Kopiera nu de genererade filerna till Azure Blob.  Se [Flytta data till och från ett lokalt filsystem med hjälp av Azure Data Factory](data-factory-onprem-file-system-connector.md) för hur du gör det med hjälp av ADF Copy.    
* Azure SQL Data Warehouse: det här experimentet läser in data i Azure SQL Data Warehouse som skapats med 6 000 DWUs

    Mer information om hur du skapar en SQL Data Warehouse-databas finns i [Skapa ett Azure SQL Data Warehouse-lager.](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)  För att få bästa möjliga inläsningsprestanda i SQL Data Warehouse med Polybase väljer vi maximalt antal datalagerenheter (DWUs) som tillåts i prestandainställningen, vilket är 6 000 DWUs.

  > [!NOTE]
  > När du läser in från Azure Blob är datainläsningsprestanda direkt proportionell mot antalet DWUs som du konfigurerar på SQL Data Warehouse:
  >
  > Inläsning av 1 TB i 1 000 DWU SQL Data Warehouse tar 87 minuter (~200 MBps dataflöde) Inläsning av 1 TB i 2 000 DWU SQL Data Warehouse tar 46 minuter (~380 MBps dataflöde) Lastning 1 TB till 6 000 DWU SQL Data Warehouse tar 14 minuter (~1,2 GBps-dataflöde)
  >
  >

    Om du vill skapa ett SQL Data Warehouse med 6 000 DWUs flyttar du skjutreglaget Prestanda hela vägen till höger:

    ![Skjutreglage för prestanda](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    För en befintlig databas som inte är konfigurerad med 6 000 DWUs kan du skala upp den med Azure-portalen.  Navigera till databasen i Azure Portal och det finns en **skalningsknapp** på **översiktspanelen** som visas i följande bild:

    ![Knappen Skala](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klicka på knappen **Skala** för att öppna följande panel, flytta skjutreglaget till det maximala värdet och klicka på **Knappen Spara.**

    ![Dialogrutan Skala](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Det här experimentet läser in `xlargerc` data i Azure SQL Data Warehouse med hjälp av resursklass.

    För att uppnå bästa möjliga dataflöde måste kopiering utföras med `xlargerc` en SQL Data Warehouse-användare som tillhör resursklassen.  Lär dig hur du gör det genom att följa [Ändra ett exempel på användarresursklass](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md).  
* Skapa måltabellschema i Azure SQL Data Warehouse-databasen genom att köra följande DDL-uttryck:

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
  Med de nödvändiga stegen slutförda är vi nu redo att konfigurera kopieringsaktiviteten med hjälp av kopieringsguiden.

## <a name="launch-copy-wizard"></a>Använda guiden Kopiera
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **Skapa en resurs** från det övre vänstra hörnet, klicka på Intelligens + **analys**och klicka på **Data Factory**.
3. I fönstret **Nytt datafabrik:**

   1. Ange **LoadIntoSQLDWDataFactory** för **namnet**.
       Namnet på Azure Data Factory måste vara globalt unikt. Om felet visas: **Datafabriksnamnet "LoadIntoSQLDWDataFactory" inte är tillgängligt**ändrar du namnet på datafabriken (till exempel ditt namnLoadIntoSQLDWDataFactory) och försöker skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.  
   2. Välj din **Azure-prenumeration**.
   3. För resursgruppen utför du något av följande steg:
      1. Välj **Använd befintlig** och välj en befintlig resursgrupp.
      2. Välj **Skapa nytt** och ange ett namn för en resursgrupp.
   4. Välj **plats** för datafabriken.
   5. Välj kryssrutan **PIN-kod för instrumentpanelen** längst ned på bladet.  
   6. Klicka på **Skapa**.
4. När skapandet är klart visas **bladet Data Factory** som visas i följande bild:

   ![Datafabrikens startsida](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. På startsidan Datafabrik klickar du på ikonen **Kopiera data** för att starta **guiden Kopiera**.

   > [!NOTE]
   > Om du ser att webbläsaren har fastnat på ”Auktoriserar...”, inaktiverar/avmarkerar du inställningen **Blockera cookies från tredje part och platsdata** (eller) behåller den aktiverad och skapar ett undantag för **login.microsoftonline.com**. Försök sedan starta guiden igen.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Steg 1: Konfigurera schema för inläsning av data
Det första steget är att konfigurera datainläsningsschemat.  

På sidan **Egenskaper**:

1. Ange **CopyFromBlobToAzureSqlDataWarehouse** för **uppgiftsnamn**
2. Välj **Kör en gång nu.**   
3. Klicka på **Nästa**.  

    ![Kopiera guiden - Egenskapssidan](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Steg 2: Konfigurera källa
I det här avsnittet visas stegen för att konfigurera källan: Azure Blob som innehåller 1 TB TPC-H-radobjektfilerna.

1. Välj **Azure Blob Storage** som datalager och klicka på **Nästa**.

    ![Kopiera guide - Välj källsida](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Fyll i anslutningsinformationen för Azure Blob-lagringskontot och klicka på **Nästa**.

    ![Kopiera guiden - Information om källanslutning](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Välj **mappen** som innehåller radobjektfilerna TPC-H och klicka på **Nästa**.

    ![Kopiera guiden - välj inmatningsmapp](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. När du klickar på **Nästa**identifieras inställningarna för filformat automatiskt.  Kontrollera att kolumnavgränsaren är |i stället för standardtecknet ''.  Klicka på **Nästa** när du har förhandsgranskat data.

    ![Kopiera guiden - inställningar för filformat](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Steg 3: Konfigurera mål
I det här avsnittet visas `lineitem` hur du konfigurerar måltabellen: tabellen i Azure SQL Data Warehouse-databasen.

1. Välj **Azure SQL Data Warehouse** som mållager och klicka på **Nästa**.

    ![Kopiera guiden - välj måldatalag](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Fyll i anslutningsinformationen för Azure SQL Data Warehouse.  Se till att du anger den användare `xlargerc` som är medlem i rollen (se **avsnittet för förutsättningar** för detaljerade instruktioner) och klicka på **Nästa**.

    ![Kopiera guiden - målanslutningsinformation](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Välj måltabellen och klicka på **Nästa**.

    ![Kopiera guide - tabellmappningssida](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Låt alternativet "Använd kolumnmappning" vara avmarkerat på schemamappningssidan och klicka på **Nästa**.

## <a name="step-4-performance-settings"></a>Steg 4: Prestandainställningar

**Tillåt att polybasen** är markerad som standard.  Klicka på **Nästa**.

![Kopiera guiden - schemamappningssida](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Steg 5: Distribuera och övervaka belastningsresultat
1. Klicka på **Knappen Slutför** för att distribuera.

    ![Kopiera guide - sammanfattningssida](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. När distributionen är `Click here to monitor copy pipeline` klar klickar du för att övervaka förloppet för kopieringskörning. Markera den kopia pipeline som du skapade i listan **Aktivitetsfönster.**

    ![Kopiera guide - sammanfattningssida](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Du kan visa information om kopieringskörningen i Utforskaren i **aktivitetsfönstret** på den högra panelen, inklusive datavolymen som läs avstÃ¤r från källa och skrivs in i mål, varaktighet och genomsnittligt dataflöde för körningen.

    Som du kan se av följande skärmdump tog kopiering av 1 TB från Azure Blob Storage till SQL Data Warehouse 14 minuter, vilket effektivt uppnådde 1,22 GBIT-korts dataflöde!

    ![Kopiera guiden - efterföljande dialog](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Bästa praxis
Här är några metodtips för att köra din Azure SQL Data Warehouse-databas:

* Använd en större resursklass när du läser in i ett CLUSTERED COLUMNSTORE INDEX.
* För effektivare kopplingar bör du överväga att använda hash-distribution med en utvald kolumn i stället för standarddistribution av round robin.
* För snabbare belastningshastigheter bör du överväga att använda heap för tillfälliga data.
* Skapa statistik när du har läst in Azure SQL Data Warehouse.

Mer [information finns i Metodtips för Azure SQL Data Warehouse.](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)

## <a name="next-steps"></a>Nästa steg
* [Guiden Kopiera datafabrik](data-factory-copy-wizard.md) – Den här artikeln innehåller information om kopieringsguiden.
* [Kopiera aktivitetsprestanda och justeringsguide](data-factory-copy-activity-performance.md) - Den här artikeln innehåller referensprestandamätningar och justeringsguide.

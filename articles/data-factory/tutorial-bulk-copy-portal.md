---
title: Kopiera data i grupp med Azure-portalen
description: Lär dig hur du använder Azure Data Factory och kopieringsaktiviteten till att masskopiera data från ett källdatalager till ett måldatalager.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/27/2020
ms.openlocfilehash: 04469fa1bd0473710d9fa0bf0190c6459f1f8a07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418787"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Kopiera flera tabeller i grupp med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här självstudien visar **hur du kopierar ett antal tabeller från Azure SQL Database till Azure Synapse Analytics (tidigare SQL DW).** Du kan även använda samma mönster i andra kopieringssituationer. Kopiera till exempel tabeller från SQL Server/Oracle till Azure SQL Database/Azure Synapse Analytics (tidigare SQL DW)/Azure Blob, kopiera olika sökvägar från Blob till Azure SQL Database-tabeller.

> [!NOTE]
> - Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

Sett på en hög nivå ingår följande steg i självstudierna:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Azure SQL Database, Azure Synapse Analytics (tidigare SQL DW) och Azure Storage-länkade tjänster.
> * Skapa Azure SQL Database och Azure Synapse Analytics (tidigare SQL DW) datauppsättningar.
> * Skapa en pipeline för sökning av de tabeller som ska kopieras och en annan pipeline för den faktiska kopieringsåtgärden. 
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.

I den här självstudien används Azure-portalen. Läs mer om att använda andra verktyg/SDK:er för att skapa en datafabrik i [Snabbstarter](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt
I det här fallet har du ett antal tabeller i Azure SQL Database som du vill kopiera till Azure Synapse Analytics (tidigare SQL DW). Här är den logiska ordningsföljden i arbetsflödet som sker i våra pipelines:

![Arbetsflöde](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Den första pipelinen letar rätt på listan med tabeller som ska kopieras till de mottagande datalagren.  Du kan istället underhålla en metadatatabell som innehåller alla tabeller som ska kopieras till de mottagande datalagren. Sedan utlöser pipelinen en annan pipeline, som itererar över varje tabell i databasen och utför själva datakopieringen.
* Den andra pipelinen utför den faktiska kopieringen. Den tar listan med tabeller som en parameter. För varje tabell i listan kopierar du den specifika tabellen i Azure SQL Database till motsvarande tabell i Azure Synapse Analytics (tidigare SQL DW) med hjälp av [stegvis kopia via Blob-lagring och PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) för bästa prestanda. I det här exemplet skickar den första pipelinen listan med tabeller som värde för parametern. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav
* **Azure Storage-konto**. Azure Storage-kontot används för mellanlagring för Blob Storage i masskopieringsåtgärden. 
* **Azure SQL-databas**. Den här databasen innehåller källdata. 
* **Azure Synapse Analytics (tidigare SQL DW)**. Det här datalagret innehåller de data som kopieras från SQL Database. 

### <a name="prepare-sql-database-and-azure-synapse-analytics-formerly-sql-dw"></a>Förbereda SQL Database och Azure Synapse Analytics (tidigare SQL DW)

**Förbered Azure SQL Database-källan**:

Skapa en Azure SQL-databas med exempeldata för Adventure Works LT genom att följa anvisningarna i artikeln [Skapa en Azure SQL-databas](../sql-database/sql-database-get-started-portal.md). Den här självstudien kopierar alla tabeller från den här exempeldatabasen till en Azure Synapse Analytics (tidigare SQL DW).

**Förbered diskhon Azure Synapse Analytics (tidigare SQL DW):**

1. Om du inte har en Azure Synapse Analytics (tidigare SQL DW) läser du artikeln [Skapa ett SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) för steg för att skapa en.

1. Skapa motsvarande tabellscheman i Azure Synapse Analytics (tidigare SQL DW). Du kommer att använda Azure Data Factory till att migrera/kopiera data i ett senare steg.

## <a name="azure-services-to-access-sql-server"></a>Azure-tjänster för åtkomst till SQL-servern

För både SQL Database och Azure Synapse Analytics (tidigare SQL DW) tillåt Azure-tjänster åtkomst till SQL-servern. Kontrollera att **tillåt Azure-tjänster och resurser för åtkomst till den här serverinställningen** är **aktiverad** för din Azure SQL-server. Med den här inställningen kan tjänsten Data Factory läsa data från din Azure SQL-databas och skriva data till din Azure Synapse Analytics (tidigare SQL DW). 

Om du vill verifiera och aktivera den här inställningen går du till din Azure SQL-server > Security > Firewalls och virtuella nätverk > anger **tillåt Azure-tjänster och resurser för att komma åt den här servern** till **ON**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. Gå till [Azure-portalen](https://portal.azure.com). 
1. Till vänster på Azure-portalmenyn väljer du **Skapa en resurs** > **Analytics** > **Data Factory**. 
   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)
1. På sidan **Ny datafabrik** anger du **ADFTutorialBulkCopyDF** för **namn**. 
 
   Namnet på Azure-datafabriken måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialBulkCopyDF). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
       `Data factory name "ADFTutorialBulkCopyDF" is not available`
1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
1. För **resursgruppen** utför du något av följande steg:
     
   - Välj **Använd befintlig**och välj en befintlig resursgrupp i listrutan. 
   - Välj **Skapa ny**och ange namnet på en resursgrupp.   
         
     Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
1. Välj **V2** för **versionen**.
1. Välj **plats** för datafabriken. Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
1. Klicka på **Skapa**.
1. När skapandet är klart väljer du **Gå till resurs** för att navigera till sidan Data **Factory.** 
   
1. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.
1. Växla till fliken **Författare** på sidan Låt oss komma igång på sidan **Låt oss komma igång** på sidan Låt oss komma igång på sidan Låt oss komma igång på följande bild:

     ![Sidan Kom igång](./media/doc-common-process/get-started-page-author-button.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster för att länka dina datalager och beräkna till en datafabrik. En länkad tjänst har anslutningsinformationen som Data Factory-tjänsten använder för att ansluta till datalagret i körningsfasen. 

I den här självstudien länkar du dina Azure SQL Database, Azure Synapse Analytics (tidigare SQL DW) och Azure Blob Storage-datalager till din datafabrik. Azure SQL Database är källdatalagret. Azure Synapse Analytics (tidigare SQL DW) är datalagrings- och mållagringsplatsen för handfat/mål. Azure Blob Storage är att arrangera data innan data läses in i Azure Synapse Analytics (tidigare SQL DW) med hjälp av PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Skapa den länkade tjänsten för Azure SQL Database-källan
I det här steget skapar du en länkad tjänst för att länka Azure SQL-databasen till datafabriken. 

1. Klicka på **Anslutningar** längst ned i fönstret och klicka på **+ Nytt** i**verktygsfältet (knappen Anslutningar** finns längst ned i den vänstra kolumnen under **Fabriksresurser**). 

1. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure SQL Database** och klickar på **Fortsätt**. 
1. Gör följande i fönstret **Ny länkad tjänst (Azure SQL Database):** 

    a. Ange **AzureSqlDatabaseLinkedService** som **namn**.
    
    b. Välj din Azure SQL-server som **servernamn**
    
    c. Välj din Azure SQL-databas som **databasnamn**. 
    
    d. Ange **användarens namn** för att ansluta till Azure SQL-databasen. 
    
    e. Ange **lösenord** för användaren. 

    f. Om du vill testa anslutningen till Azure SQL-databasen med den angivna informationen klickar du på **Testanslutning**.
  
    g. Klicka på **Skapa** om du vill spara den länkade tjänsten.


### <a name="create-the-sink-azure-synapse-analytics-formerly-sql-dw-linked-service"></a>Skapa den länkade tjänsten Sink Azure Synapse Analytics (tidigare SQL DW)

1. På fliken **Anslutningar** klickar du på **+ Ny** på verktygsfältet igen. 
1. I fönstret **Ny länkad tjänst** väljer du **Azure Synapse Analytics (tidigare SQL DW)** och klickar på **Fortsätt**. 
1. I fönstret **Ny länkad tjänst (Azure Synapse Analytics (tidigare SQL DW))** gör du följande steg: 
   
    a. Ange **AzureSqlDWLinkedService** som **namn**.
     
    b. Välj din Azure SQL-server som **servernamn**
     
    c. Välj din Azure SQL-databas som **databasnamn**. 
     
    d. Ange **användarnamn** för att ansluta till Azure SQL-databas. 
     
    e. Ange **lösenord** för användaren. 
     
    f. Om du vill testa anslutningen till Azure SQL-databasen med den angivna informationen klickar du på **Testanslutning**.
     
    g. Klicka på **Skapa**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Skapa den länkade tjänsten för Azure Storage-mellanlagringen
I den här självstudien använder du Azure Blob Storage som ett mellanlagringsutrymme så att PolyBase aktiveras och ger kopieringen bättre prestanda.

1. På fliken **Anslutningar** klickar du på **+ Ny** på verktygsfältet igen. 
1. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**. 
1. Gör följande i fönstret **Ny länkad tjänst (Azure Blob Storage):** 

    a. Ange **AzureStorageLinkedService** som **namn**.                                                 
    b. Välj ditt **Azure-lagringskonto** som **Lagringskontonamn**.
    
    c. Klicka på **Skapa**.


## <a name="create-datasets"></a>Skapa datauppsättningar
I den här självstudien skapar du datauppsättningar för källa och mottagare som anger var data lagras. 

Datauppsättningen för indata **AzureSqlDatabaseDataset** refererar till **AzureSqlDatabaseLinkedService**. Den länkade tjänsten anger anslutningssträngen för att ansluta till databasen. Datauppsättningen anger namnet på databasen och tabellen som innehåller källdata. 

Datauppsättningen för utdata **AzureSqlDWDataset** refererar till **AzureSqlDWLinkedService**. Den länkade tjänsten anger anslutningssträngen för att ansluta till Azure Synapse Analytics (tidigare SQL DW). Datauppsättningen anger databasen och tabellen som data kopieras till. 

I den här självstudien är käll- och måltabellerna i SQL inte hårdkodade i definitionen för datauppsättningen. Istället skickar ForEach-aktiviteten tabellens namn under körningsfasen till kopieringsaktiviteten. 

### <a name="create-a-dataset-for-source-sql-database"></a>Skapa en datauppsättning för SQL Database-källan

1. Klicka på **+ (plus)** i den vänstra rutan och klicka sedan på **Datauppsättning**. 

    ![Menyn Ny datauppsättning](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. I fönstret **Ny datauppsättning** väljer du **Azure SQL Database**och klickar sedan på **Fortsätt**. 
    
1. Ange **AzureSqlDatabaseDataset**under **Namn**i fönstret **Ange egenskaper.** Under **Länkad tjänst**väljer du **AzureSqlDatabaseLinkedService**. Klicka sedan på **OK**.

1. Växla till fliken **Anslutning,** välj en tabell för **Tabell**. Den här tabellen är en dummytabell. Du anger en fråga för källdatauppsättningen när du skapar en pipeline. Frågan används för att extrahera data från Azure SQL-databasen. Du kan också klicka på **Kryssrutan Redigera** och ange **dbo.dummyName** som tabellnamn. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics-formerly-sql-dw"></a>Skapa en datauppsättning för sink Azure Synapse Analytics (tidigare SQL DW)

1. Klicka på **+ (plus)** i den vänstra rutan och sedan på **Datauppsättning**. 
1. I fönstret **Ny datauppsättning** väljer du **Azure Synapse Analytics (tidigare SQL DW)** och klickar sedan på **Fortsätt**.
1. Ange **AzureSqlDWDataset**under **Namn**i fönstret **Ange egenskaper.** Under **Länkad tjänst**väljer du **AzureSqlDWLinkedService**. Klicka sedan på **OK**.
1. Byt till fliken **Parametrar**, klicka på **+ Ny** och ange **DWTableName** som parameternamn. Om du kopierar/klistrar in det här namnet från sidan kontrollerar du att det inte finns något **avslutande blanksteg** i slutet av **DWTableName**.
1. Växla till fliken **Anslutningar**. 

    a. **För Tabell**markerar du alternativet **Redigera.** Ange **dbo** i inmatningsrutan för för tabellnamn. Och välj sedan i den andra inmatningsrutan och klicka på länken **Lägg till dynamiskt innehåll** nedan. 

    ![Tabellnamn för datauppsättningsanslutning](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    b. På sidan **Lägg till dynamiskt innehåll** klickar du på **DWTAbleName** under **Parametrar**, som automatiskt fyller i textrutan `@dataset().DWTableName`för det översta uttrycket och sedan på **Slutför**. Uppsättningens egenskap **tableName** är inställd på värdet som har skickats som argument för parametern **DWTableName**. ForEach-aktiviteten itereras via en lista över tabeller och skickar en i taget till kopieringsaktiviteten. 

    ![Byggare för datauppsättningsparametern](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)
 
## <a name="create-pipelines"></a>Skapa pipelines
I den här självstudien skapar du två pipeliner: **IterateAndCopySQLTables** och **GetTableListAndTriggerCopyData**. 

**Pipelinen GetTableListAndTriggerCopyData** utför två åtgärder:

* Den söker i Azure SQL Database-systemtabellen för att få fram listan med tabeller som ska kopieras
* Utlöser pipeline **IterateAndCopySQLTables** för att göra den faktiska datakopian.

Pipelinen **IterateAndCopySQLTables** tar en lista med tabeller som en parameter. För varje tabell i listan kopieras data från tabellen i Azure SQL Database till Azure Synapse Analytics (tidigare SQL DW) med hjälp av stegvis kopia och PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Skapa pipelinen IterateAndCopySQLTables

1. I den vänstra rutan klickar du på **+ (plus)** och sedan på **Pipeline**.

    ![Meny för ny pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. På fliken **Allmänt** anger du **IterateAndCopySQLTables** som namn. 

1. Växla till fliken **Parametrar** och gör följande: 

    a. Klicka på **+ Ny**. 
    
    b. Ange **tabellLista** för parametern **Namn**.
    
    c. Välj **Matris** för **Typ**.

1. I verktygslådan **Aktiviteter** expanderar du **Iteration & Conditions** (Iteration och villkor) och drar och släpper aktiviteten **ForEach** till pipelinedesignytan. Du kan också söka efter aktiviteter i verktygslådan **Aktiviteter**. 

    a. I fliken **Allmänt ** längst ned skriver du **IterateSQLTables** för **Namn**. 

    b. Växla till fliken **Inställningar,** klicka på inmatningsrutan för **Objekt**och klicka sedan på länken **Lägg till dynamiskt innehåll** nedan. 

    c. På sidan **Lägg till dynamiskt innehåll** döljer du avsnitten **Systemvariabler** och **funktioner,** klickar på **tabelllistan** under **Parametrar**, som automatiskt fyller i den övre uttryckstextrutan som `@pipeline().parameter.tableList`. Klicka sedan på **Slutför**. 

    ![Byggare för parametern Foreach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Växla till fliken **Aktiviteter** genom att klicka på **pennikonen** för att lägga till en underordnad aktivitet i **forEach-aktiviteten.**
    ![Aktivitetsbyggare för Foreach](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. Expandera **Flytta & överföring i** **verktygslådan Aktiviteter** och dra ned **kopieringsdata** till pipelinedesignerytan. Lägg märke till adressfältmenyn längst upp. **IterateAndCopySQLTable** är pipelinenamnet och **IterateSQLTables är aktivitetsnamnet** ForEach. Designern är i aktivitetsomfånget. Om du vill växla tillbaka till pipelineredigeraren från ForEach-redigeraren kan du klicka på länken i sökvägen. 

    ![Kopiera i ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Växla till fliken **Källa** och gör följande:

    1. Välj **AzureSqlDatabaseDataset** för **källdatauppsättning**. 
    1. Välj **frågealternativ** för **Använd fråga**. 
    1. Klicka på textrutan **Fråga** -> välj **Lägg till dynamiskt innehåll** nedan -> ange följande uttryck för **Fråga** -> välj **Slutför**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 


1. Växla till fliken **Mottagare** och gör följande: 

    1. Välj **AzureSqlDWDataset** för **Sink Dataset** (Datauppsättning för mottagare).
    1. Klicka på indatarutan för parametern VALUE of DWTableName -> väljer **lägg till dynamiskt innehåll** nedan, anger `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` uttryck som skript, -> väljer **Slutför**.
    1. För kopieringsmetod väljer du **PolyBase**. 
    1. Avmarkera **standardalternativet Använd typ.** 
    1. Klicka på textrutan **Pre-copy Script** (Förkopieringsskript) -> välj **Lägg till dynamiskt innehåll** nedan -> ange följande uttryck för Fråga -> välj **Slutför**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Inställningar för att kopiera mottagare](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
1. Växla till fliken **Inställningar** och gör följande: 

    1. Markera kryssrutan för **Aktivera förproduktion**.
    1. Välj **AzureStorageLinkedService** för **Store Account Linked Service** (Länkad tjänst för lagringskonto).

1. Verifiera pipelineinställningarna genom att klicka på **Verifiera** i verktygsfältet för pipelinen. Kontrollera att det inte finns något valideringsfel. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>**.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Skapa pipelinen GetTableListAndTriggerCopyData

Den här pipelinen gör två åtgärder:

* Den söker i Azure SQL Database-systemtabellen för att få fram listan med tabeller som ska kopieras
* den utlöser pipelinen ”IterateAndCopySQLTables” för att utföra den faktiska kopieringen.

1. I den vänstra rutan klickar du på **+ (plus)** och sedan på **Pipeline**.
1. På fliken **Allmänt** ändrar du namnet på pipelinen till **GetTableListAndTriggerCopyData**. 

1. Expandera aktiviteten **Allmänt**och dra släpp **upp** till pipelinedesignerytan i **verktygslådan Aktiviteter** och gör följande:

    1. Skriv **LookupTableList** som **namn**. 
    1. Ange **Retrieve the table list from Azure SQL database** (Hämta tabellistan från Azure SQL-databas) som **Beskrivning**.

1. Växla till fliken **Inställningar** och gör följande:

    1. Välj **AzureSqlDatabaseDataset** för **källdatauppsättning**. 
    1. Välj **Fråga** för **användningsfråga**. 
    1. Ange följande SQL-fråga för **Fråga**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Avmarkera kryssrutan för fältet **First row only** (Endast första raden).

        ![Sökningsaktivitet – inställningssida](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Dra-släpp **Kör pipeline-aktivitet** från verktygslådan Aktiviteter till pipelinedesignerytan och ange namnet på **TriggerCopy**.

1. Om du vill **ansluta** **uppslagsaktiviteten** till aktiviteten **Kör pipeline** drar du den gröna **rutan** som är kopplad till uppslagsaktiviteten till vänster om aktiviteten Kör pipeline.

    ![Ansluta kopierings- och Kör pipeline-aktiviteter](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Växla till fliken **Inställningar för** **Kör pipeline-aktivitet** och gör följande: 

    1. Välj **IterateAndCopySQLTables** som **Invoked pipeline** (Anropad pipeline). 
    1. Expandera avsnittet **Avancerat** och avmarkera kryssrutan vänta **när du är klar**.
    1. Klicka på **+ Ny** i avsnittet **Parametrar**. 
    1. Ange **tabellLista** för **parameternamn**.
    1. Klicka på textrutan VALUE -> välj **Lägg till dynamiskt innehåll** nedan -> ange `@activity('LookupTableList').output.value` som värde för tabellens namn -> välj **Slutför**. Du anger resultatlistan från uppslagsaktiviteten som en indata till den andra pipelinen. Resultatlistan innehåller listan över tabeller vars data ska kopieras till målet. 

        ![Kör pipeline-aktivitet – sidan inställningar](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Verifiera pipelinen genom att klicka på **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>**.

1. Om du vill publicera entiteter (datauppsättningar, pipelines osv.) till tjänsten Data Factory klickar du på **Publicera alla** ovanpå fönstret. Vänta tills publiceringen har lyckats. 

## <a name="trigger-a-pipeline-run"></a>Utlösa en pipelinekörning

1. Gå till pipeline **GetTableListAndTriggerCopyData**, klicka på **Lägg till utlösare** i det övre pipelineverktygsfältet och klicka sedan på **Utlösare nu**. 

1. Bekräfta körningen på sidan **Pipeline-körning** och välj sedan **Slutför**.

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Växla till fliken **Bildskärm.** Klicka på **Uppdatera** tills du ser körs för båda pipelines i din lösning. Fortsätt att uppdatera listan tills du ser statusen **Lyckades**. 

1. Om du vill visa aktivitetskörningar som är associerade med Pipelinen **GetTableListAndTriggerCopyData** klickar du på pipelinens namnlänk för pipelinen. Du ska se två aktivitetskörningar för den här pipelinekörningen. 
    ![Övervaka pipeline-körning](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Om du vill visa utdata för **uppslagsaktiviteten** klickar du på länken **Utdata bredvid** aktiviteten under kolumnen **AKTIVITETSNAMN.** Du kan maximera och återställa fönstret **Utdata**. Stäng fönstret **Utdata** när du har sett klart genom att klicka på **X**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Om du vill växla tillbaka till vyn **Pipeline-körningar** klickar du på länken **Alla pipelinekörningar** högst upp på sökvägen på sökvägen. Klicka på **Länken IterateAndCopySQLTables** (under kolumn **för PIPELINE NAME)** om du vill visa aktivitetskörningar av pipelinen. Observera att det finns en **kopiera** aktivitet som körs för varje tabell i **uppslagsaktivitetutdata.** 

1. Bekräfta att data har kopierats till målet Azure Synapse Analytics (tidigare SQL DW) som du använde i den här självstudien. 

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen fick du: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Azure SQL Database, Azure Synapse Analytics (tidigare SQL DW) och Azure Storage-länkade tjänster.
> * Skapa Azure SQL Database och Azure Synapse Analytics (tidigare SQL DW) datauppsättningar.
> * Skapa en pipeline för sökning av de tabeller som ska kopieras och en annan pipeline för den faktiska kopieringsåtgärden. 
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.

Fortsätt till följande självstudiekurs om du vill lära dig att kopiera data stegvis från en källa till ett mål:
> [!div class="nextstepaction"]
>[Kopiera data stegvis](tutorial-incremental-copy-portal.md)

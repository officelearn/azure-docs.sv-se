---
title: Masskopiera data med Azure Data Factory | Microsoft Docs
description: Lär dig hur du använder Azure Data Factory och kopieringsaktiviteten till att masskopiera data från ett källdatalager till ett måldatalager.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 16741461df2431cbf4433899dd375741e944ce0f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58112576"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Kopiera flera tabeller i grupp med Azure Data Factory
I den här självstudien visas hur du **kopierar ett antal tabeller från Azure SQL Database till Azure SQL Data Warehouse**. Du kan även använda samma mönster i andra kopieringssituationer. Till exempel kan du kopiera tabeller från SQL Server/Oracle till Azure SQL Database/Data Warehouse/Azure Blob eller kopiera olika sökvägar från Blob till Azure SQL Database-tabeller.

> [!NOTE]
> - Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

Sett på en hög nivå ingår följande steg i självstudierna:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa länkade tjänster för Azure SQL Database, Azure SQL Data Warehouse och Azure Storage.
> * Skapa datauppsättningar för Azure SQL Database och Azure SQL Data Warehouse.
> * Skapa en pipeline för sökning av de tabeller som ska kopieras och en annan pipeline för den faktiska kopieringsåtgärden. 
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.

I den här självstudien används Azure Portal. Läs mer om att använda andra verktyg/SDK:er för att skapa en datafabrik i [Snabbstarter](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt
I det här scenariot har du ett antal tabeller i Azure SQL Database som du vill kopiera till SQL Data Warehouse. Här är den logiska ordningsföljden i arbetsflödet som sker i våra pipelines:

![Arbetsflöde](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Den första pipelinen letar rätt på listan med tabeller som ska kopieras till de mottagande datalagren.  Du kan istället underhålla en metadatatabell som innehåller alla tabeller som ska kopieras till de mottagande datalagren. Sedan utlöser pipelinen en annan pipeline, som itererar över varje tabell i databasen och utför själva datakopieringen.
* Den andra pipelinen utför den faktiska kopieringen. Den tar listan med tabeller som en parameter. För varje tabell i listan kopieras tabellen i Azure SQL Database till motsvarande tabell i SQL Data Warehouse, med hjälp av [mellanlagrad kopiering via Blob Storage och PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) för bästa möjliga prestanda. I det här exemplet skickar den första pipelinen listan med tabeller som värde för parametern. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
* **Azure Storage-konto**. Azure Storage-kontot används för mellanlagring för Blob Storage i masskopieringsåtgärden. 
* **Azure SQL Database**. Den här databasen innehåller källdata. 
* **Azure SQL Data Warehouse**. Det här datalagret innehåller de data som kopieras från SQL Database. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Förbereda SQL Database och SQL Data Warehouse

**Förbered Azure SQL Database-källan**:

Skapa en Azure SQL Database med exempeldata för Adventure Works LT genom att följa anvisningarna i artikeln [Skapa en Azure SQL-databas](../sql-database/sql-database-get-started-portal.md). I den här självstudien kopieras alla tabeller från den här exempeldatabasen till SQL Data Warehouse.

**Förbered det mottagande Azure SQL Data Warehouse-datalagret**:

1. Om du inte har något Azure SQL Database Warehouse kan du läsa om att skapa ett i artikeln [Skapa ett Azure SQL Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).

1. Skapa motsvarande tabellscheman i SQL Data Warehouse. Du kan använda [migreringsverktyget](https://www.microsoft.com/download/details.aspx?id=49100) till att **migrera scheman** från Azure SQL Database till Azure SQL Data Warehouse. Du kommer att använda Azure Data Factory till att migrera/kopiera data i ett senare steg.

## <a name="azure-services-to-access-sql-server"></a>Azure-tjänster för åtkomst till SQL-servern

Ge Azure-tjänster åtkomst till SQL-servern för både SQL Database och SQL Data Warehouse. Se till att inställningen **Tillåt åtkomst till Azure-tjänster** är **aktiverad** för Azure SQL-servern. Den här inställningen gör att Data Factory-tjänsten kan läsa data från Azure SQL Database och skriva data till Azure SQL Data Warehouse. Gör så här för att kontrollera och aktivera den här inställningen:

1. Klicka på hubben **Fler tjänster** till vänster och klicka på **SQL-servrar**.
1. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
1. På sidan **Brandväggsinställningar** väljer du **På** för **Tillåt åtkomst till Azure-tjänster**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
1. På sidan **Ny datafabrik** anger du **ADFTutorialBulkCopyDF** som **namn**. 
      
     ![Sida för ny datafabrik](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialBulkCopyDF). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
1. För **resursgruppen** utför du något av följande steg:
     
   - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
   - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
     Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
1. Välj **V2** för **versionen**.
1. Välj **plats** för datafabriken. Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
1. Välj **fäst till instrumentpanelen**.     
1. Klicka på **Skapa**.
1. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

     ![panelen distribuerar datafabrik](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
1. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
     ![Datafabrikens startsida](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
1. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.
1. På sidan **kom igång** växlar du till fliken **Redigera** på den vänstra panelen som på följande bild:  

     ![Sidan Kom igång](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster för att länka dina datalager och beräkna till en datafabrik. En länkad tjänst har anslutningsinformationen som Data Factory-tjänsten använder för att ansluta till datalagret i körningsfasen. 

I den här självstudien länkar du dina Azure SQL Database-, Azure SQL Data Warehouse- och Azure Blob Storage-datalager till datafabriken. Azure SQL Database är källdatalagret. Azure SQL Data Warehouse är mottagar-/måldatalagret. Azure Blob Storage mellanlagrar data innan data läses in i SQL Data Warehouse med hjälp av PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Skapa den länkade tjänsten för Azure SQL Database-källan
I det här steget skapar du en länkad tjänst för att länka Azure SQL-databasen till datafabriken. 

1. Klicka på **Anslutningar** längst ned i fönstret och sedan på **+Ny** i verktygsfältet. 

    ![Knapp för ny länkad tjänst](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
1. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure SQL Database** och klickar på **Fortsätt**. 

    ![Välj Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
1. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureSqlDatabaseLinkedService** som **namn**. 
    1. Välj din Azure SQL-server som **servernamn**
    1. Välj din Azure SQL-databas som **databasnamn**. 
    1. Ange **användarens namn** för att ansluta till Azure SQL-databasen. 
    1. Ange **lösenord** för användaren. 
    1. Om du vill testa anslutningen till Azure SQL-databasen med den angivna informationen klickar du på **Testanslutning**.
    1. Klicka på **Spara**.

        ![Inställningar för Azure SQL Database](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Skapa den länkade tjänsten för Azure SQL Data Warehouse-mottagaren

1. På fliken **Anslutningar** klickar du på **+ Ny** på verktygsfältet igen. 
1. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure SQL Data Warehouse** och klickar på **Fortsätt**. 
1. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureSqlDWLinkedService** som **namn**. 
    1. Välj din Azure SQL-server som **servernamn**
    1. Välj din Azure SQL-databas som **databasnamn**. 
    1. Ange **användarens namn** för att ansluta till Azure SQL-databasen. 
    1. Ange **lösenord** för användaren. 
    1. Om du vill testa anslutningen till Azure SQL-databasen med den angivna informationen klickar du på **Testanslutning**.
    1. Klicka på **Spara**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Skapa den länkade tjänsten för Azure Storage-mellanlagringen
I den här självstudien använder du Azure Blob Storage som ett mellanlagringsutrymme så att PolyBase aktiveras och ger kopieringen bättre prestanda.

1. På fliken **Anslutningar** klickar du på **+ Ny** på verktygsfältet igen. 
1. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**. 
1. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureStorageLinkedService** som **namn**. 
    1. Välj ditt **Azure-lagringskonto** som **Lagringskontonamn**.
    1. Klicka på **Spara**.


## <a name="create-datasets"></a>Skapa datauppsättningar
I den här självstudien skapar du datauppsättningar för källa och mottagare som anger var data lagras. 

Datauppsättningen för indata **AzureSqlDatabaseDataset** refererar till **AzureSqlDatabaseLinkedService**. Den länkade tjänsten anger anslutningssträngen för att ansluta till databasen. Datauppsättningen anger namnet på databasen och tabellen som innehåller källdata. 

Datauppsättningen för utdata **AzureSqlDWDataset** refererar till **AzureSqlDWLinkedService**. Den länkade tjänsten anger anslutningssträngen för att ansluta till informationslagret. Datauppsättningen anger databasen och tabellen som data kopieras till. 

I den här självstudien är käll- och måltabellerna i SQL inte hårdkodade i definitionen för datauppsättningen. Istället skickar ForEach-aktiviteten tabellens namn under körningsfasen till kopieringsaktiviteten. 

### <a name="create-a-dataset-for-source-sql-database"></a>Skapa en datauppsättning för SQL Database-källan

1. Klicka på **+ (plus)** i den vänstra rutan och sedan på **Datauppsättning**. 

    ![Menyn Ny datauppsättning](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. Välj **Azure SQL Database** i fönstret **Ny datauppsättning** och klicka på **Slutför**. Du ser en ny flik som heter **AzureSqlTable1**. 
    
    ![Välja Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
1. I egenskapsfönstret längst ned skriver du **AzureSqlDatabaseDataset** som **namn**.

1. Växla till fliken **Anslutning** och gör följande: 

   1. Välj **AzureSqlDatabaseLinkedService** som **länkad tjänst**.
   1. Välj valfri tabell för **Tabell**. Den här tabellen är en dummytabell. Du anger en fråga för källdatauppsättningen när du skapar en pipeline. Frågan används för att extrahera data från Azure SQL-databasen. Du kan även klicka i kryssrutan **Redigera** och ange **dummyName** som tabellnamn. 

      ![Sida för källdatauppsättningsanslutning](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Skapa en datauppsättning för SQL Data Warehouse-mottagaren

1. Klicka på **+ (plus)** i den vänstra rutan och sedan på **Datauppsättning**. 
1. Välj **Azure SQL Data Warehouse** i fönstret **Ny datauppsättning** och klicka på **Slutför**. Du ser en ny flik som heter **AzureSqlDWTable1**. 
1. I egenskapsfönstret längst ned skriver du **AzureSqlDWDataset** som **namn**.
1. Byt till fliken **Parametrar**, klicka på **+ Ny** och ange **DWTableName** som parameternamn. Se till att inga **avslutande blanksteg** följer med i slutet av **DWTableName** om du kopierar/klistrar in det här namnet från sidan. 

    ![Sida för källdatauppsättningsanslutning](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter.png)

1. Växla till fliken **Anslutningar**. 

    a. Välj **AzureSqlDatabaseLinkedService** som **Länkad tjänst**.

    b. För **Tabell**, markera alternativet **Redigera** och klicka i textrutan för tabellnamnet. Klicka sedan på länken **Lägg till dynamiskt innehåll** nedan. 
    
    ![Parameternamn](./media/tutorial-bulk-copy-portal/table-name-parameter.png)

    c. På sidan **Lägg till dynamiskt innehåll** klickar du på **DWTAbleName** under **Parametrar**. Då populeras automatiskt textrutan för uttryck – överkant `@dataset().DWTableName`. Klicka sedan på **Slutför**. Uppsättningens egenskap **tableName** är inställd på värdet som har skickats som argument för parametern **DWTableName**. ForEach-aktiviteten itereras via en lista över tabeller och skickar en i taget till kopieringsaktiviteten. 

    ![Byggare för datauppsättningsparametern](./media/tutorial-bulk-copy-portal/dataset-parameter-builder.png)

## <a name="create-pipelines"></a>Skapa pipelines
I den här självstudien skapar du två pipelines: **IterateAndCopySQLTables** och **GetTableListAndTriggerCopyData**. 

Pipelinen **GetTableListAndTriggerCopyData** utför två steg:

* den söker i Azure SQL Database-systemtabellen för att få fram listan med tabeller som ska kopieras
* Den utlöser pipelinen **IterateAndCopySQLTables** för att utföra den faktiska datakopieringen.

**GetTableListAndTriggerCopyData** listar tabeller som en parameter. För varje tabell i listan kopieras data från tabellen i Azure SQL Database till Azure SQL Data Warehouse med hjälp av mellanlagrad kopiering och PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Skapa pipelinen IterateAndCopySQLTables

1. I den vänstra rutan klickar du på **+ (plus)** och sedan på **Pipeline**.

    ![Meny för ny pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. På fliken **Allmänt** anger du **IterateAndCopySQLTables** som namn. 

1. Växla till fliken **Parametrar** och gör följande: 

    1. Klicka på **+ Ny**. 
    1. Ange **tableList** som parameterns **namn**.
    1. Välj **Matris** för **Typ**.

        ![Pipeline-parameter](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
1. I verktygslådan **Aktiviteter** expanderar du **Iteration & Conditions** (Iteration och villkor) och drar och släpper aktiviteten **ForEach** till pipelinedesignytan. Du kan också söka efter aktiviteter i verktygslådan **Aktiviteter**. 

    a. I fliken **Allmänt**  längst ned skriver du **IterateSQLTables** för **Namn**. 

    b. Växla till fliken **Inställningar** och klicka på textrutan för **Objekt**. Klicka på länken **Lägg till dynamiskt innehåll** nedan. 

    ![ForEach-aktivitetsinställning](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)

    c. På sidan **Lägg till dynamiskt innehåll** döljer du avsnittet för systemvariabler och funktioner. Klicka på **tableList** under **Parametrar**. Då populeras automatiskt textrutan för uttryck – överkant som `@pipeline().parameter.tableList`. Klicka sedan på **Slutför**. 

    ![Byggare för parametern Foreach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Växla till fliken **Aktiviteter**. Klicka på **Lägg till aktivitet** för att lägga till en underordnad aktivitet till aktiviteten **ForEach**.

1. I verktygslådan **Aktiviteter** visar du **Dataflöde** och drar och släpper aktiviteten **Kopiera** till pipelinedesignytan. Lägg märke till adressfältmenyn längst upp. IterateAndCopySQLTable är pipelinenamnet och IterateSQLTables är ForEach-aktivitetsnamnet. Designern är i aktivitetsomfånget. Om du vill gå tillbaka till pipeline-redigeringsprogrammet från ForEach-redigeringsprogrammet klickar du på länken på adressfältmenyn. 

    ![Kopiera i ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
1. Växla till fliken **Källa** och gör följande:

    1. Välj **AzureSqlDatabaseDataset** för **källdatauppsättning**. 
    1. Välj alternativet **Fråga** för **User Query** (Användarfråga). 
    1. Klicka på textrutan **Fråga** -> välj **Lägg till dynamiskt innehåll** nedan -> ange följande uttryck för **Fråga** -> välj **Slutför**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![Inställningar för att kopiera källa](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
1. Växla till fliken **Mottagare** och gör följande: 

    1. Välj **AzureSqlDWDataset** för **Sink Dataset** (Datauppsättning för mottagare).
    1. Klicka på textrutan för VALUE för parametern DWTableName -> välj **Lägg till dynamiskt innehåll** nedan, ange uttrycket `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` som skript -> välj **Slutför**.
    1. Expandera **Polybase Settings** (Polybase-inställningar) och välj **Allow polybase** (Tillåt polybase). 
    1. Avmarkera alternativet **Use Type default** (Standardanvändartyp). 
    1. Klicka på textrutan **Pre-copy Script** (Förkopieringsskript) -> välj **Lägg till dynamiskt innehåll** nedan -> ange följande uttryck för Fråga -> välj **Slutför**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Inställningar för att kopiera mottagare](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)

1. Växla till fliken **Inställningar** och gör följande: 

    1. Välj **Sant** för **Enable Staging** (Aktivera mellanlagring).
    1. Välj **AzureStorageLinkedService** för **Store Account Linked Service** (Länkad tjänst för lagringskonto).

        ![Aktivera mellanlagring](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)

1. Verifiera pipelineinställningarna genom att klicka på **Verifiera** i verktygsfältet för pipelinen. Kontrollera att det inte finns några verifieringsfel. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>**.

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Skapa pipelinen GetTableListAndTriggerCopyData

Den här pipelinen utför två steg:

* den söker i Azure SQL Database-systemtabellen för att få fram listan med tabeller som ska kopieras
* den utlöser pipelinen ”IterateAndCopySQLTables” för att utföra den faktiska kopieringen.

1. I den vänstra rutan klickar du på **+ (plus)** och sedan på **Pipeline**.

    ![Meny för ny pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
1. I fönstret Egenskaper ändrar du pipelinenamnet till **GetTableListAndTriggerCopyData**. 

1. I verktygslådan **Aktiviteter** expanderar du **Allmänt** och drar och släpper **sökningsaktiviteten** till pipelinedesignytan och utför följande steg:

    1. Skriv **LookupTableList** som **namn**. 
    1. Ange **Retrieve the table list from Azure SQL database** (Hämta tabellistan från Azure SQL Database) som **Beskrivning**.

        ![Sökningsaktivitet – sidan allmänt](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
1. Växla till sidan **Inställningar** och gör följande:

    1. Välj **AzureSqlDatabaseDataset** för **källdatauppsättning**. 
    1. Välj **Fråga** för **Använd fråga**. 
    1. Ange följande SQL-fråga för **Fråga**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Avmarkera kryssrutan för fältet **First row only** (Endast första raden).

        ![Sökningsaktivitet – inställningssida](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Dra och släpp en till **Kör pipeline-aktivitet** från verktygslådan Aktiviteter till pipelinedesignerytan och ange **TriggerCopy** som namn.

    ![Kör pipeline-aktivitet – sidan allmänt](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
1. Växla till sidan **Inställningar** och gör följande: 

    1. Välj **IterateAndCopySQLTables** som **Invoked pipeline** (Anropad pipeline). 
    1. Expandera avsnittet **Avancerat**. 
    1. Klicka på **+ Ny** i avsnittet **Parametrar**. 
    1. Skriv **tableList** som parameterns **namn**.
    1. Klicka på textrutan VALUE -> välj **Lägg till dynamiskt innehåll** nedan -> ange `@activity('LookupTableList').output.value` som värde för tabellens namn -> välj **Slutför**. Du ställer in resultatlistan från sökningsaktiviteten som indata för den andra pipelinen. Resultatlistan innehåller listan över tabeller vars data ska kopieras till målet. 

        ![Kör pipeline-aktivitet – sidan inställningar](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
1. **Anslut** **sökningsaktiviteten** till **Kör pipeline-aktiviteten** genom att dra den **gröna rutan** som är ansluten till sökningsaktiviteten till vänster om Kör pipeline-aktiviteten.

    ![Ansluta kopierings- och Kör pipeline-aktiviteter](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
1. Verifiera pipelinen genom att klicka på **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>**.

1. Om du vill publicera entiteter (datauppsättningar, pipelines osv.) till Data Factory-tjänsten klickar du på **Publicera alla** upptill i fönstret. Vänta tills publiceringen har lyckats. 

## <a name="trigger-a-pipeline-run"></a>Utlös en pipelinekörning

Gå till pipelinen **GetTableListAndTriggerCopyData**, klicka på **Utlösare** och sedan på **Utlös nu**. 

![Utlös nu](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Välj fliken **Övervaka**. Klicka på **Uppdatera** tills du ser körningar för båda dina pipeliner i lösningen. Fortsätt att uppdatera listan tills du ser statusen **Lyckades**. 

    ![Pipelinekörningar](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
1. Om du vill visa aktivitetskörningar som är associerade med GetTableListAndTriggerCopyData-pipelinen klickar du på den första länken i Åtgärder-länken för den pipelinen. Du ska se två aktivitetskörningar för den här pipelinekörningen. 

    ![Aktivitetskörningar](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
1. Om du vill visa utdata för **sökningsaktiviteten** klickar du på länken i kolumnen **Utdata** för den aktiviteten. Du kan maximera och återställa fönstret **Utdata**. Stäng fönstret **Utdata** när du har sett klart genom att klicka på **X**.

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
1. Om du vill växla tillbaka till vyn med **pipelinekörningar** klickar du på länken **Pipeliner** högst upp. Klicka på länken **View Activity Runs** (Visa aktivitetskörningar) (den första länken i kolumnen **Åtgärder**) för pipelinen **IterateAndCopySQLTables**. Du bör se utdata som dem i följande bild: Observera att det finns en körning av **kopieringsaktiviteten** för varje tabell i utdataresultatet för **uppslagsaktiviteten**. 

    ![Aktivitetskörningar](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
1. Bekräfta att data har kopierats till SQL Data Warehouse-målet du använde i den här självstudien. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du fått: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa länkade tjänster för Azure SQL Database, Azure SQL Data Warehouse och Azure Storage.
> * Skapa datauppsättningar för Azure SQL Database och Azure SQL Data Warehouse.
> * Skapa en pipeline för sökning av de tabeller som ska kopieras och en annan pipeline för den faktiska kopieringsåtgärden. 
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.

Fortsätt till följande självstudiekurs om du vill lära dig att kopiera data stegvis från en källa till ett mål:
> [!div class="nextstepaction"]
>[Kopiera data stegvis](tutorial-incremental-copy-portal.md)

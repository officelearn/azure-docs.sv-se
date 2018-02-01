---
title: Masskopiera data med Azure Data Factory | Microsoft Docs
description: "Lär dig hur du använder Azure Data Factory och kopieringsaktiviteten till att masskopiera data från ett källdatalager till ett måldatalager."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 6aa5d4aa032ef4dc3583bf76b9c451874b74f9a6
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Kopiera flera tabeller i grupp med Azure Data Factory
I den här självstudien visas hur du **kopierar ett antal tabeller från Azure SQL Database till Azure SQL Data Warehouse**. Du kan även använda samma mönster i andra kopieringssituationer. Till exempel kan du kopiera tabeller från SQL Server/Oracle till Azure SQL Database/Data Warehouse/Azure Blob eller kopiera olika sökvägar från Blob till Azure SQL Database-tabeller.

> [!NOTE]
> - Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).
> - Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

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

## <a name="prerequisites"></a>Nödvändiga komponenter
* **Azure Storage-konto**. Azure Storage-kontot används för mellanlagring för Blob Storage i masskopieringsåtgärden. 
* **Azure SQL Database**. Den här databasen innehåller källdata. 
* **Azure SQL Data Warehouse**. Det här datalagret innehåller de data som kopieras från SQL Database. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Förbereda SQL Database och SQL Data Warehouse

**Förbered Azure SQL Database-källan**:

Skapa en Azure SQL Database med exempeldata för Adventure Works LT genom att följa anvisningarna i artikeln [Skapa en Azure SQL-databas](../sql-database/sql-database-get-started-portal.md). I den här självstudien kopieras alla tabeller från den här exempeldatabasen till SQL Data Warehouse.

**Förbered det mottagande Azure SQL Data Warehouse-datalagret**:

1. Om du inte har något Azure SQL Database Warehouse kan du läsa om att skapa ett i artikeln [Skapa ett Azure SQL Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).

2. Skapa motsvarande tabellscheman i SQL Data Warehouse. Du kan använda [migreringsverktyget](https://www.microsoft.com/download/details.aspx?id=49100) till att **migrera scheman** från Azure SQL Database till Azure SQL Data Warehouse. Du kommer att använda Azure Data Factory till att migrera/kopiera data i ett senare steg.

## <a name="azure-services-to-access-sql-server"></a>Azure-tjänster för åtkomst till SQL-servern

Ge Azure-tjänster åtkomst till SQL-servern för både SQL Database och SQL Data Warehouse. Se till att inställningen **Tillåt åtkomst till Azure-tjänster** är **aktiverad** för Azure SQL-servern. Den här inställningen gör att Data Factory-tjänsten kan läsa data från Azure SQL Database och skriva data till Azure SQL Data Warehouse. Gör så här för att kontrollera och aktivera den här inställningen:

1. Klicka på hubben **Fler tjänster** till vänster och klicka på **SQL-servrar**.
2. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
3. På sidan **Brandväggsinställningar** väljer du **På** för **Tillåt åtkomst till Azure-tjänster**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-bulk-copy-portal/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialBulkCopyDF** som **namn**. 
      
     ![Sida för ny datafabrik](./media/tutorial-bulk-copy-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialBulkCopyDF). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
       `Data factory name “ADFTutorialBulkCopyDF” is not available`
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
      Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. För närvarande kan du endast skapa datafabriker i Data Factory V2 i regionerna USA, östra; USA; östra 2 och Europa, västra. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media//tutorial-bulk-copy-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
    ![Datafabrikens startsida](./media/tutorial-bulk-copy-portal/data-factory-home-page.png)
10. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.
11. På sidan **kom igång** växlar du till fliken **Redigera** på den vänstra panelen som på följande bild:  

    ![Sidan Kom igång](./media/tutorial-bulk-copy-portal/get-started-page.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster för att länka dina datalager och beräkna till en datafabrik. En länkad tjänst har anslutningsinformationen som Data Factory-tjänsten använder för att ansluta till datalagret i körningsfasen. 

I den här självstudien länkar du dina Azure SQL Database-, Azure SQL Data Warehouse- och Azure Blob Storage-datalager till datafabriken. Azure SQL Database är källdatalagret. Azure SQL Data Warehouse är mottagar-/måldatalagret. Azure Blob Storage mellanlagrar data innan data läses in i SQL Data Warehouse med hjälp av PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Skapa den länkade tjänsten för Azure SQL Database-källan
I det här steget skapar du en länkad tjänst för att länka Azure SQL-databasen till datafabriken. 

1. Klicka på **Anslutningar** längst ned i fönstret och sedan på **+Ny** i verktygsfältet. 

    ![Knapp för ny länkad tjänst](./media/tutorial-bulk-copy-portal/new-linked-service-button.png)
2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure SQL Database** och klickar på **Fortsätt**. 

    ![Välj Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database.png)
3. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureSqlDatabaseLinkedService** som **namn**. 
    2. Välj din Azure SQL-server som **servernamn**
    3. Välj din Azure SQL-databas som **databasnamn**. 
    4. Ange **användarens namn** för att ansluta till Azure SQL-databasen. 
    5. Ange **lösenord** för användaren. 
    6. Om du vill testa anslutningen till Azure SQL-databasen med den angivna informationen klickar du på **Testanslutning**.
    7. Klicka på **Spara**.

        ![Inställningar för Azure SQL Database](./media/tutorial-bulk-copy-portal/azure-sql-database-settings.png)

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Skapa den länkade tjänsten för Azure SQL Data Warehouse-mottagaren

1. På fliken **Anslutningar** klickar du på **+ Ny** på verktygsfältet igen. 
2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure SQL Data Warehouse** och klickar på **Fortsätt**. 
3. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureSqlDWLinkedService** som **namn**. 
    2. Välj din Azure SQL-server som **servernamn**
    3. Välj din Azure SQL-databas som **databasnamn**. 
    4. Ange **användarens namn** för att ansluta till Azure SQL-databasen. 
    5. Ange **lösenord** för användaren. 
    6. Om du vill testa anslutningen till Azure SQL-databasen med den angivna informationen klickar du på **Testanslutning**.
    7. Klicka på **Spara**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Skapa den länkade tjänsten för Azure Storage-mellanlagringen
I den här självstudien använder du Azure Blob Storage som ett mellanlagringsutrymme så att PolyBase aktiveras och ger kopieringen bättre prestanda.

1. På fliken **Anslutningar** klickar du på **+ Ny** på verktygsfältet igen. 
2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**. 
3. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Ange **AzureStorageLinkedService** som **namn**. 
    2. Välj ditt **Azure-lagringskonto** som **Lagringskontonamn**.
    4. Klicka på **Spara**.


## <a name="create-datasets"></a>Skapa datauppsättningar
I den här självstudien skapar du datauppsättningar för källa och mottagare som anger var data lagras. 

Indatauppsättningen AzureSqlDatabaseDataset refererar till AzureSqlDatabaseLinkedService. Den länkade tjänsten anger anslutningssträngen för att ansluta till databasen. Datauppsättningen anger namnet på databasen och tabellen som innehåller källdata. 

Datauppsättningen för utdata AzureSqlDWDataset refererar till AzureSqlDWLinkedService. Den länkade tjänsten anger anslutningssträngen för att ansluta till informationslagret. Datauppsättningen anger databasen och tabellen som data kopieras till. 

I den här självstudien är käll- och måltabellerna i SQL inte hårdkodade i definitionen för datauppsättningen. Istället skickar ForEach-aktiviteten tabellens namn under körningsfasen till kopieringsaktiviteten. 

### <a name="create-a-dataset-for-source-sql-database"></a>Skapa en datauppsättning för SQL Database-källan

1. Klicka på **+ (plus)** i den vänstra rutan och sedan på **Datauppsättning**. 

    ![Menyn Ny datauppsättning](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
2. Välj **Azure SQL Database** i fönstret **Ny datauppsättning** och klicka på **Slutför**. Du ser en ny flik som heter **AzureSqlTable1**. 
    
    ![Välja Azure SQL Database](./media/tutorial-bulk-copy-portal/select-azure-sql-database-dataset.png)
3. I egenskapsfönstret längst ned skriver du **AzureSqlDatabaseDataset** som **namn**.

    ![Namn på källdatauppsättning](./media/tutorial-bulk-copy-portal/source-dataset-general.png)
4. Växla till fliken **Anslutning** och gör följande: 

    1. Välj **AzureSqlDatabaseLinkedService** som **länkad tjänst**.
    2. Välj valfri tabell för **Tabell**. Den här tabellen är en dummytabell. Du anger en fråga för källdatauppsättningen när du skapar en pipeline. Frågan används för att extrahera data från Azure SQL-databasen. Du kan även klicka i kryssrutan **Redigera** och ange **dummyName** som tabellnamn. 

    ![Sida för källdatauppsättningsanslutning](./media/tutorial-bulk-copy-portal/source-dataset-connection-page.png)
 

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Skapa en datauppsättning för SQL Data Warehouse-mottagaren

1. Klicka på **+ (plus)** i den vänstra rutan och sedan på **Datauppsättning**. 
2. Välj **Azure SQL Data Warehouse** i fönstret **Ny datauppsättning** och klicka på **Slutför**. Du ser en ny flik som heter **AzureSqlDWTable1**. 
3. I egenskapsfönstret längst ned skriver du **AzureSqlDWDataset** som **namn**.
4. Välj fliken **Anslutning** och välj **AzureSqlDatabaseLinkedService** som **länkad tjänst**.
5. Växla till fliken **Parametrar** och klicka på **+ Ny**

    ![Sida för källdatauppsättningsanslutning](./media/tutorial-bulk-copy-portal/sink-dataset-new-parameter-button.png)
6. Ange **DWTableName** som parameternamn. Se till att inga **avslutande blanksteg** följer med i slutet av **DWTableName** om du kopierar/klistrar in det här namnet från sidan. 
7. I avsnittet **Parametriserade egenskaper** anger du `@{dataset().DWTableName}` för egenskapen **tableName**. Uppsättningens egenskap **tableName** är inställd på värdet som har skickats som argument för parametern **DWTableName**. ForEach-aktiviteten itereras via en lista över tabeller och skickar en i taget till kopieringsaktiviteten. 
   
    ![Parameternamn](./media/tutorial-bulk-copy-portal/dwtablename-tablename.png)

## <a name="create-pipelines"></a>Skapa pipelines
I den här självstudien skapar du två pipeliner: **IterateAndCopySQLTables** och **GetTableListAndTriggerCopyData**. 

Pipelinen **GetTableListAndTriggerCopyData** utför två steg:

* den söker i Azure SQL Database-systemtabellen för att få fram listan med tabeller som ska kopieras
* Den utlöser pipelinen **IterateAndCopySQLTables** för att utföra den faktiska datakopieringen.

**GetTableListAndTriggerCopyData** listar tabeller som en parameter. För varje tabell i listan kopieras data från tabellen i Azure SQL Database till Azure SQL Data Warehouse med hjälp av mellanlagrad kopiering och PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Skapa pipelinen IterateAndCopySQLTables

1. I den vänstra rutan klickar du på **+ (plus)** och sedan på **Pipeline**.

    ![Meny för ny pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. I fönstret Egenskaper byter du namn på pipelinen till **IterateAndCopySQLTables**. 

    ![Namn på pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-name.png)
3. Växla till fliken **Parametrar** och gör följande: 

    1. Klicka på **+ Ny**. 
    2. Ange **tableList** som parameterns **namn**.
    3. Välj **Objekt** som **typ**.

        ![Pipeline-parameter](./media/tutorial-bulk-copy-portal/first-pipeline-parameter.png)
4. I verktygslådan **Aktiviteter** expanderar du **Iteration & Conditions** (Iteration och villkor) och drar och släpper aktiviteten **ForEach** till pipelinedesignytan. Du kan också söka efter aktiviteter i verktygslådan **Aktiviteter**. I **egenskapsfönstret** längst ned skriver du **IterateSQLTables** som **namn**. 

    ![ForEach-aktivitetsnamn](./media/tutorial-bulk-copy-portal/for-each-activity-name.png)
5. Växla till fliken **Inställningar** och ange `@pipeline().parameters.tableList` för **Objekt**.

    ![ForEach-aktivitetsinställning](./media/tutorial-bulk-copy-portal/for-each-activity-settings.png)
6. Om du vill lägga till en underordnad aktivitet till aktiviteten **ForEach** **dubbelklickar du på** ForEach-aktiviteten (eller) klickar på **Redigera (pennikonen)**. Du ser bara åtgärdslänkar för en aktivitet när du markerar den. 

    ![ForEach-aktivitetsnamn](./media/tutorial-bulk-copy-portal/edit-for-each-activity.png)
7. I verktygslådan **Aktiviteter** expanderar du **DataFlow** och drar och släpper **kopieringsaktiviteten** till pipelinedesignerytan och byter namn i egenskapsfönstret till **CopyData**. Lägg märke till adressfältmenyn längst upp. IterateAndCopySQLTable är pipelinenamnet och IterateSQLTables är ForEach-aktivitetsnamnet. Designern är i aktivitetsomfånget. Om du vill gå tillbaka till pipeline-redigeringsprogrammet från ForEach-redigeringsprogrammet klickar du på länken på adressfältmenyn. 

    ![Kopiera i ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)
8. Växla till fliken **Källa** och gör följande:

    1. Välj **AzureSqlDatabaseDataset** för **källdatauppsättning**. 
    2. Välj alternativet **Fråga** för **User Query** (Användarfråga). 
    3. Ange följande SQL-fråga för **Fråga**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

        ![Inställningar för att kopiera källa](./media/tutorial-bulk-copy-portal/copy-source-settings.png)
9. Växla till fliken **Mottagare** och gör följande: 

    1. Välj **AzureSqlDWDataset** för **Sink Dataset** (Datauppsättning för mottagare).
    2. Expandera **Polybase Settings** (Polybase-inställningar) och välj **Allow polybase** (Tillåt polybase). 
    3. Avmarkera alternativet **Use Type default** (Standardanvändartyp). 
    4. Ange följande SQL-skript för **Cleanup Script** (Rensningsskript). 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Inställningar för att kopiera mottagare](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)
10. Växla till fliken **Parametrar**, och om det behövs bläddrar du ned för att se avsnittet **Sink Dataset** (Datauppsättning för mottagare) med parametern **DWTableName**. Ange `[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]` som parameterns värde.

    ![Parametrar för kopieringsmottagare](./media/tutorial-bulk-copy-portal/copy-sink-parameters.png)
11. Växla till fliken **Inställningar** och gör följande: 

    1. Välj **Sant** för **Enable Staging** (Aktivera mellanlagring).
    2. Välj **AzureStorageLinkedService** för **Store Account Linked Service** (Länkad tjänst för lagringskonto).

        ![Aktivera mellanlagring](./media/tutorial-bulk-copy-portal/copy-sink-staging-settings.png)
12. Verifiera pipelineinställningarna genom att klicka på **Verifiera**. Kontrollera att det inte finns några verifieringsfel. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>**.

    ![Verifieringsrapport för pipeline](./media/tutorial-bulk-copy-portal/first-pipeline-validation-report.png)

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Skapa pipelinen GetTableListAndTriggerCopyData

Den här pipelinen utför två steg:

* den söker i Azure SQL Database-systemtabellen för att få fram listan med tabeller som ska kopieras
* den utlöser pipelinen ”IterateAndCopySQLTables” för att utföra den faktiska kopieringen.

1. I den vänstra rutan klickar du på **+ (plus)** och sedan på **Pipeline**.

    ![Meny för ny pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
2. I fönstret Egenskaper ändrar du pipelinenamnet till **GetTableListAndTriggerCopyData**. 

    ![Namn på pipeline](./media/tutorial-bulk-copy-portal/second-pipeline-name.png)
3. I verktygslådan **Aktiviteter** expanderar du **SQL Database** och drar och släpper **sökningsaktiviteten** till pipelinedesignytan och utför följande steg:

    1. Skriv **LookupTableList** som **namn**. 
    2. Ange **Retrieve the table list from Azure SQL database** (Hämta tabellistan från Azure SQL Database) som **Beskrivning**.

        ![Sökningsaktivitet – sidan allmänt](./media/tutorial-bulk-copy-portal/lookup-general-page.png)
4. Växla till sidan **Inställningar** och gör följande:

    1. Välj **AzureSqlDatabaseDataset** för **källdatauppsättning**. 
    2. Välj **Fråga** för **Använd fråga**. 
    3. Ange följande SQL-fråga för **Fråga**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    4. Avmarkera kryssrutan för fältet **First row only** (Endast första raden).

        ![Sökningsaktivitet – inställningssida](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
5. Dra och släpp en till **Kör pipeline-aktivitet** från verktygslådan Aktiviteter till pipelinedesignerytan och ange **TriggerCopy** som namn.

    ![Kör pipeline-aktivitet – sidan allmänt](./media/tutorial-bulk-copy-portal/execute-pipeline-general-page.png)    
6. Växla till sidan **Inställningar** och gör följande: 

    1. Välj **IterateAndCopySQLTables** som **Invoked pipeline** (Anropad pipeline). 
    2. Expandera avsnittet **Avancerat**. 
    3. Klicka på **+ Ny** i avsnittet **Parametrar**. 
    4. Skriv **tableList** som parameterns **namn**.
    5. Anger `@activity('LookupTableList').output.value`som parametervärde****. Du ställer in resultatlistan från sökningsaktiviteten som indata för den andra pipelinen. Resultatlistan innehåller listan över tabeller vars data ska kopieras till målet. 

        ![Kör pipeline-aktivitet – sidan inställningar](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)
7. **Anslut** **sökningsaktiviteten** till **Kör pipeline-aktiviteten** genom att dra den **gröna rutan** som är ansluten till sökningsaktiviteten till vänster om Kör pipeline-aktiviteten.

    ![Ansluta kopierings- och Kör pipeline-aktiviteter](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)
8. Verifiera pipelinen genom att klicka på **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>**.

    ![Den andra pipelinen – verifieringsrapport](./media/tutorial-bulk-copy-portal/second-pipeline-validation-report.png)
9. Om du vill publicera entiteter (datauppsättningar, pipeliner osv.) till Data Factory-tjänsten klickar du på **Publicera**. Vänta tills publiceringen har lyckats. 

    ![Knappen Publicera](./media/tutorial-bulk-copy-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Utlös en pipelinekörning

1. Kontrollera att fliken **GetTableListAndTriggerCopyData** är aktiv. 
2. Klicka på **Utlösare** i och klicka på **Trigger Now** (Utlös nu). 

    ![Utlös nu](./media/tutorial-bulk-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Välj fliken **Övervaka**. Klicka på **Uppdatera** tills du ser körningar för båda dina pipeliner i lösningen. Fortsätt att uppdatera listan tills du ser statusen **Lyckades**. 

    ![Pipelinekörningar](./media/tutorial-bulk-copy-portal/pipeline-runs.png)
2. Om du vill visa aktivitetskörningar som är associerade med GetTableListAndTriggerCopyData-pipelinen klickar du på den första länken i Åtgärder-länken för den pipelinen. Du ska se två aktivitetskörningar för den här pipelinekörningen. 

    ![Aktivitetskörningar](./media/tutorial-bulk-copy-portal/activity-runs-1.png)    
3. Om du vill visa utdata för **sökningsaktiviteten** klickar du på länken i kolumnen **Utdata** för den aktiviteten. Du kan maximera och återställa fönstret **Utdata**. Stäng fönstret **Utdata** när du har sett klart genom att klicka på **X**.

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
4. Om du vill växla tillbaka till vyn med **pipelinekörningar** klickar du på länken **Pipeliner** högst upp. Klicka på länken **View Activity Runs** (Visa aktivitetskörningar) (den första länken i kolumnen **Åtgärder**) för pipelinen **IterateAndCopySQLTables**. Du bör se utdata som på följande avbildning: Lägg märke till att det finns en **kopieringsaktivitetskörning** för varje tabell i **sökningsaktivitetens** utdata. 

    ![Aktivitetskörningar](./media/tutorial-bulk-copy-portal/activity-runs-2.png)
5. Bekräfta att data har kopierats till SQL Data Warehouse-målet du använde i den här självstudien. 

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
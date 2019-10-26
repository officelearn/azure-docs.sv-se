---
title: Använda SQL Database referens data i ett Azure Stream Analytics jobb
description: Den här artikeln beskriver hur du använder en SQL Database som referens data inmatning för ett Azure Stream Analytics jobb i Azure Portal och i Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 733ac7d1ff1d50e5fdcfa0dec2ad3fd3f30f6d86
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72926678"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Använda referens data från en SQL Database för ett Azure Stream Analytics jobb

Azure Stream Analytics stöder Azure SQL Database som data källa för referens data. Du kan använda SQL Database som referens data för ditt Stream Analytics-jobb i Azure Portal och i Visual Studio med Stream Analytics verktyg. Den här artikeln visar hur du gör båda metoderna.

## <a name="azure-portal"></a>Azure portal

Använd följande steg för att lägga till Azure SQL Database som referens källa med hjälp av Azure Portal:

### <a name="portal-prerequisites"></a>Portal krav

1. Skapa ett Stream Analytics jobb.

2. Skapa ett lagrings konto som ska användas av Stream Analyticss jobbet.

3. Skapa din Azure SQL Database med en data uppsättning som ska användas som referens data av Stream Analyticss jobbet.

### <a name="define-sql-database-reference-data-input"></a>Definiera SQL Database referens data inmatning

1. I ditt Stream Analytics jobb väljer du **indata** under **jobb sto pol Ogin**. Klicka på **Lägg till referens inmatare** och välj **SQL Database**.

   ![Stream Analytics jobbets ingångar](./media/sql-reference-data/stream-analytics-inputs.png)

2. Fyll i Stream Analytics inmatade konfigurationer. Välj databas namn, Server namn, användar namn och lösen ord. Om du vill att referens data indata ska uppdateras regelbundet väljer du "på" för att ange uppdaterings frekvensen i DD: HH: MM. Om du har stora data mängder med en kort uppdaterings frekvens kan du använda en [delta fråga](sql-reference-data.md#delta-query).

   ![SQL Database referens konfiguration](./media/sql-reference-data/sql-input-config.png)

3. Testa ögonblicks bild frågan i SQL-Frågeredigeraren. Mer information finns i [använda Azure Portal SQL-Frågeredigeraren för att ansluta och fråga efter data](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Ange lagrings konto i jobb konfigurationen

Gå till **Inställningar för lagrings kontot** under **Konfigurera** och välj **Lägg till lagrings konto**.

   ![Stream Analytics inställningar för lagrings konto](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Starta jobbet

När du har konfigurerat andra indata, utdata och frågor kan du starta Stream Analytics jobbet.

## <a name="tools-for-visual-studio"></a>Verktyg för Visual Studio

Använd följande steg för att lägga till Azure SQL Database som referens källa med Visual Studio:

### <a name="visual-studio-prerequisites"></a>Krav för Visual Studio

1. [Installera Stream Analytics verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Följande versioner av Visual Studio stöds:

   * Visual Studio 2015
   * Visual Studio 2019

2. Bekanta dig med [Stream Analytics Tools för Visual Studio](stream-analytics-quick-create-vs.md) snabb start.

3. Skapa ett lagringskonto.

### <a name="create-a-sql-database-table"></a>Skapa en SQL Database tabell

Använd SQL Server Management Studio för att skapa en tabell för att lagra dina referens data. Se [utforma din första Azure SQL-databas med SSMS](../sql-database/sql-database-design-first-database.md) för mer information.

Exempel tabellen som används i följande exempel skapades från följande instruktion:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Välj din prenumeration

1. Gå till menyn **Visa** och välj **Server Explorer** i Visual Studio.

2. Högerklicka på **Azure**, Välj **Anslut till Microsoft Azure prenumeration**och logga in med ditt Azure-konto.

### <a name="create-a-stream-analytics-project"></a>Skapa ett Stream Analytics-projekt

1. Välj **Arkiv > Nytt projekt**. 

2. I listan över mallar till vänster väljer du **Stream Analytics** och sedan **Azure Stream Analytics-programmet**. 

3. Ange projekt **namn**, **plats**och **lösnings namn**och välj **OK**.

   ![Nytt Stream Analytics projekt i Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definiera SQL Database referens data inmatning

1. Skapa en ny indatamängd.

   ![Nya Stream Analytics inmatade i Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Dubbelklicka på **mata in. JSON** i **Solution Explorer**.

3. Fyll i **konfigurationen för Stream Analytics Indatamängden**. Välj databasens namn, Server namn, uppdaterings typ och uppdaterings frekvens. Ange uppdaterings frekvensen i formatet `DD:HH:MM`.

   ![Stream Analytics indatamängds konfiguration i Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Om du väljer "kör endast en gång" eller "kör regelbundet", skapas en SQL CodeBehind-fil med namnet **[indata Ali Aset]. snapshot. SQL** i projektet under noden **indata-JSON** -fil.

   ![Mata in kod bakom i Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Om du väljer att uppdatera regelbundet med delta kommer två SQL CodeBehind-filer att genereras: **[Indataområde]. snapshot. SQL** och **[Indataområde]. delta. SQL**.

   ![Kod bakom i Solution Explorer](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Öppna SQL-filen i redigeraren och Skriv SQL-frågan.

5. Om du använder Visual Studio 2019 och har installerat SQL Server data verktyg kan du testa frågan genom att klicka på **Kör**. Ett guide fönster visas för att hjälpa dig att ansluta till SQL-databasen och frågeresultatet visas i fönstret längst ned.

### <a name="specify-storage-account"></a>Ange lagrings konto

Öppna **JobConfig. JSON** för att ange lagrings konto för lagring av ögonblicks bilder av SQL-referens.

   ![Stream Analytics jobb konfiguration i Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testa lokalt och distribuera till Azure

Innan du distribuerar jobbet till Azure kan du testa frågans logik lokalt mot Live-indata. Mer information om den här funktionen finns i [testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio (för hands version)](stream-analytics-live-data-local-testing.md). När du är klar med testningen klickar du på **Skicka till Azure**. Referera till [skapa en Stream Analytics med hjälp av Azure Stream Analytics verktyg för Visual Studio](stream-analytics-quick-create-vs.md) snabb start för att lära dig hur du startar jobbet.

## <a name="delta-query"></a>Delta fråga

När du använder delta frågan rekommenderas [temporala tabeller i Azure SQL Database](../sql-database/sql-database-temporal-tables.md) .

1. Skapa en temporal tabell i Azure SQL Database.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Redigera ögonblicks bild frågan. 

   Använd parametern **\@snapshotTime** för att instruera Stream Analytics runtime att hämta referens data uppsättningen från den temporala SQL Database-tabellen som är giltig vid system tiden. Om du inte anger den här parametern riskerar du att få en felaktig bas referens data uppsättning på grund av klock skevar. Ett exempel på en fullständig ögonblicks bild fråga visas nedan:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Redigera delta frågan. 
   
   Den här frågan hämtar alla rader i SQL-databasen som infogades eller togs bort inom en start tid, **\@deltaStartTime**och en slut tid **\@deltaEndTime**. Delta frågan måste returnera samma kolumner som ögonblicks bild frågan och kolumn **_åtgärden_** . Den här kolumnen definierar om raden infogas eller tas bort mellan **\@deltaStartTime** och **\@deltaEndTime**. De resulterande raderna flaggas som **1** om posterna infogades eller **2** om de tagits bort. 

   För poster som har uppdaterats bokförs den temporala tabellen genom att en infognings-och borttagnings åtgärd fångas. Den Stream Analytics körningen använder sedan resultatet av delta frågan till föregående ögonblicks bild för att hålla referens data uppdaterade. Ett exempel på en delta fråga visas nedan:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Observera att Stream Analytics runtime kan köra ögonblicks bilds frågan med jämna mellanrum förutom delta-frågan för att lagra kontroll punkter.

## <a name="test-your-query"></a>Testa frågan
   Det är viktigt att kontrol lera att din fråga returnerar den förväntade data uppsättning som Stream Analyticss jobbet kommer att använda som referens data. Om du vill testa din fråga går du till ininformation under jobb sto sektion på portalen. Du kan sedan välja exempel data på SQL Database referens indata. När exemplet blir tillgängligt kan du hämta filen och kontrol lera om de data som returneras är som förväntat. Om du vill optimera dina utvecklings-och test iterationer rekommenderar vi att du använder [Stream Analytics verktyg för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install). Du kan också använda andra verktyg för att först se till att frågan returnerar rätt resultat från dig Azure SQL Database och sedan använder du den i ditt Stream Analytics-jobb. 

## <a name="faqs"></a>Vanliga frågor och svar

**Debiteras jag ytterligare kostnader genom att använda indata från SQL-referenser i Azure Stream Analytics?**

Det finns inga ytterligare [kostnader per strömnings enhet](https://azure.microsoft.com/pricing/details/stream-analytics/) i Stream Analyticss jobbet. Stream Analyticss jobbet måste dock ha ett associerat Azure Storage-konto. Stream Analyticss jobbet frågar SQL DB (under jobb start och uppdaterings intervall) för att hämta referens data uppsättningen och lagrar ögonblicks bilden i lagrings kontot. Genom att lagra dessa ögonblicks bilder debiteras ytterligare avgifter på [sidan med priser](https://azure.microsoft.com/pricing/details/storage/) för Azure Storage-kontot.

**Hur gör jag för att vet du att ögonblicks bilden av referens data från SQL DB och används i Azure Stream Analyticss jobbet?**

Det finns två mått som filtreras efter logiskt namn (under Metrics Azure Portal) som du kan använda för att övervaka hälsan för SQL Database referens data inmatning.

   * InputEvents: det här måttet mäter antalet poster som läses in i från referens data uppsättningen för SQL Database.
   * InputEventBytes: det här måttet mäter storleken på den referens data ögonblicks bild som lästs in i minnet för Stream Analytics jobbet. 

Kombinationen av båda dessa mått kan användas för att härleda om jobbet frågar SQL Database för att hämta referens data uppsättningen och sedan läser in den i minnet.

**Måste jag ha en särskild typ av Azure SQL Database?**

Azure Stream Analytics kommer att fungera med vilken typ av Azure SQL Database som helst. Det är dock viktigt att förstå att uppdaterings frekvensen som angetts för dina data indata kan påverka inläsningen av frågan. Om du vill använda delta-frågealternativet rekommenderar vi att du använder temporala tabeller i Azure SQL Database.

**Varför lagrar Azure Stream Analytics ögonblicks bilder i Azure Storage konto?**

Stream Analytics garanterar händelsebearbetning exakt en gång och leverans av händelser minst en gång. I de fall där tillfälliga problem påverkar ditt jobb är en liten mängd omuppspelning nödvändig för att återställa status. Om du vill aktivera uppspelning måste du ha dessa ögonblicks bilder lagrade i ett Azure Storage-konto. Mer information om kontroll punkts återuppspelning finns i avsnittet [om kontroll punkter och omuppspelning av begrepp i Azure Stream Analytics jobb](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Nästa steg

* [Använda referens data för sökningar i Stream Analytics](stream-analytics-use-reference-data.md)
* [Snabb start: skapa ett Stream Analytics jobb med hjälp av Azure Stream Analytics verktyg för Visual Studio](stream-analytics-quick-create-vs.md)
* [Testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio (för hands version)](stream-analytics-live-data-local-testing.md)

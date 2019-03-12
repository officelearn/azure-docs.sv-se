---
title: Använd referensdata från en SQL-databas för Azure Stream Analytics-jobb (förhandsversion)
description: Den här artikeln beskriver hur du använder en SQL-databas som referensindata för Azure Stream Analytics-jobb i Azure-portalen och i Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: efd450edb87316e75fc240cac80eda93151a22b3
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765092"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job-preview"></a>Använd referensdata från en SQL-databas för Azure Stream Analytics-jobb (förhandsversion)

Azure Stream Analytics har stöd för Azure SQL Database som en källa av indata för referensdata. Du kan använda SQL-databas som referensdata för ditt Stream Analytics-jobb i Azure-portalen och i Visual Studio med Stream Analytics-verktyg. Den här artikeln visar hur du gör båda metoderna.

## <a name="azure-portal"></a>Azure Portal

Använd följande steg för att lägga till Azure SQL Database som en referens Indatakällan med Azure portal:

### <a name="portal-prerequisites"></a>Krav för Portal

1. Skapa ett Stream Analytics-jobb.

2. Skapa ett lagringskonto som ska användas av Stream Analytics-jobb.

3. Skapa din Azure SQL Database med en mängd data som ska användas som för referensdata av Stream Analytics-jobb.

### <a name="define-sql-database-reference-data-input"></a>Definiera referensindata för SQL-databas

1. I ditt Stream Analytics-jobb väljer **indata** under **jobbtopologi**. Klicka på **Lägg till referens indata** och välj **SQL Database**.

   ![Stream Analytics-jobbindata](./media/sql-reference-data/stream-analytics-inputs.png)

2. Fyll i Stream Analytics indata konfigurationer. Välj databasens namn, servernamn, användarnamn och lösenord. Om du vill att din referensdata som indata för att uppdatera med jämna mellanrum, väljer du ”på” Ange uppdateringsfrekvensen i DD:HH:MM. Om du har stora datauppsättningar med ett kort intervall kan du använda en [deltafråga](sql-reference-data.md#delta-query).

   ![Konfiguration för SQL Database-referens](./media/sql-reference-data/sql-input-config.png)

3. Testa ögonblicksbild-fråga i SQL-frågeredigeraren. Mer information finns i [använder Azure portal SQL-frågeredigeraren för att ansluta och fråga efter data](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Ange storage-konto i jobbkonfigurationen

Gå till **inställningarna för Lagringskontot** under **konfigurera** och välj **Lägg till lagringskonto**.

   ![Inställningarna för Stream Analytics-konto](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Starta jobbet

När du har konfigurerat andra indata, utdata och frågor kan starta du Stream Analytics-jobb.

## <a name="tools-for-visual-studio"></a>Verktyg för Visual Studio

Använd följande steg för att lägga till Azure SQL Database som en referens Indatakällan med Visual Studio:

### <a name="visual-studio-prerequisites"></a>Visual Studio-krav

1. Om du använder Visual Studio 2017, uppdatera 15.8.2 eller senare. Observera att 16,0 och senare stöds inte i det här läget.

2. [Installera Stream Analytics-verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Följande versioner av Visual Studio stöds:

   * Visual Studio 2015
   * Visual Studio 2017

3. Bekanta dig med den [Stream Analytics-verktyg för Visual Studio](stream-analytics-quick-create-vs.md) Snabbstart.

4. Skapa ett lagringskonto.

### <a name="create-a-sql-database-table"></a>Skapa en SQL-databastabell

Använda SQL Server Management Studio för att skapa en tabell för att lagra dina referensdata. Se [utforma din första Azure SQL-databas med SSMS](../sql-database/sql-database-design-first-database.md) mer information.

Exempeltabell som används i följande exempel har skapats från följande uttryck:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Välj din prenumeration

1. Gå till menyn **Visa** och välj **Server Explorer** i Visual Studio.

2. Högerklicka på **Azure**väljer **Anslut till Microsoft Azure-prenumeration**, och logga in med ditt Azure-konto.

### <a name="create-a-stream-analytics-project"></a>Skapa ett Stream Analytics-projekt

1. Välj **Arkiv > Nytt projekt**. 

2. I listan över mallar till vänster väljer du **Stream Analytics** och sedan **Azure Stream Analytics-programmet**. 

3. Ange projektet **namn**, **plats**, och **lösningsnamn**, och välj **OK**.

   ![Nytt Stream Analytics-projekt i Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definiera referensindata för SQL-databas

1. Skapa en ny indata.

   ![Nya indata för Stream Analytics i Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Dubbelklicka på **Input.json** i den **Solution Explorer**.

3. Fyll i **Stream Analytics indata Configuration**. Välj databasens namn, servernamn, uppdateringstyp av och uppdateringsintervall. Ange uppdateringsfrekvensen i formatet `DD:HH:MM`.

   ![Stream Analytics inkommande konfigurationen i Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Om du väljer ”Kör bara en gång” eller ”kör regelbundet”, en bakomliggande koden SQL-fil med namnet **[indata Alias].snapshot.sql** har genererats i projektet under den **Input.json** noden fil.

   ![Inkommande koden bakom i Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Om du väljer ”Uppdatera regelbundet med Delta” två SQL bakomliggande koden filer kommer att genereras: **[indata Alias].snapshot.sql** och **[indata Alias].delta.sql**.

   ![Code bakom i solution explorer](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Öppna SQL-filen i redigeraren och skriva SQL-frågan.

5. Om du använder Visual Studio 2017 och du har installerat SQL Server Data tools, du kan testa frågan genom att klicka på **kör**. Ett fönster med guiden kommer att visas för att ansluta till SQL-databasen och frågeresultatet visas i fönstret längst ned på sidan.

### <a name="specify-storage-account"></a>Ange storage-konto

Öppna **JobConfig.json** att ange konto för att lagra SQL-referens för ögonblicksbilder.

   ![Stream Analytics-jobbkonfigurationen i Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testa lokalt och distribuera till Azure

Du kan testa frågelogiken lokalt mot live indata innan du distribuerar jobbet till Azure. Mer information om den här funktionen finns i [testa realtidsdata lokalt med hjälp av Azure Stream Analytics-verktyg för Visual Studio (förhandsversion)](stream-analytics-live-data-local-testing.md). När du är klar testning, klickar du på **skicka till Azure**. Referens för den [skapar ett Stream Analytics med hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-quick-create-vs.md) Snabbstart och lär dig att starta jobbet.

## <a name="delta-query"></a>Deltafråga

När du använder deltafråga [temporala tabeller i Azure SQL Database](../sql-database/sql-database-temporal-tables.md) rekommenderas.

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
2. Redigera frågan som ögonblicksbild. 

   Använd den  **\@snapshotTime** parametern att instruera Stream Analytics-körning för att hämta referensdatauppsättningen från SQL database temporaltabellen giltig på systemklockan. Om du inte anger den här parametern kan riskerar du att en felaktig grundläggande referensdatauppsättning följd av klockavvikelser. Ett exempel på fullständig ögonblicksbild fråga visas nedan:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Redigera deltafråga. 
   
   Den här frågan returnerar alla rader i din SQL-databas som har infogats eller togs bort inom en starttid  **\@deltaStartTime**, och en sluttid  **\@deltaEndTime**. Delta-frågan måste returnera samma kolumner som ögonblicksbild frågan, samt kolumnen  **_åtgärden_**. Den här kolumnen anger om raden har infogats eller tagits bort mellan  **\@deltaStartTime** och  **\@deltaEndTime**. De resulterande raderna är flaggade som **1** om posterna infogades eller **2** om tas bort. 

   För poster som har uppdaterats, gör den temporala tabellen bokföring genom att samla in en infogning och borttagning åtgärd. Stream Analytics-runtime gäller sedan delta frågans resultat för den tidigare ögonblicksbilden att hålla referensdata uppdaterade. Ett exempel på deltafråga som visas nedan:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
  Observera att Stream Analytics runtime regelbundet kan köra ögonblicksbild frågan förutom deltafråga för att lagra kontrollpunkter.

## <a name="faqs"></a>Vanliga frågor och svar

**Kommer jag att ha ytterligare kostnader med hjälp av SQL-referensindata i Azure Stream Analytics?**

Det finns inga fler [kostnad per enhet för strömning](https://azure.microsoft.com/pricing/details/stream-analytics/) i Stream Analytics-jobb. Stream Analytics-jobb måste dock ha en associerad Azure storage-konto. Stream Analytics-jobbet frågar SQL DB (under jobbet startar och uppdateringsintervall) att hämta referensdatauppsättning och Arkiv som ögonblicksbild i lagringskontot. Lagring av de här ögonblicksbilderna tillkommer ytterligare kostnader som beskrivs i den [prissättningssidan](https://azure.microsoft.com/pricing/details/storage/) för Azure storage-konto.

**Hur vet jag referens data ögonblicksbild som efterfrågas från SQL DB och används i Azure Stream Analytics-jobbet?**

Det finns två mått som filtreras via logiskt namn (under mått Azure Portal) som du kan använda för att övervaka hälsotillståndet för SQL database referensdata indata.

   * InputEvents: Det här mätvärdet mäter antalet poster som har lästs in i från referensdatauppsättning för SQL-databas.
   * InputEventBytes: Det här mätvärdet mäter storleken på referens dataögonblicksbilden inlästa i minnet för Stream Analytics-jobb. 

En kombination av båda de här måtten kan användas för att härleda om jobbet är SQL-databasen för att hämta referensdatauppsättningen och läsa in den till minne.

**Behöver jag en särskild typ av Azure SQL Database?**

Azure Stream Analytics fungerar med alla typer av Azure SQL Database. Det är dock viktigt att förstå att uppdateringsfrekvensen för din referensindata kan påverka din frågebelastning. Om du vill använda frågealternativet delta, rekommenderar vi att du använder temporala tabeller i Azure SQL Database.

**Kan jag ta prov på indata från SQL Database referensdata indata?**

Den här funktionen är inte tillgänglig.

**Varför Azure Stream Analytics lagrar ögonblicksbilder i Azure Storage-konto?**

Stream Analytics garanterar händelsebearbetning exakt en gång och leverans av händelser minst en gång. I fall där utfärdar tillfälligt inverkan ditt jobb är en liten mängd repetitionsattacker nödvändigt att återställa tillståndet. Om du vill aktivera repetitionsattacker, krävs det att ha dessa ögonblicksbilder som lagras i ett Azure Storage-konto. Läs mer på kontrollpunkt repetitionsattacker [kontrollpunkt och återuppspelning begrepp i Azure Stream Analytics-jobb](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Nästa steg

* [Med hjälp av referensdata för sökningar i Stream Analytics](stream-analytics-use-reference-data.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-quick-create-vs.md)
* [Testa realtidsdata lokalt med hjälp av Azure Stream Analytics-verktyg för Visual Studio (förhandsversion)](stream-analytics-live-data-local-testing.md)

---
title: Använda SQL Database-referensdata i ett Azure Stream Analytics-jobb
description: I den här artikeln beskrivs hur du använder en SQL-databas som referensdataindata för ett Azure Stream Analytics-jobb i Azure-portalen och i Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: aebb590d93b3fb26151f15c176a2941845cdd50c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426508"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Använda referensdata från en SQL-databas för ett Azure Stream Analytics-jobb

Azure Stream Analytics stöder Azure SQL Database som en källa till indata för referensdata. Du kan använda SQL Database som referensdata för ditt Stream Analytics-jobb i Azure-portalen och i Visual Studio med Stream Analytics-verktyg. Den här artikeln visar hur du gör båda metoderna.

## <a name="azure-portal"></a>Azure Portal

Följ följande steg för att lägga till Azure SQL Database som referensindatakälla med Azure-portalen:

### <a name="portal-prerequisites"></a>Portal förutsättningar

1. Skapa ett Stream Analytics-jobb.

2. Skapa ett lagringskonto som ska användas av Stream Analytics-jobbet.

3. Skapa din Azure SQL-databas med en datauppsättning som ska användas som referensdata av Stream Analytics-jobbet.

### <a name="define-sql-database-reference-data-input"></a>Definiera referensdataindata för SQL-databas

1. I ditt Stream Analytics-jobb väljer du **Indata** under **Jobbtopologi**. Klicka på **Lägg till referensindata** och välj **SQL Database**.

   ![Strömma analytics-jobbindata](./media/sql-reference-data/stream-analytics-inputs.png)

2. Fyll i indatakonfigurationerna för Stream Analytics. Välj databasnamn, servernamn, användarnamn och lösenord. Om du vill att referensdatainmatningen ska uppdateras med jämna mellanrum väljer du "På" för att ange uppdateringsfrekvensen i DD:HH:MM. Om du har stora datauppsättningar med en kort uppdateringsfrekvens kan du använda en [deltafråga](sql-reference-data.md#delta-query).

   ![Referenskonfiguration för SQL-databas](./media/sql-reference-data/sql-input-config.png)

3. Testa ögonblicksbildfrågan i SQL-frågeredigeraren. Mer information finns i [Använda Azure-portalens SQL-frågeredigerare för att ansluta och fråga data](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Ange lagringskonto i Jobbkonfiguration

Navigera till **Lagringskontoinställningar** under **Konfigurera** och välj **Lägg till lagringskonto**.

   ![Inställningar för Stream Analytics-lagringskonto](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Starta jobbet

När du har konfigurerat andra indata, utdata och frågor kan du starta Stream Analytics-jobbet.

## <a name="tools-for-visual-studio"></a>Verktyg för Visual Studio

Så här lägger du till Azure SQL Database som referensindatakälla med Visual Studio:

### <a name="visual-studio-prerequisites"></a>Förutsättningar för Visual Studio

1. [Installera Stream Analytics-verktygen för Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Följande versioner av Visual Studio stöds:

   * Visual Studio 2015
   * Visual Studio 2019

2. Bekanta dig med [snabbstarten streamanalys för Visual Studio.](stream-analytics-quick-create-vs.md)

3. Skapa ett lagringskonto.

### <a name="create-a-sql-database-table"></a>Skapa en SQL Database-tabell

Använd SQL Server Management Studio för att skapa en tabell för att lagra referensdata. Mer information [finns i Designa din första Azure SQL-databas med SSMS.](../sql-database/sql-database-design-first-database.md)

Exempeltabellen som används i följande exempel skapades från följande satsen:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Välj din prenumeration

1. Gå till menyn **Visa** och välj **Server Explorer** i Visual Studio.

2. Högerklicka på **Azure**, välj **Anslut till Microsoft Azure-prenumeration**och logga in med ditt Azure-konto.

### <a name="create-a-stream-analytics-project"></a>Skapa ett Stream Analytics-projekt

1. Välj **Arkiv > Nytt projekt**. 

2. I listan över mallar till vänster väljer du **Stream Analytics** och sedan **Azure Stream Analytics-programmet**. 

3. Ange projektet **Namn,** **Plats**och **Lösningsnamn**och välj **OK**.

   ![Nytt Stream Analytics-projekt i Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definiera referensdataindata för SQL-databas

1. Skapa en ny indata.

   ![Ny Stream Analytics-inmatning i Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Dubbelklicka på **Input.json** i **Lösningsutforskaren**.

3. Fyll i **indatakonfigurationen för Stream Analytics**. Välj databasnamn, servernamn, uppdateringstyp och uppdateringsfrekvens. Ange uppdateringsfrekvensen i `DD:HH:MM`formatet .

   ![Stream Analytics-indatakonfiguration i Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Om du väljer "Kör bara en gång" eller "Kör med jämna mellanrum" genereras en SQL-codeBehind-fil med namnet **[Input Alias].snapshot.sql** i projektet under filnoden **Input.json.**

   ![Indatakod bakom i Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Om du väljer "Uppdatera med jämna mellanrum med Delta" genereras två SQL-codeBehind-filer: **[Input Alias].snapshot.sql** och **[Input Alias].delta.sql**.

   ![Kod bakom i lösningsutforskaren](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Öppna SQL-filen i redigeraren och skriv SQL-frågan.

5. Om du använder Visual Studio 2019 och har installerat SQL Server Data-verktyg kan du testa frågan genom att klicka på **Kör**. Ett guidefönster visas för att hjälpa dig att ansluta till SQL-databasen och frågeresultatet visas i fönstret längst ned.

### <a name="specify-storage-account"></a>Ange lagringskonto

Öppna **JobConfig.json** för att ange lagringskontot för lagring av SQL-referensögonblicksbilder.

   ![Stream Analytics-jobbkonfiguration i Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testa lokalt och distribuera till Azure

Innan du distribuerar jobbet till Azure kan du testa frågelogiken lokalt mot liveindata. Mer information om den här funktionen finns i [Testa livedata lokalt med Hjälp av Azure Stream Analytics-verktyg för Visual Studio (förhandsversion).](stream-analytics-live-data-local-testing.md) När du är klar med testningen klickar du på **Skicka till Azure**. Referera till [snabbstarten Skapa en Stream Analytics med snabbstarten i Azure Stream Analytics för Visual Studio](stream-analytics-quick-create-vs.md) för att lära dig hur du startar jobbet.

## <a name="delta-query"></a>Delta-fråga

När deltafrågan med delta-frågan kan du använda [tidstabeller i Azure SQL Database.](../sql-database/sql-database-temporal-tables.md)

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
2. Skapa ögonblicksbildfrågan. 

   Använd ** \@parametern snapshotTime** för att instruera Stream Analytics-körningen att hämta referensdatauppsättningen från TIDStabellen för SQL-databasen som är giltig vid systemtiden. Om du inte anger den här parametern riskerar du att få en felaktig basreferensdatauppsättning på grund av klocksnedställning. Ett exempel på fullständig ögonblicksbild fråga visas nedan:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Skapa deltafrågan. 
   
   Den här frågan hämtar alla rader i SQL-databasen som har infogats eller tagits bort inom en starttid, ** \@deltaStartTime**och en sluttid ** \@deltaEndTime**. Delta-frågan måste returnera samma kolumner som ögonblicksbildfrågan, samt **_kolumnåtgärden_**. Den här kolumnen definierar om raden infogas eller tas bort mellan ** \@deltaStartTime** och ** \@deltaEndTime**. De resulterande raderna flaggas som **1** om posterna infogades, eller **2** om de tas bort. 

   För poster som har uppdaterats bokförs den tidsmässiga tabellen genom att en insättnings- och borttagningsåtgärd fångas in. Stream Analytics-körningen kommer sedan att tillämpa resultatet av deltafrågan på föregående ögonblicksbild för att hålla referensdata uppdaterade. Ett exempel på deltafråga visas nedan:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Observera att Stream Analytics-körningen regelbundet kan köra ögonblicksbildfrågan utöver deltafrågan för att lagra kontrollpunkter.

## <a name="test-your-query"></a>Testa frågan
   Det är viktigt att kontrollera att frågan returnerar den förväntade datauppsättningen som Stream Analytics-jobbet kommer att använda som referensdata. Om du vill testa frågan går du till Indata under avsnittet Jobbtopologi på portalen. Du kan sedan välja Exempeldata på sql database reference-indata. När exemplet blir tillgängligt kan du hämta filen och kontrollera om de data som returneras är som förväntat. Om du vill optimera din utveckling och testa iterationer, rekommenderas att använda [Stream Analytics-verktyg för Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install). Du kan också alla andra verktyg för dina inställningar för att först se till att frågan returnerar rätt resultat från din Azure SQL-databas och sedan använda den i ditt Stream Analytics-jobb. 

## <a name="faqs"></a>Vanliga frågor och svar

**Kommer jag att ådra mig extra kostnader genom att använda SQL-referensdata som matas in i Azure Stream Analytics?**

Det finns ingen extra [kostnad per streamingenhet](https://azure.microsoft.com/pricing/details/stream-analytics/) i Stream Analytics-jobbet. Stream Analytics-jobbet måste dock ha ett associerat Azure-lagringskonto. Stream Analytics-jobbet frågar SQL DB (under jobbstart- och uppdateringsintervallet) för att hämta referensdatauppsättningen och lagrar ögonblicksbilden i lagringskontot. Om du lagrar dessa ögonblicksbilder medför ytterligare avgifter som beskrivs på [prissidan](https://azure.microsoft.com/pricing/details/storage/) för Azure-lagringskontot.

**Hur vet jag att referensdataögonblicksbild efterfrågas från SQL DB och används i Azure Stream Analytics-jobbet?**

Det finns två mått filtrerade efter logiskt namn (under Metrics Azure Portal) som du kan använda för att övervaka hälsotillståndet för SQL-databasreferensdataindata indata.

   * InputEvents: Det här måttet mäter antalet poster som läses in från SQL-databasreferensdatauppsättningen.
   * InputEventBytes: Det här måttet mäter storleken på referensdataögonblicksbilden som läses in i minnet för Stream Analytics-jobbet. 

Kombinationen av båda dessa mått kan användas för att dra slutsatsen om jobbet frågar SQL-databasen för att hämta referensdatauppsättningen och sedan läsa in den till minnet.

**Kommer jag att behöva en särskild typ av Azure SQL Database?**

Azure Stream Analytics fungerar med alla typer av Azure SQL Database. Det är dock viktigt att förstå att uppdateringsfrekvensen för referensdatainmatningen kan påverka frågeinläsningen. Om du vill använda deltafrågan rekommenderas att du använder temporala tabeller i Azure SQL Database.

**Varför lagrar Azure Stream Analytics ögonblicksbilder i Azure Storage-konto?**

Stream Analytics garanterar händelsebearbetning exakt en gång och leverans av händelser minst en gång. I de fall där tillfälliga problem påverkar ditt jobb krävs en liten mängd repris för att återställa tillståndet. För att aktivera uppspelning måste dessa ögonblicksbilder lagras i ett Azure Storage-konto. Mer information om kontrollpunktsuppspelning finns [i Kontrollpunkts- och reprisbegrepp i Azure Stream Analytics-jobb](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Nästa steg

* [Använda referensdata för uppslag i Stream Analytics](stream-analytics-use-reference-data.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktygen för Visual Studio](stream-analytics-quick-create-vs.md)
* [Testa live-data lokalt med Hjälp av Azure Stream Analytics-verktyg för Visual Studio (förhandsversion)](stream-analytics-live-data-local-testing.md)

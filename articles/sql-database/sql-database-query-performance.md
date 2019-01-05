---
title: Query Performance Insight för Azure SQL Database | Microsoft Docs
description: Fråga prestandaövervakning identifierar de flesta CPU förbrukar frågor för en Azure SQL-databas.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 5d892005881436dec89c0d0d010f7f02e7bdebf9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039205"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Query Performance Insight för Azure SQL Database

Hantera och finjustera prestanda för relationsdatabaser tar expertis och tid. Query Performance Insight är en del av Azure SQL Database-intelligent prestanda produktserie. Det hjälper dig att spendera mindre tid på att felsöka databasens prestanda genom att tillhandahålla:

* Bättre insikt i dina databaser-resursförbrukning (DTU).
* Information om främsta databasfrågor efter antal CPU, varaktighet och körning (potentiella justering kandidater för att öka prestanda).
* Möjligheten att granska nedåt i detaljerna för en fråga för att visa frågetexten och historik över resursanvändningen.
* Anteckningar som visar prestandarekommendationer från [SQL Database Advisor](sql-database-advisor.md).

![Query Performance Insight](./media/sql-database-query-performance/opening-title.png)

> [!TIP]
> För grundläggande prestandaövervakning med Azure SQL Database, rekommenderar vi Query Performance Insight. Observera produkt-begränsningar som publicerats i den här artikeln. För avancerad övervakning av databasprestanda i stor skala, rekommenderar vi att [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md). Den har inbyggd intelligens för prestandafelsökning av automatiserade. För att automatiskt justera några av de vanligaste prestandaproblemen för databasen, rekommenderar vi [automatisk justering](sql-database-automatic-tuning.md).

## <a name="prerequisites"></a>Förutsättningar

Query Performance Insight kräver att [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) är aktiv på din databas. Den aktiveras automatiskt för alla Azure SQL-databaser som standard. Om Query Store inte körs, uppmanas du att aktivera det i Azure-portalen.

> [!NOTE]
> Om meddelandet ”Query Store har inte konfigurerats korrekt på den här databasen” visas i portalen, se [optimerar konfigurationen av Query Store](#optimize-the-query-store-configuration-for-query-performance-insight).
>

## <a name="permissions"></a>Behörigheter

Du behöver följande för [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) behörighet att använda Query Performance Insight:

* **Läsare**, **ägare**, **deltagare**, **SQL DB-deltagare**, eller **SQL Server-deltagare** behörigheter som krävs Visa resursintensiva toppfrågor och diagram.
* **Ägare**, **deltagare**, **SQL DB-deltagare**, eller **SQL Server-deltagare** behörigheter som krävs för att visa frågetexten.

## <a name="use-query-performance-insight"></a>Använda Query Performance Insight

Query Performance Insight är lätt att använda:

1. Öppna den [Azure-portalen](https://portal.azure.com/) och hitta en databas som du vill undersöka.
2. Öppna från menyn till vänster **Intelligent prestanda** > **Query Performance Insight**.
  
   ![Query Performance Insight på menyn](./media/sql-database-query-performance/tile.png)

3. Granska listan över de viktigaste resursintensiva frågorna på den första fliken.
4. Välj en enskild fråga för att visa information om den.
5. Öppna **Intelligent prestanda** > **prestandarekommendationer** och kontrollera om det finns några prestandarekommendationer. Läs mer på inbyggda prestandarekommendationer [SQL Database Advisor](sql-database-advisor.md).
6. Använd skjutreglagen eller Zooma ikoner ändra observerade intervallet.

   ![instrumentpanel för prestanda](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Query Store måste avbilda ett par timmar av data för SQL-databas att återge informationen i Query Performance Insight. Om databasen har ingen aktivitet eller om Query Store har inte varit aktiv under en viss period, är diagrammen tom om Query Performance Insight visar det tidsintervallet. Du kan aktivera Query Store när som helst om den inte körs. Mer information finns i [bästa praxis med Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).

## <a name="review-top-cpu-consuming-queries"></a>Granska de viktigaste frågorna för CPU-användning

Som standard visar Query Performance Insight de översta fem förbrukar CPU-frågorna när du öppnar den.

1. Markera eller avmarkera enskilda frågor för att inkludera eller exkludera från diagrammet dem med hjälp av kryssrutorna.

    Den översta raden visar övergripande DTU-procent för databasen. CPU-procent som de valda förfrågningarna som förbrukats under det valda intervallet visar staplarna. Till exempel om **senaste veckan** är valt, varje stapel representerar en dag.

    ![de viktigaste frågorna](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > DTU-raden visas slås samman till ett värde för maximal användning under en timme perioder. Den är avsedd för en övergripande jämförelse endast med statistik för körning av frågan. DTU-användningen kan verka högt jämfört med körda frågor i vissa fall, men det kanske inte är fallet.
   >
   > Om en fråga överutnyttjade ut DTU till 100% i några minuter, till exempel visar den DTU-raden i Query Performance Insight hela timmes användning som 100% (en följd av det maximala aggregerat värdet).
   >
   > Överväg att skapa ett anpassat DTU användning diagram för en mer detaljerad jämförelse (upp till en minut):
   >
   > 1. I Azure-portalen väljer du **Azure SQL Database** > **övervakning**.
   > 2. Välj **Mått**.
   > 3. Välj **+ Lägg till diagram**.
   > 4. Välj DTU-procent i diagrammet.
   > 5. Dessutom väljer **senaste 24 timmarna** på menyn längst upp till vänster och ändra den till en minut.
   >
   > Använd anpassade DTU-diagrammet med en mer detaljerad information om för att jämföra med frågan körning diagrammet.

   Rutnätet längst ned visar sammanställd information för frågor som visas:

   * Fråge-ID, vilket är en unik identifierare för frågan i databasen.
   * CPU per fråga under ett synliga intervall som är beroende av aggregeringsfunktionen.
   * Varaktighet per frågan, vilket beror också på aggregeringsfunktionen.
   * Totalt antal körningar för en specifik fråga.

2. Om dina data blir inaktuella, väljer du den **uppdatera** knappen.

3. Använd skjutreglagen och zooma knappar för att ändra intervallet Fjärrvisning och undersöka förbrukning toppar:

   ![Skjutreglagen och zoomning knappar för att ändra intervallet](./media/sql-database-query-performance/zoom.png)

4. Du kan också markera den **anpassad** fliken för att anpassa vyn för:

   * Mått (CPU, varaktighet, antalet körningar).
   * Tidsintervall (senaste 24 timmarna, senaste veckan eller senaste månaden).
   * Antal frågor.
   * Aggregeringsfunktionen.
  
   ![Anpassad flik](./media/sql-database-query-performance/custom-tab.png)
  
5. Välj den **gå >** för att visa den anpassade vyn.

   > [!IMPORTANT]
   > Query Performance Insight är begränsad till och visa upp konsumerande 5-20-frågor, beroende på ditt val. Din databas kan köra många fler frågor utöver översta visas och de här frågorna kan inte ingå i diagrammet.
   >
   > Det kan finnas en arbetsbelastning databastyp där många mindre frågor, utöver översta visas, ofta kör och använder flesta DTU. De här frågorna visas inte på prestandadiagrammet.
   >
   > Till exempel kanske en fråga har förbrukat en stor mängd DTU ett tag, även om dess total förbrukning i observerade perioden är mindre än andra förbrukar upp frågorna. I detta fall är visas resursanvändningen för den här frågan inte i diagrammet.
   >
   > Om du vill förstå översta fråga körningar begränsningarna med Query Performance Insight kan du använda [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) för avancerade databasprestanda övervakning och felsökning.
   >

## <a name="view-individual-query-details"></a>Visa information om enskild fråga

Visa information om fråga:

1. Välj en fråga i listan över de viktigaste frågorna.

    ![Lista över de viktigaste frågorna](./media/sql-database-query-performance/details.png)

   En detaljerad vy öppnas. CPU-förbrukning, varaktighet och antalet körningar visas över tid.

2. Välj vilka diagramfunktioner för information.

   * Översta diagrammet visas en rad med den övergripande databasen DTU-procent. Staplarna är CPU-procent som förbrukas av den valda frågan.
   * Det andra diagrammet visar den totala varaktigheten för den valda frågan.
   * Diagrammet längst ned visar det totala antalet körningar av den valda frågan.

   ![Frågedetaljer](./media/sql-database-query-performance/query-details.png)

3. Du kan också använda skjutreglage, använda zoomning knappar eller välj **inställningar** att anpassa visningen av fråga data eller välja ett annat tidsintervall.

   > [!IMPORTANT]
   > Query Performance Insight in inte alla DDL-frågor. I vissa fall kan kanske den inte registrerar alla ad hoc-frågor.
   >

## <a name="review-top-queries-per-duration"></a>Granska de viktigaste frågorna per varaktighet

Två mått i Query Performance Insight kan hjälpa dig att hitta potentiella flaskhalsar: antalet varaktighet och körning.

Tidskrävande frågor har störst risken för att låsa resurser längre blockerar andra användare och begränsa skalbarhet. De är också de bästa kandidaterna för optimering.

Att identifiera tidskrävande frågor:

1. Öppna den **anpassad** flik i Query Performance Insight för den valda databasen.
2. Ändra måtten **varaktighet**.
3. Välj antalet frågor och utan uppmaning i intervallet.
4. Välj aggregeringsfunktionen:

   * **Summa** lägger ihop alla fråga körningstid för Fjärrvisning av hela intervallet.
   * **Max** hittar frågar i vilka körningstid var maximala för Fjärrvisning av hela intervallet.
   * **Genomsnittlig** söker efter Genomsnittlig körningstid för alla körningar för frågan och visar översta för dessa medelvärden.

   ![Frågevaraktighet](./media/sql-database-query-performance/top-duration.png)

5. Välj den **gå >** för att visa den anpassade vyn.

   > [!IMPORTANT]
   > Justera frågevyn uppdateras inte den DTU-raden. DTU-rad visar alltid det högsta användning värdet för intervallet.
   >
   > Överväg att skapa ett anpassat diagram i Azure-portalen för att förstå databasens DTU-användning med mer information (upp till en minut):
   >
   > 1. Välj **Azure SQL Database** > **övervakning**.
   > 2. Välj **Mått**.
   > 3. Välj **+ Lägg till diagram**.
   > 4. Välj DTU-procent i diagrammet.
   > 5. Dessutom väljer **senaste 24 timmarna** på menyn längst upp till vänster och ändra den till en minut.
   >
   > Vi rekommenderar att du använder anpassade DTU-diagrammet ska jämföras med prestandadiagram för frågan.
   >

## <a name="review-top-queries-per-execution-count"></a>Granska de viktigaste frågorna per antal körningar

Ett användarprogram som använder databasen kan get långsam, även om ett stort antal körningar inte kanske påverkar själva databasen och resurser användningen är låg.

I vissa fall kan kan en hög körningar leda till mer nätverksändningar. Tur och RETUR påverka prestanda. De är föremål nätverkslatensen och underordnad serversvarstid.

Till exempel har många datadrivna webbplatser kraftigt åtkomst till databasen för varje användarbegäran. Även om anslutningspooler hjälper långsam ökad nätverkstrafik och belastning på databasservern prestanda. I allmänhet Behåll tur och RETUR till ett minimum.

För att identifiera ofta körda (”trafikintensiva”) frågor:

1. Öppna den **anpassad** flik i Query Performance Insight för den valda databasen.
2. Ändra måtten **antal körningar**.
3. Välj antalet frågor och utan uppmaning i intervallet.
4. Välj den **gå >** för att visa den anpassade vyn.

   ![Antal körningar för frågan](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Förstå anteckningar för prestandajustering

När du utforskar arbetsbelastningen i Query Performance Insight, märker du ikoner med ett lodrätt streck ovanpå diagrammet.

Dessa ikoner är anteckningar. De visar prestandarekommendationer från [SQL Database Advisor](sql-database-advisor.md). Du kan få sammanfattad information på prestandarekommendationer genom att hovra över en anteckning.

   ![fråga anteckning](./media/sql-database-query-performance/annotation.png)

Om du vill veta mer eller tillämpa den advisor-rekommendationen väljer du ikonen för att öppna information om den rekommenderade åtgärden. Om det är en aktiv rekommendation kan använda du den direkt från portalen.

   ![Frågedetaljer för anteckning](./media/sql-database-query-performance/annotation-details.png)

I vissa fall, på grund av zoomningsnivån är det möjligt att anteckningar nära varandra är dolda i en enskild anteckning. Query Performance Insight representerar detta som en ikon för anteckningen. Att välja ikonen grupp anteckning öppnas ett nytt blad som visar dina kommentarer.

Korrelera frågor och prestandajustering åtgärder kan hjälpa dig att bättre förstå din arbetsbelastning.

## <a name="optimize-the-query-store-configuration-for-query-performance-insight"></a>Optimera Query Store-konfiguration för Query Performance Insight

När du använder Query Performance Insight kan du se följande felmeddelanden för Query Store:

* ”Query Store har inte konfigurerats korrekt på den här databasen. Klicka här om du vill veta mer ”.
* ”Query Store har inte konfigurerats korrekt på den här databasen. Klicka här om du vill ändra inställningarna ”.

Dessa meddelanden visas vanligtvis när Query Store inte kan samla in nya data.

Det första fallet sker när Query Store är i skrivskyddat läge och parametrar är optimalt inställda. Du kan åtgärda detta genom att öka storleken på datalagret eller genom att avmarkera Query Store. (Om du avmarkerar Query Store, alla tidigare insamlade telemetri går förlorade.)

   ![Query Store-information](./media/sql-database-query-performance/qds-off.png)

Det andra fallet sker när Query Store inte är aktiverad eller parametrar inte är optimalt inställda. Du kan ändra princip för kvarhållning och göra en avbildning och även aktivera Query Store, genom att köra följande kommandon från [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) eller Azure-portalen.

### <a name="recommended-retention-and-capture-policy"></a>Rekommenderade kvarhållning och avbilda princip

Det finns två typer av principer för kvarhållning:

* **Storleksbaserad**: Om den här principen är konfigurerad att **automatisk**, kommer den att rensa data automatiskt när nära maximal storlek har uppnåtts.
* **Tidsbaserad**: Den här principen är som standard är 30 dagar. Läsa information som är äldre än 30 dagar tas bort om Query Store körs slut på utrymme.

Du kan ange principen avbildning till:

* **Alla**: Query Store samlar in alla frågor.
* **Automatisk**: Query Store ignorerar sporadiska frågor och frågor med obetydlig kompilera och köra varaktighet. Tröskelvärden för körningar, kompilerar varaktighet och körningstiden bestäms internt. Det här är standardalternativet.
* **Ingen**: Query Store slutar att fånga in nya frågor, men körningsstatistik för redan avbildade frågor är fortfarande samlas in.

Vi rekommenderar att ställa in alla principer **automatisk** och rengöringsband principen till 30 dagar genom att köra följande kommandon från [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) eller Azure-portalen. (Ersätt `YourDB` med namnet på databasen.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Öka storleken på Query Store genom att ansluta till en databas via [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) eller Azure-portalen och köra följande fråga. (Ersätt `YourDB` med namnet på databasen.)

```T-SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Tillämpa dessa inställningar blir så småningom Query Store samlar in telemetri för nya frågor. Om du behöver Query Store ska fungera direkt, kan du välja att rensa Query Store genom att köra följande fråga med SSMS eller Azure-portalen. (Ersätt `YourDB` med namnet på databasen.)

> [!NOTE]
> Tar bort alla tidigare insamlade övervakade telemetri i Query Store att köra följande fråga.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="summary"></a>Sammanfattning

Query Performance Insight hjälper dig att förstå effekten av frågan arbetsbelastning och hur den relaterar till förbrukningen av databasresurser i. Du får lära dig om frågorna som förbrukar längst upp i databasen med den här funktionen och hittar du frågor för att optimera innan de blir problem.

## <a name="next-steps"></a>Nästa steg

* Prestandarekommendationer för databasen, Välj [rekommendationer](sql-database-advisor.md) på navigeringsbladet Query Performance Insight.

    ![Fliken rekommendationer](./media/sql-database-query-performance/ia.png)

* Överväg att aktivera [automatisk justering](sql-database-automatic-tuning.md) för vanliga prestandaproblem för databasen.
* Lär dig hur [smarta insikter](sql-database-intelligent-insights.md) kan hjälpa dig att felsöka prestandaproblem för databasen automatiskt.
* Överväg att använda [Azure SQL Analytics]( ../azure-monitor/insights/azure-sql.md) för avancerad prestandaövervakning av en stor flotta med SQL-databaser, elastiska pooler och instanser som hanteras med inbyggd intelligens.

---
title: Fråga efter prestandainsikter för Azure SQL Database | Microsoft Docs
description: Fråga prestandaövervakning identifierar de flesta förbrukar CPU-frågor för en Azure SQL Database.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: 5069b4e69c53ed93e9018cef2517f6125b838d12
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524173"
---
# <a name="azure-sql-database-query-performance-insight"></a>Azure SQL Database Query Performance Insight
Hantera och finjustera prestanda för relationsdatabaser är en krävande uppgift som kräver betydande resurser och tid investering. Query Performance Insight kan du ägna mindre tid Felsök databasprestanda med hjälp av följande:

* Bättre insikt i dina databaser-resursförbrukning (DTU). 
* De viktigaste frågorna av CPU/varaktighet/körningar, vilket potentiellt kan anpassas för bättre prestanda.
* Möjligheten att granska nedåt i detaljerna för en fråga, visa text och historik över resursanvändningen. 
* Prestandajustering anteckningar som visar åtgärder som utförs av [SQL Azure Database Advisor](sql-database-advisor.md)  



## <a name="prerequisites"></a>Förutsättningar
* Query Performance Insight kräver att [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) är aktiv på din databas. Om Query Store inte körs, uppmanas du att aktivera den i portalen.

## <a name="permissions"></a>Behörigheter
Följande [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) behörigheter som krävs för att använda Query Performance Insight: 

* **Läsare**, **ägare**, **deltagare**, **SQL DB-deltagare**, eller **SQL Server-deltagare** behörigheter som krävs Visa översta resurskrävande frågor och diagram. 
* **Ägare**, **deltagare**, **SQL DB-deltagare**, eller **SQL Server-deltagare** behörigheter som krävs för att visa frågetexten.

## <a name="using-query-performance-insight"></a>Använda Query Performance Insight
Query Performance Insight är lätt att använda:

* Öppna [Azure-portalen](https://portal.azure.com/) och hitta-databas som du vill undersöka. 
  * Menyn till vänster under support och felsökning, väljer du ”Query Performance Insight”.
* Granska listan över de viktigaste resursintensiva frågorna på den första fliken.
* Välj en enskild fråga för att visa information om den.
* Öppna [SQL Azure Database Advisor](sql-database-advisor.md) och kontrollera om det finns inga rekommendationer.
* Använd skjutreglagen eller Zooma ikoner för att ändra observerade intervall.
  
    ![instrumentpanel för prestanda](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Ett par timmar efter data måste läsas av Query Store för SQL-databas att ge prestandainsikter för frågan. Om databasen har ingen aktivitet eller Query Store har inte varit aktiv under en viss tidsperiod, vara diagrammen tom när du visar den aktuella tidsperioden. Du kan aktivera Query Store när som helst om den inte körs.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Granska frågor som förbrukar mest Processorkraft
I den [portal](http://portal.azure.com) gör du följande:

1. Bläddra till en SQL-databas och på **alla inställningar** > **stöd + felsökning** > **fråga prestandainsikt**. 
   
    ![Query Performance Insight][1]
   
    De viktigaste frågorna vyn öppnas och visas de viktigaste frågorna för CPU-användning.
2. Klicka på runt diagrammet information.<br>Den översta raden visar den totala DTU % för databasen, medan staplarna visar processor som förbrukas av de valda förfrågningarna under det valda intervallet (till exempel om **senaste veckan** väljs varje stapel representerar en dag).
   
    ![de viktigaste frågorna][2]
   
    Rutnätet längst ned representerar samlad information för frågor som visas.
   
   * Fråge-ID - Unik identifierare för frågan i databasen.
   * CPU per fråga under synliga intervallet (beror på aggregeringsfunktionen).
   * Varaktighet per fråga (beror på aggregeringsfunktionen).
   * Totalt antal körningar för en viss fråga.
     
     Markera eller avmarkera enskilda frågor för att inkludera eller exkludera dem från diagrammet med hjälp av kryssrutorna.
3. Om dina data blir inaktuella, klickar du på den **uppdatera** knappen.
4. Du kan använda reglagen och zooma knappar för att ändra Fjärrvisning intervall och undersöka toppar: ![inställningar](./media/sql-database-query-performance/zoom.png)
5. Om du vill att en annan vy, du kan också markera **anpassad** fliken och ange:
   
   * Mått (CPU, varaktighet, antalet körningar)
   * Tidsintervall (senaste 24 timmarna, senaste veckan, senaste månaden). 
   * Antal frågor.
   * Aggregeringsfunktionen.
     
     ![settings](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Visa information om enskild fråga
Visa information om fråga:

1. Klicka på alla frågor i listan över de viktigaste frågorna.
   
    ![detaljer](./media/sql-database-query-performance/details.png)
2. Detaljvyn öppnas och antalet CPU-förbrukning/varaktighet/körningar för frågor bryts ned över tid.
3. Klicka på runt diagrammet information.
   
   * Översta diagrammet visar raden med övergripande databasen DTU % och staplarna är processor som används av den valda frågan.
   * Det andra diagrammet visar total varaktighet av den valda frågan.
   * Längst ned diagrammet visar totalt antal körningar av den valda frågan.
     
     ![Frågedetaljer][3]
4. Du kan också använda skjutreglage, Zooma knappar eller klicka på **inställningar** att anpassa visningen av fråga data eller välja en annan tidsperiod.

## <a name="review-top-queries-per-duration"></a>Granska de viktigaste frågorna per varaktighet
I den senaste uppdateringen av Query Performance Insight introducerade vi två nya mått som kan hjälpa dig att identifiera potentiella flaskhalsar: antalet varaktighet och körning.<br>

Tidskrävande frågor har störst risken för att låsa resurser längre blockerar andra användare och begränsa skalbarhet. De är också de bästa kandidaterna för optimering.<br>

Att identifiera långvariga frågor:

1. Öppna **anpassad** flik i Query Performance Insight för valda databasen
2. Ändra måtten ska **varaktighet**
3. Välj antalet frågor och Fjärrvisning intervall
4. Välj aggregeringsfunktionen
   
   * **Summa** lägger ihop alla fråga körningstid under hela utan uppmaning i intervallet.
   * **Max** hittar frågor vilka körningstid var maximala med hela Fjärrvisning intervall.
   * **Genomsnittlig** hittar Genomsnittlig körningstid för alla körningar för frågan och visar upp utanför dessa medelvärden. 
     
     ![Frågevaraktighet][4]

## <a name="review-top-queries-per-execution-count"></a>Granska de viktigaste frågorna per antal körningar
Högt antal körningar kanske inte påverkar själva databasen och Resursanvändning kan vara lite men övergripande program kan få långsam.

I vissa fall kan antalet mycket hög körningar kan leda till att öka av nätverksöverföringar. Tur och RETUR påverka prestanda avsevärt. De är föremål nätverkslatensen och underordnad serversvarstid. 

Till exempel har många datadrivna webbplatser kraftigt åtkomst till databasen för varje användarbegäran. När anslutningspooler kan, ökad nätverkstrafik och belastningen på servern som kan påverka prestanda negativt.  Allmän rådgivning är att behålla tur och RETUR till ett absolut minimum.

Körda frågor (”trafikintensiva”) frågor för att identifiera vanliga:

1. Öppna **anpassad** flik i Query Performance Insight för valda databasen
2. Ändra måtten ska **antal körningar**
3. Välj antalet frågor och Fjärrvisning intervall
   
    ![Antal körningar för frågan][5]

## <a name="understanding-performance-tuning-annotations"></a>Förstå justering prestandaanteckningar
När du utforskar arbetsbelastningen i Query Performance Insight, märker du ikoner med lodrät linje ovanpå diagrammet.<br>

Dessa ikoner är uppgifter; de representerar prestandapåverkande åtgärder som utförs av [SQL Azure Database Advisor](sql-database-advisor.md). Genom att hovra anteckning får du grundläggande information om åtgärder som:

![fråga anteckning][6]

Klicka på ikonen om du vill veta mer eller använda advisor-rekommendationen. Information om åtgärden öppnas. Om det är en aktiv rekommendation som du kan använda det direkt med hjälp av kommandot.

![Frågedetaljer för anteckning][7]

### <a name="multiple-annotations"></a>Flera kommentarer.
Det är möjligt att på grund av zoomningsnivån anteckningar som ligger nära varandra kommer få minimeras till en. Detta kommer att representeras av en särskild ikon, att klicka på den öppna nytt blad där listan över grupperade anteckningar ska visas.
Korrelera frågor och åtgärder för prestandajustering hjälper dig att bättre förstå din arbetsbelastning. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optimera Query Store-konfiguration för Query Performance Insight
Du kan stöta på följande Query Store meddelanden under din användning av Query Performance Insight:

* ”Query Store har inte konfigurerats korrekt på den här databasen. Klicka här om du vill veta mer ”.
* ”Query Store har inte konfigurerats korrekt på den här databasen. Klicka här om du vill ändra inställningarna ”. 

Dessa meddelanden visas vanligtvis när Query Store inte samla in nya data. 

Första fallet sker när Query Store är i skrivskyddat läge och parametrar är optimalt inställda. Du kan åtgärda detta genom att öka storleken på Query Store eller rensa Query Store.

![qds-knappen][8]

Andra fallet sker när Query Store är inaktiverad eller parametrar inte är optimalt. <br>Du kan ändra princip för kvarhållning och göra en avbildning och aktivera Query Store genom att köra kommandona nedan eller direkt från portalen:

![qds-knappen][9]

### <a name="recommended-retention-and-capture-policy"></a>Rekommenderade kvarhållning och avbilda princip
Det finns två typer av principer för kvarhållning:

* Storleksbaserad - om värdet är AUTO det kommer att rensa data automatiskt när nära maxstorlek har uppnåtts.
* Tid baserat - som standard som vi anger den till 30 dagar, vilket innebär att om Query Store kommer slut på utrymme, läsa information som är äldre än 30 dagar tas bort

Samla in principen kan anges till:

* **Alla** -samlar in alla frågor.
* **Automatisk** -sporadiska frågor och frågor med obetydlig kompilera och köra varaktighet ignoreras. Tröskelvärden för körningstid för antal, kompilera och runtime bestäms internt. Det här är standardalternativet.
* **Ingen** -Query Store slutar samla in nya frågor, men körningsstatistik för redan avbildade frågor samlas fortfarande.

Vi rekommenderar att du automatiskt och principen om ren till 30 dagar att ställa in alla principer:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Öka storleken på Query Store. Detta kan utföras genom att ansluta till en databas och utfärda följande fråga:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Tillämpa dessa inställningar blir så småningom Query Store insamling av nya frågor, men om du inte vill vänta kan du rensa Query Store. 

> [!NOTE]
> Kör följande fråga tar bort alla aktuella informationen i Query Store. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Sammanfattning
Query Performance Insight hjälper dig att förstå effekten av frågearbetsbelastningen och hur den relaterar till användning av databasresurser. Med den här funktionen kommer du lär dig mer om de viktigaste förbrukningsfrågorna och enkelt identifiera de att åtgärda innan de blir problem.

## <a name="next-steps"></a>Nästa steg
För ytterligare rekommendationer om hur du förbättrar prestandan för SQL-databasen klickar du på [rekommendationer](sql-database-advisor.md) på den **Query Performance Insight** bladet.

![Klassificering av prestanda](./media/sql-database-query-performance/ia.png)

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png


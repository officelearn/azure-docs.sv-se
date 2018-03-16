---
title: "Fråga insikter i frågeprestanda för Azure SQL Database | Microsoft Docs"
description: "Prestandaövervakning av frågan identifierar de flesta förbrukning av CPU-frågor för en Azure SQL Database."
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 18d03ffcd586a809b37cbc1ca9a7843c25c7758d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-query-performance-insight"></a>Azure SQL Database Query Performance Insight
Hantera och finjustera prestanda hos relationsdatabaser är en utmaning som kräver betydande resurser och tid investeringar. Query Performance Insight kan du ägna mindre tid felsökning databasprestanda med hjälp av följande:

* Djupare inblick i dina databaser resursförbrukning (DTU). 
* De vanligaste frågorna med CPU/varaktighet/körningar, vilket potentiellt kan anpassas för bättre prestanda.
* Möjlighet att öka detaljnivån till information om en fråga visa text och historik över resursutnyttjandet. 
* Prestandajustering anteckningar som visar åtgärder som utförs av [SQL Azure Database Advisor](sql-database-advisor.md)  



## <a name="prerequisites"></a>Förutsättningar
* Query Performance Insight kräver att [Frågearkivet](https://msdn.microsoft.com/library/dn817826.aspx) är aktiv på din databas. Om Query Store inte körs, uppmanas du att aktivera den i portalen.

## <a name="permissions"></a>Behörigheter
Följande [rollbaserad åtkomstkontroll](../active-directory/role-based-access-control-what-is.md) behörigheter som krävs för att använda Query Performance Insight: 

* **Läsaren**, **ägare**, **deltagare**, **SQL DB-deltagare**, eller **SQL Server-deltagare** behörigheter som krävs Visa översta resursen förbrukar frågor och diagram. 
* **Ägare**, **deltagare**, **SQL DB-deltagare**, eller **SQL Server-deltagare** behörigheter som krävs för att visa frågetext.

## <a name="using-query-performance-insight"></a>Använda Query Performance Insight
Det är lätt att använda Query Performance Insight:

* Öppna [Azure-portalen](https://portal.azure.com/) och hitta databasen som du vill undersöka. 
  * Välj ”Query Performance Insight” vänster menyn under support och felsökning.
* Granska listan med de vanligaste frågorna för förbrukning av resursen på den första fliken.
* Välj en enskild fråga om du vill visa detaljerad information.
* Öppna [SQL Azure Database Advisor](sql-database-advisor.md) och kontrollera om det finns några rekommendationer.
* Använd skjutreglagen eller Zooma ikoner för att ändra observerade intervall.
  
    ![instrumentpanel för prestanda](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> Några timmars data behöver samlas in av Frågearkivet för SQL Database tillhandahåller insikter i frågeprestanda. Om databasen har ingen aktivitet eller Query Store inte var aktiv under en viss tidsperiod, är diagrammen tom när du visar den tidsperioden. Du kan aktivera Query Store när som helst om den inte körs.   
> 
> 

## <a name="review-top-cpu-consuming-queries"></a>Granska högsta CPU förbrukningsfrågorna
I den [portal](http://portal.azure.com) gör du följande:

1. Bläddra till en SQL-databas och på **alla inställningar** > **stöd + felsökning** > **fråga performance insight**. 
   
    ![Query Performance Insight][1]
   
    De vanligaste frågorna vyn öppnar visas de vanligaste frågorna för CPU-användning.
2. Klicka på runt diagrammet mer information.<br>Den översta raden visar den totala DTU % för databasen, medan staplar processor som används av de valda frågorna under det valda intervallet (till exempel om **föregående vecka** väljs varje stapel representerar en dag).
   
    ![de vanligaste frågorna][2]
   
    Nedre rutnätet representerar aggregerade information för frågorna som visas.
   
   * Fråge-ID - Unik identifierare för frågan i databasen.
   * CPU per fråga under synliga intervallet (beroende på aggregeringsfunktionen).
   * Varaktighet per fråga (beroende på aggregeringsfunktionen).
   * Totalt antal körningar för en viss fråga.
     
     Markera eller avmarkera enskilda frågor för att inkludera eller exkludera dem från diagrammet med hjälp av kryssrutorna.
3. Om dina data blir inaktuella, klickar du på den **uppdatera** knappen.
4. Du kan använda reglagen och zooma knappar för att ändra observationsintervallet och undersöka toppar: ![inställningar](./media/sql-database-query-performance/zoom.png)
5. Om du vill att en annan vy, du kan också markera **anpassad** fliken och ange:
   
   * Mått (CPU, varaktighet, körningar)
   * Tidsintervall (föregående vecka förra månaden senaste 24 timmar). 
   * Antal frågor.
   * Aggregeringsfunktionen.
     
     ![settings](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Visa enskilda Frågedetaljer
Visa information om frågan:

1. Klicka på alla frågor i listan över de vanligaste frågorna.
   
    ![detaljer](./media/sql-database-query-performance/details.png)
2. Detaljvyn öppnas och frågor körning-förbrukning/varaktighet processorer är uppdelad över tid.
3. Klicka på runt diagrammet mer information.
   
   * Övre diagrammet visar raden med övergripande databasen DTU % och staplarna har processor i procent som används av den valda frågan.
   * Det andra diagrammet visas total varaktighet av den valda frågan.
   * Längst ned diagrammet visar totalt antal körningar av den valda frågan.
     
     ![Frågedetaljer][3]
4. Du kan också använda reglagen, Zooma knappar eller klicka på **inställningar** att anpassa visningen av fråga efter data eller välj en annan tidsperiod.

## <a name="review-top-queries-per-duration"></a>Granska de vanligaste frågorna per varaktighet
Vi har fört två nya mått som kan hjälpa dig att identifiera potentiella flaskhalsar i den senaste uppdateringen av Query Performance Insight: antalet varaktighet och körningen.<br>

Tidskrävande frågor har störst risken för att låsa resurser längre, blockerar andra användare och begränsa skalbarhet. De är också bra kandidater för optimering.<br>

Att identifiera tidskrävande frågor:

1. Öppna **anpassad** fliken i Query Performance Insight för den valda databasen
2. Ändra måtten ska **varaktighet**
3. Välj antalet frågor och observationsintervallet
4. Välj aggregeringsfunktionen
   
   * **Summa** alla frågan körningstid adderar under hela observationsintervallet.
   * **Max** hittar frågor som körningstid har högsta på hela observationsintervallet.
   * **Genomsnittlig** hittar Genomsnittlig körningstid för alla frågan körningar och visa upp utanför dessa medelvärden. 
     
     ![frågan varaktighet][4]

## <a name="review-top-queries-per-execution-count"></a>Granska de vanligaste frågorna per körningar
Stort antal körningar kan inte påverkar själva databasen och användning av resurser kan vara låg, men övergripande få långsam.

I vissa fall mycket hög körningar kan leda till att öka av nätverk sändningar. Sändningar påverka prestanda avsevärt. De är föremål nätverkslatensen och att underordnad server latens. 

Till exempel har många datadrivna webbplatser kraftigt åtkomst till databasen för varje användarbegäran. Medan anslutningspooler hjälper, ökad nätverkstrafik och belastningen på servern som kan påverka prestanda negativt.  Allmänna råd är att hålla sändningar till ett absolut minimum.

Köra frågor (”chatty”) frågor för att identifiera vanliga:

1. Öppna **anpassad** fliken i Query Performance Insight för den valda databasen
2. Ändra måtten ska **körningar**
3. Välj antalet frågor och observationsintervallet
   
    ![antal frågor för körning][5]

## <a name="understanding-performance-tuning-annotations"></a>Förstå prestanda prestandajustering anteckningar
När utforska din arbetsbelastning i Query Performance Insight märker du ikoner med lodrät linje på diagrammet.<br>

Ikonerna är anteckningar; de representerar prestanda som påverkar åtgärder som utförs av [SQL Azure Database Advisor](sql-database-advisor.md). Genom att hovra anteckningen hämta grundläggande information om åtgärder som:

![kommentar för frågan][6]

Klicka på ikonen om du vill veta mer eller använda advisor rekommendation. Information om åtgärden öppnas. Om det finns en aktiv rekommendation som du kan använda den direkt med hjälp av kommandot.

![anteckningen Frågedetaljer][7]

### <a name="multiple-annotations"></a>Flera kommentarer.
Det är möjligt att på grund av zoomningsnivån anteckningar som ligger nära varandra kommer Kom komprimerats till en. Detta kommer representeras av särskild ikon, klickar på den öppna nytt blad när listan över grupperade anteckningar ska visas.
Korrelerar frågor och åtgärder för prestandajustering hjälper dig att bättre förstå din arbetsbelastning. 

## <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optimera Query Store-konfiguration för Query Performance Insight
Du kan stöta på följande Query Store-meddelanden under din användning av Query Performance Insight:

* ”Query Store har inte konfigurerats korrekt på den här databasen. Klicka här om du vill veta mer ”.
* ”Query Store har inte konfigurerats korrekt på den här databasen. Klicka här om du vill ändra inställningarna ”. 

Dessa meddelanden visas vanligen när Query Store inte har möjlighet att samla in nya data. 

Första fallet händer när Query Store är i skrivskyddat läge och parametrarna har ställts in optimalt. Du kan åtgärda detta genom att öka storleken på Query Store eller rensa Query Store.

![knappen qds][8]

Andra fallet sker när Frågearkivet är inaktiverat eller parametrar har ställts in optimalt. <br>Du kan ändra kvarhållning och avbilda principen och aktivera Query Store genom att köra kommandona nedan eller direkt från portalen:

![knappen qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Rekommenderade kvarhållning och avbilda princip
Det finns två typer av bevarandeprinciper:

* Storlek baserat - om värdet AUTO kommer rensa data automatiskt när nära maxstorlek har uppnåtts.
* Tid baserat - som standard vi ställs på 30 dagar, vilket innebär att, om Query Store körs för slut på utrymme, läsa information som är äldre än 30 dagar tas bort

Samla in princip kan anges till:

* **Alla** -samlar in alla frågor.
* **Automatisk** -ovanligt frågor och frågor med obetydlig kompilera och köra varaktighet ignoreras. Tröskelvärde för antal, kompilera och runtime varaktighet för körning av bestäms internt. Detta är standardalternativet.
* **Ingen** -Query Store slutar att fånga in nya frågor, men runtime statistik för redan avbildade frågor samlas fortfarande.

Vi rekommenderar att ange alla principer till automatiskt och principen om ren till 30 dagar:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Öka storleken på Query Store. Detta kan utföras genom att ansluta till en databas och skapa följande fråga:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Dessa inställningar tillämpas gör slutligen Query Store att samla in nya frågor, men om du inte vill vänta kan du rensa Query Store. 

> [!NOTE]
> Kör följande fråga tar bort alla aktuella informationen i Query Store. 
> 
> 

    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Sammanfattning
Query Performance Insight hjälper dig att förstå effekten av din fråga arbetsbelastning och hur det relaterar till databasen resursförbrukning. Med den här funktionen kommer du lär dig mer om de viktigaste förbrukningsfrågorna och enkelt identifiera de att åtgärda innan de hunnit bli problem.

## <a name="next-steps"></a>Nästa steg
Ytterligare rekommendationer om hur du förbättrar prestandan för SQL-databasen klickar du på [rekommendationer](sql-database-advisor.md) på den **Query Performance Insight** bladet.

![Performance Advisor](./media/sql-database-query-performance/ia.png)

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


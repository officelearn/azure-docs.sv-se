---
title: Query Performance Insight
description: Frågeprestandaövervakning identifierar de mest processorkrävande och tidskrävande frågorna för enskilda och poolade databaser i Azure SQL-databasen.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: f5998fde6659715de4fcb533cb0f41a8939b1c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214049"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Frågeprestandainsikt för Azure SQL Database

Query Performance Insight ger intelligent frågeanalys för enskilda och poolade databaser. Det hjälper till att identifiera de viktigaste resurskonsumerande och tidskrävande frågorna i din arbetsbelastning. Detta hjälper dig att hitta frågor för att optimera för att förbättra den totala arbetsbelastningen prestanda och effektivt använda den resurs som du betalar för. Frågeprestanda insikt hjälper dig att spendera mindre tid på att felsöka databasprestanda genom att tillhandahålla:

* Djupare inblick i förbrukningen av databasresurser (DTU)
* Information om de vanligaste databasfrågorna efter CPU, varaktighet och körning (potentiella justeringskandidater för prestandaförbättringar)
* Möjligheten att öka detaljnivån i information om en fråga, visa frågetexten och historiken för resursutnyttjandet
* Anteckningar som visar prestandarekommendationer från [databasrådgivare](sql-database-advisor.md)

![Query Performance Insight](./media/sql-database-query-performance/opening-title.png)

## <a name="prerequisites"></a>Krav

Frågeprestandainsikt kräver att [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) är aktivt i databasen. Det är automatiskt aktiverat för alla Azure SQL-databaser som standard. Om Query Store inte körs kommer Azure-portalen att uppmana dig att aktivera den.

> [!NOTE]
> Om meddelandet "Query Store inte är korrekt konfigurerat för den här databasen" visas i portalen läser [du Optimera konfigurationen för Query Store](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Behörigheter

Du behöver följande [rollbaserade åtkomstkontrollbehörigheter](../role-based-access-control/overview.md) för att kunna använda Query Performance Insight:

* **Läsar-,** **ägar-,** **deltagar-,** **SQL DB-deltagar-** eller **SQL Server-deltagarbehörigheter** krävs för att visa de viktigaste resurskrävande frågorna och diagrammen.
* **Behörigheter**för ägare, **deltagare**, **SQL DB Contributor**eller SQL Server **Contributor** krävs för att visa frågetext.

## <a name="use-query-performance-insight"></a>Använda Query Performance Insight

Query Performance Insight är lätt att använda:

1. Öppna [Azure-portalen](https://portal.azure.com/) och hitta en databas som du vill undersöka.
2. Öppna Intelligent > **prestandainsikt** **Intelligent Performance**på menyn till vänster .
  
   ![Frågeprestandainsikt på menyn](./media/sql-database-query-performance/tile.png)

3. På den första fliken granskar du listan över de mest resurskrävande frågorna.
4. Välj en enskild fråga om du vill visa dess information.
5. Öppna**rekommendationer för** **intelligent prestanda** > och kontrollera om det finns några prestandarekommendationer. Mer information om inbyggda prestandarekommendationer finns i [SQL Database Advisor](sql-database-advisor.md).
6. Använd skjutreglage eller zoomikoner för att ändra det observerade intervallet.

   ![Instrumentpanel för prestanda](./media/sql-database-query-performance/performance.png)

> [!NOTE]
> För att SQL Database ska kunna återge informationen i Query Performance Insight måste Query Store samla in ett par timmar data. Om databasen inte har någon aktivitet eller om Query Store inte var aktivt under en viss period, kommer diagrammen att vara tomma när Query Performance Insight visar det tidsintervallet. Du kan aktivera Query Store när som helst om det inte körs. Mer information finns i [Metodtips med Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store).
>

Om du vill ha rekommendationer om databasprestanda väljer du [Rekommendationer](sql-database-advisor.md) på navigeringsbladet Query Performance Insight.

![Fliken Rekommendationer](./media/sql-database-query-performance/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Granska de vanligaste cpu-tidskrävande frågorna

Som standard visar Query Performance Insight de fem mest processorkrävande frågorna när du öppnar den första gången.

1. Markera eller avmarkera enskilda frågor om du vill inkludera eller utesluta dem från diagrammet med hjälp av kryssrutor.

   Den översta raden visar den totala DTU-procentsatsen för databasen. Staplarna visar CPU-procent som de valda frågorna förbrukas under det valda intervallet. Om till exempel **Föregående vecka** är markerat representerar varje stapel en enda dag.

   ![Vanligaste frågorna](./media/sql-database-query-performance/top-queries.png)

   > [!IMPORTANT]
   > Den DTU-rad som visas aggregeras till ett maximalt förbrukningsvärde under en timme. Det är avsett för en hög nivå jämförelse endast med fråga körning statistik. I vissa fall kan DTU-användning verka för hög jämfört med utförda frågor, men så kanske inte är fallet.
   >
   > Om till exempel en fråga maxade ut DTU till 100 % bara för några minuter, visar DTU-raden i Query Performance Insight hela förbrukningstimmen som 100 % (konsekvensen av det maximala aggregerade värdet).
   >
   > För en finare jämförelse (upp till en minut) kan du överväga att skapa ett anpassat DTU-användningsdiagram:
   >
   > 1. I **Azure-portalen** > väljer du Azure SQL Database**Monitoring**.
   > 2. Välj **Mått**.
   > 3. Välj **+Lägg till diagram**.
   > 4. Välj DTU-procenten i diagrammet.
   > 5. Dessutom väljer du **Senaste 24 timmarna** på menyn längst upp till vänster och ändra den till en minut.
   >
   > Använd det anpassade DTU-diagrammet med en finare detaljnivå för att jämföra med frågekörningsdiagrammet.

   Det nedre rutnätet visar aggregerad information för synliga frågor:

   * Fråge-ID, som är en unik identifierare för frågan i databasen.
   * CPU per fråga under ett observerbart intervall, vilket beror på aggregeringsfunktionen.
   * Varaktighet per fråga, vilket också beror på aggregeringsfunktionen.
   * Totalt antal körningar för en viss fråga.

2. Om dina data blir inaktuella väljer du knappen **Uppdatera.**

3. Använd skjutreglage och zoomknappar för att ändra observationsintervallet och undersöka förbrukningsspikar:

   ![Skjutreglage och zoomknappar för att ändra intervallet](./media/sql-database-query-performance/zoom.png)

4. Du kan också välja fliken **Anpassad** för att anpassa vyn efter:

   * Mått (CPU, varaktighet, körningsantal).
   * Tidsintervall (senaste 24 timmarna, den senaste veckan eller den senaste månaden).
   * Antal frågor.
   * Aggregeringsfunktion.
  
   ![Fliken Anpassad](./media/sql-database-query-performance/custom-tab.png)
  
5. Välj knappen **Gå >** för att se den anpassade vyn.

   > [!IMPORTANT]
   > Query Performance Insight är begränsad till att visa de 5-20 vanligaste tidskrävande frågorna, beroende på ditt val. Databasen kan köra många fler frågor utöver de vanligaste som visas, och dessa frågor kommer inte att inkluderas i diagrammet.
   >
   > Det kan finnas en databasarbetsbelastningstyp där många mindre frågor, utöver de översta som visas, körs ofta och använder majoriteten av DTU. Dessa frågor visas inte i prestandadiagrammet.
   >
   > En fråga kan till exempel ha förbrukat en betydande mängd DTU ett tag, även om dess totala förbrukning under den observerade perioden är mindre än de andra mest tidskrävande frågorna. I så fall visas inte resursutnyttjandet av den här frågan i diagrammet.
   >
   > Om du behöver förstå de vanligaste frågekörningarna utöver begränsningarna i Query Performance Insight kan du överväga att använda [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) för avancerad övervakning och felsökning av databasprestanda.
   >

## <a name="view-individual-query-details"></a>Visa information om enskilda frågor

Så här visar du frågeinformation:

1. Markera en fråga i listan över de vanligaste frågorna.

    ![Lista över de vanligaste frågorna](./media/sql-database-query-performance/details.png)

   En detaljerad vy öppnas. Den visar cpu-förbrukning, varaktighet och körning räkna över tiden.

2. Välj diagramfunktioner för mer information.

   * Det översta diagrammet visar en linje med den totala databasen DTU procent. Staplarna är den CPU-procentandel som den valda frågan förbrukas.
   * Det andra diagrammet visar den totala varaktigheten för den markerade frågan.
   * Det nedre diagrammet visar det totala antalet körningar av den valda frågan.

   ![Frågeinformation](./media/sql-database-query-performance/query-details.png)

3. Du kan också använda skjutreglage, använda zoomknappar eller välja **Inställningar** för att anpassa hur frågedata visas eller för att välja ett annat tidsintervall.

   > [!IMPORTANT]
   > Query Performance Insight samlar inte in några DDL-frågor. I vissa fall kanske den inte samlar in alla ad hoc-frågor.
   >

## <a name="review-top-queries-per-duration"></a>Granska de vanligaste frågorna per varaktighet

Två mått i Query Performance Insight kan hjälpa dig att hitta potentiella flaskhalsar: varaktighet och körningsantal.

Tidskrävande frågor har störst potential att låsa resurser längre, blockera andra användare och begränsa skalbarheten. De är också de bästa kandidaterna för optimering.

Så här identifierar du tidskrävande frågor:

1. Öppna fliken **Anpassad** i Frågeprestandainsikt för den valda databasen.
2. Ändra måtten till **varaktighet**.
3. Välj antalet frågor och observationsintervallet.
4. Välj aggregeringsfunktionen:

   * **Sammanfattning** summerar all körningstid för frågor för hela observationsintervallet.
   * **Max** hittar frågor där körningstiden var maximal för hela observationsintervallet.
   * **Genomsnittligt** hittar den genomsnittliga körningstiden för alla frågekörningar och visar de bästa för dessa medelvärden.

   ![Varaktighet för fråga](./media/sql-database-query-performance/top-duration.png)

5. Välj knappen **Gå >** för att se den anpassade vyn.

   > [!IMPORTANT]
   > Om du justerar frågevyn uppdateras inte DTU-raden. DTU-raden visar alltid det maximala förbrukningsvärdet för intervallet.
   >
   > Om du vill förstå databas-DTU-förbrukning med mer information (upp till en minut) kan du överväga att skapa ett anpassat diagram i Azure-portalen:
   >
   > 1. Välj **Övervakning av Azure SQL Database** > **.**
   > 2. Välj **Mått**.
   > 3. Välj **+Lägg till diagram**.
   > 4. Välj DTU-procenten i diagrammet.
   > 5. Dessutom väljer du **Senaste 24 timmarna** på menyn längst upp till vänster och ändra den till en minut.
   >
   > Vi rekommenderar att du använder det anpassade DTU-diagrammet för att jämföra med frågeprestandadiagrammet.
   >

## <a name="review-top-queries-per-execution-count"></a>Granska de vanligaste frågorna per körningsantal

Ett användarprogram som använder databasen kan bli långsamt, även om ett stort antal körningar kanske inte påverkar själva databasen och resursanvändningen är låg.

I vissa fall kan ett högt antal körningar leda till fler nätverksresor. Tur- och returresor påverkar prestanda. De är föremål för nätverksfördröjning och svarstid för underordnade servrar.

Många datadrivna webbplatser har till exempel stor åtkomst till databasen för varje användarbegäran. Även om anslutningspoolning hjälper kan den ökade nätverkstrafiken och bearbetningsbelastningen på databasservern försämra prestanda. I allmänhet hålla tur och retur till ett minimum.

Så här identifierar du ofta utförda ("pratsamma") frågor:

1. Öppna fliken **Anpassad** i Frågeprestandainsikt för den valda databasen.
2. Ändra måtten till **körningsantal**.
3. Välj antalet frågor och observationsintervallet.
4. Välj knappen **Gå >** för att se den anpassade vyn.

   ![Antal frågor om körning](./media/sql-database-query-performance/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Förstå prestandajusteringsanteckningar

När du utforskar din arbetsbelastning i Query Performance Insight kanske du märker ikoner med en lodrät linje överst i diagrammet.

Dessa ikoner är anteckningar. De visar prestandarekommendationer från [SQL Database Advisor](sql-database-advisor.md). Genom att hovra över en anteckning kan du få sammanfattad information om prestandarekommendationer.

   ![Frågeärende](./media/sql-database-query-performance/annotation.png)

Om du vill förstå mer eller tillämpa rådgivarens rekommendation väljer du ikonen för att öppna information om den rekommenderade åtgärden. Om detta är en aktiv rekommendation kan du använda den direkt från portalen.

   ![Information om frågeärende](./media/sql-database-query-performance/annotation-details.png)

I vissa fall är det möjligt att anteckningar nära varandra komprimeras till en enda anteckning på grund av zoomnivån. Query Performance Insight representerar detta som en gruppnoteringsikon. Om du markerar gruppanteckningsikonen öppnas ett nytt blad med en lista över anteckningarna.

Korrelera frågor och prestandajusteringsåtgärder kan hjälpa dig att bättre förstå din arbetsbelastning.

## <a name="optimize-the-query-store-configuration"></a>Optimera konfigurationen för Query Store

När du använder Query Performance Insight kan följande felmeddelanden om Query Store visas:

* "Query Store är inte korrekt konfigurerat i den här databasen. Klicka här för att läsa mer."
* "Query Store är inte korrekt konfigurerat i den här databasen. Klicka här för att ändra inställningar."

Dessa meddelanden visas vanligtvis när Query Store inte kan samla in nya data.

Det första fallet inträffar när Query Store är skrivskyddat tillstånd och parametrar ställs in optimalt. Du kan åtgärda detta genom att öka storleken på datalagret eller genom att rensa Query Store. (Om du avmarkerar Query Store går all tidigare insamlad telemetri förlorad.)

   ![Information om Query Store](./media/sql-database-query-performance/qds-off.png)

Det andra fallet inträffar när Query Store inte är aktiverat eller parametrar inte är optimalt inställda. Du kan ändra bevarande- och insamlingsprincipen och även aktivera Query Store genom att köra följande kommandon från [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) eller Azure-portalen.

### <a name="recommended-retention-and-capture-policy"></a>Rekommenderad bevarande- och insamlingsprincip

Det finns två typer av bevarandeprinciper:

* **Storleksbaserad:** Om den här principen är inställd **på AUTO**rensas data automatiskt när nära maximal storlek har uppnåtts.
* **Tidsbaserad**: Som standard är den här principen inställd på 30 dagar. Om Frågearkivet tar tar det bort utrymme tas frågeinformation som är äldre än 30 dagar bort.

Du kan ställa in infångningsprincipen till:

* **Alla**: Query Store samlar in alla frågor.
* **Auto**: Query Store ignorerar sällsynta frågor och frågor med obetydlig kompilering och körningsvaraktighet. Tröskelvärden för körningsantal, kompileringslängd och körningsvaraktighet bestäms internt. Det här är standardalternativet.
* **Ingen**: Query Store slutar fånga nya frågor, men körningsstatistik för redan insamlade frågor samlas fortfarande in.

Vi rekommenderar att du ställer in alla principer till **AUTO** och rensningsprincipen till 30 dagar genom att köra följande kommandon från [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) eller Azure-portalen. (Ersätt `YourDB` med databasnamnet.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Öka storleken på Query Store genom att ansluta till en databas via [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) eller Azure-portalen och köra följande fråga. (Ersätt `YourDB` med databasnamnet.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Om du använder dessa inställningar får du så småningom Query Store att samla in telemetri för nya frågor. Om du behöver Query Store för att fungera direkt kan du välja att rensa Query Store genom att köra följande fråga via SSMS eller Azure-portalen. (Ersätt `YourDB` med databasnamnet.)

> [!NOTE]
> Om du kör följande fråga tas alla tidigare insamlade övervakade telemetri bort i Query Store.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Nästa steg

Överväg att använda [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) för avancerad prestandaövervakning av en stor flotta av enstaka och poolade databaser, elastiska pooler, hanterade instanser och instansdatabaser.

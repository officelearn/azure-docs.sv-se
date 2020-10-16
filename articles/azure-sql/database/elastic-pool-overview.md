---
title: Hantera flera databaser med elastiska pooler
description: Hantera och skala flera databaser i Azure SQL Database – hundratals och tusentals användning av elastiska pooler. Ett pris för resurser som du kan distribuera vid behov.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, sstein
ms.date: 07/28/2020
ms.openlocfilehash: 3b76af2c6c949f2591cee880a1991c6f240806a2
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107903"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>Elastiska pooler hjälper dig att hantera och skala flera databaser i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database elastiska pooler är en enkel och kostnads effektiv lösning för hantering och skalning av flera databaser med varierande och oförutsägbara användnings krav. Databaserna i en elastisk pool finns på en enda server och delar ett visst antal resurser till ett visst pris. Med elastiska pooler i Azure SQL Database kan SaaS-utvecklare optimera prisprestanda för en grupp med databaser inom en fastställd budget samtidigt som de levererar flexibla prestanda för varje databas.

## <a name="what-are-sql-elastic-pools"></a>Vad är elastiska SQL-pooler

SaaS-utvecklare utvecklar program på storskaliga datanivåer som består av flera databaser. Ett vanligt programmönster är att etablera en enkel databas för varje kund. Men olika kunder har ofta varierande och oförutsägbara användnings mönster, och det är svårt att förutse resurs kraven för varje enskild databas användare. Traditionellt har du två alternativ:

- Resurser med överetablering baserat på högsta användning och över betalning, eller
- Under etableringen för att spara kostnaderna, vid kostnader för prestanda och kund nöjdhet under toppar.

Elastiska pooler löser problemet genom att se till att databaserna får de prestanda resurser de behöver när de behöver det. De tillhandahåller en enkel resursallokeringsmekanism med en förutsägbar budget. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](saas-tenancy-app-design-patterns.md).
>
> [!IMPORTANT]
> Det finns ingen avgift per databas för elastiska pooler. Du debiteras för varje timme som en pool finns på högsta eDTU eller virtuella kärnor, oavsett användning eller om poolen var aktiv i mindre än en timme.

Elastiska pooler gör det möjligt för utvecklaren att köpa resurser för en pool som delas av flera databaser för att hantera oförutsägbara användnings perioder för enskilda databaser. Du kan konfigurera resurser för poolen baserat antingen på den [DTU-baserade inköps modellen](service-tiers-dtu.md) eller den [vCore-baserade inköps modellen](service-tiers-vcore.md). Resurs kravet för en pool bestäms av de sammanställda databasernas sammanlagda användning. Mängden resurser som är tillgängliga för poolen styrs av utvecklings budgeten. Utvecklaren lägger bara till databaser i poolen, om du vill kan du ange de lägsta och högsta resurserna för databaserna (antingen minsta och högsta DTU: er eller lägsta eller högsta virtuella kärnor beroende på vilken modell du väljer) och sedan ange resurserna för poolen baserat på deras budget. Med hjälp av pooler kan utvecklare sömlöst expandera sina tjänster från en idé till en mogen affärsverksamhet som bara fortsätter att växa.

I poolen kan de enskilda databaserna skalas automatiskt inom fastställda parametrar. Under hög belastning kan en databas använda fler resurser för att möta efter frågan. Databaser under lätta inläsningar förbrukar mindre och databaser utan belastning förbrukar inga resurser. Genom att etablera resurser för hela poolen i stället för enskilda databaser kan du förenkla dina hanteringsuppgifter. Dessutom har du en förutsägbar budget för poolen. Ytterligare resurser kan läggas till i en befintlig pool med minsta stillestånds tid. Om extra resurser inte längre behövs kan de tas bort från en befintlig pool vid en viss tidpunkt. Och du kan lägga till eller ta bort databaser från poolen. Om du vet att en databas underförbrukar resurser tar du bort den.

> [!NOTE]
> När du flyttar databaser till eller från en elastisk pool finns det ingen stillestånds tid, förutom under en kort tids period (i ordningen på sekunder) i slutet av åtgärden när databas anslutningarna släpps.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>När bör du överväga en SQL Database elastisk pool

Pooler lämpar sig väl för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av låg genomsnittlig användning med relativt ovanliga användningstoppar. Däremot bör flera databaser med beständig medium-hög användning inte placeras i samma elastiska pool.

Ju fler databaser du kan lägga till i en pool desto större blir dina besparingar. Beroende på ditt användnings mönster för ditt program är det möjligt att se besparingar med så få som två S3-databaser.

Följande avsnitt hjälper dig att förstå hur du avgör om en specifik samling databaser kan ha nytta av att tillhöra en pool. I exemplen används Standard-pooler, men samma principer gäller även för Basic- och Premium-pooler.

### <a name="assessing-database-utilization-patterns"></a>Utvärdera databasanvändningsmönster

Följande bild visar ett exempel på en databas med mycket inaktiv tid, men även med regelbundna aktivitetstoppar. Det här är ett användningsmönster som passar för en pool:

   ![en enkel databas som är lämplig för en pool](./media/elastic-pool-overview/one-database.png)

Diagrammet illustrerar DTU-användning över en tids period på 1 timme från 12:00 till 1:00 där varje data punkt har 1 minut kornig het. Vid 12:10 DB1s toppar upp till 90 DTU: er, men den totala genomsnittliga användningen är mindre än fem DTU: er. En S3-beräknings storlek krävs för att köra den här arbets belastningen i en enda databas, men det lämnar de flesta av de resurser som inte används under perioder med låg aktivitet.

En pool gör att dessa oanvända DTU:er kan delas av flera databaser och minskar således de DTU:er som krävs och den sammanlagda kostnaden.

Vi ska bygga vidare på föregående exempel och antar att det finns ytterligare databaser med liknande användningsmönster som DB1. I de följande två figurerna nedan är användningen av fyra databaser och 20 databaser skiktas i samma graf för att illustrera den icke-överlappande typen av användning över tid med hjälp av den DTU-baserade inköps modellen:

   ![4 databaser med ett användningsmönster som passar för en pool](./media/elastic-pool-overview/four-databases.png)

   ![20 databaser med ett användningsmönster som passar för en pool](./media/elastic-pool-overview/twenty-databases.png)

Den sammanlagda DTU-användningen över alla 20 databaser illustreras av den svarta linjen i föregående bild. Detta visar att den sammanlagda DTU-användningen aldrig överskrider 100 DTU:er och indikerar att 20 databaser kan dela 100 eDTU:er under den här tidsperioden. Detta resulterar i en 20x minskning av DTU: er och en 13 gånger lägre pris reducering jämfört med att placera varje databas i S3 Compute-storlekarna för enskilda databaser.

Det här exemplet är idealisk av följande anledningar:

- Skillnaderna mellan användningen vid hög aktivitet och den genomsnittliga användningen per databas är stora.
- Belastningstopparna för varje databas inträffar vid olika tidpunkter.
- eDTU:er som delas mellan flera databaser.

Priset för en pool är associerat med poolens eDTU:er. Även om eDTU-enhetspriset för en pool är 1,5 gånger större än DTU-enhetspriset för en enkel databas, så kan **pool-DTU:erna delas av många databaser och det behövs färre eDTU:er**. Dessa skillnader i pris och eDTU-delning utgör grunden för de prisbesparingar som pooler kan medföra.

Följande regler för tummen för antal databaser och databas användning bidrar till att säkerställa att en pool ger lägre kostnad jämfört med att använda beräknings storlekar för enskilda databaser.

### <a name="minimum-number-of-databases"></a>Minsta antal databaser

Om den totala mängden resurser för enskilda databaser är mer än 1,5 x resurserna som behövs för poolen, är en elastisk pool mer kostnads effektiv.

***Exempel på DTU-baserad inköps modell*** Minst två S3-databaser eller minst 15 S0-databaser behövs för att en 100 eDTU-pool ska vara mer kostnads effektiv än att använda beräknings storlekar för enskilda databaser.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Högsta antal samtidigt databaser med aktivitetstoppar

Genom att dela resurser kan inte alla databaser i en pool samtidigt använda resurser upp till den tillgängliga gränsen för enskilda databaser. Ju färre databaser som är löpande, desto lägre kan poolens resurser anges och den mer kostnads effektiva poolen blir. I allmänhet är inte mer än 2/3 (eller 67%) av databaserna i poolen bör samtidigt ha hög belastning på sin resurs gräns.

***Exempel på DTU-baserad inköps modell*** För att minska kostnaderna för tre S3-databaser i en 200 eDTU-pool, kan de flesta två av dessa databaser samtidigt ha hög belastning i användningen. Annars, om fler än två av dessa fyra S3-databaser har toppar samtidigt, skulle poolen behöva utökas till mer än 200 eDTU:er. Om poolen ändras till mer än 200 eDTU: er, skulle fler S3-databaser behöva läggas till i poolen för att hålla lägre kostnader än beräknings storlekar för enskilda databaser.

Observera att det här exemplet inte förlitar sig på användning av andra databaser i poolen. Om alla databaser har viss belastning vid en given tidpunkt kan mindre än 2/3 (eller 67 %) av databaserna ha aktivitetstoppar samtidigt.

### <a name="resource-utilization-per-database"></a>Resursutnyttjande per databas

En stor skillnad mellan topp- och genomsnittsanvändningen av en databas indikerar långa perioder med låg belastning och korta perioder med hög användning. Det här användningsmönstret är idealisk för delning av resurser mellan databaser. Du bör överväga att lägga till en databas i en pool om dess högsta användning är runt 1,5 gånger större än dess genomsnittliga användning.

***Exempel på DTU-baserad inköps modell*** En S3-databas som är hög till 100 DTU: er och i genomsnitt använder 67 DTU: er eller mindre är en bra kandidat för att dela eDTU: er i en pool. På motsvarande sätt är en S1-databas som använder 20 DTU:er vid hög belastning och som har en genomsnittsanvändning på 13 DTU:er inte lämplig för en pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hur gör jag för att välja rätt pool-storlek

Den bästa storleken för en pool beror på vilka sammanställda resurser som behövs för alla databaser i poolen. Detta förutsätter att du fastställer följande:

- Maximalt antal resurser som används av alla databaser i poolen (antingen maximalt DTU: er eller maximalt virtuella kärnor beroende på ditt val av inköps modell).
- Högsta lagringsutrymme i byte som används av alla databaser i poolen.

För tillgängliga tjänst nivåer och begränsningar för varje resurs modell, se den [DTU-baserade inköps modellen](service-tiers-dtu.md) eller den [vCore-baserade inköps modellen](service-tiers-vcore.md).

Följande steg kan hjälpa dig att beräkna om en pool är mer kostnads effektiv än enkla databaser:

1. Beräkna eDTU: er eller virtuella kärnor som krävs för poolen enligt följande:

För DTU-baserad inköps modell:

MAX (<det *totala antalet* DTU- *användning per DB* -> för databaser x, <*antalet samtidigt toppvärde databaser* X *högsta DTU-användning per DB*>)

För vCore-baserad inköps modell:

MAX (<*totala antalet databaser* x *genomsnittlig vCore användning per db*>, <*antal databaser* X *högsta högsta vCore belastning per DB*>)

2. Beräkna hur stort lagringsutrymme som krävs för poolen genom att lägga till antalet byte som behövs för alla databaser i poolen. Fastställ sedan den eDTU-poolstorlek som ger den här mängden lagringsutrymme.
3. För den DTU-baserade inköps modellen tar du den större av eDTU-beräkningarna från steg 1 och steg 2. För den vCore-baserade inköps modellen ska du ta vCore uppskattningen från steg 1.
4. Se [sidan SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/) och hitta den minsta pool storlek som är större än uppskattningen från steg 3.
5. Jämför pool priset från steg 5 till priset för att använda lämpliga beräknings storlekar för enskilda databaser.

> [!IMPORTANT]
> Om antalet databaser i en pool närmar sig det högsta antal som stöds, se till att du funderar på [resurs hantering i kompakta elastiska pooler](elastic-pool-resource-management.md).

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Använda andra SQL Database funktioner med elastiska pooler

### <a name="elastic-jobs-and-elastic-pools"></a>Elastiska jobb och elastiska pooler

Med en pool förenklas hanteringsuppgifterna eftersom du kan köra skript i **[elastiska jobb](elastic-jobs-overview.md)**. Ett elastiskt jobb underlättar hanteringen av stora antal databaser.

Mer information om andra databasverktyg för att jobba med flera databaser, finns i [Skala ut med Azure SQL Database](elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Affärs kontinuitets alternativ för databaser i en elastisk pool

Pooldatabaser stöder generellt sett samma [funktioner för affärskontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md) som finns tillgängliga för enkla databaser.

- **Återställning från tidpunkt**

  Vid återställning av tidpunkt används automatiska databas säkerhets kopieringar för att återställa en databas i en pool till en viss tidpunkt. Mer information finns i avsnittet om [återställning till tidpunkt](recovery-using-backups.md#point-in-time-restore)

- **Geo-återställning**

  Geo-återställning tillhandahåller standard återställnings alternativet när en databas inte är tillgänglig på grund av en incident i den region där databasen finns. Se [återställa en Azure SQL Database eller redundans till en sekundär](disaster-recovery-guidance.md)

- **Aktiv geo-replikering**

  För program som har mer aggressiva återställnings krav än geo-återställning kan erbjuda, konfigurera [aktiv geo-replikering](active-geo-replication-overview.md) eller en [grupp för automatisk redundans](auto-failover-group-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Skapa en ny SQL Database elastisk pool med hjälp av Azure Portal

Det finns två sätt att skapa en elastisk pool i Azure Portal.

1. Gå till [Azure Portal](https://portal.azure.com) för att skapa en elastisk pool. Sök efter och välj **Azure SQL**.
2. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa mer information om elastiska pooler genom att välja **Visa information** på panelen **databaser** .
3. På panelen **databaser** väljer du **elastisk pool** i list rutan **resurs typ** och väljer sedan **skapa**:

   ![Skapa en elastisk pool](./media/elastic-pool-overview/create-elastic-pool.png)

4. Du kan också skapa en elastisk pool genom att gå till en befintlig server och klicka på **+ ny pool** för att skapa en pool direkt till den servern.

> [!NOTE]
> Du kan skapa flera pooler på en server, men du kan inte lägga till databaser från olika servrar i samma pool.

Poolens tjänst nivå bestämmer vilka funktioner som är tillgängliga för de elastiska i poolen och den maximala mängden resurser som är tillgängliga för varje databas. Mer information finns i resurs gränser för elastiska pooler i [DTU-modellen](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). VCore resurs gränser för elastiska pooler finns i [vCore resurs gränser-elastiska pooler](resource-limits-vcore-elastic-pools.md).

Klicka på **Konfigurera pool**om du vill konfigurera resurser och priser för poolen. Välj sedan en tjänst nivå, Lägg till databaser i poolen och konfigurera resurs gränserna för poolen och dess databaser.

När du har konfigurerat poolen kan du klicka på "Använd", namnge poolen och klicka på OK för att skapa poolen.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Övervaka en elastisk pool och dess databaser

I Azure Portal kan du övervaka användningen av en elastisk pool och databaserna i poolen. Du kan också göra en uppsättning ändringar i den elastiska poolen och skicka alla ändringar samtidigt. Ändringarna omfattar att lägga till eller ta bort databaser, ändra inställningarna för elastisk pool eller ändra databas inställningarna.

Starta övervakningen av den elastiska poolen genom att söka efter och öppna en elastisk pool i portalen. Först ser du en skärm som ger dig en översikt över statusen för den elastiska poolen. Det här omfattar:

- Övervaka diagram som visar resursanvändningen i den elastiska poolen
- Nya aviseringar och rekommendationer, om det är tillgängligt, för den elastiska poolen

Följande bild visar ett exempel på en elastisk pool:

![Vy för pool](./media/elastic-pool-overview/basic.png)

Om du vill ha mer information om poolen kan du klicka på någon av de tillgängliga uppgifterna i den här översikten. Om du klickar **på diagrammet resursutnyttjande** går du till vyn Azure-övervakning där du kan anpassa fönstret mått och tid som visas i diagrammet. Om du klickar på alla tillgängliga meddelanden går du till ett blad som visar den fullständiga informationen om aviseringen eller rekommendationen.

Om du vill övervaka databaserna inuti poolen kan du klicka på **resursutnyttjande i avsnittet** **övervakning** på resurs-menyn till vänster.

![Sidan resursanvändning för databas](./media/elastic-pool-overview/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Anpassa diagrammets visning

Du kan redigera diagrammet och mått sidan om du vill visa andra mått som processor procent, data IO-procent och logg-i/o-procent som används.

I formuläret **Redigera diagram** kan du välja ett fast tidsintervall eller klicka på **anpassad** för att välja ett 24-timmarsformat under de senaste två veckorna och sedan välja de resurser som ska övervakas.

### <a name="to-select-databases-to-monitor"></a>Välj databaser som ska övervakas

Diagrammet i bladet **databas resurs användning** visar som standard de 5 främsta databaserna av DTU eller CPU (beroende på din tjänst nivå). Du kan växla mellan databaserna i det här diagrammet genom att markera och avmarkera databaser från listan nedanför diagrammet via kryss rutorna till vänster.

Du kan också välja fler mått för att Visa sida vid sida i den här databas tabellen för att få en mer fullständig vy över dina databas prestanda.

Mer information finns i [skapa SQL Database aviseringar i Azure Portal](alerts-insights-configure-portal.md).

## <a name="customer-case-studies"></a>Kundfallstudier

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart använde elastiska pooler med Azure SQL Database för att snabbt utöka sina affärs tjänster till en hastighet av 1 000 nya Azure SQL-databaser per månad.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco använder elastiska pooler med Azure SQL Database för att snabbt etablera och skala tjänster för tusentals klienter i molnet.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI använder elastiska pooler med Azure SQL Database för att påskynda dess utvecklings cykel och förbättra sina kund tjänster och prestanda.

## <a name="next-steps"></a>Nästa steg

- Information om priser finns i [priser för elastisk pool](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Om du vill skala elastiska pooler, se [skala elastiska pooler](elastic-pool-scale.md) och [skala en elastisk pool – exempel kod](scripts/monitor-and-scale-pool-powershell.md)
- Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](saas-tenancy-app-design-patterns.md).
- En SaaS-självstudie med elastiska pooler finns i [Introduktion till Wingtip SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md).
- Mer information om resurs hantering i elastiska pooler med många databaser finns [i resurs hantering i kompakta elastiska pooler](elastic-pool-resource-management.md).

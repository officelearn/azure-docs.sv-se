---
title: Hantera flera SQL-databaser med elastiska pooler – Azure | Microsoft Docs
description: Hantera och skala flera SQL-databaser – hundratals och tusentals - med elastiska pooler. Ett pris för resurser som du kan distribuera där det behövs.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 292d24e8fb6d87174c481cd9dbca616497ff8ca3
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868930"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Hjälper dig att hantera och skala flera Azure SQL-databaser för elastiska pooler

Elastiska pooler i SQL Database är en enkel och kostnadseffektiv lösning för att hantera och skala flera databaser med varierande och oförutsägbara användningskrav. Databaser i en elastisk pool är på en enskild Azure SQL Database-server och dela ett bestämt antal resurser på ett fast pris. Med elastiska pooler i Azure SQL Database kan SaaS-utvecklare optimera prisprestanda för en grupp med databaser inom en fastställd budget samtidigt som de levererar flexibla prestanda för varje databas.

## <a name="what-are-sql-elastic-pools"></a>Vad är elastiska SQL-pooler?

SaaS-utvecklare utvecklar program på storskaliga datanivåer som består av flera databaser. Ett vanligt programmönster är att etablera en enkel databas för varje kund. Men olika kunder har ofta varierande och oförutsägbara användningsmönster, och det är svårt att förutse resursbehoven för varje enskild databasanvändare. Förr hade du två alternativ:

- Överetablera resurser baserat på högsta användning och betala per användning, eller
- Underetablera för att spara kostnader på bekostnad av prestanda och kundnöjdhet under perioder med aktivitetstoppar.

Elastiska pooler löser det här problemet genom att se till att databaserna får de prestandaresurser som de behöver när de behöver den. De tillhandahåller en enkel resursallokeringsmekanism med en förutsägbar budget. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Elastiska pooler gör att utvecklare kan köpa resurser för en pool som delas av flera databaser för att därigenom hantera oförutsägbara perioder av användning av enskilda databaser. Du kan konfigurera resurser för poolen baserat antingen på den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md). Resurskrav för poolen bestäms av den sammanlagda användningen av dess databaser. Mängden resurser som är tillgängligt för poolen beror på utvecklarens budget. Utvecklaren bara lägger till databaser i poolen, anger de lägsta och högsta resurserna för databaserna (antingen lägsta och högsta dtu: er eller lägsta eller högsta vCores beroende på ditt val av resourcing modellen), och ställer sedan resurser på poolen baserat på sina budget. Med hjälp av pooler kan utvecklare sömlöst expandera sina tjänster från en idé till en mogen affärsverksamhet som bara fortsätter att växa.

I poolen kan de enskilda databaserna skalas automatiskt inom fastställda parametrar. Vid hög belastning använda en databas fler resurser för att möta efterfrågan. Databaser under lätta laster förbrukar mindre, och databaser utan belastning förbrukar inga resurser. Genom att etablera resurser för hela poolen i stället för enskilda databaser kan du förenkla dina hanteringsuppgifter. Dessutom kan ha du en förutsägbar budget för poolen. Ytterligare resurser kan läggas till en befintlig pool utan något avbrott i databasen, förutom att databaserna kan behöva flyttas för att ge ytterligare beräkningsresurser för den nya eDTU-reservationen. På samma sätt, om extra resurser inte längre behövs de kan tas bort från en befintlig pool när som helst i tid. Och du kan lägga till eller ta bort databaser i poolen. Om du vet att en databas underförbrukar resurser tar du bort den.

> [!NOTE]
> När du flyttar databaser till eller från en elastisk pool, finns det inget avbrott utom en kort tidsperiod (i storleksordningen sekunder) i slutet av åtgärden när databasanslutningar ignoreras.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>När ska du tänka på en SQL Database-elastisk pool?

Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av låg genomsnittlig användning med relativt ovanliga användningstoppar.

Ju fler databaser du kan lägga till i en pool desto större blir dina besparingar. Beroende på ditt programanvändningsmönster kan du få besparingar med så lite som två S3-databaser.

Följande avsnitt hjälper dig att förstå hur du avgör om en specifik samling databaser kan ha nytta av att tillhöra en pool. I exemplen används Standard-pooler, men samma principer gäller även för Basic- och Premium-pooler.

### <a name="assessing-database-utilization-patterns"></a>Utvärdera databasanvändningsmönster

Följande bild visar ett exempel på en databas med mycket inaktiv tid, men även med regelbundna aktivitetstoppar. Det här är ett användningsmönster som passar för en pool:

   ![en enkel databas som är lämplig för en pool](./media/sql-database-elastic-pool/one-database.png)

Under femminutersperioden som visas har DB1 toppar med 90 DTU:er, men en genomsnittlig användning på mindre än 5 DTU:er. En S3-beräkningsstorleken krävs för att köra den här arbetsbelastningen i en enda databas, men det innebär att de flesta resurserna inte används under perioder med låg aktivitet.

En pool gör att dessa oanvända DTU:er kan delas av flera databaser och minskar således de DTU:er som krävs och den sammanlagda kostnaden.

Vi ska bygga vidare på föregående exempel och antar att det finns ytterligare databaser med liknande användningsmönster som DB1. I de två bilderna nedan är användningen av 4 databaser och 20 databaser lager i samma diagram som illustrerar den inte överlappar varandra deras användning över tid med hjälp av den DTU-baserade inköpsmodellen:

   ![4 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool/four-databases.png)

   ![20 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool/twenty-databases.png)

Den sammanlagda DTU-användningen över alla 20 databaser illustreras av den svarta linjen i föregående bild. Detta visar att den sammanlagda DTU-användningen aldrig överskrider 100 DTU:er och indikerar att 20 databaser kan dela 100 eDTU:er under den här tidsperioden. Detta resulterar i 20 gånger färre dtu: er och 13 gånger jämfört med att placera alla databaser i S3-prissänkning compute storlekar för enskilda databaser.

Det här exemplet är idealisk av följande anledningar:

* Skillnaderna mellan användningen vid hög aktivitet och den genomsnittliga användningen per databas är stora.
* Belastningstopparna för varje databas inträffar vid olika tidpunkter.
* eDTU:er som delas mellan flera databaser.

Priset för en pool är associerat med poolens eDTU:er. Även om eDTU-enhetspriset för en pool är 1,5 gånger större än DTU-enhetspriset för en enkel databas, så kan **pool-DTU:erna delas av många databaser och det behövs färre eDTU:er**. Dessa skillnader i pris och eDTU-delning utgör grunden för de prisbesparingar som pooler kan medföra.

I följande tumregler för databasantalet och databasanvändning att se till att en pool ger mindre kostnader jämfört med användningen av storlekar för enskilda databaser.

### <a name="minimum-number-of-databases"></a>Minsta antal databaser

Om summan av resurser för enskilda databaser är mindre än 1,5 gånger resurser som behövs för poolen, är det mer kostnadseffektivt med en elastisk pool.

***DTU-baserade inköpschef modell-exempel***<br>
Minst två S3-databaser eller minst 15 S0-databaser behövs för en pool med 100 edtu: er ska vara mer kostnadseffektivt än användningen av storlekar för enskilda databaser.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Högsta antal samtidigt databaser med aktivitetstoppar

Genom att dela resurser kan använda inte alla databaser i en pool samtidigt resurser upp till den tillgängliga gränsen för enskilda databaser. Ju färre databaser som har hög aktivitet som samtidigt, desto lägre poolresurser kan anges och desto mer kostnadseffektiv blir poolen. I allmänhet bör bör inte mer än 2/3 (eller 67%) av databaserna i poolen toppar samtidigt till sin gräns på resurser.

***DTU-baserade inköpschef modell-exempel***<br>
För att minska kostnaderna för tre S3-databaser i en pool med 200 eDTU:er kan högst två av dessa databaser ha belastningstoppar samtidigt. Annars, om fler än två av dessa fyra S3-databaser har toppar samtidigt, skulle poolen behöva utökas till mer än 200 eDTU:er. Om poolen utökas till fler än 200 edtu: er skulle fler S3-databaser behöva läggas till i poolen för att hålla kostnaderna lägre än compute storlekar för enskilda databaser.

Observera att det här exemplet inte tar hänsyn till användningen av andra databaser i poolen. Om alla databaser har viss belastning vid en given tidpunkt kan mindre än 2/3 (eller 67 %) av databaserna ha aktivitetstoppar samtidigt.

### <a name="resource-utilization-per-database"></a>Resursutnyttjande per databas
En stor skillnad mellan topp- och genomsnittsanvändningen av en databas indikerar långa perioder med låg belastning och korta perioder med hög användning. Det här användningsmönstret är idealisk för delning av resurser mellan databaser. Du bör överväga att lägga till en databas i en pool om dess högsta användning är runt 1,5 gånger större än dess genomsnittliga användning.

***DTU-baserade inköpschef modell-exempel***<br>
En S3-databas som behöver 100 DTU:er vid hög aktivitet och som har en genomsnittlig användning på 67 DTU:er eller mindre är lämplig för delning av eDTU:er i en pool. På motsvarande sätt är en S1-databas som använder 20 DTU:er vid hög belastning och som har en genomsnittsanvändning på 13 DTU:er inte lämplig för en pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hur väljer jag rätt poolstorlek?

Den bästa storleken för en pool beror på de sammanställda resurser som krävs för alla databaser i poolen. Detta innebär att du fastställa följande:

* Maximal resurser som används av alla databaser i poolen (maximala dtu: er eller högsta vCores beroende på ditt val av resourcing modell).
* Högsta lagringsutrymme i byte som används av alla databaser i poolen.

Tillgängliga tjänstnivåer för varje resursmodell, finns det [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

I de fall då du inte kan använda verktygsuppsättningar kan du följa stegen nedan för att ta reda på om en pool är ett mer kostnadseffektivt alternativ än enskilda databaser:

1. Beräkna edtu: er eller v-kärnor som behövs för en pool på följande sätt:

   För DTU-baserade inköpsmodellen: MAX (<*Totalt antal databaser* X *genomsnittlig DTU-användningen per databas*>,<br>  
   <*Antal databaser som har aktivitetstoppar samtidigt* X *DTU-toppbelastning per databas*)

   För vCore-baserade inköpsmodellen: MAX (<*Totalt antal databaser* X *genomsnittlig vCore användning per databas*>,<br>  
   <*Antal har aktivitetstoppar samtidigt databaser* X *vCore belastningstopparna per DB*)

2. Beräkna hur stort lagringsutrymme som krävs för poolen genom att lägga till antalet byte som behövs för alla databaser i poolen. Fastställ sedan den eDTU-poolstorlek som ger den här mängden lagringsutrymme.
3. DTU-baserade inköpsmodellen ta för större av eDTU-beräkningarna från steg 1 och steg 2. För vCore-baserade inköpsmodellen ta vCore-beräkningen från steg 1.
4. Se den [SQL Database-sidan med priser](https://azure.microsoft.com/pricing/details/sql-database/) och hitta det minsta poolstorleken som är större än beräkningen från steg3.
5. Jämför poolpriset i steg 5 med priset för med de lämpliga storlekarna för enskilda databaser.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Med hjälp av andra SQL Database-funktioner med elastiska pooler

### <a name="elastic-jobs-and-elastic-pools"></a>Elastiska jobb och elastiska pooler

Med en pool förenklas hanteringsuppgifterna eftersom du kan köra skript i **[elastiska jobb](sql-database-elastic-jobs-overview.md)**. Ett elastiskt jobb underlättar hanteringen av stora antal databaser. För att komma igång kan du se [Kom igång med elastiska jobb](sql-database-elastic-jobs-getting-started.md).

Mer information om andra databasverktyg för att jobba med flera databaser, finns i [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Alternativ för verksamhetskontinuitet för databaser i en elastisk pool
Databaser i pool stöder generellt sett samma [funktioner för affärskontinuitet](sql-database-business-continuity.md) som finns tillgängliga för enskilda databaser.

- **Point-in-time-återställning**: Point-in-time-återställning använder automatiska databassäkerhetskopieringar för att återställa en databas i en pool till en viss tidpunkt. Mer information finns i avsnittet om [återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore)

- **GEO-återställning**: Geo-återställning tillhandahåller standardalternativet för återställning när en databas är inte tillgänglig på grund av en incident i den region där databasen finns. Mer information finns i avsnittet [Restore an Azure SQL Database or failover to a secondary](sql-database-disaster-recovery.md) (Återställa en Azure SQL-databas eller växla över till en sekundär databas)

- **Aktiv geo-replikering**: program som har mer aggressiva återställningskrav än vad geo-återställning kan erbjuda, konfigurera [aktiv geo-replikering](sql-database-geo-replication-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Skapa en ny SQL Database-elastisk pool med Azure portal

Det finns två sätt som du kan skapa en elastisk pool i Azure-portalen.
1. Du kan skapa en elastisk pool genom att söka **SQL-databaspool** i den **Marketplace** eller klicka på **+ Lägg till** Bläddra bladet på de elastiska SQL-poolerna. Du kan ange en ny eller befintlig server via det här arbetsflödet för pooldistribution.
2. Eller så kan du skapa en elastisk pool genom att gå till en befintlig SQLServer och klicka **Skapa pool** att skapa en pool direkt till den servern. Den enda skillnaden är att du hoppa över steget där du anger servern under arbetsflödet för pooldistribution.

> [!NOTE]
> Du kan skapa flera pooler på en server, men du kan inte lägga till databaser från olika servrar i samma pool.

Poolens tjänstnivå avgör vilka funktioner som är tillgängliga för elastiska poolen och den maximala mängden resurser som är tillgängliga för varje databas. Mer information finns i resursgränser för elastiska pooler i den [DTU modellen](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). VCore-baserade resursbegränsningar för elastiska pooler, se [vCore-baserade resursbegränsningar - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

Konfigurera resurserna och priser för poolen klickar du på **konfigurera pool**. Välj sedan en tjänstnivå, lägga till databaser i poolen och konfigurera resursgränser för poolen och dess databaser.

När du har slutfört konfigurationen av poolen, kan du klicka på Verkställ, namnet på poolen och klicka på 'OK' om du vill skapa poolen.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Övervaka en elastisk pool och dess databaser

I Azure-portalen kan du övervaka användningen av en elastisk pool och databaserna i poolen. Du kan också göra en uppsättning ändringar i din elastiska pool och skicka alla ändringar på samma gång. De här förändringarna innefattar att lägga till eller ta bort databaser, ändra dina inställningar för elastisk pool eller ändra inställningarna för din databas.

Om du vill börja övervaka din elastiska pool, hitta och öppna en elastisk pool i portalen. En skärm som ger dig en översikt över statusen för din elastiska pool visas först. Det här omfattar:

* Övervakning av diagram som visar Resursanvändning för elastisk pool
* De senaste aviseringarna och rekommendationer om alternativet är tillgängligt för den elastiska poolen

Följande bild visar ett exempel elastisk pool:

![Pool-vy](./media/sql-database-elastic-pool-manage-portal/basic.png)

Om du vill ha mer information om poolen som du kan klicka på någon av tillgänglig information i den här översikten. När du klickar på den **resursutnyttjande** diagrammet dirigeras du till vyn övervakning av Azure där du kan anpassa fönstret mått och tid som visas i diagrammet. När du klickar på meddelanden som har tillgängliga tar dig till ett blad som visar fullständig information om aviseringen eller rekommendation.

Om du vill övervaka databaserna på din pool kan du klicka på **databasen resursutnyttjande** i den **övervakning** delen av resurs-menyn till vänster.

![Användning av databasen resurssida](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Du kan anpassa diagram

Du kan redigera diagrammet och sidan mått att visa andra mått som CPU-procentandel, data IO-procent och logg-i/o-procent Använd.

På den **redigera diagram** formuläret, kan du välja en fast tidpunkt intervall eller klicka på **anpassade** att välja alla 24-timmarsperiod under de senaste två veckorna Välj sedan resurserna som ska övervaka.

### <a name="to-select-databases-to-monitor"></a>Att välja databaser för att övervaka

Som standard diagrammet i den **Databasresursanvändning** bladet visar de 5 främsta databaserna av DTU eller CPU (beroende på din tjänstenivå). Du kan växla av databaserna i det här diagrammet genom att markera och avmarkera databaser i listan under diagrammet via kryssrutorna till vänster.

Du kan också välja fler mått att visa sida vid sida i den här databastabell för att få en mer komplett vy över dina databaser prestanda.

Mer information finns i [skapa aviseringar för SQL-databas i Azure-portalen](sql-database-insights-alerts-portal.md).

## <a name="next-steps"></a>Nästa steg

- Om du vill skala elastiska pooler finns i [skalning elastiska pooler](sql-database-elastic-pool.md) och [skala en elastisk pool - exempelkod](scripts/sql-database-monitor-and-scale-pool-powershell.md)
* Se en video [Microsoft Virtual Academy-videokurs om elastiska Azure SQL Database-funktioner](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Om du har en SaaS-självstudiekurs med hjälp av elastiska pooler finns i [introduktion till Wingtip SaaS-program](sql-database-wtp-overview.md).

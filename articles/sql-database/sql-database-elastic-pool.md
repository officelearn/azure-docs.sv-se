---
title: Hantera flera databaser med elastiska pooler
description: Hantera och skala flera SQL-databaser – hundratals och tusentals användning av elastiska pooler. Ett pris för resurser som du kan distribuera vid behov.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 08/06/2019
ms.openlocfilehash: ba309b864056b10fe6540e85ffbc4c013af00455
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186475"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastiska pooler hjälper dig att hantera och skala flera Azure SQL-databaser

Elastiska pooler i SQL Database är en enkel och kostnadseffektiv lösning för att hantera och skala flera databaser med varierande och oförutsägbara användningskrav. Databaserna i en elastisk pool finns på en enda Azure SQL Database Server och delar ett visst antal resurser till ett visst pris. Med elastiska pooler i Azure SQL Database kan SaaS-utvecklare optimera prisprestanda för en grupp med databaser inom en fastställd budget samtidigt som de levererar flexibla prestanda för varje databas.

## <a name="what-are-sql-elastic-pools"></a>Vad är elastiska SQL-pooler

SaaS-utvecklare utvecklar program på storskaliga datanivåer som består av flera databaser. Ett vanligt programmönster är att etablera en enkel databas för varje kund. Men olika kunder har ofta varierande och oförutsägbara användningsmönster, och det är svårt att förutse resursbehoven för varje enskild databasanvändare. Traditionellt har du två alternativ:

- Resurser med överetablering baserat på högsta användning och över betalning, eller
- Under etableringen för att spara kostnaderna, vid kostnader för prestanda och kund nöjdhet under toppar.

Elastiska pooler löser problemet genom att se till att databaserna får de prestanda resurser de behöver när de behöver det. De tillhandahåller en enkel resursallokeringsmekanism med en förutsägbar budget. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Det finns ingen avgift per databas för elastiska pooler. Du debiteras för varje timme som en pool finns på högsta eDTU eller virtuella kärnor, oavsett användning eller om poolen var aktiv i mindre än en timme.

Elastiska pooler gör det möjligt för utvecklaren att köpa resurser för en pool som delas av flera databaser för att hantera oförutsägbara användnings perioder för enskilda databaser. Du kan konfigurera resurser för poolen baserat antingen på den [DTU-baserade inköps modellen](sql-database-service-tiers-dtu.md) eller den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md). Resurs kravet för en pool bestäms av de sammanställda databasernas sammanlagda användning. Mängden resurser som är tillgängliga för poolen styrs av utvecklings budgeten. Utvecklaren lägger bara till databaser i poolen, ställer in de lägsta och högsta resurserna för databaserna (antingen minsta och högsta DTU: er eller lägsta eller högsta virtuella kärnor beroende på ditt val av omkällans modell) och anger sedan poolens resurser baserat på deras finans. Med hjälp av pooler kan utvecklare sömlöst expandera sina tjänster från en idé till en mogen affärsverksamhet som bara fortsätter att växa.

I poolen kan de enskilda databaserna skalas automatiskt inom fastställda parametrar. Under hög belastning kan en databas använda fler resurser för att möta efter frågan. Databaser under lätta inläsningar förbrukar mindre och databaser utan belastning förbrukar inga resurser. Genom att etablera resurser för hela poolen i stället för enskilda databaser kan du förenkla dina hanteringsuppgifter. Dessutom har du en förutsägbar budget för poolen. Ytterligare resurser kan läggas till i en befintlig pool utan avbrott i databasen, förutom att databaserna kan behöva flyttas för att ge ytterligare beräknings resurser för den nya eDTU-reservationen. Om extra resurser inte längre behövs kan de tas bort från en befintlig pool vid en viss tidpunkt. Och du kan lägga till eller ta bort databaser i poolen. Om du vet att en databas underförbrukar resurser tar du bort den.

> [!NOTE]
> När du flyttar databaser till eller från en elastisk pool finns det ingen stillestånds tid, förutom under en kort tids period (i ordningen på sekunder) i slutet av åtgärden när databas anslutningarna släpps.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>När bör du överväga en SQL Database elastisk pool

Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av låg genomsnittlig användning med relativt ovanliga användningstoppar.

Ju fler databaser du kan lägga till i en pool desto större blir dina besparingar. Beroende på ditt programanvändningsmönster kan du få besparingar med så lite som två S3-databaser.

Följande avsnitt hjälper dig att förstå hur du avgör om en specifik samling databaser kan ha nytta av att tillhöra en pool. I exemplen används Standard-pooler, men samma principer gäller även för Basic- och Premium-pooler.

### <a name="assessing-database-utilization-patterns"></a>Utvärdera databasanvändningsmönster

Följande bild visar ett exempel på en databas med mycket inaktiv tid, men även med regelbundna aktivitetstoppar. Det här är ett användningsmönster som passar för en pool:

   ![en enkel databas som är lämplig för en pool](./media/sql-database-elastic-pool/one-database.png)

Under femminutersperioden som visas har DB1 toppar med 90 DTU:er, men en genomsnittlig användning på mindre än 5 DTU:er. En S3-beräknings storlek krävs för att köra den här arbets belastningen i en enda databas, men det lämnar de flesta av de resurser som inte används under perioder med låg aktivitet.

En pool gör att dessa oanvända DTU:er kan delas av flera databaser och minskar således de DTU:er som krävs och den sammanlagda kostnaden.

Vi ska bygga vidare på föregående exempel och antar att det finns ytterligare databaser med liknande användningsmönster som DB1. I de följande två figurerna nedan är användningen av fyra databaser och 20 databaser skiktas i samma graf för att illustrera den icke-överlappande typen av användning över tid med hjälp av den DTU-baserade inköps modellen:

   ![4 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool/four-databases.png)

   ![20 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool/twenty-databases.png)

Den sammanlagda DTU-användningen över alla 20 databaser illustreras av den svarta linjen i föregående bild. Detta visar att den sammanlagda DTU-användningen aldrig överskrider 100 DTU:er och indikerar att 20 databaser kan dela 100 eDTU:er under den här tidsperioden. Detta resulterar i en 20x minskning av DTU: er och en 13 gånger lägre pris reducering jämfört med att placera varje databas i S3 Compute-storlekarna för enskilda databaser.

Det här exemplet är idealisk av följande anledningar:

- Skillnaderna mellan användningen vid hög aktivitet och den genomsnittliga användningen per databas är stora.
- Belastningstopparna för varje databas inträffar vid olika tidpunkter.
- eDTU:er som delas mellan flera databaser.

Priset för en pool är associerat med poolens eDTU:er. Även om eDTU-enhetspriset för en pool är 1,5 gånger större än DTU-enhetspriset för en enkel databas, så kan **pool-DTU:erna delas av många databaser och det behövs färre eDTU:er**. Dessa skillnader i pris och eDTU-delning utgör grunden för de prisbesparingar som pooler kan medföra.

Följande regler för tummen för antal databaser och databas användning bidrar till att säkerställa att en pool ger lägre kostnad jämfört med att använda beräknings storlekar för enskilda databaser.

### <a name="minimum-number-of-databases"></a>Minsta antal databaser

Om den totala mängden resurser för enskilda databaser är mer än 1,5 x resurserna som behövs för poolen, är en elastisk pool mer kostnads effektiv.

***Exempel på DTU-baserad inköps modell***<br>
Minst två S3-databaser eller minst 15 S0-databaser behövs för att en 100 eDTU-pool ska vara mer kostnads effektiv än att använda beräknings storlekar för enskilda databaser.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Högsta antal samtidigt databaser med aktivitetstoppar

Genom att dela resurser kan inte alla databaser i en pool samtidigt använda resurser upp till den tillgängliga gränsen för enskilda databaser. Ju färre databaser som är löpande, desto lägre kan poolens resurser anges och den mer kostnads effektiva poolen blir. I allmänhet är inte mer än 2/3 (eller 67%) av databaserna i poolen bör samtidigt ha hög belastning på sin resurs gräns.

***Exempel på DTU-baserad inköps modell***

För att minska kostnaderna för tre S3-databaser i en pool med 200 eDTU:er kan högst två av dessa databaser ha belastningstoppar samtidigt. Annars, om fler än två av dessa fyra S3-databaser har toppar samtidigt, skulle poolen behöva utökas till mer än 200 eDTU:er. Om poolen ändras till mer än 200 eDTU: er, skulle fler S3-databaser behöva läggas till i poolen för att hålla lägre kostnader än beräknings storlekar för enskilda databaser.

Observera att det här exemplet inte tar hänsyn till användningen av andra databaser i poolen. Om alla databaser har viss belastning vid en given tidpunkt kan mindre än 2/3 (eller 67 %) av databaserna ha aktivitetstoppar samtidigt.

### <a name="resource-utilization-per-database"></a>Resursutnyttjande per databas

En stor skillnad mellan topp- och genomsnittsanvändningen av en databas indikerar långa perioder med låg belastning och korta perioder med hög användning. Det här användningsmönstret är idealisk för delning av resurser mellan databaser. Du bör överväga att lägga till en databas i en pool om dess högsta användning är runt 1,5 gånger större än dess genomsnittliga användning.

**Exempel på DTU-baserad inköps modell**: en S3-databas som är hög till 100 DTU: er och i genomsnitt använder 67 DTU: er eller mindre är en bra kandidat för att dela eDTU: er i en pool. På motsvarande sätt är en S1-databas som använder 20 DTU:er vid hög belastning och som har en genomsnittsanvändning på 13 DTU:er inte lämplig för en pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hur gör jag för att välja rätt pool-storlek

Den bästa storleken för en pool beror på vilka sammanställda resurser som behövs för alla databaser i poolen. Detta förutsätter att du fastställer följande:

- Maximalt antal resurser som används av alla databaser i poolen (antingen maximalt DTU: er eller maximalt virtuella kärnor beroende på ditt val av omkällaing-modell).
- Högsta lagringsutrymme i byte som används av alla databaser i poolen.

För tillgängliga tjänst nivåer för varje resurs modell, se den [DTU-baserade inköps modellen](sql-database-service-tiers-dtu.md) eller den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md).

I de fall då du inte kan använda verktygsuppsättningar kan du följa stegen nedan för att ta reda på om en pool är ett mer kostnadseffektivt alternativ än enskilda databaser:

1. Beräkna eDTU: er eller virtuella kärnor som krävs för poolen enligt följande:

   För DTU-baserad inköps modell: MAX (<*Totalt antal databaser* X *genomsnittlig DTU-användning per DB*>,<br>  
   <*Antal databaser som har aktivitetstoppar samtidigt* × *DTU-toppbelastning per databas*)

   För vCore-baserad inköps modell: MAX (<*totala antalet databaser* X *genomsnittlig vCore användning per DB*>,<br>  
   <*antal databaser* X *Top vCore belastning per DB*)

2. Beräkna hur stort lagringsutrymme som krävs för poolen genom att lägga till antalet byte som behövs för alla databaser i poolen. Fastställ sedan den eDTU-poolstorlek som ger den här mängden lagringsutrymme.
3. För den DTU-baserade inköps modellen tar du den större av eDTU-beräkningarna från steg 1 och steg 2. För den vCore-baserade inköps modellen ska du ta vCore uppskattningen från steg 1.
4. Se [sidan SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/) och hitta den minsta pool storlek som är större än uppskattningen från steg 3.
5. Jämför pool priset från steg 5 till priset för att använda lämpliga beräknings storlekar för enskilda databaser.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Använda andra SQL Database funktioner med elastiska pooler

### <a name="elastic-jobs-and-elastic-pools"></a>Elastiska jobb och elastiska pooler

Med en pool förenklas hanteringsuppgifterna eftersom du kan köra skript i **[elastiska jobb](elastic-jobs-overview.md)** . Ett elastiskt jobb underlättar hanteringen av stora antal databaser.

Mer information om andra databasverktyg för att jobba med flera databaser, finns i [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Affärs kontinuitets alternativ för databaser i en elastisk pool

Pooldatabaser stöder generellt sett samma [funktioner för affärskontinuitet](sql-database-business-continuity.md) som finns tillgängliga för enkla databaser.

- **Återställning av tidpunkt**

  Vid återställning av tidpunkt används automatiska databas säkerhets kopieringar för att återställa en databas i en pool till en viss tidpunkt. Mer information finns i avsnittet om [återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Geo-återställning**

  Geo-återställning tillhandahåller standard återställnings alternativet när en databas inte är tillgänglig på grund av en incident i den region där databasen finns. Mer information finns i avsnittet [Restore an Azure SQL Database or failover to a secondary](sql-database-disaster-recovery.md) (Återställa en Azure SQL-databas eller växla över till en sekundär databas)

- **Aktiv geo-replikering**

  För program som har mer aggressiva återställnings krav än geo-återställning kan erbjuda, konfigurera [aktiv geo-replikering](sql-database-active-geo-replication.md) eller en [grupp för automatisk redundans](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Skapa en ny SQL Database elastisk pool med hjälp av Azure Portal

Det finns två sätt att skapa en elastisk pool i Azure Portal.

1. Välj **Azure SQL** i den vänstra menyn i Azure Portal. Om Azure SQL inte finns i listan väljer du **alla tjänster**och skriver sedan *Azure SQL* i sökrutan.
2. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa mer information om elastiska pooler genom att välja **Visa information** på panelen **databaser** .
3. På panelen **databaser** väljer du **elastisk pool** i list rutan **resurs typ** och väljer sedan **skapa**:

   ![Skapa en elastisk pool](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. Du kan också skapa en elastisk pool genom att gå till en befintlig Azure SQL-Server och klicka på **+ ny pool** för att skapa en pool direkt till den servern.

> [!NOTE]
> Du kan skapa flera pooler på en server, men du kan inte lägga till databaser från olika servrar i samma pool.

Poolens tjänst nivå bestämmer vilka funktioner som är tillgängliga för de elastiska i poolen och den maximala mängden resurser som är tillgängliga för varje databas. Mer information finns i resurs gränser för elastiska pooler i [DTU-modellen](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). VCore resurs gränser för elastiska pooler finns i [vCore resurs gränser-elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

Klicka på **Konfigurera pool**om du vill konfigurera resurser och priser för poolen. Välj sedan en tjänst nivå, Lägg till databaser i poolen och konfigurera resurs gränserna för poolen och dess databaser.

När du har konfigurerat poolen kan du klicka på "Använd", namnge poolen och klicka på OK för att skapa poolen.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Övervaka en elastisk pool och dess databaser

I Azure Portal kan du övervaka användningen av en elastisk pool och databaserna i poolen. Du kan också göra en uppsättning ändringar i den elastiska poolen och skicka alla ändringar samtidigt. Ändringarna omfattar att lägga till eller ta bort databaser, ändra inställningarna för elastisk pool eller ändra databas inställningarna.

Starta övervakningen av den elastiska poolen genom att söka efter och öppna en elastisk pool i portalen. Först visas en skärm som ger dig en översikt över statusen för den elastiska poolen. Det här omfattar:

- Övervaka diagram som visar resursanvändningen i den elastiska poolen
- Nya aviseringar och rekommendationer, om det är tillgängligt, för den elastiska poolen

Följande bild visar ett exempel på en elastisk pool:

![Vy för pool](./media/sql-database-elastic-pool-manage-portal/basic.png)

Om du vill ha mer information om poolen kan du klicka på någon av de tillgängliga uppgifterna i den här översikten. Om du klickar **på diagrammet resursutnyttjande** går du till vyn Azure-övervakning där du kan anpassa fönstret mått och tid som visas i diagrammet. Om du klickar på alla tillgängliga meddelanden går du till ett blad som visar den fullständiga informationen om aviseringen eller rekommendationen.

Om du vill övervaka databaserna inuti poolen kan du klicka på **resursutnyttjande i avsnittet** **övervakning** på resurs-menyn till vänster.

![Sidan resursanvändning för databas](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Anpassa diagrammets visning

Du kan redigera diagrammet och mått sidan om du vill visa andra mått som processor procent, data IO-procent och logg-i/o-procent som används.

I formuläret **Redigera diagram** kan du välja ett fast tidsintervall eller klicka på **anpassad** för att välja ett 24-timmarsformat under de senaste två veckorna och sedan välja de resurser som ska övervakas.

### <a name="to-select-databases-to-monitor"></a>Välj databaser som ska övervakas

Diagrammet i bladet **databas resurs användning** visar som standard de 5 främsta databaserna av DTU eller CPU (beroende på din tjänst nivå). Du kan växla mellan databaserna i det här diagrammet genom att markera och avmarkera databaser från listan nedanför diagrammet via kryss rutorna till vänster.

Du kan också välja fler mått för att Visa sida vid sida i den här databas tabellen för att få en mer fullständig vy över dina databas prestanda.

Mer information finns i [skapa SQL Database aviseringar i Azure Portal](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Kundfallstudier

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart använde elastiska pooler med Azure SQL Database för att snabbt utöka sina affärs tjänster till en hastighet av 1 000 nya Azure SQL-databaser per månad.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco använder elastiska pooler med Azure SQL Database för att snabbt etablera och skala tjänster för tusentals klienter i molnet.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   Daxko/CSI använder elastiska pooler med Azure SQL Database för att påskynda dess utvecklings cykel och förbättra sina kund tjänster och prestanda.   

## <a name="next-steps"></a>Nästa steg

- Om du vill skala elastiska pooler, se [skala elastiska pooler](sql-database-elastic-pool-scale.md) och [skala en elastisk pool – exempel kod](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- För en video, se [Microsoft Virtual Academy video kurs på Azure SQL Database elastiska funktioner](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- En SaaS-självstudie med elastiska pooler finns i [Introduktion till Wingtip SaaS-programmet](sql-database-wtp-overview.md).
- Mer information om resurs hantering i elastiska pooler med många databaser finns [i resurs hantering i kompakta elastiska pooler](sql-database-elastic-pool-resource-management.md).

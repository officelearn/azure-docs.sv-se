---
title: Hantera flera databaser med elastiska pooler
description: Hantera och skala flera SQL-databaser - hundratals och tusentals - med hjälp av elastiska pooler. Ett pris för resurser som du kan fördela där det behövs.
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
ms.openlocfilehash: 8139ed8f4f4799a963a051eed96dd87c4ac38aec
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981434"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastiska pooler hjälper dig att hantera och skala flera Azure SQL-databaser

Elastiska pooler i SQL Database är en enkel och kostnadseffektiv lösning för att hantera och skala flera databaser med varierande och oförutsägbara användningskrav. Databaserna i en elastisk pool finns på en enda Azure SQL Database-server och delar ett bestämt antal resurser till ett fast pris. Med elastiska pooler i Azure SQL Database kan SaaS-utvecklare optimera prisprestanda för en grupp med databaser inom en fastställd budget samtidigt som de levererar flexibla prestanda för varje databas.

## <a name="what-are-sql-elastic-pools"></a>Vad är ELASTISKA SQL-pooler

SaaS-utvecklare utvecklar program på storskaliga datanivåer som består av flera databaser. Ett vanligt programmönster är att etablera en enkel databas för varje kund. Men olika kunder har ofta olika och oförutsägbara användningsmönster, och det är svårt att förutsäga resurskraven för varje enskild databasanvändare. Traditionellt hade du två alternativ:

- Överetableringsresurser baserat på maximal användning och överlön, eller
- Under-provision för att spara kostnader, på bekostnad av prestanda och kundnöjdhet under toppar.

Elastiska pooler löser det här problemet genom att se till att databaser får de prestandaresurser de behöver när de behöver det. De tillhandahåller en enkel resursallokeringsmekanism med en förutsägbar budget. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Det finns ingen avgift per databas för elastiska pooler. Du faktureras för varje timme som en pool finns vid den högsta eDTU- eller vCore-vyn, oavsett användning eller om poolen var aktiv i mindre än en timme.

Elastiska pooler gör det möjligt för utvecklaren att köpa resurser för en pool som delas av flera databaser för att hantera oförutsägbara perioder av användning av enskilda databaser. Du kan konfigurera resurser för poolen baserat antingen på den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) eller den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md). Resursbehovet för en pool bestäms av den sammanlagda användningen av dess databaser. Mängden resurser som är tillgängliga för poolen styrs av utvecklarbudgeten. Utvecklaren lägger helt enkelt till databaser i poolen, anger de lägsta och högsta resurserna för databaserna (antingen lägsta och högsta DTU:er eller lägsta eller högsta virtuella kärnor beroende på ditt val av resursmodell) och anger sedan poolens resurser baserat på deras budget. Med hjälp av pooler kan utvecklare sömlöst expandera sina tjänster från en idé till en mogen affärsverksamhet som bara fortsätter att växa.

I poolen kan de enskilda databaserna skalas automatiskt inom fastställda parametrar. Under tung belastning kan en databas förbruka mer resurser för att möta efterfrågan. Databaser under lätta belastningar förbrukar mindre och databaser under ingen belastning förbrukar inga resurser. Genom att etablera resurser för hela poolen i stället för enskilda databaser kan du förenkla dina hanteringsuppgifter. Dessutom har du en förutsägbar budget för poolen. Ytterligare resurser kan läggas till i en befintlig pool utan avbrott i databasen, förutom att databaserna kan behöva flyttas för att tillhandahålla ytterligare beräkningsresurser för den nya eDTU-reservationen. På samma sätt, om extra resurser inte längre behövs de kan tas bort från en befintlig pool när som helst. Och du kan lägga till eller ta bort databaser i poolen. Om du vet att en databas underförbrukar resurser tar du bort den.

> [!NOTE]
> När du flyttar databaser till eller från en elastisk pool finns det inga driftstopp förutom en kort tidsperiod (i storleksordningen sekunder) i slutet av åtgärden när databasanslutningar tas bort.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>När bör du överväga en elastisk SQL Database-pool

Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av låg genomsnittlig användning med relativt ovanliga användningstoppar.

Ju fler databaser du kan lägga till i en pool desto större blir dina besparingar. Beroende på ditt programutnyttjandemönster är det möjligt att se besparingar med så få som två S3-databaser.

Följande avsnitt hjälper dig att förstå hur du avgör om en specifik samling databaser kan ha nytta av att tillhöra en pool. I exemplen används Standard-pooler, men samma principer gäller även för Basic- och Premium-pooler.

### <a name="assessing-database-utilization-patterns"></a>Utvärdera databasanvändningsmönster

Följande bild visar ett exempel på en databas med mycket inaktiv tid, men även med regelbundna aktivitetstoppar. Det här är ett användningsmönster som passar för en pool:

   ![en enkel databas som är lämplig för en pool](./media/sql-database-elastic-pool/one-database.png)

Under femminutersperioden som visas har DB1 toppar med 90 DTU:er, men en genomsnittlig användning på mindre än 5 DTU:er. En S3-beräkningsstorlek krävs för att köra den här arbetsbelastningen i en enda databas, men det lämnar de flesta resurser oanvända under perioder med låg aktivitet.

En pool gör att dessa oanvända DTU:er kan delas av flera databaser och minskar således de DTU:er som krävs och den sammanlagda kostnaden.

Vi ska bygga vidare på föregående exempel och antar att det finns ytterligare databaser med liknande användningsmönster som DB1. I de följande två siffrorna nedan, utnyttjandet av fyra databaser och 20 databaser är skiktad på samma diagram för att illustrera den icke-överlappande karaktären av deras användning över tiden med hjälp av DTU-baserade inköpsmodell:

   ![4 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool/four-databases.png)

   ![20 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool/twenty-databases.png)

Den sammanlagda DTU-användningen över alla 20 databaser illustreras av den svarta linjen i föregående bild. Detta visar att den sammanlagda DTU-användningen aldrig överskrider 100 DTU:er och indikerar att 20 databaser kan dela 100 eDTU:er under den här tidsperioden. Detta resulterar i en 20x minskning av DU:er och en 13x prissänkning jämfört med att placera var och en av databaserna i S3-beräkningsstorlekar för enskilda databaser.

Det här exemplet är idealisk av följande anledningar:

- Skillnaderna mellan användningen vid hög aktivitet och den genomsnittliga användningen per databas är stora.
- Belastningstopparna för varje databas inträffar vid olika tidpunkter.
- eDTU:er som delas mellan flera databaser.

Priset för en pool är associerat med poolens eDTU:er. Även om eDTU-enhetspriset för en pool är 1,5 gånger större än DTU-enhetspriset för en enkel databas, så kan **pool-DTU:erna delas av många databaser och det behövs färre eDTU:er**. Dessa skillnader i pris och eDTU-delning utgör grunden för de prisbesparingar som pooler kan medföra.

Följande tumregler relaterade till databasantal och databasanvändning hjälper till att säkerställa att en pool levererar minskade kostnader jämfört med att använda beräkningsstorlekar för enskilda databaser.

### <a name="minimum-number-of-databases"></a>Minsta antal databaser

Om den sammanlagda mängden resurser för enskilda databaser är mer än 1,5 x de resurser som behövs för poolen, är en elastisk pool mer kostnadseffektiv.

***DTU-baserat inköpsmodellexempel***<br>
Minst två S3-databaser eller minst 15 S0-databaser behövs för att en 100 eDTU-pool ska vara mer kostnadseffektiv än att använda beräkningsstorlekar för enskilda databaser.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Högsta antal samtidigt databaser med aktivitetstoppar

Genom att dela resurser kan inte alla databaser i en pool samtidigt använda resurser upp till den gräns som är tillgänglig för enskilda databaser. Ju färre databaser som samtidigt når sin topp, desto lägre kan poolresurserna ställas in och desto mer kostnadseffektiv blir poolen. I allmänhet är högst 2/3 (eller 67%) av databaserna i poolen bör samtidigt topp till sina resurser gräns.

***DTU-baserat inköpsmodellexempel***

För att minska kostnaderna för tre S3-databaser i en pool med 200 eDTU:er kan högst två av dessa databaser ha belastningstoppar samtidigt. Annars, om fler än två av dessa fyra S3-databaser har toppar samtidigt, skulle poolen behöva utökas till mer än 200 eDTU:er. Om poolen ändras till mer än 200 eDTUs, fler S3-databaser skulle behöva läggas till poolen för att hålla kostnaderna lägre än beräkningsstorlekar för enskilda databaser.

Observera att det här exemplet inte tar hänsyn till användning av andra databaser i poolen. Om alla databaser har viss belastning vid en given tidpunkt kan mindre än 2/3 (eller 67 %) av databaserna ha aktivitetstoppar samtidigt.

### <a name="resource-utilization-per-database"></a>Resursutnyttjande per databas

En stor skillnad mellan topp- och genomsnittsanvändningen av en databas indikerar långa perioder med låg belastning och korta perioder med hög användning. Det här användningsmönstret är idealisk för delning av resurser mellan databaser. Du bör överväga att lägga till en databas i en pool om dess högsta användning är runt 1,5 gånger större än dess genomsnittliga användning.

**DTU-baserade inköpsmodellexempel:** En S3-databas som når 100 DTU:er och i genomsnitt använder 67 DTU:er eller mindre är en bra kandidat för att dela eDTU:er i en pool. På motsvarande sätt är en S1-databas som använder 20 DTU:er vid hög belastning och som har en genomsnittsanvändning på 13 DTU:er inte lämplig för en pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hur väljer jag rätt poolstorlek

Den bästa storleken för en pool beror på de samlade resurser som behövs för alla databaser i poolen. Detta innebär att fastställa följande:

- Maximala resurser som används av alla databaser i poolen (antingen maximala DTU:er eller högsta virtuella kärnor beroende på ditt val av resursmodell).
- Högsta lagringsutrymme i byte som används av alla databaser i poolen.

För tillgängliga tjänstnivåer för varje resursmodell finns i den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) eller den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

I de fall då du inte kan använda verktygsuppsättningar kan du följa stegen nedan för att ta reda på om en pool är ett mer kostnadseffektivt alternativ än enskilda databaser:

1. Uppskatta de eDTUs eller virtuella kärnor som behövs för poolen enligt följande:

   För DTU-baserad inköpsmodell: MAX(<*Totalt antal DBs* X *genomsnittlig DTU-användning per* DB->,<br>  
   <*Antal databaser som har aktivitetstoppar samtidigt* × *DTU-toppbelastning per databas*)

   För vCore-baserad inköpsmodell: MAX(<*Totalt antal antal virtuella VCore-användning* per *DB->,*<br>  
   <*Antal samtidiga topp-DBs* X *Peak vCore utnyttjande per DB*)

2. Beräkna hur stort lagringsutrymme som krävs för poolen genom att lägga till antalet byte som behövs för alla databaser i poolen. Fastställ sedan den eDTU-poolstorlek som ger den här mängden lagringsutrymme.
3. För den DTU-baserade inköpsmodellen tar du det större av eDTU-uppskattningarna från steg 1 och steg 2. För den vCore-baserade inköpsmodellen tar du vCore-uppskattningen från steg 1.
4. Se [prissidan för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) och hitta den minsta poolstorleken som är större än uppskattningen från steg 3.
5. Jämför poolpriset från steg 5 till priset för att använda lämpliga beräkningsstorlekar för enskilda databaser.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Använda andra SQL Database-funktioner med elastiska pooler

### <a name="elastic-jobs-and-elastic-pools"></a>Elastiska jobb och elastiska pooler

Med en pool förenklas hanteringsuppgifterna eftersom du kan köra skript i **[elastiska jobb](elastic-jobs-overview.md)**. Ett elastiskt jobb underlättar hanteringen av stora antal databaser.

Mer information om andra databasverktyg för att jobba med flera databaser, finns i [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Alternativ för kontinuitet i verksamheten för databaser i en elastisk pool

Pooldatabaser stöder generellt sett samma [funktioner för affärskontinuitet](sql-database-business-continuity.md) som finns tillgängliga för enkla databaser.

- **Återställning från tidpunkt**

  Point-in-time-återställning använder automatiska säkerhetskopieringar av databaser för att återställa en databas i en pool till en viss tidpunkt. Mer information finns i avsnittet om [återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Geo-återställande**

  Geoåterställning ger standardalternativet återställning när en databas inte är tillgänglig på grund av en incident i den region där databasen finns. Se [Återställa en Azure SQL-databas eller redundans till en sekundär](sql-database-disaster-recovery.md)

- **Aktiv geo-replikering**

  Konfigurera [Active geo-replication](sql-database-active-geo-replication.md) eller en grupp för [automatisk redundans](sql-database-auto-failover-group.md)för program som har mer aggressiva återställningskrav än geoåterställning.

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Skapa en ny elastisk SQL Database-pool med Azure-portalen

Det finns två sätt att skapa en elastisk pool i Azure-portalen.

1. Gå till [Azure-portalen](https://portal.azure.com) för att skapa en elastisk pool. Sök efter och välj **Azure SQL**.
2. Välj **+Lägg till** om du vill öppna **alternativsidan Välj SQL-distribution.** Du kan visa ytterligare information om elastiska pooler genom att välja **Visa information** på panelen **Databaser.**
3. På panelen **Databaser** väljer du **Elastisk pool** i listrutan **Resurstyp** och väljer sedan **Skapa:**

   ![Skapa en elastisk pool](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. Du kan också skapa en elastisk pool genom att navigera till en befintlig Azure SQL-server och klicka på **+ Ny pool** för att skapa en pool direkt till den servern.

> [!NOTE]
> Du kan skapa flera pooler på en server, men du kan inte lägga till databaser från olika servrar i samma pool.

Poolens tjänstnivå bestämmer vilka funktioner som är tillgängliga för resåren i poolen och den maximala mängden resurser som är tillgängliga för varje databas. Mer information finns i Resursgränser för elastiska pooler i [DTU-modellen](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). För vCore-baserade resursgränser för elastiska pooler finns i [vCore-baserade resursgränser - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

Om du vill konfigurera poolens resurser och priser klickar du på **Konfigurera pool**. Välj sedan en tjänstnivå, lägg till databaser i poolen och konfigurera resursgränserna för poolen och dess databaser.

När du har konfigurerat poolen kan du klicka på Använd, namnge poolen och klicka på OK för att skapa poolen.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Övervaka en elastisk pool och dess databaser

I Azure-portalen kan du övervaka användningen av en elastisk pool och databaserna i poolen. Du kan också göra en uppsättning ändringar i den elastiska poolen och skicka alla ändringar samtidigt. Dessa ändringar inkluderar att lägga till eller ta bort databaser, ändra inställningarna för elastiska pooler eller ändra databasinställningarna.

För att börja övervaka din elastiska pool, hitta och öppna en elastisk pool i portalen. Du ser först en skärm som ger dig en översikt över statusen för din elastiska pool. Det här omfattar:

- Övervakningsdiagram som visar resursanvändningen för den elastiska poolen
- Nya varningar och rekommendationer, om sådana finns tillgängliga, för den elastiska poolen

Följande bild visar ett exempel elastisk pool:

![Poolvy](./media/sql-database-elastic-pool-manage-portal/basic.png)

Om du vill ha mer information om poolen kan du klicka på någon av de tillgängliga informationen i den här översikten. Om du klickar på **resursutnyttjandediagrammet** kommer du till vyn Azure Monitoring där du kan anpassa måtten och tidsfönstret som visas i diagrammet. Om du klickar på tillgängliga meddelanden kommer du till ett blad som visar alla detaljer om den aviseringen eller rekommendationen.

Om du vill övervaka databaserna i poolen kan du klicka på **Databasresursanvändning** i avsnittet **Övervakning** på resursmenyn till vänster.

![Sidan Användning av databasresurser](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Så här anpassar du diagramvisningen

Du kan redigera diagrammet och måttsidan för att visa andra mått, till exempel CPU-procent, data-IO-procent och log IO-procent som används.

I formuläret **Redigera diagram** kan du välja ett fast tidsintervall eller klicka **på anpassat** för att välja ett 24-timmarsfönster under de senaste två veckorna och sedan välja de resurser som ska övervakas.

### <a name="to-select-databases-to-monitor"></a>Så här väljer du databaser som ska övervakas

Som standard visar diagrammet i bladet **Databasresursanvändning** de översta 5 databaserna efter DTU eller CPU (beroende på tjänstnivå). Du kan växla upp databaserna i det här diagrammet genom att markera och avmarkera databaser från listan under diagrammet via kryssrutorna till vänster.

Du kan också välja fler mått som ska visas sida vid sida i den här databastabellen för att få en mer fullständig bild av databasernas prestanda.

Mer information finns [i skapa SQL Database-aviseringar i Azure Portal](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Kundfallstudier

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart använde elastiska pooler med Azure SQL Database för att snabbt utöka sina företagstjänster med en hastighet av 1 000 nya Azure SQL-databaser per månad.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco använder elastiska pooler med Azure SQL Database för att snabbt etablera och skala tjänster för tusentals klienter i molnet.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   Daxko/CSI använder elastiska pooler med Azure SQL Database för att påskynda utvecklingscykeln och förbättra sina kundtjänster och prestanda.   

## <a name="next-steps"></a>Nästa steg

- Prisinformation finns i [Priserpoolprissättning](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Så här skalar du elastiska pooler, se [Skala elastiska pooler](sql-database-elastic-pool-scale.md) och [Skala en elastisk pool - exempelkod](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- En SaaS-självstudie med elastiska pooler finns i [Introduktion till Wingtip SaaS-programmet](sql-database-wtp-overview.md).
- Mer information om resurshantering i elastiska pooler med många databaser finns [i Resurshantering i täta elastiska pooler](sql-database-elastic-pool-resource-management.md).

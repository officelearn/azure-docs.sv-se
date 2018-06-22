---
title: Hantera flera SQL-databaser med elastiska pooler Azure | Microsoft Docs
description: Hantera och skala flera databaser i SQL - hundratals och tusentalsavgränsare - med elastiska pooler. Ett pris för resurser som du kan distribuera om det behövs.
keywords: flera databaser, databasresurser, databasprestanda
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/20/2018
ms.author: ninarn
ms.topic: conceptual
ms.openlocfilehash: 5ef32b231a77906a6840ad3550e81b631ddc0c13
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309662"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Hjälper dig att hantera och skala flera Azure SQL-databaser för elastiska pooler

SQL-databas elastiska pooler är en enkel och kostnadseffektiv lösning för att hantera och skala flera databaser som har olika och oförutsägbart krav för användning. Databaserna i en elastisk pool finns på en enda Azure SQL Database-server och dela ett visst antal resurser på ett fast pris. Med elastiska pooler i Azure SQL Database kan SaaS-utvecklare optimera prisprestanda för en grupp med databaser inom en fastställd budget samtidigt som de levererar flexibla prestanda för varje databas.

## <a name="what-are-sql-elastic-pools"></a>Vad är SQL elastiska pooler?

SaaS-utvecklare utvecklar program på storskaliga datanivåer som består av flera databaser. Ett vanligt programmönster är att etablera en enkel databas för varje kund. Men olika kunder har ofta varierande och oförutsägbara användningsmönster, och det är svårt att förutse resursbehoven för varje enskild databasanvändare. Traditionellt har du två alternativ:

- Etablera över resurser baserat på högsta användningsnivå och över lön, eller
- Under etablera spara kostnaden på bekostnad av prestanda och nöjda kunder under toppar.

Elastiska pooler lösa detta problem genom att säkerställa att databaser får prestanda-resurser som de behöver, när de behöver den. De tillhandahåller en enkel resursallokeringsmekanism med en förutsägbar budget. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Elastiska pooler kan utvecklare att köpa resurser för en pool som delas av flera databaser för oväntade perioder med användning av enskilda databaser. Du kan konfigurera resurser för poolen baserat antingen på den [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md). Resurskrav för en pool bestäms av den sammanlagda användningen av databaserna. Mängden resurser som är tillgängliga för poolen styrs av utvecklare budget. Utvecklaren bara lägger till databaserna i poolen, anger de lägsta och högsta resurserna för databaserna (lägsta och högsta dtu: er eller lägsta eller högsta vCores beroende på ditt val av resourcing modellen), och anger resurserna från poolen baserat på sina budget. Med hjälp av pooler kan utvecklare sömlöst expandera sina tjänster från en idé till en mogen affärsverksamhet som bara fortsätter att växa.

I poolen kan de enskilda databaserna skalas automatiskt inom fastställda parametrar. Hårt belastad, kan en databas använda mer resurser för att uppfylla begäran. Databaser under lätta belastningar förbrukar mindre och databaser under inga belastningen använda några resurser. Genom att etablera resurser för hela poolen i stället för enskilda databaser kan du förenkla dina hanteringsuppgifter. Dessutom har en förutsägbar budget för poolen. Ytterligare resurser kan läggas till en befintlig adresspool utan avbrott i databasen, förutom att databaserna kan behöva flyttas att tillhandahålla ytterligare beräkningsresurser för ny eDTU-reservation. På liknande sätt, om extra resurser inte längre behövs de kan tas bort från en befintlig adresspool när som helst i tid. Och du kan lägga till eller ta bort databaser i poolen. Om du vet att en databas underförbrukar resurser tar du bort den.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>När bör du överväga en SQL Database-elastisk pool?

Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av låg genomsnittlig användning med relativt ovanliga användningstoppar.

Ju fler databaser du kan lägga till i en pool desto större blir dina besparingar. Beroende på ditt programanvändningsmönster kan du få besparingar med så lite som två S3-databaser.

Följande avsnitt hjälper dig att förstå hur du avgör om en specifik samling databaser kan ha nytta av att tillhöra en pool. I exemplen används Standard-pooler, men samma principer gäller även för Basic- och Premium-pooler.

### <a name="assessing-database-utilization-patterns"></a>Utvärdera databasanvändningsmönster

Följande bild visar ett exempel på en databas med mycket inaktiv tid, men även med regelbundna aktivitetstoppar. Det här är ett användningsmönster som passar för en pool:

   ![en enkel databas som är lämplig för en pool](./media/sql-database-elastic-pool/one-database.png)

Under femminutersperioden som visas har DB1 toppar med 90 DTU:er, men en genomsnittlig användning på mindre än 5 DTU:er. En S3-prestandanivå krävs för att köra den här arbetsbelastningen i en enkel databas, men det innebär att de flesta av resurserna inte används under perioder med låg aktivitet.

En pool gör att dessa oanvända DTU:er kan delas av flera databaser och minskar således de DTU:er som krävs och den sammanlagda kostnaden.

Vi ska bygga vidare på föregående exempel och antar att det finns ytterligare databaser med liknande användningsmönster som DB1. I de två figurerna nedan användning av fyra databaser och 20 databaser lager till samma diagram som illustrerar den icke-överlappande strukturen på deras belastning över tid med DTU-baserade inköpsmodell:

   ![4 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool/four-databases.png)

   ![20 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool/twenty-databases.png)

Den sammanlagda DTU-användningen över alla 20 databaser illustreras av den svarta linjen i föregående bild. Detta visar att den sammanlagda DTU-användningen aldrig överskrider 100 DTU:er och indikerar att 20 databaser kan dela 100 eDTU:er under den här tidsperioden. Detta resulterar i 20 gånger färre DTU:er och 13 gånger lägre pris jämfört med om varje databas läggs till i S3-prestandanivåer för enskilda databaser.

Det här exemplet är idealisk av följande anledningar:

* Skillnaderna mellan användningen vid hög aktivitet och den genomsnittliga användningen per databas är stora.
* Belastningstopparna för varje databas inträffar vid olika tidpunkter.
* eDTU:er som delas mellan flera databaser.

Priset för en pool är associerat med poolens eDTU:er. Även om eDTU-enhetspriset för en pool är 1,5 gånger större än DTU-enhetspriset för en enkel databas, så kan **pool-DTU:erna delas av många databaser och det behövs färre eDTU:er**. Dessa skillnader i pris och eDTU-delning utgör grunden för de prisbesparingar som pooler kan medföra.

Genom att följa nedanstående tumregler för antalet databaser och databasanvändning kan du vara säker på att en pool ger mindre kostnader jämfört med användningen av prestandanivåer för enskilda databaser.

### <a name="minimum-number-of-databases"></a>Minsta antal databaser

Om summan av resurser för enskilda databaser är mer än 1,5 x de resurser som krävs för poolen, är det mer kostnadseffektivt med en elastisk pool.

***DTU-baserade köp modell-exempel***<br>
Minst två S3-databaser eller minst 15 S0-databaser behövs för att en pool med 100 eDTU:er ska vara mer kostnadseffektivt än användningen av prestandanivåer för enskilda databaser.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Högsta antal samtidigt databaser med aktivitetstoppar

Genom att dela resurser, kan inte alla databaser i poolen samtidigt använda resurser upp till gränsen som är tillgängliga för enskilda databaser. Färre databaserna som samtidigt högsta, Ju lägre pool-resurser kan ställas in och det mer kostnadseffektivt poolen blir. I allmänhet bör inte mer än 2/3 (eller 67%) för databaserna i poolen samtidigt högsta att begränsa deras resurser.

***DTU-baserade köp modell-exempel***<br>
För att minska kostnaderna för tre S3-databaser i en pool med 200 eDTU:er kan högst två av dessa databaser ha belastningstoppar samtidigt. Annars, om fler än två av dessa fyra S3-databaser har toppar samtidigt, skulle poolen behöva utökas till mer än 200 eDTU:er. Om poolen utökas till mer än 200 eDTU:er skulle fler S3-databaser behöva läggas till i poolen för att kostnaderna ska vara lägre än med prestandanivåer för enskilda databaser.

Observera att det här exemplet inte tar hänsyn till användningen av andra databaser i poolen. Om alla databaser har viss belastning vid en given tidpunkt kan mindre än 2/3 (eller 67 %) av databaserna ha aktivitetstoppar samtidigt.

### <a name="resource-utilization-per-database"></a>Resursutnyttjande per databas
En stor skillnad mellan topp- och genomsnittsanvändningen av en databas indikerar långa perioder med låg belastning och korta perioder med hög användning. Det här användningsmönstret är idealisk för delning av resurser mellan databaser. Du bör överväga att lägga till en databas i en pool om dess högsta användning är runt 1,5 gånger större än dess genomsnittliga användning.

***DTU-baserade köp modell-exempel***<br>
En S3-databas som behöver 100 DTU:er vid hög aktivitet och som har en genomsnittlig användning på 67 DTU:er eller mindre är lämplig för delning av eDTU:er i en pool. På motsvarande sätt är en S1-databas som använder 20 DTU:er vid hög belastning och som har en genomsnittsanvändning på 13 DTU:er inte lämplig för en pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hur väljer rätt poolstorleken?

Den rekommenderade storleken för en pool beror på de sammanställda resurser som krävs för alla databaser i poolen. Detta omfattar att fastställa följande:

* Maximal resurser som används av alla databaser i poolen (högsta dtu: er eller maximal vCores beroende på ditt val av resourcing modell).
* Högsta lagringsutrymme i byte som används av alla databaser i poolen.

Tillgängliga nivåer för varje resursmodell, finns det [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md).

SQL Database utvärderar automatiskt den historiska resursanvändningen för databaser på en befintlig SQL Database-server och rekommenderar lämplig poolkonfiguration på Azure Portal. Förutom rekommendationerna finns det en inbyggd beräkning som uppskattar eDTU-användningen för en anpassad grupp databaser på servern. Detta gör att du kan utföra en konsekvensanalys genom att interaktivt lägga till databaser i poolen och sedan ta bort dem för att visa en analys över resursanvändningen och storleksrekommendationer innan du genomför ändringarna. Mer information finns i [Monitor, manage, and size an elastic pool](#monitor-an-elastic-pool-and-its-databases) (Övervaka, hantera och ändra storlek på en elastisk pool).

I de fall då du inte kan använda verktygsuppsättningar kan du följa stegen nedan för att ta reda på om en pool är ett mer kostnadseffektivt alternativ än enskilda databaser:

1. Beräkna edtu: er eller vCores som behövs för poolen på följande sätt:

   För DTU-baserade inköpsmodell: MAX (<*Totalt antal DBs* X *genomsnittlig DTU-användning per DB*>,<br>
   <*Antal databaser som har aktivitetstoppar samtidigt* × *DTU-toppbelastning per databas*)

   För vCore-baserade inköpsmodell (förhandsversion): MAX (<*Totalt antal DBs* X *genomsnittlig vCore användning per DB*>,<br>
   <*Antal samtidigt peaking DBs* X *belastning vCore användning per DB*)

2. Beräkna hur stort lagringsutrymme som krävs för poolen genom att lägga till antalet byte som behövs för alla databaser i poolen. Fastställ sedan den eDTU-poolstorlek som ger den här mängden lagringsutrymme.
3. DTU-baserade inköpsmodell ta för större eDTU uppskattningar från steg 1 och 2. Ta vCore uppskattning från steg 1 för vCore-baserade köp modellen (förhandsversion).
4. Finns det [SQL-databas sida med priser](https://azure.microsoft.com/pricing/details/sql-database/) och hitta poolen minsta storlek som är större än uppskattningen från steg3.
5. Jämför poolpriset i steg 5 med priset för att använda lämpliga prestandanivåer för enskilda databaser.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Använda andra funktioner i SQL-databas med elastiska pooler

### <a name="elastic-jobs-and-elastic-pools"></a>Elastiska jobb och elastiska pooler

Med en pool förenklas hanteringsuppgifterna eftersom du kan köra skript i **[elastiska jobb](sql-database-elastic-jobs-overview.md)**. Ett elastiskt jobb underlättar hanteringen av stora antal databaser. För att komma igång kan du se [Kom igång med elastiska jobb](sql-database-elastic-jobs-getting-started.md).

Mer information om andra databasverktyg för att jobba med flera databaser, finns i [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Alternativ för verksamhetskontinuitet för databaser i en elastisk pool
Databaser i pool stöder generellt sett samma [funktioner för affärskontinuitet](sql-database-business-continuity.md) som finns tillgängliga för enskilda databaser.

- **Point-in-time-återställning**: Point-in-time-återställning använder automatiska säkerhetskopieringar för att återställa en databas i en pool till en specifik tidpunkt. Mer information finns i avsnittet om [återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore)

- **GEO-återställning**: Geo-återställning ger standardalternativet när en databas är inte tillgänglig på grund av en incident i den region där databasen finns. Mer information finns i avsnittet [Restore an Azure SQL Database or failover to a secondary](sql-database-disaster-recovery.md) (Återställa en Azure SQL-databas eller växla över till en sekundär databas)

- **Aktiv geo-replikering**: konfigurera för program som har mer aggressivt recovery krav än geo-återställning kan erbjuda [aktiv geo-replikering](sql-database-geo-replication-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Skapa en ny SQL Database-elastisk pool med Azure-portalen

Det finns två sätt att skapa en elastisk pool i Azure-portalen.
1. Du kan skapa en elastisk pool genom att söka **elastiska SQL-poolen** i den **Marketplace** eller klicka på **+ Lägg till** på SQL elastiska pooler bläddrar du bladet. Du kan ange en ny eller befintlig server via den här poolen etablering av arbetsflöde.
2. Du kan också skapa en elastisk pool genom att gå till en befintlig SQLServer och klicka **skapa poolen** att skapa en pool direkt till servern. Den enda skillnaden är att du hoppa över steg där du anger servern under poolen etablering av arbetsflöde.

> [!NOTE]
> Du kan skapa flera pooler på en server, men du kan inte lägga till databaser från olika servrar i samma pool.

Den pooltjänstnivå avgör vilka funktioner som är tillgängliga för elastics i poolen och maximala mängden resurser som är tillgängliga för varje databas. Mer information finns i gränserna för elastiska pooler i den [DTU modellen](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels). VCore-baserade gränserna för elastiska pooler, se [vCore-baserade gränserna - elastiska pooler](sql-database-vcore-resource-limits-elastic-pools.md).

Konfigurera resurserna och prisnivå för poolen, klickar du på **konfigurera pool**. Välj en tjänstnivå, lägga till databaser i poolen och konfigurera resurs-gränser för poolen och databaserna.

När du har slutfört konfigurationen poolen, kan du på Verkställ, namn i poolen, och klicka på 'OK' om du vill skapa poolen.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Övervaka en elastisk pool och databaserna

Du kan övervaka användningen av en elastisk pool och databaserna i poolen i Azure-portalen. Du kan också göra en uppsättning ändringar i den elastiska poolen och skicka alla ändringar på samma gång. De här förändringarna innefattar att lägga till eller ta bort databaser, ändra inställningarna elastisk pool eller ändra databasinställningarna.

Om du vill börja övervaka din elastisk pool, hitta och öppna en elastisk pool i portalen. En skärm som ger en översikt över statusen för den elastiska poolen visas först. Det här omfattar:

* Övervakning av diagram som visar resurser användning av den elastiska poolen
* Senaste aviseringarna och rekommendationer om de är tillgängliga för den elastiska poolen

Följande bild visar ett exempel elastisk pool:

![Pool-vy](./media/sql-database-elastic-pool-manage-portal/basic.png)

Om du vill ha mer information om poolen kan du klicka på någon av informationen i den här översikten. Klicka på den **resursutnyttjande** diagrammet tar dig till den Azure-övervakning vyn där du kan anpassa fönstret mått och tid som visas i diagrammet. Klicka på alla tillgängliga meddelanden att ta dig till ett blad som visar fullständig information om aviseringen eller rekommendation.

Om du vill övervaka databaser i din pool, kan du klicka på **databasen resursutnyttjande** i den **övervakning** resurs-menyn till vänster.

![Sidan databas resurs för användning](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Du kan anpassa diagram

Du kan redigera diagrammet och sidan mått om du vill visa andra mått som CPU-procent, data IO-procent och loggen IO-procent som används.

På den **redigera diagram** formuläret som du kan välja en fast tidpunkt intervall eller klicka på **anpassade** att välja valfritt 24-timmarsformat fönster under de senaste två veckorna och välj sedan resurserna som ska övervaka.

### <a name="to-select-databases-to-monitor"></a>Att välja databaser för att övervaka

Som standard i diagrammet i den **databasen resursutnyttjande** bladet visar 5 viktigaste databaser genom DTU eller processor (beroende på din tjänstnivån). Du kan växla av databaserna i det här diagrammet genom att välja och unselecting databaser i listan under diagrammet via kryssrutorna till vänster.

Du kan också välja fler mått till vyn sida vid sida i den här databastabell för att få en mer komplett vy av dina databaser prestanda.

Mer information finns i [skapa SQL-databas aviseringar i Azure-portalen](sql-database-insights-alerts-portal.md).

## <a name="next-steps"></a>Nästa steg

- Om du vill skala elastiska pooler finns [skalning elastiska pooler](sql-database-elastic-pool.md) och [skala en elastisk pool - exempelkod](scripts/sql-database-monitor-and-scale-pool-powershell.md)
* Se en video [Microsoft Virtual Academy video kursen på Azure SQL Database-elastisk funktioner](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Om du har en SaaS-självstudiekurs med elastiska pooler finns [introduktion till Wingtip SaaS-program](sql-database-wtp-overview.md).

---
title: Hantera flera SQL-databaser med elastiska pooler Azure | Microsoft Docs
description: "Hantera och skala flera databaser i SQL - hundratals och tusentalsavgränsare - med elastiska pooler. Ett pris för resurser som du kan distribuera om det behövs."
keywords: flera databaser, databasresurser, databasprestanda
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.date: 03/02/2018
ms.author: carlrab
ms.workload: Active
ms.topic: article
ms.openlocfilehash: e89d348c4b21a249401254c64e3887ec36283022
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Hjälper dig att hantera och skala flera Azure SQL-databaser för elastiska pooler

SQL-databas elastiska pooler är en enkel och kostnadseffektiv lösning för att hantera och skala flera databaser som har olika och oförutsägbart krav för användning. Databaserna i en elastisk pool finns på en enda Azure SQL Database-server och delar ett visst antal resurser ([elastiska Database Transaction Units](sql-database-what-is-a-dtu.md) (edtu: er)) till ett angivet pris. Med elastiska pooler i Azure SQL Database kan SaaS-utvecklare optimera prisprestanda för en grupp med databaser inom en fastställd budget samtidigt som de levererar flexibla prestanda för varje databas. 

## <a name="what-are-sql-elastic-pools"></a>Vad är SQL elastiska pooler? 

SaaS-utvecklare utvecklar program på storskaliga datanivåer som består av flera databaser. Ett vanligt programmönster är att etablera en enkel databas för varje kund. Men olika kunder har ofta varierande och oförutsägbara användningsmönster, och det är svårt att förutse resursbehoven för varje enskild databasanvändare. Traditionellt har du två alternativ: 

- Etablera över resurser baserat på högsta användningsnivå och över lön, eller
- Under etablera spara kostnaden på bekostnad av prestanda och nöjda kunder under toppar. 

Elastiska pooler lösa detta problem genom att säkerställa att databaser får prestanda-resurser som de behöver, när de behöver den. De tillhandahåller en enkel resursallokeringsmekanism med en förutsägbar budget. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Elastiska pooler aktivera utvecklare att köpa [elastiska Database Transaction Units](sql-database-what-is-a-dtu.md) (edtu: er) för en pool som delas av flera databaser för oväntade perioder med användning av enskilda databaser. eDTU-kravet för en pool baseras på den sammanlagda användningen av dess databaser. Antalet eDTU:er som är tillgängligt för poolen beror på utvecklarens budget. Utvecklaren lägger bara till databaser i poolen, anger det minsta och största antalet eDTU:er för databaserna och väljer sedan poolens eDTU baserat på budget. Med hjälp av pooler kan utvecklare sömlöst expandera sina tjänster från en idé till en mogen affärsverksamhet som bara fortsätter att växa.

I poolen kan de enskilda databaserna skalas automatiskt inom fastställda parametrar. Vid hög belastning kan en databas använda fler eDTU:er för att uppfylla efterfrågan. Databaser med lätt arbetsbelastning förbrukar mindre, och databaser utan belastning förbrukar inga eDTU:er. Genom att etablera resurser för hela poolen i stället för enskilda databaser kan du förenkla dina hanteringsuppgifter. Dessutom har du en förutsägbar budget för poolen. Ytterligare eDTU:er kan läggas till i en befintlig pool utan något avbrott i databasen, förutom att databaserna kan behöva flyttas för att ge ytterligare beräkningsresurser för den nya eDTU-reservationen. På samma sätt kan eDTU:er som inte längre behövs tas bort från en befintlig pool när som helst. Och du kan lägga till eller ta bort databaser i poolen. Om du vet att en databas underförbrukar resurser tar du bort den.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>När bör du överväga en SQL Database-elastisk pool?

Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av låg genomsnittlig användning med relativt ovanliga användningstoppar.

Ju fler databaser du kan lägga till i en pool desto större blir dina besparingar. Beroende på ditt programanvändningsmönster kan du få besparingar med så lite som två S3-databaser. 

Följande avsnitt hjälper dig att förstå hur du avgör om en specifik samling databaser kan ha nytta av att tillhöra en pool. I exemplen används Standard-pooler, men samma principer gäller även för Basic- och Premium-pooler.

### <a name="assessing-database-utilization-patterns"></a>Utvärdera databasanvändningsmönster

Följande bild visar ett exempel på en databas med mycket inaktiv tid, men även med regelbundna aktivitetstoppar. Det här är ett användningsmönster som passar för en pool:

   ![en enkel databas som är lämplig för en pool](./media/sql-database-elastic-pool/one-database.png)

Under femminutersperioden som visas har DB1 toppar med 90 DTU:er, men en genomsnittlig användning på mindre än 5 DTU:er. En S3-prestandanivå krävs för att köra den här arbetsbelastningen i en enkel databas, men det innebär att de flesta av resurserna inte används under perioder med låg aktivitet.

En pool gör att dessa oanvända DTU:er kan delas av flera databaser och minskar således de DTU:er som krävs och den sammanlagda kostnaden.

Vi ska bygga vidare på föregående exempel och antar att det finns ytterligare databaser med liknande användningsmönster som DB1. De två bilderna nedan illustrerar användningen av 4 databaser och 20 databaser i samma diagram för att visa hur deras användning inte överlappar varandra över tid:

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

Om summan av DTU:er för prestandanivåer för enskilda databaser är mindre än 1,5 gånger antalet eDTU:er som behövs för en pool, så är en elastisk pool ett mer kostnadseffektivt alternativ. Information om tillgängliga storlekar finns i avsnittet om [eDTU:er och lagringsgränser för elastiska pooler och elastiska databaser](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

***Exempel***<br>
Minst två S3-databaser eller minst 15 S0-databaser behövs för att en pool med 100 eDTU:er ska vara mer kostnadseffektivt än användningen av prestandanivåer för enskilda databaser.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Högsta antal samtidigt databaser med aktivitetstoppar

När eDTU:er delas kan inte alla databaser i en pool samtidigt använda eDTU:er upp till den tillgängliga gränsen om prestandanivåer för enskilda databaser används. Ju färre databaser som har hög aktivitet samtidigt, desto lägre pool-eDTU kan anges och desto mer kostnadseffektiv blir poolen. I allmänhet bör inte mer än 2/3 (eller 67 %) av databaserna i poolen samtidigt ha hög aktivitet upp till deras eDTU-gräns.

***Exempel***<br>
För att minska kostnaderna för tre S3-databaser i en pool med 200 eDTU:er kan högst två av dessa databaser ha belastningstoppar samtidigt. Annars, om fler än två av dessa fyra S3-databaser har toppar samtidigt, skulle poolen behöva utökas till mer än 200 eDTU:er. Om poolen utökas till mer än 200 eDTU:er skulle fler S3-databaser behöva läggas till i poolen för att kostnaderna ska vara lägre än med prestandanivåer för enskilda databaser.

Observera att det här exemplet inte tar hänsyn till användningen av andra databaser i poolen. Om alla databaser har viss belastning vid en given tidpunkt kan mindre än 2/3 (eller 67 %) av databaserna ha aktivitetstoppar samtidigt.

### <a name="dtu-utilization-per-database"></a>DTU-användning per databas
En stor skillnad mellan topp- och genomsnittsanvändningen av en databas indikerar långa perioder med låg belastning och korta perioder med hög användning. Det här användningsmönstret är idealisk för delning av resurser mellan databaser. Du bör överväga att lägga till en databas i en pool om dess högsta användning är runt 1,5 gånger större än dess genomsnittliga användning.

***Exempel***<br>
En S3-databas som behöver 100 DTU:er vid hög aktivitet och som har en genomsnittlig användning på 67 DTU:er eller mindre är lämplig för delning av eDTU:er i en pool. På motsvarande sätt är en S1-databas som använder 20 DTU:er vid hög belastning och som har en genomsnittsanvändning på 13 DTU:er inte lämplig för en pool.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hur väljer rätt poolstorleken?

Den bästa storleken för en pool beror på det totala antalet eDTU:er och lagringsresurser som behövs för alla databaser i poolen. Det betyder att du måste fastställa det större av följande:

* Högsta antal DTU:er som används av alla databaser i poolen.
* Högsta lagringsutrymme i byte som används av alla databaser i poolen.

Information om tillgängliga storlekar finns i avsnittet om [eDTU:er och lagringsgränser för elastiska pooler och elastiska databaser](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

SQL Database utvärderar automatiskt den historiska resursanvändningen för databaser på en befintlig SQL Database-server och rekommenderar lämplig poolkonfiguration på Azure Portal. Förutom rekommendationerna finns det en inbyggd beräkning som uppskattar eDTU-användningen för en anpassad grupp databaser på servern. Detta gör att du kan utföra en konsekvensanalys genom att interaktivt lägga till databaser i poolen och sedan ta bort dem för att visa en analys över resursanvändningen och storleksrekommendationer innan du genomför ändringarna. Mer information finns i [Monitor, manage, and size an elastic pool](sql-database-elastic-pool-manage-portal.md) (Övervaka, hantera och ändra storlek på en elastisk pool).

I de fall då du inte kan använda verktygsuppsättningar kan du följa stegen nedan för att ta reda på om en pool är ett mer kostnadseffektivt alternativ än enskilda databaser:

1. Beräkna hur många eDTU:er som behövs för poolen så här:

   MAX(<*totalt antal databaser* × *genomsnittlig DTU-användning per databas*>,<br>
   <*Antal databaser som har aktivitetstoppar samtidigt* × *DTU-toppbelastning per databas*)
2. Beräkna hur stort lagringsutrymme som krävs för poolen genom att lägga till antalet byte som behövs för alla databaser i poolen. Fastställ sedan den eDTU-poolstorlek som ger den här mängden lagringsutrymme. Information om gränser för poollagring baserat på eDTU-poolstorlek finns i [eDTU and storage limits for elastic database pools and elastic databases](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels) (eDTU:er och lagringsgränser för elastiska pooler och elastiska databaser).
3. Använd den större av eDTU-beräkningarna från steg 1 och steg 2.
4. Gå till [sidan med SQL Database-priser](https://azure.microsoft.com/pricing/details/sql-database/) och leta upp den minsta eDTU-poolstorleken som är större än beräkningen från steg 3.
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

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Hantera elastiska pooler och databaser med hjälp av Azure portal

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Skapa en ny SQL Database-elastisk pool med Azure-portalen

Det finns två sätt att skapa en elastisk pool i Azure-portalen. Du kan göra det från grunden om du vet vilken typ av pool du vill ha, eller så kan du börja med en rekommendation från tjänsten. SQL Database har inbyggd intelligens som rekommenderar en elastisk pool-installationen om det är mer kostnadseffektivt baserat på den senaste användningstelemetrin för dina databaser. 

Att skapa en elastisk pool från en befintlig serversida i portalen är det enklaste sättet att flytta befintliga databaser till en elastisk pool. Du kan också skapa en elastisk pool genom att söka **elastiska SQL-poolen** i den **Marketplace** eller klicka på **+ Lägg till** på sidan SQL elastiska pooler. Du kan ange en ny eller befintlig server via den här poolen etablering av arbetsflöde.

> [!NOTE]
> Du kan skapa flera pooler på en server, men du kan inte lägga till databaser från olika servrar i samma pool.
> 

Prisnivån för poolen avgör vilka funktioner som är tillgängliga för elastics i poolen och maximala antalet edtu: er (eDTU MAX) och lagringsutrymme (GB) är tillgängliga för varje databas. Mer information finns i [resurs begränsar för elastiska pooler](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Om du vill ändra prisnivå för poolen klickar du på **Prisnivå**, klickar på önskad prisnivå och sedan på **Välj**.

> [!IMPORTANT]
> När du valt prisnivå och bekräftat ditt val genom att klicka på **OK** i det sista steget, kommer du inte kunna ändra prisnivå för poolen igen. Om du vill ändra prisnivå för en befintlig elastisk pool, skapa en elastisk pool i den önskade prisnivån och migrera databaserna till den nya poolen.
>

Om de databaser du arbetar med har tillräckligt med historisk användningstelemetri, kommer stapeldiagrammen **Beräknad eDTU- och GB-användning** och **Faktisk eDTU-användning** att uppdateras för att hjälpa dig att fatta konfigurationsbeslut. Tjänsten kan också ge dig ett rekommendationsmeddelande för att hjälpa dig få rätt storlek på poolen.

SQL Database-tjänsten utvärderar användningshistorik och rekommenderar en eller flera pooler när det är mer kostnadseffektivt än att använda enskilda databaser. Varje rekommendation är konfigurerad med en unik delmängd av serverns databaser som bäst passar i poolen.

![rekommenderad pool](./media/sql-database-elastic-pool-create-portal/recommended-pool.png) 

Pool-rekommendationerna omfattar:

- En prisnivå för poolen (Basic, Standard eller Premium)
- Lämpliga **POOL-eDTU:er** (kallas även Max eDTU:er per pool)
- **eDTU MAX** och **eDTU Min** per databas
- Listan över rekommenderade databaser för poolen

> [!IMPORTANT]
> Tjänsten tar hänsyn till de senaste 30 dagarnas telemetri vid rekommendation av pooler. Det måste finnas minst 7 dagar för en databas ska anses som en kandidat för en elastisk pool. Databaser som redan finns i en elastisk pool är inte rekommenderade kandidater för elastiska pooler.
>

Tjänsten utvärderar resursbehov och kostnadseffektivitet vid flytt av de enskilda databaserna i varje tjänstnivå till pooler inom samma nivå. Alla standard-databaser på servern utvärderas exempelvis för hur de skulle passa in i en standard elastisk pool. Det innebär att tjänsten inte göra rekommendationer mellan olika nivåer, som att flytta en standard-databas till en premium-pool.

När du lägger till databaserna i poolen, genereras dynamiskt rekommendationer baserat på historisk användning av databaserna som du har valt. De här rekommendationerna som visas i eDTU och GB Användningsdiagram och i en rekommendations-banderoll överst i den **konfigurera pool** sidan. De här rekommendationerna är avsedda att hjälpa dig att skapa en elastisk pool som är optimerade för dina specifika databaser.

![dynamiska rekommendationer](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

### <a name="manage-and-monitor-an-elastic-pool"></a>Hantera och övervaka en elastisk pool

Du kan övervaka användningen av en elastisk pool och databaserna i poolen i Azure-portalen. Du kan också göra en uppsättning ändringar i den elastiska poolen och skicka alla ändringar på samma gång. De här förändringarna innefattar att lägga till eller ta bort databaser, ändra inställningarna elastisk pool eller ändra databasinställningarna.

Följande bild visar ett exempel elastisk pool. Vyn innehåller:

* Diagram för övervakning av resursanvändningen för både den elastiska poolen och databaser som ingår i poolen.
* Den **konfigurera** pool för att göra ändringar i den elastiska poolen.
* Den **Skapa databas** knapp som skapar en databas och lägger till den aktuella elastisk pool.
* Elastiska jobb som hjälper dig hantera stort antal databaser genom att köra Transact-SQL-skript mot alla databaser i en lista.

![Pool-vy](./media/sql-database-elastic-pool-manage-portal/basic.png)

Du kan gå till en viss pool för att se dess resursutnyttjande. Som standard konfigureras poolen om du vill visa lagrings- och eDTU-användning för den senaste timmen. Diagrammet kan konfigureras för att visa olika mätvärden via olika tidsfönster. Klicka på den **resursutnyttjande** diagram **elastisk pool övervakning** att visa en detaljerad vy av de angivna mätvärdena via den angivna tidsperioden.

![Övervakning av elastiska pooler](./media/sql-database-elastic-pool-manage-portal/basic-2.png)

![Mått sida](./media/sql-database-elastic-pool-manage-portal/metric.png)

### <a name="to-customize-the-chart-display"></a>Du kan anpassa diagram

Du kan redigera diagrammet och sidan mått om du vill visa andra mått som CPU-procent, data IO-procent och loggen IO-procent som används.

![Klicka på Redigera](./media/sql-database-elastic-pool-manage-portal/edit-metric.png)

På den **redigera diagram** formuläret, du kan välja ett tidsintervall (efter timme, dag, eller föregående vecka), eller klicka på **anpassade** att välja alla datumintervall under de senaste två veckorna. Du kan välja mellan ett fält eller ett linjediagram och välj sedan resurserna som ska övervaka.

> [!Note]
> Endast mått med samma enheten kan visas i diagrammet på samma gång. Till exempel om du väljer ”eDTU i procent” kan du bara välja andra mått med procentandel som enheten.
>

![Klicka på Redigera](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

### <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>Hantera och övervaka databaser i en elastisk pool

Enskilda databaser kan också övervakas för potentiella problem. Under **elastisk databas övervakning**, det finns ett diagram som visar mått för fem databaser. Som standard i diagrammet visas de översta 5 databaserna i poolen efter genomsnittlig eDTU-användning under den senaste timmen. 

![Övervakning av elastiska pooler](./media/sql-database-elastic-pool-manage-portal/basic-3.png)

Klicka på den **eDTU-användning för databaser för den senaste timmen** under **elastisk databas övervakning**. Då öppnas **databasen resursutnyttjande** och ger en detaljerad vy av databasanvändningen i poolen. Med rutnätet i den nedre delen av sidan kan välja du alla databaser i poolen för att visa dess användning i diagrammet (upp till 5 databaser). Du kan också anpassa fönstret mått och tid som visas i diagrammet genom att klicka på **redigera diagram**.

![Sidan databas resurs för användning](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-view"></a>Anpassa vyn

Du kan redigera diagram om du vill markera ett tidsintervall (efter timme eller senaste 24 timmarna) eller klicka på **anpassade** att välja en annan dag under de senaste 2 veckorna ska visas.

![Klicka på Redigera diagram](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

![Klicka på anpassad](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

Du kan också klicka på **databaser genom att jämföra** listrutan och välj ett annat mått som ska användas vid jämförelse av databaser.

![Redigera schemat](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Att välja databaser för att övervaka

I databaslistan på den **databasen resursutnyttjande** sidan du hittar särskilda databaser genom att titta igenom sidorna i listan eller genom att skriva namnet på en databas. Använd kryssrutan för att välja databasen.

![Sök efter databaser för att övervaka](./media/sql-database-elastic-pool-manage-portal/select-dbs.png)


### <a name="add-an-alert-to-an-elastic-pool-resource"></a>Lägga till en avisering till en elastisk pool-resurs

Du kan lägga till regler för en elastisk pool som skickar e-post till personer eller avisering strängar till URL: en slutpunkter när den elastiska poolen träffar ett tröskelvärde för användning som du skapat.

**Lägga till en avisering till en resurs:**

1. Klicka på den **resursutnyttjande** diagrammet för att öppna den **mått** klickar du på **Lägg till avisering**, och fyller sedan informationen i den **lägga till en varningsregel** sida (**resurs** har angetts automatiskt upp till att du arbetar med poolen).
2. Ange en **namn** och **beskrivning** som identifierar aviseringen du och mottagare.
3. Välj en **mått** som du vill Varna från listan.

   Diagrammet visar dynamiskt resursanvändningen för den måtten för att välja ett tröskelvärde.

4. Välj en **villkoret** (större än mindre än osv) och en **tröskelvärdet**.
5. Välj en **Period** som mått regeln måste uppfyllas innan aviseringen utlösare.
6. Klicka på **OK**.

Mer information finns i [skapa SQL-databas aviseringar i Azure-portalen](sql-database-insights-alerts-portal.md).

### <a name="move-a-database-into-an-elastic-pool"></a>Flytta en databas till en elastisk pool

Du kan lägga till eller ta bort databaser från en befintlig adresspool. Databaserna kan vara i andra pooler. Du kan bara lägga till databaser som finns på samma logiska server.

 ![Klicka på Konfigurera pool](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

![Klicka på Lägg till pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

![Välj databaser som ska läggas till](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

![Väntande pool-tillägg](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

![Klicka på Spara](./media/sql-database-elastic-pool-manage-portal/click-save.png)

### <a name="move-a-database-out-of-an-elastic-pool"></a>Flytta en databas från en elastisk pool

![Visar en lista över databaser](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

### <a name="change-performance-settings-of-an-elastic-pool"></a>Ändra inställningar för prestanda för en elastisk pool

När du övervakar resursanvändningen för en elastisk pool kan du märka att några justeringar behövs. Poolen måste kanske en ändring i gränser prestanda eller lagring. Kanske vill du ändra databasinställningarna i poolen. Du kan ändra inställningarna för poolen när som helst för att hämta den bästa balansen mellan prestanda och kostnader. Se [när en elastisk pool kan användas?](sql-database-elastic-pool.md) för mer information.

Begränsar per pool och edtu: er per databas om du vill ändra edtu: er eller lagring:

![Resursutnyttjande elastisk pool](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>Hantera elastiska pooler och databaser med hjälp av PowerShell

Använd följande PowerShell-cmdlets för att skapa och hantera SQL-databas elastiska pooler med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). Om du vill skapa och hantera databaser, servrar och brandväggsregler, se [skapa och hantera Azure SQL Database-servrar och databaser med hjälp av PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell). 

> [!TIP]
> PowerShell-exempelskript, se [skapa elastiska pooler och flytta databaser mellan pooler och från en pool med PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) och [Använd PowerShell för att övervaka och skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Skapar en elastisk databaspool på en logisk SQLServer.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Hämtar elastiska pooler och deras värden på en logisk SQLServer.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Ändrar egenskaper för en elastisk databaspool på en logisk SQLServer. Till exempel använda den **StorageMB** egenskapen att ändra maximal lagringskapacitet för en elastisk pool.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Tar bort en elastisk databaspool på en logisk SQLServer.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Hämtar status för åtgärder på en elastisk pool på en logisk SQLServer.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Skapar en ny databas i en befintlig adresspool eller som en enskild databas. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Hämtar en eller flera databaser.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Anger egenskaperna för en databas eller flyttar en befintlig databas i, slut på eller mellan elastiska pooler.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Tar bort en databas.|


> [!TIP]
> Skapandet av många databaser i en elastisk pool kan ta tid när det görs med portalen eller PowerShell-cmdletar som skapar bara en enskild databas åt gången. Om du vill automatisera skapande i en elastisk pool, se [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Hantera elastiska pooler och databaser med hjälp av Azure CLI

Skapa och hantera SQL-databas elastiska pooler med den [Azure CLI](/cli/azure), Använd följande [Azure CLI SQL Database](/cli/azure/sql/db) kommandon. Använd [Cloud Shell](/azure/cloud-shell/overview) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows. 

> [!TIP]
> Azure CLI exempelskript finns [Använd CLI för att flytta en Azure SQL database i en elastisk pool SQL](scripts/sql-database-move-database-between-pools-cli.md) och [Använd Azure CLI för att skala en elastisk SQL-pool i Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[Skapa AZ sql elastisk pool](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Skapar en elastisk pool.|
|[AZ sql elastisk pool lista](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Returnerar en lista över elastiska pooler i en server.|
|[AZ sql elastisk pool lista-databaser](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Returnerar en lista över databaser i en elastisk pool.|
|[AZ sql elastisk pool lista-versioner](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Även tillgängliga poolen DTU inställningar, Lagringsgränser och per databasinställningarna. För att minska detaljnivå ytterligare Lagringsgränser och per databas inställningar döljs som standard.|
|[AZ sql elastisk pool uppdateringen](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Uppdaterar en elastisk pool.|
|[ta bort AZ sql-elastisk pool](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Tar bort den elastiska poolen.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Hantera databaser i elastiska pooler med Transact-SQL

Skapa och flytta databaser i befintliga elastiska pooler eller att returnera information om en SQL Database-elastisk pool med Transact-SQL, Använd följande T-SQL-kommandon. Du kan skicka dessa kommandon med hjälp av Azure portal [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), eller andra program som kan ansluta till en Azure SQL Database-server och skicka Transact-SQL-kommandon. Om du vill skapa och hantera databaser, servrar och brandväggsregler, se [skapa och hantera Azure SQL Database-servrar och databaser med hjälp av Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Du kan inte skapa, uppdatera eller ta bort en Azure SQL Database-elastisk pool med Transact-SQL. Du kan lägga till eller ta bort databaser från en elastisk pool och du kan använda av DMV: er för att returnera information om befintliga elastiska pooler.
>

| Kommando | Beskrivning |
| --- | --- |
|[Skapa databas (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas i en befintlig adresspool eller som en enskild databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Flytta en databas i, slut på eller mellan elastiska pooler.|
|[Ta bort databasen (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Returnerar resurs användningsstatistik för elastiska databaspooler i en logisk server. För varje elastisk databaspool att det finns en rad för varje 15 sekunder reporting fönstret (fyra rader per minut). Detta inkluderar CPU, IO, Log, användningen av lagringsutrymme och samtidiga begäran-session användning av alla databaser i poolen.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar edition (tjänstnivån), tjänstmålet (prisnivån) och namn på elastisk pool, för en Azure SQL-databas eller ett Azure SQL Data Warehouse. Returnerar information om alla databaser om inloggad på master-databasen i en Azure SQL Database-server. För Azure SQL Data Warehouse, måste du vara ansluten till master-databasen.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>Hantera elastiska pooler och databaser med hjälp av REST-API

Om du vill skapa och hantera SQL Database-elastisk använda pooler dessa REST API-begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Elastiska pooler – skapa eller uppdatera](/rest/api/sql/elasticpools/createorupdate)|Skapar en ny elastisk pool eller uppdaterar en befintlig elastisk pool.|
|[Elastiska pooler - ta bort](/rest/api/sql/elasticpools/delete)|Tar bort den elastiska poolen.|
|[Elastiska pooler - Get](/rest/api/sql/elasticpools/get)|Hämtar en elastisk pool.|
|[Elastiska pooler - lista av servern](/rest/api/sql/elasticpools/listbyserver)|Returnerar en lista över elastiska pooler i en server.|
|[Elastiska pooler - uppdatering](/rest/api/sql/elasticpools/update)|Uppdaterar en befintlig elastisk pool.|
|[Rekommenderade elastiska pooler - Get](/rest/api/sql/recommendedelasticpools/get)|Hämtar en recommented elastisk pool.|
|[Rekommenderade elastiska pooler - lista av servern](/rest/api/sql/recommendedelasticpools/listbyserver)|Returnerar rekommenderade elastiska pooler.|
|[Rekommenderade elastiska pooler - listan mått](/rest/api/sql/recommendedelasticpools/listmetrics)|Returnerar recommented elastisk pool mått.|
|[Elastisk Pool aktiviteter](/rest/api/sql/elasticpoolactivities)|Returnerar elastisk pool aktiviteter.|
|[Databasaktiviteter elastisk Pool](/rest/api/sql/elasticpooldatabaseactivities)|Returnerar aktivitet på databaser i en elastisk pool.|
|[Databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Get](/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – få genom elastisk Pool](/rest/api/sql/databases/getbyelasticpool)|Hämtar en databas i en elastisk pool.|
|[Databaser – få genom rekommenderad elastisk Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|Hämtar en databas i en recommented elastisk pool.|
|[Databaser - listan efter elastisk Pool](/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser - listan efter rekommenderad elastisk Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|Returnerar en lista över databaser i en recommented elastisk pool.|
|[Databaser – lista av servern](/rest/api/sql/databases/listbyserver)|Returnerar en lista över databaser i en server.|
|[Databaser - uppdatering](/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|

## <a name="next-steps"></a>Nästa steg

* Se en video [Microsoft Virtual Academy video kursen på Azure SQL Database-elastisk funktioner](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Om du har en SaaS-självstudiekurs med elastiska pooler finns [introduktion till Wingtip SaaS-program](sql-database-wtp-overview.md).

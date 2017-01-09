---
title: "När ska jag använda en elastisk pool?"
description: "En elastisk pool är en samling tillgängliga resurser som delas av en grupp med elastiska databaser. Det här dokumentet innehåller riktlinjer som hjälper dig att avgöra om du bör använda en elastisk pool för en grupp databaser."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3d3941d5-276c-4fd2-9cc1-9fe8b1e4c96c
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 12/19/2016
ms.author: sstein;carlrab
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 6c8420a154d998aa95c0220049ee54b3039a872b
ms.openlocfilehash: a79b78a4e8e683afe5b41a41911e7d5f020eff88


---
# <a name="when-should-an-elastic-pool-be-used"></a>När ska jag använda en elastisk pool?
Ta reda på om användningen av en elastisk pool är ett kostnadseffektivt alternativ baserat på databasens användningsmönster och prisskillnaden mellan en elastisk pool och fristående databaser. Du får också information som hjälper dig att avgöra vilken poolstorlek som krävs för en befintlig uppsättning SQL-databaser.  

* En översikt över pooler finns i avsnittet om [elastiska SQL Database-pooler](sql-database-elastic-pool.md).

> [!NOTE]
> Elastiska pooler är allmänt tillgängliga (GA) i alla Azure-regioner utom Västra Indien där de genomgår förhandsgranskning. Allmän tillgänglighet för elastiska pooler i den här regionen kommer att erbjudas så snart som möjligt.
>
>

## <a name="elastic-pools"></a>Elastiska pooler
SaaS-utvecklare utvecklar program på storskaliga datanivåer som består av flera databaser. Ett vanligt programmönster är att etablera en enkel databas för varje kund. Men olika kunder har ofta varierande och oförutsägbara användningsmönster, och det är svårt att förutse resursbehoven för varje enskild databasanvändare. Utvecklaren kanske därför överetablerar resurser till en högre kostnad för att säkerställa ett bra dataflöde och korta svarstider för alla databaser. Eller så väljer utvecklaren att spendera mindre och riskera en sämre prestandaupplevelse för kunderna. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Med elastiska pooler i Azure SQL Database kan SaaS-utvecklare optimera prisprestanda för en grupp med databaser inom en fastställd budget samtidigt som de levererar flexibla prestanda för varje databas. Pooler gör att utvecklare kan köpa elastiska databastransaktionsenheter (eDTU:er) för en pool som delas av flera databaser för att därigenom hantera oförutsägbara perioder av användning av enskilda databaser. eDTU-kravet för en pool baseras på den sammanlagda användningen av dess databaser. Antalet eDTU:er som är tillgängligt för poolen beror på utvecklarens budget. Pooler gör det enkelt för utvecklare att avgöra hur budgeten påverkar prestanda och även deras pool. Utvecklaren lägger bara till databaser i poolen, anger det minsta och största antalet eDTU:er för databaserna och väljer sedan poolens eDTU baserat på budget. Med hjälp av pooler kan utvecklare sömlöst expandera sina tjänster från en idé till en mogen affärsverksamhet som bara fortsätter att växa.  

## <a name="when-to-consider-a-pool"></a>När en pool är ett bra alternativ
Pooler lämpar sig för ett stort antal databaser med specifika användningsmönster. För en viss databas kännetecknas det här mönstret av låg genomsnittlig användning med relativt ovanliga användningstoppar.

Ju fler databaser du kan lägga till i en pool desto större blir dina besparingar. Beroende på ditt programanvändningsmönster kan du få besparingar med så lite som två S3-databaser.  

Följande avsnitt hjälper dig att förstå hur du avgör om en specifik samling databaser kan ha nytta av att tillhöra en pool. I exemplen används Standard-pooler, men samma principer gäller även för Basic- och Premium-pooler.

### <a name="assessing-database-utilization-patterns"></a>Utvärdera databasanvändningsmönster
Följande bild visar ett exempel på en databas med mycket inaktiv tid, men även med regelbundna aktivitetstoppar. Det här är ett användningsmönster som passar för en pool:

   ![en enkel databas som är lämplig för en pool](./media/sql-database-elastic-pool-guidance/one-database.png)

Under femminutersperioden som visas har DB1 toppar med 90 DTU:er, men en genomsnittlig användning på mindre än 5 DTU:er. En S3-prestandanivå krävs för att köra den här arbetsbelastningen i en enkel databas, men det innebär att de flesta av resurserna inte används under perioder med låg aktivitet.

En pool gör att dessa oanvända DTU:er kan delas av flera databaser och minskar således de DTU:er som krävs och den sammanlagda kostnaden.

Vi ska bygga vidare på föregående exempel och antar att det finns ytterligare databaser med liknande användningsmönster som DB1. De två bilderna nedan illustrerar användningen av 4 databaser och 20 databaser i samma diagram för att visa hur deras användning inte överlappar varandra över tid:

   ![4 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool-guidance/four-databases.png)

  ![20 databaser med ett användningsmönster som passar för en pool](./media/sql-database-elastic-pool-guidance/twenty-databases.png)

Den sammanlagda DTU-användningen över alla 20 databaser illustreras av den svarta linjen i föregående bild. Detta visar att den sammanlagda DTU-användningen aldrig överskrider 100 DTU:er och indikerar att 20 databaser kan dela 100 eDTU:er under den här tidsperioden. Detta resulterar i 20 gånger färre DTU:er och 13 gånger lägre pris jämfört med om varje databas läggs till i S3-prestandanivåer för enskilda databaser.

Det här exemplet är idealisk av följande anledningar:

* Skillnaderna mellan användningen vid hög aktivitet och den genomsnittliga användningen per databas är stora.  
* Belastningstopparna för varje databas inträffar vid olika tidpunkter.
* eDTU:er som delas mellan flera databaser.

Priset för en pool är associerat med poolens eDTU:er. Även om eDTU-enhetspriset för en pool är 1,5 gånger större än DTU-enhetspriset för en enkel databas, så kan **pool-DTU:erna delas av många databaser och det behövs färre eDTU:er**. Dessa skillnader i pris och eDTU-delning utgör grunden för de prisbesparingar som pooler kan medföra.  

Genom att följa nedanstående tumregler för antalet databaser och databasanvändning kan du vara säker på att en pool ger mindre kostnader jämfört med användningen av prestandanivåer för enskilda databaser.

### <a name="minimum-number-of-databases"></a>Minsta antal databaser
Om summan av DTU:er för prestandanivåer för enskilda databaser är mindre än 1,5 gånger antalet eDTU:er som behövs för en pool, så är en elastisk pool ett mer kostnadseffektivt alternativ. Information om tillgängliga storlekar finns i avsnittet om [eDTU:er och lagringsgränser för elastiska pooler och elastiska databaser](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

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

## <a name="sizing-an-elastic-pool"></a>Ändra storlek på en elastisk pool
Den bästa storleken för en pool beror på det totala antalet eDTU:er och lagringsresurser som behövs för alla databaser i poolen. Det betyder att du måste fastställa det större av följande:

* Högsta antal DTU:er som används av alla databaser i poolen.
* Högsta lagringsutrymme i byte som används av alla databaser i poolen.

Information om tillgängliga storlekar finns i avsnittet om [eDTU:er och lagringsgränser för elastiska pooler och elastiska databaser](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

SQL Database utvärderar automatiskt den historiska resursanvändningen för databaser på en befintlig SQL Database-server och rekommenderar lämplig poolkonfiguration på Azure Portal. Förutom rekommendationerna finns det en inbyggd beräkning som uppskattar eDTU-användningen för en anpassad grupp databaser på servern. Detta gör att du kan utföra en konsekvensanalys genom att interaktivt lägga till databaser i poolen och sedan ta bort dem för att visa en analys över resursanvändningen och storleksrekommendationer innan du genomför ändringarna. Mer information finns i [Monitor, manage, and size an elastic pool](sql-database-elastic-pool-manage-portal.md) (Övervaka, hantera och ändra storlek på en elastisk pool).

Information om hur du utvärderar en mer flexibel resursanvändning med ad hoc-storleksberäkningar för servrar tidigare än V12 och storleksberäkningar för databaser på olika servrar, finns i [Powershell-skript för att identifiera databaser som är lämpliga för en elastisk pool](sql-database-elastic-pool-database-assessment-powershell.md).

| Funktion | Portalmiljö | PowerShell-skript |
|:--- |:--- |:--- |
| Precision |15 sekunder |15 sekunder |
| Tar hänsyn till prisskillnaderna mellan en pool och prestandanivåer för enskilda databaser |Ja |Nej |
| Tillåter anpassning av listan över databaser som analyseras |Ja |Ja |
| Tillåter anpassning av tidsperioden som används i analysen |Nej |Ja |
| Tillåter anpassning av listan över databaser som analyseras mellan olika servrar |Nej |Ja |
| Tillåter anpassning av listan över databaser som analyseras på v11-servrar |Nej |Ja |

I de fall då du inte kan använda verktygsuppsättningar kan du följa stegen nedan för att ta reda på om en pool är ett mer kostnadseffektivt alternativ än enskilda databaser:

1. Beräkna hur många eDTU:er som behövs för poolen så här:

   MAX(<*totalt antal databaser* × *genomsnittlig DTU-användning per databas*>,<br>
   <*Antal databaser som har aktivitetstoppar samtidigt* × *DTU-toppbelastning per databas*)
2. Beräkna hur stort lagringsutrymme som krävs för poolen genom att lägga till antalet byte som behövs för alla databaser i poolen. Fastställ sedan den eDTU-poolstorlek som ger den här mängden lagringsutrymme. Information om gränser för poollagring baserat på eDTU-poolstorlek finns i [eDTU and storage limits for elastic database pools and elastic databases](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools) (eDTU:er och lagringsgränser för elastiska pooler och elastiska databaser).
3. Använd den större av eDTU-beräkningarna från steg 1 och steg 2.
4. Gå till [sidan med SQL Database-priser](https://azure.microsoft.com/pricing/details/sql-database/) och leta upp den minsta eDTU-poolstorleken som är större än beräkningen från steg 3.
5. Jämför poolpriset i steg 5 med priset för att använda lämpliga prestandanivåer för enskilda databaser.

## <a name="summary"></a>Sammanfattning
Inte alla enskilda databaser är optimala kandidater för pooler. Databaser med användningsmönster som kännetecknas av en låg genomsnittlig användning och relativt ovanliga användningstoppar är perfekta kandidater. Programanvändningsmönster är dynamiska. Använd därför informationen och verktygen som beskrivs i den här artikeln för att göra en första utvärdering och se om en pool är ett bra alternativ för några av eller alla dina databaser. Den här artikeln är bara en startpunkt som hjälper dig att komma fram till om en elastisk pool är ett bra alternativ eller inte. Kom ihåg att du kontinuerligt bör övervaka den historiska resursanvändningen och regelbundet utvärdera prestandanivåerna för alla dina databaser. Tänk på att du enkelt kan lägga till och ta bort databaser i elastiska pooler, och om du har ett stort antal databaser kan du använda flera pooler med olika storlekar som du kan dela in dina databaser i.

## <a name="next-steps"></a>Nästa steg
* [Skapa en elastisk pool](sql-database-elastic-pool-create-portal.md)
* [Övervaka, hantera och ändra storlek på en elastisk pool](sql-database-elastic-pool-manage-portal.md)
* [SQL Database-alternativ och prestanda: Förstå vad varje tjänstnivå erbjuder](sql-database-service-tiers.md)
* [PowerShell-skript för att identifiera databaser som är lämpliga för en elastisk pool](sql-database-elastic-pool-database-assessment-powershell.md)



<!--HONumber=Jan17_HO1-->



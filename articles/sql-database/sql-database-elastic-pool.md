---
title: "Vad är en elastisk pool i Azure? | Microsoft Docs"
description: "Hantera flera hundra eller flera tusen databaser med hjälp av en pool. Ett pris för en uppsättning prestandaenheter kan fördelas över poolen. Flytta in eller ut databaser efter behov."
keywords: elastisk pool, sql-databaser
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 02/01/2017
ms.author: ddove
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 65b7e72d2cf0afd57aebd2e5938b540b2a2b4f20
ms.openlocfilehash: a38e1bf40bde45d0122e07b50c748d57757635a5


---
# <a name="what-is-an-azure-sql-elastic-pool"></a>Vad är en elastisk pool i Azure SQL?
En elastisk SQL-pool får ett fast antal eDTU:er för ett fast pris, på en logisk Azure-server som delas av en pool med databaser på den servern. Elastiska SQL DB-pooler erbjuder en enkel kostnadseffektiv lösning som hjälper dig att hantera prestandamål för flera databaser som har mycket varierande och oförutsägbara användningsmönster.

> [!NOTE]
> Elastiska pooler är allmänt tillgängliga (GA) i alla Azure-regioner utom Västra Indien där de genomgår förhandsgranskning.  GA för elastiska pooler i den här regionen inträffar så snart som möjligt.
>
>

## <a name="how-it-works"></a>Hur det fungerar
Ett vanligt SaaS-programmönster är databasmodellen för en enda klient: varje kund får sin egen databas. Varje kund (databas) har oförutsägbara resursbehov vad gäller minne, IO och processor. Hur allokerar du resurser på ett effektivt och kostnadseffektivt sätt med sådana toppar och dalar i efterfrågan? Förr hade du två alternativ: (1) överetablera resurser baserat på högsta användning och betala för mycket, eller (2) underetablera för att spara kostnader på bekostnad av prestanda och kundnöjdhet under perioder med aktivitetstoppar. Elastiska pooler löser det här problemet genom att se till att databaserna får de prestandaresurser som de behöver, när de behöver dem. De tillhandahåller en enkel resursallokeringsmekanism med en förutsägbar budget. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

I SQL Database uttrycks det relativa måttet på en databas förmåga att hantera resursbehoven i databastransaktionsenheter (DTU:er) för enskilda databaser och elastiska DTU:er (eDTU:er) för databaser i en elastisk pool. Mer information om DTU:er och eDTU:er finns i [Introduktion till SQL Database](sql-database-technical-overview.md).

En pool tilldelas ett bestämt antal eDTU:er till ett fast pris. I poolen kan de enskilda databaserna skalas automatiskt inom fastställda parametrar. Vid hög belastning kan en databas använda fler eDTU:er för att uppfylla efterfrågan. Databaser med lätt arbetsbelastning förbrukar mindre, och databaser utan belastning förbrukar inga eDTU:er. Genom att etablera resurser för hela poolen i stället för enskilda databaser kan du förenkla dina hanteringsuppgifter. Dessutom har du en förutsägbar budget för poolen.

Ytterligare eDTU:er kan läggas till i en befintlig pool utan något avbrott i databasen, förutom att databaserna kan behöva flyttas för att ge ytterligare beräkningsresurser för den nya eDTU-reservationen. På samma sätt kan eDTU:er som inte längre behövs tas bort från en befintlig pool när som helst.

Och du kan lägga till eller ta bort databaser i poolen. Om du vet att en databas underförbrukar resurser tar du bort den.

## <a name="which-databases-go-in-a-pool"></a>Vilka databaser kan finnas i en pool?
![SQL-databaser delar eDTU:er i en elastisk pool.][1]

Databaser som är bra kandidater för elastiska pooler har vanligtvis perioder av aktivitet och andra perioder av inaktivitet. I exemplet ovan ser du aktiviteten för en enkel databas, 4 databaser och sist en elastisk pool med 20 databaser. Databaser med aktivitet som varierar över tid är bra kandidater för elastiska pooler eftersom alla inte är aktiva samtidigt och kan dela eDTU:er. Alla databaser passar inte det här mönstret. Databaser som har ett mer konstant resursbehov passar bättre för tjänstnivåerna Basic, Standard och Premium där resurserna tilldelas individuellt.

[Pris- och prestandaöverväganden för en elastisk pool](sql-database-elastic-pool-guidance.md).

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>eDTU:er och lagringsgränser för elastiska pooler

I följande tabell beskrivs egenskaperna för elastiska pooler på nivåerna Basic, Standard och Premium.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Om alla DTU:er för en elastisk pool används får varje databas i poolen lika många resurser för att bearbeta frågor.  SQL Database-tjänsten tillhandahåller rättvis resursdelning mellan databaser genom att tilldela lika mycket beräkningstid till dem. En rättvis resursdelning i elastiska pooler tillämpas utöver den mängd resurser som varje databas är garanterad om det minsta antalet DTU:er per databas har angetts till ett annat värde än noll.

## <a name="elastic-pool-properties"></a>Egenskaper för elastisk pool

Följande tabeller beskriver begränsningarna för elastiska pooler och databaser i en pool.

### <a name="limits-for-elastic-pools"></a>Begränsningar för elastiska pooler
| Egenskap | Beskrivning |
|:--- |:--- |
| Tjänstenivå |Basic, Standard eller Premium. Tjänstnivån anger intervallet för prestanda- och lagringsbegränsningar som kan konfigureras samt alternativ för affärskontinuitet. Varje databas i en pool har samma tjänstnivå som poolen. ”Tjänstenivå” kallas också för ”utgåva”. |
| eDTU:er per pool |Det högsta antalet eDTU:er som kan delas av databaser i poolen. Det sammanlagda antalet eDTU:er som används av databaser i poolen får inte överskrida den här gränsen vid samma tidpunkt. |
| Högsta lagringsutrymme per pool (GB) |Den högsta mängden lagringsutrymme i antal GB som kan delas av databaser i poolen. Det totala lagringsutrymmet som används av databaser i poolen får inte överskrida den här gränsen. Den här gränsen bestäms av antalet eDTU:er per pool. Om den här gränsen överskrids blir alla databaser skrivskyddade. Maximalt lagringsutrymme per pool avser det maximala lagringsutrymmet för datafiler i poolen och omfattar inte utrymme som används av loggfiler. |
| Maximalt antal databaser per pool |Det högsta antalet databaser som tillåts per pool. |
| Maximalt antal samtidiga arbetare per pool |Det högsta antalet samtidiga arbetare (begäranden) som är tillgängliga för alla databaser i poolen. |
| Maximalt antal samtidiga inloggningar per pool |Det högsta antalet samtidiga inloggningar för alla databaser i poolen. |
| Maximalt antal samtidiga sessioner per pool |Det högsta antal sessioner som är tillgängliga för alla databaser i poolen. |

### <a name="limits-for-pooled-databases"></a>Begränsningar för databaser i en pool
| Egenskap | Beskrivning |
|:--- |:--- |
| Maximalt antal eDTU:er per databas |Det högsta antalet eDTU:er som en databas i poolen kan använda, om de är tillgängliga beroende på användningen av andra databaser i poolen.  Det högsta antalet eDTU:er per databas utgör ingen resursgaranti för en databas.  Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ange ett högsta antal eDTU:er per databas som är tillräckligt högt för att hantera toppar i databasanvändningen. En viss grad av övertilldelning är att förvänta eftersom poolen normalt arbetar efter ett mönster med frekvent och lågfrekvent användning av databaser där inte alla databaser har aktivitetstoppar samtidigt. Anta exempelvis att toppanvändningen per databas är 20 eDTU:er och att endast 20 % av de 100 databaserna i poolen har hög belastning samtidigt.  Om det högsta antalet eDTU:er per databas har angetts till 20 eDTU:er är det rimligt att övertilldela poolen med 5 gånger så mycket och ange eDTU:erna per pool till 400. |
| Minimalt antal eDTU:er per databas |Det minsta antalet eDTU:er som en databas i poolen är garanterad.  Den här inställningen är en global inställning som gäller för alla databaser i poolen. Det minsta antalet eDTU:er per databas kan anges till 0, vilket även är standardvärdet. Den här egenskapen anges till ett värde mellan 0 och den genomsnittliga eDTU-användningen per databas. Produkten av antalet databaser i poolen och det minsta antalet eDTU:er per databas får inte överskrida antalet eDTU:er per pool.  Om en pool till exempel har 20 databaser och det minsta antalet eDTU:er per databas har angetts till 10 eDTU:er så måste antalet eDTU:er per pool vara minst 200 eDTU:er. |
| Största lagringsutrymme per databas (GB) |Det största lagringsutrymmet för en databas i en pool. Eftersom databaser i en pool delar poolens lagringsutrymme är databaslagringen begränsad till poolens återstående lagringsutrymme eller det största lagringsutrymmet per databas, beroende på vilket som är minst. Maximalt lagringsutrymme per pool avser det maximala lagringsutrymmet för datafiler i poolen och omfattar inte utrymme som används av loggfiler. |

## <a name="elastic-jobs"></a>Elastiska jobb
Med en pool förenklas hanteringsuppgifterna eftersom du kan köra skript i **[elastiska jobb](sql-database-elastic-jobs-overview.md)**. Ett elastiskt jobb underlättar hanteringen av stora antal databaser. För att komma igång kan du se [Kom igång med elastiska jobb](sql-database-elastic-jobs-getting-started.md).

Mer information om andra databasverktyg för att jobba med flera databaser, finns i [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md).

## <a name="business-continuity-features-for-databases-in-a-pool"></a>Funktioner för affärskontinuitet för databaser i en pool
Databaser i pool stöder generellt sett samma [funktioner för affärskontinuitet](sql-database-business-continuity.md) som finns tillgängliga för enskilda databaser.

### <a name="point-in-time-restore"></a>Återställning till tidpunkt
Återställning till tidpunkt använder automatiska databassäkerhetskopieringar för att återställa en databas i en pool till en viss tidpunkt. Mer information finns i avsnittet om [återställning till tidpunkt](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>Geo-återställning
Geo-återställning tillhandahåller standardalternativet för återställning när en databas inte är tillgänglig på grund av en incident i den region som databasen finns i. Mer information finns i avsnittet [Restore an Azure SQL Database or failover to a secondary](sql-database-disaster-recovery.md) (Återställa en Azure SQL-databas eller växla över till en sekundär databas)

### <a name="active-geo-replication"></a>Aktiv geo-replikering
För program som har mer aggressiva återställningskrav än vad geo-återställning kan erbjuda konfigurerar du aktiv geo-replikering med hjälp av [Azure Portal](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) eller [Transact-SQL](sql-database-geo-replication-transact-sql.md).

## <a name="additional-resources"></a>Ytterligare resurser
* [Microsoft Virtual Academy-videokurs om elastiska funktioner i Azure SQL Database](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)

<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png



<!--HONumber=Feb17_HO1-->



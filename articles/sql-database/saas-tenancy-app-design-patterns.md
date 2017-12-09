---
title: "Flera innehavare SaaS mönster - Azure SQL Database | Microsoft Docs"
description: "Läs mer om krav och gemensamma data arkitektur mönster för flera innehavare programvara som en tjänst (SaaS)-databasprogram som körs i Azure-molnet-miljön."
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen,srinia
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: billgib
ms.openlocfilehash: 1b6c780000d8c5e31a78f7f83ae74c002e8f8349
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Flera innehavare SaaS databasen innehavare mönster

När du designar ett SaaS-program med flera innehavare, måste du noggrant välja innehavare modellen som passar bäst för ditt program.  En innehavare modell avgör hur varje klient data mappas till lagring.  Ditt val av innehavare modellen påverkar programmet design och hantering.  Växla till en annan modell senare är ibland kostsamma.

En beskrivning av alternativa innehavare modeller följer.

## <a name="a-how-to-choose-the-appropriate-tenancy-model"></a>A. Så här väljer du lämplig innehavare modellen

I allmänhet innehavare modellen påverkar inte funktionen för ett program, men det sannolikt påverkar andra aspekter av virtualiseringslösningen.  Följande villkor används för att utvärdera var och en av:

- **Skalbarhet:**
    - Antal klienter.
    - Lagring per-klient.
    - Lagring i mängden.
    - Arbetsbelastning.

- **Klientisolering:** &nbsp; isolering av Data och prestanda (om en klient arbetsbelastning påverkar andra).

- **Kostnaden per klient:** &nbsp; databasen kostnader.

- **Utveckling komplexitet:**
    - Ändringar i schemat.
    - Ändringar av frågor (obligatoriskt för mönstret).

- **Operativa komplikationer:**
    - Övervaka och hantera prestanda.
    - Schemahantering av.
    - Återställa en klient.
    - Haveriberedskap.

- **Anpassningsbarheten:** &nbsp; enkel stödja schemat anpassningar som antingen klient-specifika eller klass-specifika-klient.

Innehavare diskussionen fokuserar på de *data* lager.  Men kom ihåg att ett ögonblick de *programmet* lager.  Applikationsnivån behandlas som en monolitisk entitet.  Om du dividerar programmet till många små komponenter kan valet av modellen för innehavare ändras.  Vissa komponenter gick behandlas annorlunda än andra om både innehavare och lagringsteknik eller plattform som används.

## <a name="b-standalone-single-tenant-app-with-single-tenant-database"></a>B. Stöd för en innehavare fristående app med stöd för en innehavare databas

#### <a name="application-level-isolation"></a>Isolering av servicenivå

I den här modellen installeras hela programmet upprepade gånger, en gång för varje klient.  Varje instans av appen är en fristående instans, så den aldrig interagerar med en annan instans av fristående.  Varje instans av appen har endast en klient, och måste därför bara en databas.  Klienten har databas till sig själv.

![Design av fristående app med exakt en enskild klient-databas.][image-standalone-app-st-db-111a]

Varje app-instansen har installerats i en separat Azure resursgrupp.  Resursgruppen kan tillhöra en prenumeration som ägs av program- eller klienten.  I båda fallen kan leverantören hantera programvara för klienten.  Varje instans av programmet har konfigurerats för att ansluta till dess motsvarande databas.

Varje klient databas distribueras som en fristående databas.  Den här modellen ger den mest databasen isoleringen.  Men isolering kräver att tillräckligt med resurser tilldelas varje databas för att hantera dess belastning belastning.  Här är den viktiga att elastiska pooler inte kan användas för databaser som distribuerats i olika resursgrupper olika prenumerationer.  Den här begränsningen är fristående stöd för en innehavare appen modellen dyraste lösningen ur ett övergripande databasen kostnaden.

#### <a name="vendor-management"></a>Hantering av leverantörer

Leverantören kan komma åt alla databaser i alla fristående app instanser, även om app-instanser som är installerade på olika klient prenumerationer.  Åtkomst uppnås via SQL-anslutningar.  Åtkomst mellan instansen kan aktivera leverantören för att centralisera schemahantering och mellan databasfrågan för rapportering eller i analytics.  Om den här typen av centraliserad hantering önskas måste en katalog distribueras som mappar klient-ID: n till databasen URI: er.  Azure SQL-databasen innehåller ett bibliotek för horisontell partitionering som används tillsammans med en SQL-databas för att tillhandahålla en katalog.  Horisontell partitionering biblioteket heter formellt den [klientbibliotek för elastisk databas][docu-elastic-db-client-library-536r].

## <a name="c-multi-tenant-app-with-database-per-tenant"></a>C. Flera innehavare app med databasen per klient

Den här nästa mönster använder ett program med flera innehavare med många databaser som alla databaser som en klient.  En ny databas etableras för varje ny klient.  Programmet nivån skalas *in* lodrätt genom att lägga till fler resurser per nod.  Eller appen skalas *ut* vågrätt genom att lägga till fler noder.  Skalningen baserat på arbetsbelastning och är oberoende av det antal eller skalan för enskilda databaser.

![Design av flera innehavare app till databas per klient.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Anpassa för en klient

Som det fristående app-mönstret ger användningen av en klient databaser starka klientisolering.  Schemat för alla en viss databas kan anpassas och optimeras för dess klient i alla appar som vars modellen anger endast stöd för en innehavare databaser.  Den här anpassningen påverkar inte andra klienter i appen. En klient kan kanske behöver data utöver de grundläggande datafält som alla klienter behöver.  Dessutom behöva extra datafältet ett index.

Med databasen per klient är det enkelt att uppnå att anpassa schemat för en eller flera enskilda klienter.  Leverantören av tillämpningsprogrammet måste skapa procedurer för att hantera noggrant schemat anpassningar i större skala.

#### <a name="elastic-pools"></a>Elastiska pooler

När databaser distribueras i samma resursgrupp, kan de grupperas i elastiska databaspooler.  Pooler ger ett kostnadseffektivt sätt att dela resurser över flera databaser.  Det här alternativet om poolen är billigare än att kräva att varje databasen ska vara tillräckligt stor för att hantera användningstopparna som möjligt.  Även om grupperade databaser dela åtkomst till resurser kan de fortfarande få en hög grad av isolering av prestandan.

![Designen för flera innehavare app med databas-per-klient med elastisk pool.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL-databasen innehåller verktyg som krävs för att konfigurera, övervaka och hantera delning.  Båda pool-nivå och databasnivå prestandamått är tillgängliga i Azure-portalen och via logganalys.  Mätvärdena kan ge bra insikter om sammanställd och klient-specifika prestanda.  Enskilda databaser kan flyttas mellan pooler för att tillhandahålla reserverade resurser för en viss klient.  Dessa verktyg kan du se till att goda prestanda på ett kostnadseffektivt sätt.

#### <a name="operations-scale-for-database-per-tenant"></a>Operations skala för databasen per klient

Azure SQL Database-plattform har många funktioner för hantering av stort antal databaser i skala, till exempel väl över 100 000 databaser.  Dessa funktioner gör databasen per klient mönstret rimligt.

Anta exempelvis att ett system har en databas som 1000-klient som bara en databas.  Databasen kan ha 20 index.  Om systemet konverterar till med 1000 stöd för en innehavare databaser, stiger quatity av index till 20 000.  I SQL-databas som en del av [automatisk justering][docu-sql-db-automatic-tuning-771a], funktionerna för automatisk indexering är aktiverade som standard.  Automatisk indexering hanterar du alla 20 000 index och deras pågående optimeringar skapa och släpp.  Dessa automatiserade åtgärder som sker inom en individuell databas och de inte samordnas eller begränsade av liknande åtgärder i andra databaser.  Automatisk indexering behandlar index annorlunda än upptagen databaser i en ledig databas.  Den här typen av index management anpassning är opraktiska i databasen per klient skala om den här aktiviteten för hantering av stora var du tvungen att manuellt.

Andra funktioner som kan skalas efter era behov inkluderar följande:

- Inbyggda säkerhetskopiering.
- Hög tillgänglighet.
- Kryptering på disken.
- Prestanda telemetri.

#### <a name="automation"></a>Automation

Hanteringsåtgärderna som kan användas av skriptet och erbjuds via en [devops] [ http-visual-studio-devops-485m] modell.  Åtgärder kan även automatiskt och visas i programmet.

Exempelvis kan du automatisera återställning av en enskild klient till en tidigare tidpunkt.  Återställningen behöver bara återställa en enskild klient-databas som lagrar innehavaren.  Den här återställningen har ingen inverkan på andra klienter som bekräftar att hanteringsåtgärder är på varje enskild klientorganisation buffertstorleken detaljerad nivå.

## <a name="d-multi-tenant-app-with-multi-tenant-databases"></a>D. Flera innehavare app med flera innehavare databaser

En annan tillgänglig mönstret är att lagra många klienter i en databas med flera innehavare.  Programinstansen kan ha valfritt antal databaser för flera innehavare.  Schemat för en databas med flera innehavare måste ha en eller flera innehavare identifierare kolumner så att data från en viss klient kan hämtas selektivt.  Schemat kan dessutom kräva några tabeller eller kolumner som används av en delmängd av klienter.  Statiska kod och referens data lagras bara en gång och delas av alla klienter.

#### <a name="tenant-isolation-is-sacrificed"></a>Klientisolering bort

*Data:* &nbsp; en databas med flera innehavare nödvändigtvis offrar klientisolering.  Data för flera innehavare lagras tillsammans i en databas.  Se till att frågor aldrig exponera data från mer än en klient under utvecklingen.  SQL Database stöder [radnivå säkerhet][docu-sql-svr-db-row-level-security-947w], som kan tillämpa dessa data som returnerats från en fråga vara begränsad till en enskild klient.

*Bearbetning:* &nbsp; en databas med flera innehavare resurser beräkning och lagring på alla klienter.  Databasen som helhet kan övervakas så acceptable.  Azure systemet har dock inget inbyggt sätt att övervaka och hantera användningen av dessa resurser av en enskild klientorganisation.  Därför innebär flera innehavare databasen en ökad risk för störningar grannar, där arbetsbelastningen för en overactive klient påverkar prestanda upplevelse av andra klienter i samma databas.  Ytterligare programnivå övervakning kan övervaka prestanda för klient-nivå.

#### <a name="lower-cost"></a>Lägre kostnader

I allmänhet har databaser för flera innehavare den lägsta per-klient kostnad.  Resurskostnader för en fristående databas är lägre än för en elastisk pool equivalently storlek.  Dessutom för scenarier där klienter behöver endast begränsad kan potentiellt miljontals innehavare lagras i en databas.  Inga elastiska poolen kan innehålla miljontals databaser.  Dock kan en lösning som innehåller 1000 databaser per pool med 1000 pooler nå skalan miljoner dess risk blir svårhanterliga att hantera.

Två varianter av en databasmodell för flera innehavare beskrivs vad följer med delat modell för flera klienter som är den mest flexibla och skalbara.

## <a name="e-multi-tenant-app-with-a-single-multi-tenant-database"></a>E. Flera innehavare app med en enskild databas för flera innehavare

Enklaste mönstret för flera innehavare databasen använder en enda fristående databas som värd för data för alla klienter.  När flera innehavare läggs skalas databasen med mer resurser för lagring och beräkning.  Den här skalas upp kan vara allt som krävs, även om det finns alltid en ultimate skala gräns.  Lång innan gränsen nås databasen blir emellertid svårhanterliga att hantera.

Hanteringsåtgärder som fokuserar på enskilda klienter är mer komplex att implementera i en databas med flera innehavare.  Och i skala dessa åtgärder bli oacceptabelt långsamt.  Ett exempel är en point-in-time-återställning av data för en klient.

## <a name="f-multi-tenant-app-with-sharded-multi-tenant-databases"></a>F. Flera innehavare app med delat databaser för flera innehavare

De flesta SaaS-program för att komma åt data för endast en klient i taget.  Det här åtkomstmönstret tillåter klientdata sprids över flera databaser eller delar, där alla data för en klient finns i en Fragmentera.  I kombination med ett mönster för flera innehavare databas, kan en delat modell nästan obegränsad skala.

![Design av flera innehavare app med delat databaser för flera innehavare.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Hantera shards

Horisontell partitionering lägger till komplexitet både design och operativa management.  En katalog krävs att underhålla mappningen mellan klienter och databaser.  Hanteringen av är dessutom krävs för att hantera delar och klient population.  Till exempel måste procedurer utformas att lägga till och ta bort delar och flytta klientdata mellan shards.  Ett sätt att skala är att genom att lägga till en ny Fragmentera och fyller den med nya klienter.  Vid andra tillfällen kan du dela en tätbefolkade Fragmentera i två mindre tätt ifyllda shards.  När flera klienter har flyttats eller upphöra att gälla, kan du koppla glesbefolkade shards tillsammans.  Kopplingen leder mer kostnadseffektivt resursutnyttjande.  Klienter kan också flyttas mellan shards saldo arbetsbelastningar.

SQL Database ger ett dela/merge-verktyg som fungerar tillsammans med bibliotek för horisontell partitionering och katalogdatabasen för.  Den angivna appen kan dela och merge delar och den kan flytta klientdata mellan shards.  Appen har också katalog under dessa åtgärder, markera påverkas klienter som offline innan flyttas.  Efter överflyttningen uppdaterar appen katalogen igen med en ny mappning och markera innehavaren som online igen.

#### <a name="smaller-databases-more-easily-managed"></a>Mindre databaser mer enkelt hanteras

Delat lösning för flera innehavare resulterar i mindre databaser som är enklare att hantera genom att distribuera klienter över flera databaser.  Återställa en viss klient till en tidigare punkt i tiden nu omfattar till exempel en enda mindre databas återställs från en säkerhetskopia, snarare än en större databas som innehåller alla klienter. Databasens storlek och antalet klienter per databas, kan du välja att balansera arbetsbelastningen och hantering av arbete.

#### <a name="tenant-identifier-in-the-schema"></a>Klient-ID i schemat

Beroende på den horisontell partitionering-metod som används, kan ytterligare begränsningar åläggas databasschemat.  SQL-databas dela/merge programmet kräver att schemat innehåller delning nyckel, vilket normalt är klient-ID.  Klient-ID är det inledande elementet i den primära nyckeln i alla tabeller som delat.  Klient-ID kan dela/merge-programmet för att snabbt hitta och flytta data som är associerade med en viss klient.

#### <a name="elastic-pool-for-shards"></a>Elastisk pool för shards

Delat databaser för flera innehavare kan placeras i elastiska pooler.  I allmänhet är har många stöd för en innehavare databaser i poolen kostnad effektiv som har många hyresgäster i några databaser för flera innehavare.  Flera innehavare databaser är bra när det finns ett stort antal relativt inaktiva klienter.

## <a name="g-hybrid-sharded-multi-tenant-database-model"></a>G. Hybrid delat flera innehavare databasmodellen

I hybrid-modellen har alla databaser klient-ID i schemat.  Databaserna finns alla kan lagra mer än en klient och databaserna kan vara delat.  Så i schemat mening, de är alla databaser för flera innehavare.  I praktiken kan vissa av dessa databaser ännu innehålla endast en klient.  Oavsett, har antalet klienter som lagras i en viss databas ingen effekt på databasschemat.

#### <a name="move-tenants-around"></a>Flytta klienter

När som helst kan du flytta en viss klient till en egen databas för flera innehavare.  Och när som helst kan du ångrar och flytta innehavaren tillbaka till en databas som innehåller flera innehavare.  Du kan också tilldela en klient till nya enskild klient-databasen när du etablerar den nya databasen.

Hybrid-modellen kommer till sin rätt när det finns stora skillnader mellan resursbehov för att identifiera grupper av klienter.  Anta exempelvis att klienter deltar i en kostnadsfri utvärderingsversion inte är garanterat samma hög nivå av prestanda som prenumerationsapp klienter.  Principen kanske för klienter i den kostnadsfria utvärderingsversion fasen lagras i en databas med flera innehavare som delas mellan alla de kostnadsfria utvärderingsversioner för klienterna.  När en kostnadsfri utvärderingsversion klient prenumererar på servicenivån som grundläggande, kan klienten flyttas till en annan databas för flera innehavare som kan ha färre klienter.  En prenumerant som betalar för servicenivån premium kunde flyttas till en egen ny enskild klient-databas.

#### <a name="pools"></a>Pooler

Stöd för en innehavare databaser för prenumeranten klienter kan placeras i resurspooler för att minska kostnaderna för databasen per klient i den här hybrid-modellen.  Detta görs även i databasen per klient-modellen.

## <a name="h-tenancy-models-compared"></a>H. Innehavare modeller jämfört med

I följande tabell sammanfattas skillnaderna mellan de huvudsakliga innehavare modellerna.

| Mätning | Fristående app | Databasen per klient | Delat flera innehavare |
| :---------- | :------------- | :------------------ | :------------------- |
| Skala | Medel<br />1-100-tal | Mycket hög<br />100 1-000-tal | Obegränsat<br />1 1,000,000s |
| Klientisolering | Mycket hög | Hög | Låg; Förutom alla singleton-klient (det är enbart i en Huvudmålservern db). |
| Databasen kostnad per klient | Hög. storlek för toppar. | Låg; Pooler används. | Lägsta, för små klienter i Huvudmålservern DBs. |
| Hantering och övervakning av programprestanda | Per-klienten endast | Mängd + per klient | Aggregera; även om är per klient endast för singletons. |
| Utveckling komplexitet | Låg | Låg | Medel; på grund av horisontell partitionering. |
| Operativa komplikationer | Låg till hög. Individuellt enkla, komplexa i större skala. | Låg-medel. Mönster adress komplexiteten i större skala. | Låg till hög. Hantering av enskilda klient är komplex. |
| &nbsp; ||||

## <a name="next-steps"></a>Nästa steg

- [Distribuera och utforska en flera innehavare Wingtip program som använder databasen per klient SaaS modell - Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Välkommen till Wingtip biljetter SaaS Azure SQL Database innehavare exempelappen][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Design av fristående app med exakt en enskild klient-databas."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Design av flera innehavare app till databas per klient."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Designen för flera innehavare app med databas-per-klient med elastisk pool."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Design av flera innehavare app med delat databaser för flera innehavare."


---
title: SaaS-mönster med flera innehavare
description: Lär dig mer om kraven och de vanliga dataarkitekturmönstren för saaS-databasprogram (multi-tenant software as a service) som körs i Azure-molnmiljön.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 01/25/2019
ms.custom: seoapril2019
ms.openlocfilehash: 956d74467c69d9924d26f9cae8d902a6ddd84496
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067494"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Mönster för hyresrätt i SaaS-databasen med flera innehavare

I den här artikeln beskrivs de olika hyresmodeller som är tillgängliga för ett SaaS-program med flera innehavare.

När du utformar ett SaaS-program med flera innehavare måste du noggrant välja den hyresmodell som bäst passar behoven hos ditt program.  En hyresmodell bestämmer hur varje klients data mappas till lagring.  Ditt val av hyresmodell påverkar programdesign och hantering.  Att byta till en annan modell senare är ibland kostsamt.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS-begrepp och terminologi

I SaaS-modellen (Software as a Service) säljer ditt företag inte *licenser* till din programvara. Istället gör varje kund hyra betalningar till ditt företag, vilket gör varje kund en *hyresgäst* i ditt företag.

Mot betalning av hyran får varje klient åtkomst till dina SaaS-programkomponenter och har sina data lagrade i SaaS-systemet.

Termen *hyresmodell* avser hur hyresgästernas lagrade data är ordnade:

- *Enkel innehavare:* &nbsp; Varje databas lagrar data från endast en klient.
- *Flera innehavare:* &nbsp; Varje databas lagrar data från flera separata klienter (med mekanismer för att skydda datasekretessen).
- Hybrid hyresmodeller finns också.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Hur man väljer lämplig hyresmodell

I allmänhet påverkar hyresmodellen inte funktionen hos ett program, men det påverkar sannolikt andra aspekter av den övergripande lösningen.  Följande kriterier används för att bedöma var och en av modellerna:

- **Skalbarhet:**
    - Antal klienter.
    - Lagring per klient.
    - Lagring sammanlagt.
    - Arbetsbelastning.

- **Klientisolering:** &nbsp; Dataisolering och prestanda (oavsett om en klients arbetsbelastning påverkar andra).

- **Kostnad per klient:** &nbsp; Databaskostnader.

- **Komplexitet i utvecklingen:**
    - Ändringar i schemat.
    - Ändringar i frågor (krävs av mönstret).

- **Operativ komplexitet:**
    - Övervakning och hantering av prestanda.
    - Schemahantering.
    - Återställa en klient.
    - Haveriberedskap.

- **Anpassningsbarhet:** &nbsp; Enkel stöd för schemaanpassningar som antingen är klientspecifika eller klientklassspecifika.

Hyresdiskussionen är inriktad *data* på datalagret.  Men tänk för ett ögonblick *på applikationslagret.*  Programskiktet behandlas som en monolitisk entitet.  Om du delar upp programmet i många små komponenter kan ditt val av hyresmodell ändras.  Du kan behandla vissa komponenter annorlunda än andra när det gäller både hyresrätt och lagringsteknik eller plattform som används.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Fristående app med en klientorganisation med databas för en klientorganisation

#### <a name="application-level-isolation"></a>Isolering på programnivå

I den här modellen installeras hela programmet upprepade gånger, en gång för varje klient.  Varje instans av appen är en fristående instans, så den interagerar aldrig med någon annan fristående instans.  Varje instans av appen har bara en klient och behöver därför bara en databas.  Klienten har databasen för sig själv.

![Design av fristående app med exakt en databas med en enda klient.][image-standalone-app-st-db-111a]

Varje appinstans installeras i en separat Azure-resursgrupp.  Resursgruppen kan tillhöra en prenumeration som ägs av antingen programvaruleverantören eller klienten.  I båda fallen kan leverantören hantera programvaran för klienten.  Varje programinstans är konfigurerad för att ansluta till motsvarande databas.

Varje klientdatabas distribueras som en enda databas.  Den här modellen ger den största databasisoleringen.  Men isoleringen kräver att tillräckliga resurser allokeras till varje databas för att hantera sina toppbelastningar.  Här spelar det roll att elastiska pooler inte kan användas för databaser som distribueras i olika resursgrupper eller för olika prenumerationer.  Den här begränsningen gör den här fristående appmodellen för en klient till den dyraste lösningen ur ett övergripande databaskostnadsperspektiv.

#### <a name="vendor-management"></a>Hantering av leverantörer

Leverantören kan komma åt alla databaser i alla fristående appinstanser, även om appinstanserna är installerade i olika klientprenumerationer.  Åtkomsten uppnås via SQL-anslutningar.  Den här åtkomsten mellan instanser kan göra det möjligt för leverantören att centralisera schemahantering och fråga över flera databaser för rapporterings- eller analysändamål.  Om den här typen av centraliserad hantering önskas måste en katalog distribueras som mappar klientidentifierare till databas-URI:er.  Azure SQL Database tillhandahåller ett fragmenteringsbibliotek som används tillsammans med en SQL-databas för att tillhandahålla en katalog.  Fragmenteringsbiblioteket heter formellt [det elastiska databasklientbiblioteket][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. App för flera innehavare med databas per klient

I nästa mönster används ett program med flera innehavare med många databaser, som alla är databaser med en klient.  En ny databas har etablerats för varje ny klient.  Programnivån skalas *upp* lodrätt genom att lägga till fler resurser per nod.  Eller appen skalas *ut* vågrätt genom att lägga till fler noder.  Skalningen baseras på arbetsbelastning och är oberoende av antalet eller omfattningen av de enskilda databaserna.

![Design av app med flera innehavare med databas per klient.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Anpassa för en klient

Precis som det fristående appmönstret ger användningen av databaser med en klient stark klientisolering.  I alla appar vars modell endast anger databaser med en enda klient kan schemat för en viss databas anpassas och optimeras för klienten.  Den här anpassningen påverkar inte andra klienter i appen. Kanske en klient kan behöva data utöver de grundläggande datafält som alla klienter behöver.  Dessutom kan det extra datafältet behöva ett index.

Med databas per klient är det enkelt att anpassa schemat för en eller flera enskilda klienter för att uppnå.  Programleverantören måste utforma procedurer för att noggrant hantera schemaanpassningar i stor skala.

#### <a name="elastic-pools"></a>Elastiska pooler

När databaser distribueras i samma resursgrupp kan de grupperas i elastiska pooler.  Poolerna är ett kostnadseffektivt sätt att dela resurser i många databaser.  Det här poolalternativet är billigare än att kräva att varje databas är tillräckligt stor för att rymma de användningstoppar som den upplever.  Även om poolade databaser delar åtkomst till resurser kan de fortfarande uppnå en hög grad av prestandaisolering.

![Design av app med flera innehavare med databas per klient med elastisk pool.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database innehåller de verktyg som krävs för att konfigurera, övervaka och hantera delning.  Prestandamått på både poolnivå och databasnivå är tillgängliga i Azure-portalen och via Azure Monitor-loggar.  Måtten kan ge bra insikter om både aggregerade och klientspecifika prestanda.  Enskilda databaser kan flyttas mellan pooler för att tillhandahålla reserverade resurser till en viss klient.  Dessa verktyg gör att du kan säkerställa god prestanda på ett kostnadseffektivt sätt.

#### <a name="operations-scale-for-database-per-tenant"></a>Åtgärder skala för databas-per-klient

Azure SQL Database-plattformen har många hanteringsfunktioner som utformats för att hantera ett stort antal databaser i stor skala, till exempel över 100 000 databaser.  Dessa funktioner gör mönstret för databas per klient rimlig.

Anta till exempel att ett system har en databas med 1 000 innehavare som enda databas.  Databasen kan ha 20 index.  Om systemet konverteras till att ha 1000 databaser med en enda klient stiger antalet index till 20 000.  I SQL Database som en del av [Automatisk justering][docu-sql-db-automatic-tuning-771a]aktiveras de automatiska indexeringsfunktionerna som standard.  Automatisk indexering hanterar för dig alla 20 000 index och deras pågående skapar- och släppoptimeringar.  Dessa automatiserade åtgärder sker i en enskild databas och de samordnas inte eller begränsas av liknande åtgärder i andra databaser.  Automatisk indexering behandlar index på olika sätt i en upptagen databas än i en mindre upptagen databas.  Den här typen av anpassning av indexhantering skulle vara opraktiskt på skalan för databas per klient om den här enorma hanteringsuppgiften måste utföras manuellt.

Andra hanteringsfunktioner som skalas väl inkluderar följande:

- Inbyggda säkerhetskopior.
- Hög tillgänglighet.
- Kryptering på disk.
- Prestandatelemetri.

#### <a name="automation"></a>Automation

Hanteringsåtgärderna kan skriptas och erbjudas via en [devops-modell.][http-visual-studio-devops-485m]  Verksamheten kan även automatiseras och exponeras i programmet.

Du kan till exempel automatisera återställningen av en enskild klient till en tidigare tidpunkt.  Återställningen behöver bara återställa en databas med en enda klientorganisation som lagrar klienten.  Den här återställningen påverkar inte andra klienter, vilket bekräftar att hanteringsåtgärderna är på den finformiga nivån för varje enskild klient.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. App för flera innehavare med databaser med flera innehavare

Ett annat tillgängligt mönster är att lagra många klienter i en databas med flera innehavare.  Programinstansen kan ha valfritt antal databaser med flera innehavare.  Schemat för en databas med flera innehavare måste ha en eller flera klientidentifierares kolumner så att data från en viss klient kan hämtas selektivt.  Dessutom kan schemat kräva några tabeller eller kolumner som endast används av en delmängd av klienter.  Statisk kod och referensdata lagras dock bara en gång och delas av alla klienter.

#### <a name="tenant-isolation-is-sacrificed"></a>Isolering av hyresgäster offras

*Data:* &nbsp; En databas med flera innehavare offrar nödvändigtvis klientisolering.  Data för flera klienter lagras tillsammans i en databas.  Under utvecklingen, se till att frågor aldrig exponera data från mer än en klient.  SQL Database stöder [säkerhet på radnivå][docu-sql-svr-db-row-level-security-947w], vilket kan framtvinga att data som returneras från en fråga begränsas till en enda klient.

*Bearbetning:* &nbsp; En databas med flera innehavare delar beräknings- och lagringsresurser över alla sina klienter.  Databasen som helhet kan övervakas för att säkerställa att den fungerar på ett godtagbart sätt.  Azure-systemet har dock inget inbyggt sätt att övervaka eller hantera användningen av dessa resurser av en enskild klientorganisation.  Därför medför databasen med flera innehavare en ökad risk att stöta på bullriga grannar, där arbetsbelastningen för en överaktiv klient påverkar prestandaupplevelsen för andra klienter i samma databas.  Ytterligare övervakning på programnivå kan övervaka prestanda på klientnivå.

#### <a name="lower-cost"></a>Lägre kostnad

I allmänhet har databaser med flera innehavare den lägsta kostnaden per klient.  Resurskostnaderna för en enskild databas är lägre än för en elastisk pool av motsvarande storlek.  Dessutom, för scenarier där klienter behöver endast begränsad lagring, kan potentiellt miljontals klienter lagras i en enda databas.  Ingen elastisk pool kan innehålla miljontals databaser.  En lösning som innehåller 1000 databaser per pool, med 1000 pooler, kan dock nå skalan på miljoner med risk för att bli tungrodd att hantera.

Två varianter av en databasmodell med flera innehavare diskuteras i följande följande, där den fragmenterade modellen för flera innehavare är den mest flexibla och skalbara.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. App med flera innehavare med en enda databas med flera innehavare

Det enklaste databasmönstret för flera innehavare använder en enda databas som värd för data för alla klienter.  När fler klienter läggs till skalas databasen upp med fler lagrings- och beräkningsresurser.  Denna skala upp kan vara allt som behövs, även om det alltid finns en slutlig skala gräns.  Men långt innan den gränsen nås blir databasen svårhanterlig att hantera.

Hanteringsåtgärder som fokuserar på enskilda klienter är mer komplexa att implementera i en databas med flera innehavare.  Och i stor skala dessa operationer kan bli oacceptabelt långsam.  Ett exempel är en point-in-time-återställning av data för bara en klient.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. App för flera innehavare med fragmenterade databaser med flera innehavare

De flesta SaaS-program får åtkomst till data för endast en klient i taget.  Med det här åtkomstmönstret kan klientdata distribueras över flera databaser eller shards, där alla data för en klient finns i en fragment.  Kombinerat med ett databasmönster med flera innehavare tillåter en fragmenterad modell nästan obegränsad skala.

![Design av app med flera innehavare med fragmenterade databaser med flera innehavare.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Hantera shards

Sharding ökar komplexiteten både i design- och drifthanteringen.  En katalog krävs för att upprätthålla mappningen mellan klienter och databaser.  Dessutom krävs hanteringsprocedurer för att hantera shards och hyresgästpopulationen.  Procedurer måste till exempel utformas för att lägga till och ta bort shards och för att flytta klientdata mellan shards.  Ett sätt att skala är att genom att lägga till en ny fragment och fylla den med nya klienter.  Vid andra tillfällen kan du dela upp en tätbefolkad shard i två mindre tätbefolkade shards.  När flera klienter har flyttats eller upphört kan du sammanfoga glesbefolkade shards tillsammans.  Sammanslagningen skulle resultera i mer kostnadseffektiv resursutnyttjande.  Klienter kan också flyttas mellan shards för att balansera arbetsbelastningar.

SQL Database tillhandahåller ett delnings-/kopplingsverktyg som fungerar tillsammans med fragmenteringsbiblioteket och katalogdatabasen.  Den angivna appen kan dela och sammanfoga shards och det kan flytta klientdata mellan shards.  Appen underhåller också katalogen under dessa åtgärder, vilket markerar berörda klienter som offline innan de flyttas.  Efter flytten uppdaterar appen katalogen igen med den nya mappningen och markerar klienten som online igen.

#### <a name="smaller-databases-more-easily-managed"></a>Mindre databaser enklare hanteras

Genom att distribuera klienter över flera databaser resulterar den fragmenterade lösningen för flera innehavare i mindre databaser som är enklare att hantera.  Till exempel innebär att återställa en viss klient till en tidigare tidpunkt nu att återställa en enda mindre databas från en säkerhetskopia, snarare än en större databas som innehåller alla klienter. Databasens storlek och antalet klienter per databas kan väljas för att balansera arbetsbelastningen och hanteringsarbetet.

#### <a name="tenant-identifier-in-the-schema"></a>Klientidentifierare i schemat

Beroende på vilken sharding-metod som används kan ytterligare begränsningar införas för databasschemat.  SQL Database split/merge-programmet kräver att schemat innehåller sharding-nyckeln, som vanligtvis är klientidentifieraren.  Klientidentifieraren är det ledande elementet i primärnyckeln för alla fragmenterade tabeller.  Klientidentifieraren gör det möjligt för delnings-/kopplingsprogrammet att snabbt hitta och flytta data som är associerade med en viss klient.

#### <a name="elastic-pool-for-shards"></a>Elastisk pool för skärvor

Fragmenterade databaser med flera innehavare kan placeras i elastiska pooler.  I allmänhet är det lika kostnadseffektivt att ha många databaser med en klient i en pool som att ha många klienter i några databaser med flera innehavare.  Databaser med flera innehavare är fördelaktiga när det finns ett stort antal relativt inaktiva klienter.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hybrid fragmenterad databasmodell för flera innehavare

I hybridmodellen har alla databaser klientidentifieraren i schemat.  Databaserna kan alla lagra mer än en klient och databaserna kan fragmenteras.  Så i schemat mening, de är alla flera innehavare databaser.  Men i praktiken innehåller vissa av dessa databaser bara en klient.  Oavsett har antalet klienter som lagras i en viss databas ingen effekt på databasschemat.

#### <a name="move-tenants-around"></a>Flytta hyresgäster runt

Du kan när som helst flytta en viss klient till en egen databas med flera innehavare.  Och när som helst kan du ändra dig och flytta tillbaka klienten till en databas som innehåller flera klienter.  Du kan också tilldela en klient till en ny databas med en enda klient när du etablerar den nya databasen.

Hybridmodellen lyser när det finns stora skillnader mellan resursbehoven hos identifierbara grupper av klienter.  Anta till exempel att klienter som deltar i en kostnadsfri utvärderingsversion inte garanteras samma höga prestandanivå som prenumererande klienter är.  Principen kan vara för klienter i den kostnadsfria utvärderingsfasen som ska lagras i en databas med flera innehavare som delas mellan alla kostnadsfria utvärderingsversionsklienter.  När en kostnadsfri utvärderingsversionsklient prenumererar på den grundläggande tjänstnivån kan klienten flyttas till en annan databas med flera innehavare som kan ha färre klienter.  En prenumerant som betalar för premiumtjänstnivån kan flyttas till sin egen nya databas med en enda klient.

#### <a name="pools"></a>Pooler

I den här hybridmodellen kan databaserna för en klient för klienter placeras i resurspooler för att minska databaskostnaderna per klient.  Detta görs också i databasen per klientmodell.

## <a name="i-tenancy-models-compared"></a>I. Hyresmodeller jämfört

I följande tabell sammanfattas skillnaderna mellan de viktigaste hyresmodellerna.

| Mått | Fristående app | Databas per klient | Sharded multi-tenant |
| :---------- | :------------- | :------------------ | :------------------- |
| Skalning | Medel<br />1-100s | Mycket hög <br />1-100 000 | Unlimited<br />1-1 000 000 |
| Isolering av klientorganisation | Mycket hög  | Hög | Låg; med undantag för en enda hyresgäst (som är ensam i en MT db). |
| Databaskostnad per klient | Hög; är dimensionerad för toppar. | Låg; pooler som används. | Lägsta, för små hyresgäster i MT DBs. |
| Övervakning och hantering av prestanda | Endast per klient | Sammanlagt + per klient | Sammanlagt; men är per-hyresgäst endast för singlar. |
| Utvecklingskomplexitet | Låg | Låg | Medium; på grund av skärva. |
| Operativ komplexitet | Låg-hög. Individuellt enkel, komplex i stor skala. | Låg-Medium. Mönster behandlar komplexitet i stor skala. | Låg-hög. Individuell klienthantering är komplex. |
| &nbsp; ||||

## <a name="next-steps"></a>Nästa steg

- [Distribuera och utforska ett Wingtip-program med flera innehavare som använder SaaS-modellen för databas per klient - Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Välkommen till wingtip-biljetternas exempel på SaaS Azure SQL Database-arrendeapp][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Design av fristående app med exakt en databas med en enda klient."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Design av app med flera innehavare med databas per klient."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Design av app med flera innehavare med databas per klient med elastisk pool."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Design av app med flera innehavare med fragmenterade databaser med flera innehavare."


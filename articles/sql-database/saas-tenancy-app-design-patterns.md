---
title: Flera innehavare SaaS-mönstren – Azure SQL Database | Microsoft Docs
description: Läs om krav och vanliga data med flera innehavare programvara som en tjänst (SaaS)-databasprogram som körs i Azure-molnet-miljön.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 6332555c1a176a06004ddfeee513844ad5875c30
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260552"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Flera innehavare SaaS innehavare mönster

Den här artikeln beskrivs olika innehavare modeller för ett SaaS-program.

När du utformar en SaaS-program, måste du noggrant välja modellen innehavare som bäst passar programmets behov.  En modell för innehavare avgör hur varje klient data mappas till lagring.  Ditt val av innehavare modellen påverkar programmets design och hantering.  Växla till en annan modell senare är ibland kostsamma.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS-koncept och terminologi

I programvaran som en tjänst (SaaS)-modell, ditt företag inte säljer *licenser* med din programvara. I stället varje kund gör hyra betalningar till företaget, vilket gör varje kund en *klient* för ditt företag.

Tack vare att de betala hyra varje klient tar emot åtkomst till beroenderelationer SaaS och har data som lagras i SaaS-system.

Termen *innehavare modellen* avser hur klienternas lagrade data ordnas:

- *Single-innehavare:*&nbsp; Varje databas lagrar data från endast en klient.
- *Multitenans:*&nbsp; Varje databas lagrar data från flera olika klienter (med mekanismer för att skydda integriteten).
- Det finns också hybridmodeller för innehavare.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Så här väljer du lämplig innehavare modellen

I allmänhet innehavare modellen påverkar inte funktionen för ett program, men det sannolikt påverkar andra aspekter av den övergripande lösningen.  Följande villkor används för att utvärdera var och en av modeller:

- **Skalbarhet:**
    - Antal klienter.
    - Lagring per-klient.
    - Lagring i samlingen.
    - Arbetsbelastning.

- **Klientisolering:**&nbsp; Dataisolering och prestanda (om en klientarbetsbelastning påverkar andra).

- **Kostnad per klient:**&nbsp; Databaskostnader.

- **Utveckling komplexiteten:**
    - Ändringar i schemat.
    - Ändringar i frågor (krävs av mönstret).

- **Komplexiteten i driften:**
    - Övervaka och hantera prestanda.
    - Schemahantering.
    - Återställa en klient.
    - Haveriberedskap.

- **Anpassningsbarhet:**&nbsp; Enkel stöder schemat anpassningar som är klientspecifika eller klass-specifika-klient.

Innehavare diskussion fokuserar på de *data* lager.  Men hänsyn till vid en tidpunkt då den *program* lager.  Programlagret behandlas som en monolitisk entitet.  Om du delar upp programmet i många små komponenter kan ditt val av innehavare modell ändras.  Du kan hantera vissa komponenter annorlunda än andra om både innehavare och lagringsteknik eller plattform.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Fristående enda klient app med en enda klient-databas

#### <a name="application-level-isolation"></a>Nivån programisolering

I den här modellen har hela programmet installerats upprepade gånger, en gång för varje klient.  Varje instans av appen är en fristående instans, så det aldrig interagerar med en annan instans av fristående.  Varje instans av appen har endast en klient, och därför behöver bara en databas.  Klienten har databas till sig själv.

![Utformningen av fristående app med exakt en enda klient-databas.][image-standalone-app-st-db-111a]

Varje app-instansen har installerats i en separat Azure-resursgrupp.  Resursgruppen kan höra till en prenumeration som ägs av program- eller klienten.  I båda fallen kan leverantören hantera programvaran för klienten.  Varje programinstans har konfigurerats för att ansluta till dess motsvarande databas.

Varje klientdatabas distribueras som en enskild databas.  Den här modellen ger den bästa databas-isoleringen.  Men isoleringen kräver att tillräckligt med resurser allokeras till varje databas för att hantera dess belastningstoppar.  Här är det viktigt att elastiska pooler inte kan användas för databaser som distribuerats i olika resursgrupper eller till olika prenumerationer.  Den här begränsningen gör den här appen för enda klient av fristående modell dyraste lösningen från en övergripande kostnadsperspektiv för databasen.

#### <a name="vendor-management"></a>Hantering av leverantörer

Leverantören kan komma åt alla databaser i alla fristående app-instanser, även om app-instanserna är installerade på en annan klient prenumerationer.  Åtkomst uppnås via SQL-anslutningar.  Cross-instans åtkomst kan aktivera leverantören att centralisera schemahantering och databasöverskridande frågor för rapportering eller i analytics.  Om den här typen av centraliserad hantering är det önskade kan måste en katalog distribueras som mappar klient-ID: n till databasen URI: er.  Azure SQL Database innehåller ett bibliotek för horisontell partitionering som används tillsammans med en SQL-databas för att tillhandahålla en katalog.  Horisontell partitionering biblioteket heter formellt den [klientbibliotek för elastiska databaser][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. App för flera klienter med databas-per-klient

Det här nästa mönstret använder ett program med flera innehavare med många databaser som alla databaser för enstaka klientorganisationer.  En ny databas etableras för varje ny klient.  Programnivån skalas *upp* lodrätt genom att lägga till fler resurser per nod.  Eller appen skalas *ut* horisontellt genom att lägga till fler noder.  Att skala baserat på arbetsbelastningen och är oberoende av antalet eller skala för individuella databaser.

![Utformningen av app för flera klienter med databas-per-klient.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Anpassa för en klient

Som fristående app ger användning av en enda klient databaser stark klientisolering.  I alla appar vars modellen anger en enda klient-databaser, kan att schemat innehåller alla en viss databas anpassas och optimerade för dess klient.  Den här anpassningen påverkar inte andra klienter i appen. En klient kan kanske behöver data utöver de grundläggande datafält som alla klienter behöver.  Dessutom behöva extra datafältet ett index.

Med databas-per-klient är det enkelt att uppnå att anpassa schemat för en eller flera enskilda klienter.  Programvaruleverantören måste utforma procedurer noggrant hantera schemat anpassningar i stor skala.

#### <a name="elastic-pools"></a>Elastiska pooler

När databaser distribueras i samma resursgrupp, kan du gruppera dem i elastiska pooler.  Poolerna ger ett prisvärt sätt att dela resurser över flera databaser.  Det här alternativet om poolen är billigare än att kräva att varje databas ska vara tillräckligt stor för att hantera användningstopparna som det inträffar.  Även om pooldatabaser delar åtkomst till resurser kan de fortfarande uppnå en hög grad av isolering av prestandan.

![Utformningen av app för flera klienter med databas-per-klient, med hjälp av elastisk pool.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL Database innehåller verktyg som behövs för att konfigurera, övervaka och hantera delning.  Båda pool på servernivå och databasnivå prestandamått är tillgängliga i Azure-portalen och via Azure Monitor-loggar.  Mått kan ge bra insikter om sammanställd och klientspecifik prestanda.  Enskilda databaser kan flyttas mellan pooler för att tillhandahålla reserverade resurser för en specifik klient.  Dessa verktyg kan du säkerställa bra prestanda på ett kostnadseffektivt sätt.

#### <a name="operations-scale-for-database-per-tenant"></a>Åtgärder som skalas för databas-per-klient

Azure SQL Database-plattformen har många hanteringsfunktioner som är utformad för att hantera många databaser i stor skala, till exempel bra över 100 000 databaser.  Dessa funktioner gör det rimligt att mönstret databas-per-klient.

Anta exempelvis att ett system har en 1000 klientdatabas som endast en-databas.  Databasen kan ha 20 index.  Om systemet konverterar till med 1000 enda klient databaser, ökar antalet index till 20 000.  I SQL-databas som en del av [automatisk justering][docu-sql-db-automatic-tuning-771a], automatisk indexering funktioner är aktiverade som standard.  Automatisk indexering hanterar du alla 20 000 index och deras pågående skapa och släpp-optimering.  Dessa automatiserade åtgärder som inträffar inom en individuell databas, och de inte samordnas eller begränsad av liknande åtgärder i andra databaser.  Automatisk indexering behandlar index på olika sätt i en upptagen databas än i en mindre upptagen databas.  Den här typen av index management anpassning skulle vara opraktiskt i databas-per-klient-skala om den här enorm hanteringsåtgärden var tvungen att göras manuellt.

Andra funktioner som skalbarheten inkluderar följande:

- Inbyggd säkerhetskopiering.
- Hög tillgänglighet.
- Kryptering på disken.
- Telemetri om prestanda.

#### <a name="automation"></a>Automation

Vilka hanteringsåtgärder de kan användas av skriptet och erbjuds via en [devops] [ http-visual-studio-devops-485m] modell.  Åtgärderna kan även automatisk och visas i programmet.

Exempelvis kan du automatisera återställning av en enda klient till en tidigare tidpunkt.  Återställningen behöver bara återställa en enda klient-databasen som lagrar klienten.  Den här återställningen har ingen inverkan på andra klienter som bekräftar att hanteringsåtgärder är på noggrant detaljerad nivå av varje enskild klientorganisation.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. App för flera klienter med databaser för flera innehavare

En annan tillgänglig mönster är att lagra många klienter i en databas för flera innehavare.  Programinstansen kan ha valfritt antal databaser för flera innehavare.  Schemat för en databas för flera klienter måste ha en eller flera klient-ID kolumner så att data från en viss klient kan hämtas selektivt.  Schemat kan dessutom kräva några tabeller eller kolumner som används av endast en delmängd av klienter.  Statisk kod och data lagras bara en gång och delas av alla klienter.

#### <a name="tenant-isolation-is-sacrificed"></a>Klientisolering bort

*Data:*&nbsp; En databas för flera innehavare offrar nödvändigtvis klientisolering.  Data för flera klienter lagras tillsammans i en databas.  Se till att frågor aldrig exponera data från fler än en klient under utvecklingen.  SQL Database stöder [säkerhet på radnivå][docu-sql-svr-db-row-level-security-947w], som kan tillämpa dessa data som returnerats från en fråga vara begränsad till en enda klient.

*Bearbetar:*&nbsp; En databas för flera klienter delar resurser för beräkning och lagring över alla klienter.  Databasen som helhet kan övervakas för att se till att den fungerar bra.  Azure-systemet har dock inget inbyggt sätt att övervaka eller hantera användningen av dessa resurser genom att en enskild klientorganisation.  Databas för flera innehavare innebär därför löper ökad risk för bort störande grannar, där arbetsbelastningen på en overactive klientorganisation påverkar prestandaupplevelse med andra klienter i samma databas.  Ytterligare programnivå övervakning kan övervaka på klientnivå prestanda.

#### <a name="lower-cost"></a>Lägre kostnader

I allmänhet har flera klientdatabaser den lägsta per-klient kostnad.  Resurskostnader för en enskild databas är lägre än för en equivalently storlekar elastisk pool.  Dessutom för scenarier där klienter behöver endast begränsad lagring, kan potentiellt miljontals klienter lagras i en enskild databas.  Ingen elastisk pool kan innehålla miljontals databaser.  En lösning som innehåller 1000 databaser per pool, med 1000 pooler kan dock nå skala av flera miljoner dess risk bli svårhanterlig att hantera.

Två varianter av en databas för flera innehavare modell diskuteras vad följer med delat modell för flera klienter som är den mest flexibla och skalbara.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. App för flera klienter med en enda databas för flera innehavare

Den enklaste mönstret för databas för flera klienter använder en enkel databas som värd för data för alla klienter.  När fler klienter läggs skalas databasen upp med mer resurser för lagring och beräkning.  Den här att skala upp kan vara allt som krävs, även om det finns alltid en ultimate skalningsgränsen.  Långa innan den gränsen har uppnåtts databasen blir dock svårhanterligt att hantera.

Hanteringsåtgärder som fokuserar på enskilda klienter är mer komplexa för att implementera i en databas för flera innehavare.  Och i stor skala kan dessa åtgärder bli oacceptabelt långsamt.  Ett exempel är en point-in-time-återställning av data för en klient.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. App för flera klienter med shardade databaser för flera innehavare

De flesta SaaS-program komma åt data för endast en klient i taget.  Det här åtkomstmönstret tillåter klientdata sprids över flera databaser eller shards, där alla data för en klient finns i en shard.  I kombination med ett mönster för databas för flera innehavare, kan en shardad modell nästan obegränsad skalning.

![Utformningen av app för flera klienter med shardade databaser för flera innehavare.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Hantera fragment

Horisontell partitionering innebär en mer komplicerad både konstruktion och drifthantering.  En katalog krävs för att underhålla mappningen mellan klienter och databaser.  Hantering av procedurer är dessutom krävs för att hantera shards och klient-befolkning.  Procedurer måste till exempel utformas för att lägga till och ta bort fragment och flyttas klientdata mellan shards.  Ett sätt att skala är att genom att lägga till en ny shard och fylla den med nya klienter.  Ibland kan du dela ett tätbefolkade fragment i två mindre tätt ifyllt shards.  När flera klienter har flyttats eller upphöra, kan du slå samman sparsamt ifyllda shards tillsammans.  Kopplingen skulle resultera i mer kostnadseffektiv resursutnyttjande.  Klienter kan också flyttas mellan shards vill jämna ut arbetsbelastningen.

SQL Database ger ett verktyg för dela/sammanslå som fungerar tillsammans med horisontell partitionering-bibliotek och katalogdatabasen.  Dela upp den angivna appen och sammanfoga shards och det kan flytta klientdata mellan fragment.  Appen har också enhetsspecifika katalogen under åtgärderna, markera påverkas klienter som offline innan flyttas.  Efter överflyttningen uppdaterar appen katalogen igen med en ny mappning och markera klienten som online igen.

#### <a name="smaller-databases-more-easily-managed"></a>Mindre databaser mer enkelt hanteras

Genom att distribuera klienter över flera databaser, shardade lösning för flera innehavare som resulterar i mindre databaser som är enklare att hantera.  Till exempel innebär återställning av en specifik klient till en tidigare punkt i tiden nu att du återställer en enda mindre databas från en säkerhetskopia i stället för en större databas som innehåller alla klienter. Databasens storlek och antalet klienter per databas, kan väljas för att balansera arbetsbelastningen och arbete för.

#### <a name="tenant-identifier-in-the-schema"></a>Klient-ID i schemat

Ytterligare begränsningar kan införas på databasschemat beroende på den horisontell partitionering-metod som används.  SQL Database dela/sammanslå programmet kräver att schemat innehåller nyckeln för horisontell partitionering, vilket normalt är klient-ID.  Klient-ID är ledande elementet i den primära nyckeln i alla tabeller för shardade.  Klient-ID gör att dela/sammanslå-programmet att snabbt hitta och flytta data som hör till en specifik klient.

#### <a name="elastic-pool-for-shards"></a>Elastisk pool för shard

Shardade databaser för flera innehavare kan placeras i elastiska pooler.  I allmänhet är att ha många enda klient databaser i poolen som kostnadseffektivt alternativ har många hyresgäster i några få databaser för flera innehavare.  Databaser för flera innehavare är bra när det finns ett stort antal relativt inaktiva klienter.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hybridmodell fragmenterade (sharded) databas för flera innehavare

I hybrid-modellen har alla databaser klient-ID i sina schemat.  Databaserna är alla kan lagra fler än en klient och databaserna kan vara fragmenterade (sharded).  Så att de är alla databaser för flera innehavare i schema-mening.  I praktiken kan vissa av dessa databaser ännu innehåller endast en klient.  Oavsett har hur många klienter som lagras i en viss databas ingen effekt på databasschemat.

#### <a name="move-tenants-around"></a>Flytta klienter

När som helst kan du flytta en viss klient till en egen databas för flera innehavare.  Och när som helst och du ändrar dig och flytta klienten tillbaka till en databas som innehåller flera klienter.  Du kan också tilldela en klient till ny enda klient databas när du etablerar den nya databasen.

Hybridmodell som bäst när det finns stora skillnader mellan resursbehov för identifierbar grupper av klienter.  Anta att klienter deltar i en kostnadsfri utvärderingsversion inte är garanterat samma hög nivå av prestanda som prenumererande klienter.  Principen kan vara för klienter i den kostnadsfria utvärderingsversionen fasen ska lagras i en databas för flera innehavare som delas mellan alla de kostnadsfria utvärderingsversioner för klienterna.  När en kostnadsfri utvärderingsversion klientorganisationen prenumererar på basic tjänstnivån, kan klienten flyttas till en annan databas för flera klienter som kan ha färre klienter.  En prenumerant som betalar för premium-tjänstnivån kunde flyttas till en egen ny enda klient-databas.

#### <a name="pools"></a>Pooler

I den här hybridmodell placeras enda klient-databaserna för prenumeranten klienter i resurspooler för att minska kostnaderna för databas per klient.  Detta görs även i databas-per-klient-modellen.

## <a name="i-tenancy-models-compared"></a>I. Innehavare modeller jämfört med

I följande tabell sammanfattas skillnaderna mellan de huvudsakliga innehavare modellerna.

| Mått | Fristående app | Databas-per-klient | Shardade flera innehavare |
| :---------- | :------------- | :------------------ | :------------------- |
| Skala | Medel<br />1-100-tal | Mycket hög<br />100 1-000-tal | Obegränsat<br />1-1,000,000s |
| Isolering av innehavare | Mycket hög | Hög | Låg; Förutom alla enskild klient (det vill säga enbart i en MT-db). |
| Databaskostnaden per klient | Höga. storlek toppar. | Låg; pooler som använts. | Lägsta för små klienter i MT-databaser. |
| Prestandaövervakning och hantering | Per-klient | Aggregering + per klient | Aggregera; även om är per klient endast för singel. |
| Utveckling komplexitet | Låg | Låg | Medel; på grund av horisontell partitionering. |
| Komplexiteten i driften | Låg och hög. Individuellt enkla, komplexa i stor skala. | Låg-medel. Mönster adress komplexiteten i stor skala. | Låg och hög. Enskilda klient-hantering är komplex. |
| &nbsp; ||||

## <a name="next-steps"></a>Nästa steg

- [Distribuera och utforska en Wingtip-programmet för flera klienter som använder modellen databas-per-klient SaaS - Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Välkommen till Wingtip biljetter SaaS Azure SQL Database innehavare exempelappen][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Utformningen av fristående app med exakt en enda klient-databas."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Utformningen av app för flera klienter med databas-per-klient."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Utformningen av app för flera klienter med databas-per-klient, med hjälp av elastisk pool."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Utformningen av app för flera klienter med shardade databaser för flera innehavare."


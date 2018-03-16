---
title: Hantera databaser som skalats ut molnet | Microsoft Docs
description: "Använda tjänsten för elastisk databas jobb för att köra ett skript på en grupp databaser."
metakeywords: azure sql database elastic databases
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: 1f0e022eb98619b22f7e62e0602d36ba821def22
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="managing-scaled-out-cloud-databases"></a>Hantera databaser som skalats ut moln
Att hantera utskalat delat databaser, den **elastisk databas jobb** funktionen (förhandsversion) kan användas att köra ett skript för Transact-SQL (T-SQL) på ett tillförlitligt sätt i en grupp av databaser, inklusive:

* ett egendefinierat mängd databaser (beskrivs nedan)
* alla databaser i en [elastisk pool](sql-database-elastic-pool.md)
* en uppsättning Fragmentera (skapat med hjälp av [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md)). 

## <a name="documentation"></a>Dokumentation
* [Installera komponenter för elastisk databas jobbet](sql-database-elastic-jobs-service-installation.md). 
* [Kom igång med elastisk databas jobb](sql-database-elastic-jobs-getting-started.md).
* [Skapa och hantera jobb med hjälp av PowerShell](sql-database-elastic-jobs-powershell.md).
* [Skapa och hantera skaländras ut Azure SQL-databaser](sql-database-elastic-jobs-getting-started.md)

**Den elastiska databasen jobb** är för närvarande en kund-värdbaserad Azure molnbaserad tjänst som möjliggör körning av ad hoc- och schemalagda administrativa uppgifter, som kallas **jobb**. Med jobb kan enkelt och tillförlitligt sätt hantera stora grupper med Azure SQL-databaser genom att köra Transact-SQL-skript för att utföra administrativa åtgärder. 

![Jobb-tjänsten för elastisk databas][1]

## <a name="why-use-jobs"></a>Varför använda jobb?
**Hantera**

Enkelt göra schemaändringar, hantering av autentiseringsuppgifter, referens uppdateringar, insamling av prestandadata eller klient (kunden) telemetri samling.

**Rapporter**

Samla in data från en samling av Azure SQL-databaser till en enda måltabell.

**Minska omkostnader**

Vanligtvis måste du ansluta till varje databas separat för att köra Transact-SQL-uttryck eller utföra andra administrativa uppgifter. Ett jobb hanterar uppgiften att logga in på varje databas i målgruppen. Du också definiera, underhålla och bevara Transact-SQL-skript som ska köras i en grupp med Azure SQL-databaser.

**Redovisning**

Jobb kör skript och status för körning för varje databas. Du kan också få automatiska försök igen när fel uppstår.

**Flexibilitet**

Definiera anpassade grupper med Azure SQL-databaser och definiera scheman för att köra ett jobb.

> [!NOTE]
> Minskad uppsättning funktioner som är begränsad till SQL Azure elastiska pooler är tillgänglig i Azure-portalen. Använd PowerShell APIs för att komma åt en fullständig uppsättning aktuella funktionalitet.
> 
> 

## <a name="applications"></a>Program
* Utföra administrativa uppgifter, till exempel att distribuera ett nytt schema.
* Uppdatera data produkten Referensinformation vanliga över alla databaser. Eller scheman automatiska uppdateringar varje veckodag efter timmar.
* Återskapa index för att förbättra prestanda för frågor. Den återskapa kan konfigureras för att köra i en samling databaser med jämna mellanrum, som vid låg belastning.
* Samla in frågeresultat från en uppsättning databaser till en central tabell på grundval av pågående. Prestandafrågor kan köras kontinuerligt och konfigurerat för att utlösaren ytterligare åtgärder ska utföras.
* Utför längre databearbetning frågor som körs i ett stort utbud av databaser, till exempel insamling av kunden telemetri. Samlas in resultaten till en enda måltabell för ytterligare analys.

## <a name="elastic-database-jobs-end-to-end"></a>Den elastiska databasen jobb: slutpunkt till slutpunkt
1. Installera den **elastisk databas jobb** komponenter. Mer information finns i [installerar elastisk databas jobb](sql-database-elastic-jobs-service-installation.md). Om installationen misslyckas, se [avinstallera](sql-database-elastic-jobs-uninstall.md).
2. Använd PowerShell APIs för att få åtkomst till fler funktioner, till exempel skapa anpassade databasen samlingar, lägga till scheman och/eller samla in resultatet anger. Använda portalen för enkel installation och skapa/övervakning av jobb som är begränsad till körning mot en **elastisk pool**. 
3. Skapa krypterade autentiseringsuppgifter för jobbkörningen och [lägga till användare (eller rollen) i varje databas i gruppen](sql-database-security-overview.md).
4. Skapa en idempotent T-SQL-skript som kan köras mot varje databas i gruppen. 
5. Följ dessa steg för att skapa jobb med hjälp av Azure portal: [skapa och hantera jobb för elastisk databas](sql-database-elastic-jobs-create-and-manage.md). 
6. Eller Använd PowerShell-skript: [skapa och hantera en SQL-databas för elastisk databas jobb med hjälp av PowerShell (förhandsgranskning)](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Idempotent skript
Skripten måste vara [idempotent](https://en.wikipedia.org/wiki/Idempotence). Enkelt uttryckt innebär ”idempotent” om skriptet lyckas och kör igen, sker samma resultat. Ett skript kan misslyckas på grund av tillfälliga nätverksproblem. I så fall jobbet kommer automatiskt att försöka igen och kör skriptet ett förinställt antal gånger innan desisting. Ett skript för idempotent har samma resultat även om har utförts två gånger. 

En enkel skräpposten är att testa om finns ett objekt innan du skapar den.  

    IF NOT EXIST (some_object)
    -- Create the object 
    -- If it exists, drop the object before recreating it.

På liknande sätt kan ett skript måste kunna köras genom att logiskt och motverka eventuella villkor som hittas.

## <a name="failures-and-logs"></a>Fel och loggar
Om ett skript misslyckas efter flera försök jobbet loggas felet och fortsätter. När ett jobb har upphört kan (vilket innebär en körning mot alla databaser i gruppen) du kontrollera listan över misslyckade försök. Loggarna ger information om du vill felsöka felaktiga skript. 

## <a name="group-types-and-creation"></a>Gruppen typer och skapa
Det finns två typer av grupper: 

1. Fragmentera uppsättningar
2. Anpassade grupper

Fragmentera uppsättning grupper skapas med hjälp av den [elastisk Databasverktyg](sql-database-elastic-scale-introduction.md). När du skapar en Fragmentera set grupp databaser läggs till eller tas bort från gruppen automatiskt. Till exempel blir en ny Fragmentera automatiskt i gruppen när du lägger till den Fragmentera kartan. Sedan kan du köra ett jobb mot gruppen.

Anpassade grupper å andra sidan definieras strikt. Du måste uttryckligen lägga till eller ta bort databaser från anpassade grupper. Om en databas i gruppen avbryts att jobbet försöka köra skriptet mot databasen, vilket resulterar i ett eventuella fel. Grupper som skapats med Azure-portalen för närvarande är anpassade grupper. 

## <a name="components-and-pricing"></a>Komponenter och prissättning
Följande komponenter fungerar tillsammans för att skapa en Azure-molntjänst som aktiverar ad hoc-körning av administrativa jobb. Komponenterna installeras och konfigureras automatiskt under installationen, i din prenumeration. Du kan identifiera tjänsterna som alla har samma automatiskt genererat namn. Namnet är unikt och består av prefixet ”edj” följt av 21 slumpmässigt genererat tecken.

* **Molntjänsten Azure**: elastisk databas jobb (förhandsgranskning) levereras som en kund-Azure-molnet värdtjänsten för körning av de begärda åtgärderna. Från portalen, tjänsten distribueras och hanteras i Microsoft Azure-prenumerationen. Standard distribuerade tjänsten körs med minst två arbetsroller för hög tillgänglighet. Standardstorleken för varje worker-rollen (ElasticDatabaseJobWorker) körs på en A0-instans. Priser, finns i [molntjänster priser](https://azure.microsoft.com/pricing/details/cloud-services/). 
* **Azure SQL Database**: tjänsten använder Azure SQL-databas kallas den **databasen** att lagra alla jobb metadata. Standard-tjänstnivå är en S0. Priser, finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
* **Azure Service Bus**: ett Azure Service Bus är för samordning av arbetet inom Azure Cloud Service. Se [Service Bus priser](https://azure.microsoft.com/pricing/details/service-bus/).
* **Azure Storage**: ett Azure Storage-konto används för att lagra diagnostikdata loggning i händelse av att ett problem som kräver ytterligare felsökning (se [aktiverar diagnostik i Azure-molntjänster och virtuella datorer](../cloud-services/cloud-services-dotnet-diagnostics.md)). Priser, finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Så här fungerar jobb för elastisk databas
1. En Azure SQL Database är tilldelad en **databasen** som lagrar alla data för metadata och tillstånd.
2. Kontrollen databasen används av den **jobbet service** att starta och spåra jobb ska köras.
3. Två olika roller kommunicerar med databasen kontrollen: 
   * Domänkontrollant: Avgör vilka jobb som kräver åtgärder att utföra det begärda jobbet och försök misslyckade jobb genom att skapa nya jobbuppgifter.
   * Jobb för körning av aktiviteten: Utför uppgifter i jobbet.

### <a name="job-task-types"></a>Uppgiften jobbtyper
Det finns flera typer av jobbuppgifter som utför körningen av jobb:

* ShardMapRefresh: Frågar Fragmentera kartan för att fastställa alla databaser som används som delar
* ScriptSplit: Delar skriptet över 'Gå'-instruktioner i batchar
* ExpandJob: Skapar underordnade jobb för varje databas från ett jobb som riktar sig till en grupp databaser
* ScriptExecution: Kör ett skript mot en viss databas med angivna autentiseringsuppgifter
* Dacpac: Gäller en DACPAC för en viss databas med viss autentiseringsuppgifter

## <a name="end-to-end-job-execution-work-flow"></a>Jobb för slutpunkt till slutpunkt-arbetsflöde för körning
1. Med portalen eller PowerShell-API kan infogas ett jobb i den **databasen**. Jobbet begär körningen av en Transact-SQL-skript mot en grupp databaser med hjälp av autentiseringsuppgifter.
2. Styrenheten identifierar det nya projektet. Jobbuppgifter skapas och körs för att dela skriptet och uppdatera gruppens databaser. Till sist skapas ett nytt jobb skapas och körs för att expandera jobbet och skapa nya underordnade jobb där varje underordnad jobb har angetts att köra Transact-SQL-skript mot en individuell databas i gruppen.
3. Styrenheten identifierar skapade underordnade jobb. För varje jobb styrenheten skapar och utlöser en projektaktivitet för att köra skriptet mot en databas. 
4. När alla aktiviteter i jobbet har slutförts, uppdaterar styrenheten jobben till slutfört tillstånd. 
   När som helst under jobbkörningen kan PowerShell API användas för att visa aktuell status för jobbkörningen. Alla tider som returneras av PowerShell APIs representeras i UTC. Om du vill kan du initiera en begäran om att avbryta om du vill stoppa ett jobb. 

## <a name="next-steps"></a>Nästa steg
[Installera komponenter](sql-database-elastic-jobs-service-installation.md), sedan [skapa och Lägg till en logg i varje databas i gruppen av databaser](sql-database-manage-logins.md). Mer information om jobbet skapande och hantering finns [skapa och hantera jobb för elastisk databas](sql-database-elastic-jobs-create-and-manage.md). Se även [komma igång med elastisk databas jobb](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->



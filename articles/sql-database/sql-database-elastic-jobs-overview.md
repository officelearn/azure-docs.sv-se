---
title: Hantera utskalade molndatabaser | Microsoft Docs
description: Använda elastiska jobb databastjänsten för att köra ett skript för en grupp med databaser.
metakeywords: azure sql database elastic databases
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 06/14/2018
ms.author: sstein
ms.openlocfilehash: 21cd1b171f25d5abdaa89f631ac5bab8e4a351af
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968888"
---
# <a name="managing-scaled-out-cloud-databases"></a>Hantera utskalade molndatabaser

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]

**Elastic Database-jobb** är en kund-värdbaserade Azure-molntjänst som möjliggör körning av ad hoc och schemalagda administrativa uppgifter, som kallas **jobb**. Med jobb, du kan enkelt och tillförlitligt sätt hantera stora grupper med Azure SQL-databaser genom att köra Transact-SQL-skript för att utföra administrativa åtgärder. 

Hantera utskalade shardade databaser, den **elastiska databasjobb** funktionen (förhandsversion) kan du tillförlitligt köra ett skript för Transact-SQL (T-SQL) i en grupp med databaser, bland annat:

* ett egendefinierat samling databaser (beskrivs nedan)
* alla databaser i en [elastisk pool](sql-database-elastic-pool.md)
* en uppsättning fragment (skapats med hjälp av [Elastic Database-klientbiblioteket](sql-database-elastic-database-client-library.md)). 

## <a name="documentation"></a>Dokumentation
* [Installera elastiska jobb](sql-database-elastic-jobs-service-installation.md). 
* [Kom igång med elastiska databasjobb](sql-database-elastic-jobs-getting-started.md).
* [Skapa och hantera jobb med hjälp av PowerShell](sql-database-elastic-jobs-powershell.md).
* [Skapa och hantera utskalade Azure SQL-databaser](sql-database-elastic-jobs-getting-started.md)



![Elastiska jobb databastjänst][1]

## <a name="why-use-jobs"></a>Varför använda jobb?
**Hantera**

Enkelt gör schemaändringar, hantering av autentiseringsuppgifter, uppdateringar av referensdata, insamling av prestandadata eller telemetriinsamling för klient (kund).

**Rapporter**

Sammanställ data från en samling Azure SQL-databaser till en enskild måltabell.

**Minska omkostnaderna**

Vanligtvis måste du ansluta till varje databas separat för att köra Transact-SQL-instruktioner eller utföra andra administrativa uppgifter. Ett jobb hanterar uppgiften att logga in på varje databas i målgruppen. Du definierar, underhåller och bevarar även Transact-SQL-skript som ska köras över en grupp Azure SQL-databaser.

**Redovisning**

Jobb kör skript och status för körning för varje databas. Du kan även få automatiska försök på nytt när fel uppstår.

**Flexibilitet**

Definiera anpassade grupper av Azure SQL-databaser och definiera scheman för att köra ett jobb.

> [!NOTE]
> Reducerad uppsättning funktioner som är begränsad till SQL Azure elastiska pooler finns i Azure-portalen. Använd PowerShell APIs för att få åtkomst till den fullständiga uppsättningen aktuella funktionerna.
> 
> 

## <a name="applications"></a>Program
* Utföra administrativa uppgifter, till exempel att distribuera ett nytt schema.
* Uppdatera data produkten Referensinformation vanliga över alla databaser. Eller scheman för automatiska uppdateringar varje veckodag efter timmar.
* Bygg om index för att förbättra frågeprestandan. De rebuilding kan konfigureras för att köra över en uppsättning databaser med jämna mellanrum, till exempel vid låg belastning.
* Samla in frågeresultat från en uppsättning databaser till en central tabell regelbundet. Prestandafrågor kan kontinuerligt köras och konfigureras för att utlösa ytterligare åtgärder som ska utföras.
* Kör databearbetningsfrågor med längre körningstid över en stor uppsättning databaser, till exempel insamling av kundtelemetri. Resultaten samlas till en enskild måltabell för ytterligare analys.

## <a name="elastic-database-jobs-end-to-end"></a>Elastic Database-jobb: slutpunkt till slutpunkt
1. Installera den **elastiska databasjobb** komponenter. Mer information finns i [installerar elastiska databasjobb](sql-database-elastic-jobs-service-installation.md). Om installationen misslyckas, finns i [så här avinstallerar du](sql-database-elastic-jobs-uninstall.md).
2. Använd PowerShell APIs för att få åtkomst till fler funktioner, till exempel skapa egendefinierat databassamlingar, att lägga till scheman och/eller samla in resultatet anger. Använda portalen för enkel installation och skapa/övervakning av jobb som är begränsad till körning mot en **elastisk pool**. 
3. Skapa krypterade autentiseringsuppgifter för jobbkörning och [lägga till användare (eller rollen) i varje databas i gruppen](sql-database-security-overview.md).
4. Skapa en idempotent T-SQL-skript som kan köras mot varje databas i gruppen. 
5. Följ dessa steg för att skapa jobb med hjälp av Azure portal: [skapa och hantera elastiska databasjobb](sql-database-elastic-jobs-create-and-manage.md). 
6. Eller Använd PowerShell-skript: [skapa och hantera en SQL Database elastic database-jobb med hjälp av PowerShell (förhandsversion)](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Idempotent-skript
Skripten måste vara [idempotenta](https://en.wikipedia.org/wiki/Idempotence). Enkelt uttryckt innebär ”idempotenta” om skriptet lyckas, och den körs igen, sker detta. Ett skript kan misslyckas på grund av tillfälliga nätverksproblem. I så fall försöker jobbet automatiskt köra skriptet på nytt ett förinställt antal gånger innan det ger upp. Ett skript för idempotent har samma resultat även om har körts utan problem två gånger. 

En enkel metod är att testa om ett objekt finns innan det skapas.  

    IF NOT EXIST (some_object)
    -- Create the object 
    -- If it exists, drop the object before recreating it.

På liknande sätt måste ett skript kunna lyckas köra genom att logiskt testa och motverka eventuella tillstånd som det hittar.

## <a name="failures-and-logs"></a>Fel och loggar
Om ett skript som misslyckas efter flera försök, loggas felet i jobbet och fortsätter. När ett jobb har upphört kan (dvs. en körning mot alla databaser i gruppen), du kontrollera listan över misslyckade försök. Loggarna ger information om du vill felsöka felaktiga skript. 

## <a name="group-types-and-creation"></a>Typer av gruppen och skapa
Det finns två typer av grupper: 

1. Shard uppsättningar
2. Anpassade grupper

Shard uppsättning grupper skapas med hjälp av den [elastiska Databasverktyg](sql-database-elastic-scale-introduction.md). När du skapar en shard set grupp databaser läggs till eller tas bort från gruppen automatiskt. Till exempel blir en ny shard automatiskt i gruppen när du lägger till i fragmentkartan. Ett jobb kan sedan köras mot gruppen.

Anpassade grupper, å andra sidan definieras strikt. Du måste uttryckligen lägga till eller ta bort databaser från anpassade grupper. Om en databas i gruppen bryts försöker jobbet köra skriptet mot databasen vilket resulterar i en motorfel. Grupper som skapats med hjälp av Azure-portalen för närvarande är anpassade grupper. 

## <a name="components-and-pricing"></a>Komponenter och priser
Följande komponenter fungerar tillsammans för att skapa en Azure-molntjänst som gör det möjligt för ad hoc-körning av administrativa jobb. Komponenterna installeras och konfigureras automatiskt under installationen, i din prenumeration. Du kan identifiera tjänsterna som alla har samma automatiskt genererade namn. Namnet är unikt och består av prefixet ”edj” följt av 21 slumpmässigt genererat tecken.

* **Molntjänsten Azure**: elastic database-jobb (förhandsversion) levereras som en kund är värd Azure molntjänst för körning av de begärda aktiviteterna. Från portalen kan tjänsten distribueras och finns i Microsoft Azure-prenumerationen. Standard distribuerat service körs med minst två arbetsroller för hög tillgänglighet. Standardstorleken för varje worker-roll (ElasticDatabaseJobWorker) körs på en A0-instans. Om priser finns i [prissättning för Cloud services](https://azure.microsoft.com/pricing/details/cloud-services/). 
* **Azure SQL Database**: tjänsten använder en Azure SQL-databas som kallas den **kontrolldatabas** att lagra alla jobbmetadata. Standard-tjänstnivå är en S0. Om priser finns i [priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
* **Azure Service Bus**: An Azure Service Bus är för samordning av arbetet i Azure-molntjänst. Se [prisinformation om Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).
* **Azure Storage**: ett Azure Storage-konto som används för att lagra diagnostikdata loggning i händelse av att ett problem måste vidare felsökning (se [hur du aktiverar diagnostik i Azure Cloud Services och virtuella datorer](../cloud-services/cloud-services-dotnet-diagnostics.md)). Om priser finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Så här fungerar Elastic Database-jobb
1. En Azure SQL Database anges en **kontrolldatabas** som lagrar alla data för metadata och tillstånd.
2. Control-databasen används av den **tjänst** att starta och spåra jobb att köra.
3. Två olika roller kommunicerar med kontroll-databasen: 
   * Domänkontrollant: Avgör vilka jobb som kräver åtgärder att utföra det begärda jobbet och återförsök misslyckade jobb genom att skapa nya aktiviteter i jobbet.
   * Jobbaktiviteter: Utför jobbaktiviteterna.

### <a name="job-task-types"></a>Uppgiften jobbtyper
Det finns flera typer av jobbuppgifter som utför körningen av jobb:

* ShardMapRefresh: Frågar fragmentkartan för att fastställa alla databaser som används som shards
* ScriptSplit: Delar upp skriptet på ”Gå”-satser i batchar
* ExpandJob: Skapar underordnat jobb för varje databas från ett jobb som riktar sig mot en grupp med databaser
* ScriptExecution: Kör ett skript mot en viss databas med definierade autentiseringsuppgifter
* Dacpac: Gäller en DACPAC för en viss databas med viss autentiseringsuppgifter

## <a name="end-to-end-job-execution-work-flow"></a>Jobb för slutpunkt till slutpunkt-arbetsflödet för körning
1. Med hjälp av portalen eller PowerShell API: et, ett jobb har satts in i **kontrolldatabas**. Jobbet begär körningen av en Transact-SQL-skript mot en grupp med databaser med specifika autentiseringsuppgifter.
2. Kontrollanten identifierar det nya projektet. Jobbuppgifter skapas och körs att dela upp skriptet och uppdatera gruppens databaser. Till sist skapas ett nytt jobb och köras för att expandera jobbet och skapa nya underordnade jobb där varje underordnat jobb har angetts att köra Transact-SQL-skript mot en individuell databas i gruppen.
3. Kontrollanten identifierar det skapa underordnade jobbet. För varje jobb kontrollanten skapar och utlöser en projektaktivitet för att exekvera skript mot en databas. 
4. När alla aktiviteter i jobbet har slutförts, uppdaterar kontrollanten jobb till slutfört tillstånd. 
   När som helst under jobbkörningen kan PowerShell API: et användas för att visa det aktuella tillståndet för jobbkörningen. Hela tiden som returneras av PowerShell APIs representeras i UTC. Om du vill kan kan en avbrottsbegäran initieras för att stoppa ett jobb. 

## <a name="next-steps"></a>Nästa steg
[Installera komponenterna](sql-database-elastic-jobs-service-installation.md), sedan [skapa och Lägg till en logg i varje databas i grupp med databaser](sql-database-manage-logins.md). Mer information om jobbskapande och hantering finns i [skapa och hantera elastiska databasjobb](sql-database-elastic-jobs-create-and-manage.md). Se även [komma igång med elastiska databasjobb](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->



<properties
    pageTitle="SQL Database prestanda och alternativ: Tjänstnivåer | Microsoft Azure"
    description="Jämför SQL Database-funktioner för prestanda och verksamhetskontinuitet för de olika tjänstnivåerna för att balansera kostnad och kapacitet när du skalar."
    keywords="database options,database performance"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="05/13/2016"
    ms.author="carlrab"/>

# SQL Database-alternativ och prestanda: Förstå vad varje tjänstnivå erbjuder

[Azure SQL Database](sql-database-technical-overview.md) har flera tjänstnivåer för att hantera olika arbetsbelastningar. Du kan ändra tjänstnivå när som helst utan avbrott för ditt program. Du kan också [skapa en enskild databas](sql-database-get-started.md) med definierade egenskaper och prissättning. Eller så kan du hantera flera databaser genom att [skapa en elastisk databaspool](sql-database-elastic-pool-create-portal.md). I båda fallen inkluderar det nivåerna **Basic**, **Standard** och **Premium**. Databasalternativen för de här nivåerna liknar enskilda databaser och elastiska pooler, men det finns fler överväganden för elastiska pooler. Den här artikeln innehåller information om tjänstnivåer för enskilda databaser och elastiska databaser.

## Tjänstnivåer och databasalternativ
Tjänstenivåerna Basic, Standard och Premium har alla en upptids-SLA på 99,99 % och erbjuder förutsägbar prestanda, flexibla alternativ för verksamhetskontinuitet, säkerhetsfunktioner och timvis debitering. Följande tabell ger exempel på vilka nivåer som lämpar sig bäst för olika programbelasningar.

| Tjänstenivå | Målbelastningar |
|---|---|
| **Basic** | Bäst lämpad för små databaser som vanligtvis stöder en aktiv åtgärd åt gången. Exempelvis databaser som används för utveckling och testning, eller småskaliga program som inte används ofta. |
| **Standard** | Alternativet för de flesta molnprogram; stöder flera samtidiga frågor. Exempelvis arbetsgrupps- eller webbappar. |
| **Premium** | Skapad för höga transaktionsvolymer som stöder ett stort antal samtidiga användare och kräver den högsta funktionsnivån i avseende på verksamhetskontinuitet. Exempelvis databaser som stöder verksamhetskritiska program. |

>[AZURE.NOTE] Webb- och Business-utgåvorna är föråldrade. Läs igenom [Sunset vanliga frågor och svar](https://azure.microsoft.com/pricing/details/sql-database/web-business/) om du planerar att fortsätta använda Webb- och Business-utgåvorna.

## Servicenivåer och prestandanivåer för enskilda databaser
För enskilda databaser, finns det flera prestandanivåer inom varje tjänstnivå. Du kan flexibelt välja den tjänstnivå som bäst motsvarar dina arbetsbelastningsbehov. Om du behöver skala upp eller ned, kan du enkelt ändra nivå för din databas, **utan avbrottstid för ditt program.** Se [Ändra tjänstnivå och prestandanivå för databasen](sql-database-scale-up.md) för mer information.

Prestandaegenskaperna som beskrivs här gäller för databaser som skapats med [SQL Database V12](sql-database-v12-whats-new.md). När den underliggande maskinvaran i Azure är värd för flera databaser, kommer din databas ändå att tilldelas en garanterad uppsättning resurser och de förväntade prestandaegenskaperna för din databas påverkas inte.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

För att förstå DTU:er bättre, kan du läsa [DTU-avsnittet](#understanding-dtus) i det här ämnet.

>[AZURE.NOTE] För en detaljerad förklaring av alla andra rader i den här tabellen över tjänstnivåer, se [Funktioner och begränsningar för tjänstnivåer](sql-database-performance-guidance.md#service-tier-capabilities-and-limits).

## Tjänstnivåer och prestanda för elastiska pooler i eDTU:er
Förutom att skapa och skala en enskild databas, kan du också hantera flera databaser i en [elastisk pool](sql-database-elastic-pool.md). Alla databaserna i en elastisk pool delar en gemensam uppsättning resurser. Prestandaegenskaperna mäts i *elastiska Database Transaction Units* (eDTU:er). Precis som med en enskild databas, finns pooler i tre tjänstnivåer: **Basic**, **Standard** och **Premium**. De här tre tjänstnivåerna definierar fortfarande de övergripande prestandabegränsningarna och funktionerna för pooler.

Pooler låter elastiska databaser dela och konsumera DTU-resurser utan att behöva tilldela specifika prestandanivåer till databaserna i poolen. En enskild databas i en standardpool kan exempelvis gå från att använda 0 eDTU:er till de maximala databas-eDTU:erna som du ställt in när du konfigurerade poolen. Det gör att flera databaser med varierande arbetsbelastningar kan använda eDTU-resurserna som finns tillgängliga för hela poolen på ett effektivt sätt. Se [pris- och prestandaöverväganden för en elastisk databaspool](sql-database-elastic-pool-guidance.md) för mer information.

Följande tabell beskriver egenskaperna för pool-tjänstnivåer.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Varje databas inom en pool följer också egenskaperna för enskilda databaser på den nivån. Basic-poolen har till exempel en begränsning för maximalt antal sessioner per pool på 4 800 – 28 800, men en enskild databas inom poolen har en begränsning på 300 sessioner (begränsningen för en enskild Basic-databas som det anges i föregående avsnitt).

## Förstå DTU:er

[AZURE.INCLUDE [SQL DB DTU description](../../includes/sql-database-understanding-dtus.md)]

## Nästa steg
- Läs mer om prissättning för de här nivåerna på [SQL Database-priser](https://azure.microsoft.com/pricing/details/sql-database/).
- Få mer information om [elastiska databaspooler](sql-database-elastic-pool-guidance.md) och [pris- och prestandaöverväganden för elastiska databaspooler](sql-database-elastic-pool-guidance.md).
- Läs mer om hur man [Övervakar, hanterar och ändrar storlek på elastiska pooler](sql-database-elastic-pool-manage-portal.md) och [Övervakar prestandan för enskilda databaser](sql-database-single-database-monitor.md).
- Nu när du lärt dig om SQL Database-nivåer, kan du testa dem med ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) och lära dig [hur du skapar din första SQL-databas](sql-database-get-started.md).



<!--HONumber=Jun16_HO2-->



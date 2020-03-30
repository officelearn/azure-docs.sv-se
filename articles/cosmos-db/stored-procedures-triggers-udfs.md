---
title: Arbeta med lagrade procedurer, utlösare och UDFs i Azure Cosmos DB
description: Den här artikeln introducerar begrepp som lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 23a14e7590eca6f63c92acdf6336ffaef8b54381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065899"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Lagrade procedurer, utlösare och användardefinierade funktioner

Azure Cosmos DB tillhandahåller språkintegrerad, transaktionell körning av JavaScript. När du använder SQL API i Azure Cosmos DB kan du skriva **lagrade procedurer,** **utlösare**och **användardefinierade funktioner (UDFs)** på JavaScript-språket. Du kan skriva logiken i JavaScript, som körs i databasmotorn. Du kan skapa och köra utlösare, lagrade procedurer och UDFs med hjälp av [Azure Portal](https://portal.azure.com/), [JavaScript-språkintegrerat fråge-API i Azure Cosmos DB](javascript-query-api.md) eller [Cosmos DB SQL API-klienten SDK: er](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Fördelar med att använda programmering på serversidan

Genom att skriva lagrade procedurer, utlösare och användardefinierade funktioner (UDFs) i JavaScript kan du skapa avancerade program och de har följande fördelar:

* **Procedurmässig logik:** JavaScript som ett programmeringsspråk på hög nivå som ger ett rikt och välbekant gränssnitt för att uttrycka affärslogik. Du kan utföra en sekvens av komplexa åtgärder på data.

* **Atomära transaktioner:** Azure Cosmos DB garanterar att de databasåtgärder som utförs inom en enda lagrad procedur eller en utlösare är atomära. Med den här atomfunktionen kan ett program kombinera relaterade åtgärder till en enda batch, så att antingen alla åtgärder lyckas eller ingen av dem lyckas.

* **Prestanda:** JSON-data är i sig mappad till JavaScript språktyp systemet. Den här mappningen möjliggör ett antal optimeringar som lat materialisering av JSON-dokument i buffertpoolen och gör dem tillgängliga på begäran till den körande koden. Det finns andra prestandafördelar i samband med leverans affärslogik till databasen, som inkluderar:

   * *Batchbearbetning:* Du kan gruppera åtgärder som infogar och skicka dem i grupp. Kostnaderna för nätverkstrafikfördröjning och omkostnader för att skapa separata transaktioner minskas avsevärt.

   * *Förkompilering:* Lagrade procedurer, utlösare och UDF-filer förkompileras implicit till bytekodformatet för att undvika kompileringskostnader vid tidpunkten för varje skriptinvocation. På grund av förkompilering är åkallan av lagrade procedurer snabb och har ett lågt fotavtryck.

   * *Sekvensering:* Ibland behöver åtgärder en utlösande mekanism som kan utföra en eller flera uppdateringar av data. Förutom Atomicity finns det också prestandafördelar när du kör på serversidan.

* **Inkapsling:** Lagrade procedurer kan användas för att gruppera logik på ett ställe. Inkapsling lägger till ett abstraktionslager ovanpå data, vilket gör att du kan utveckla dina program oberoende av data. Det här abstraktionslagret är användbart när data är schemalösa och du inte behöver hantera att lägga till ytterligare logik direkt i ditt program. Abstraktionen gör att du kan skydda data genom att effektivisera åtkomsten från skripten.

> [!TIP]
> Lagrade procedurer är bäst lämpade för åtgärder som är skrivtunna och kräver en transaktion över ett partitionsnyckelvärde. När du bestämmer om du vill använda lagrade procedurer ska du optimera runt kapsla in den maximala mängden skrivningar som möjligt. Generellt sett är lagrade procedurer inte det mest effektiva sättet att göra ett stort antal läs- eller frågeåtgärder, så att använda lagrade procedurer för att batcha ett stort antal läsningar för att återgå till klienten ger inte önskad nytta. För bästa prestanda bör dessa lästunga åtgärder utföras på klientsidan med hjälp av Cosmos SDK. 

## <a name="transactions"></a>Transaktioner

Transaktionen i en typisk databas kan definieras som en sekvens av åtgärder som utförs som en enda logisk arbetsenhet. Varje transaktion ger **ACID-egenskapsgarantier**. ACID är en välkänd akronym som står för: **A**tomicity, **C**onsistency, **I**solation och **D**urability. 

* Atomicity garanterar att alla operationer som utförs i en transaktion behandlas som en enda enhet, och antingen alla av dem är engagerade eller ingen av dem är. 

* Konsekvensen säkerställer att data alltid är i ett giltigt tillstånd mellan transaktioner. 

* Isolering garanterar att inga två transaktioner stör varandra – många kommersiella system ger flera isoleringsnivåer som kan användas baserat på programmets behov. 

* Hållbarhet säkerställer att alla ändringar som har gjorts i en databas alltid kommer att finnas.

I Azure Cosmos DB finns JavaScript-körningen i databasmotorn. Därför körs begäranden som görs inom de lagrade procedurerna och utlösarna i samma omfång som databassessionen. Med den här funktionen kan Azure Cosmos DB garantera ACID-egenskaper för alla åtgärder som ingår i en lagrad procedur eller en utlösare. Exempel på hur [du implementerar transaktionsartikel.](how-to-write-stored-procedures-triggers-udfs.md#transactions)

### <a name="scope-of-a-transaction"></a>Transaktionens omfattning

Lagrade procedurer är associerade med en Azure Cosmos-behållare och körning av lagrad procedur begränsas till en logisk partitionsnyckel. Lagrade procedurer måste innehålla ett logiskt partitionsnyckelvärde under körningen som definierar den logiska partitionen för transaktionens omfattning. Mer information finns i artikel i [Azure Cosmos DB.](partition-data.md)

### <a name="commit-and-rollback"></a>Commit och återställning

Transaktioner är inbyggda i Azure Cosmos DB JavaScript-programmeringsmodellen. Inom en JavaScript-funktion slås alla åtgärder automatiskt in under en enda transaktion. Om JavaScript-logiken i en lagrad procedur slutförs utan undantag, har alla åtgärder i transaktionen bekräftats till databasen. Satser `BEGIN TRANSACTION` `COMMIT TRANSACTION` som och (välbekanta för relationsdatabaser) är implicita i Azure Cosmos DB. Om det finns några undantag från skriptet återställs hela transaktionen i Azure Cosmos DB JavaScript.If there are any exceptions from the script, the Azure Cosmos DB JavaScript runtime will roll back the whole transaction. Att utlösa ett undantag är därför `ROLLBACK TRANSACTION` effektivt likvärdigt med en i Azure Cosmos DB.

### <a name="data-consistency"></a>Datakonsekvens

Lagrade procedurer och utlösare körs alltid på den primära repliken för en Azure Cosmos-behållare. Den här funktionen säkerställer att läsningar från lagrade procedurer ger [stark konsekvens](consistency-levels-tradeoffs.md). Frågor som använder användardefinierade funktioner kan köras på den primära eller sekundära repliken. Lagrade procedurer och utlösare är avsedda att stödja transaktionsskrivningar – under tiden implementeras skrivskyddad logik bäst som logik på programsidan och frågor med [Azure Cosmos DB SQL API SDK: er](sql-api-dotnet-samples.md), hjälper dig att mätta databasdataflödet. 

## <a name="bounded-execution"></a>Begränsad körning

Alla Azure Cosmos DB-åtgärder måste slutföras inom den angivna tidsgränsen. Det här villkoret gäller för JavaScript-funktioner - lagrade procedurer, utlösare och användardefinierade funktioner. Om en åtgärd inte slutförs inom den tidsgränsen återställs transaktionen.

Du kan antingen se till att dina JavaScript-funktioner slutförs inom tidsgränsen eller implementera en fortsättningsbaserad modell för batch-/återupptakörning. För att förenkla utvecklingen av lagrade procedurer och utlösare för att hantera tidsgränser returnerar alla funktioner under Azure Cosmos-behållaren (till exempel skapa, läsa, uppdatera och ta bort objekt) ett booleskt värde som representerar om åtgärden ska Komplett. Om det här värdet är falskt är det en indikation på att proceduren måste avsluta körningen eftersom skriptet förbrukar mer tid eller etablerat dataflöde än det konfigurerade värdet. Åtgärder som köats före den första ej accepterade arkivet är garanterade att slutföras om den lagrade proceduren slutförs i tid och inte köar några fler begäranden. Åtgärder bör därför köas en i taget med hjälp av JavaScript:s motringningskonvention för att hantera skriptets kontrollflöde. Eftersom skript körs i en servermiljö styrs de strikt. Skript som upprepade gånger bryter mot körningsgränser kan markeras inaktiva och inte kan köras, och de bör återskapas för att respektera körningsgränserna.

JavaScript-funktioner omfattas också av [etablerad dataflödeskapacitet](request-units.md). JavaScript-funktioner kan eventuellt sluta använda ett stort antal begärandeenheter inom en kort tid och kan vara räntebegränsade om den etablerade kapacitetsgränsen för dataflöde uppnås. Det är viktigt att notera att skript förbrukar ytterligare dataflöde utöver det dataflöde som används för att köra databasåtgärder, även om dessa databasåtgärder är något billigare än att köra samma åtgärder från klienten.

## <a name="triggers"></a>Utlösare

Azure Cosmos DB kan användas med två typer av utlösare:

### <a name="pre-triggers"></a>Förutlösare

Azure Cosmos DB tillhandahåller utlösare som kan anropas genom att en åtgärd utförs på ett Azure Cosmos-objekt. Du kan till exempel ange en förutlösare när du skapar ett objekt. Då körs förutlösaren innan objektet skapas. Förutlösare kan inte ha några indataparametrar. Om det behövs kan det begärda objektet användas för att uppdatera dokumenttexten från den ursprungliga begäran. När utlösare har registrerats kan användarna ange vilka åtgärder som de kan köras med. Om en utlösare `TriggerOperation.Create`skapades med, innebär det att det inte kommer att tillåtas att använda utlösaren i en ersättningsåtgärd. Exempel nedan finns i [Så här skriver du utlösare.](how-to-write-stored-procedures-triggers-udfs.md#triggers)

### <a name="post-triggers"></a>Efterutlösare

I likhet med förutlösare, efter utlösare, är också associerade med en åtgärd på ett Azure Cosmos-objekt och de kräver inga indataparametrar. De körs *när* åtgärden har slutförts och har åtkomst till svarsmeddelandet som skickas till klienten. Exempel nedan finns i [Så här skriver du utlösare.](how-to-write-stored-procedures-triggers-udfs.md#triggers)

> [!NOTE]
> Registrerade utlösare körs inte automatiskt när deras motsvarande operationer (skapa / ta bort / ersätta / uppdatera) hända. De måste anropas explicit när åtgärderna utförs. Mer information finns i [hur du kör utlösare.](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)

## <a name="user-defined-functions"></a><a id="udfs"></a>Användardefinierade funktioner

Användardefinierade funktioner (UDFs) används för att enkelt utöka SQL API-frågespråksyntaxen och implementera anpassad affärslogik. De kan bara anropas i frågor. UDF har inte åtkomst till kontextobjektet och är avsedda att användas som beräkning endast JavaScript. Därför kan UDF-filer köras på sekundära repliker. Exempel nedan finns i Så här skriver du artikel [med användardefinierade funktioner.](how-to-write-stored-procedures-triggers-udfs.md#udfs)

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>JavaScript språkintegrerat fråge-API

Förutom att utfärda frågor med hjälp av SQL API-frågesyntax kan du med [SDK på serversidan](https://azure.github.io/azure-cosmosdb-js-server) utföra frågor med hjälp av ett JavaScript-gränssnitt utan att du känner till SQL. Med JavaScript-fråge-API:et kan du programmässigt skapa frågor genom att skicka predikatfunktioner till sekvens av funktionsanrop. Frågor tolkas av JavaScript-körningen och körs effektivt i Azure Cosmos DB. Mer information om Stöd för JavaScript-frågor API finns i [Arbeta med integrerad fråge-API för JavaScript-språk.](javascript-query-api.md) Exempel nedan finns i [Så här skriver du lagrade procedurer och utlösare med javascript-fråge-API-artikeln.](how-to-write-javascript-query-api.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skriver och använder lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB med följande artiklar:

* [Så här skriver du lagrade procedurer, utlösare och användardefinierade funktioner](how-to-write-stored-procedures-triggers-udfs.md)

* [Så här använder du lagrade procedurer, utlösare och användardefinierade funktioner](how-to-use-stored-procedures-triggers-udfs.md)

* [Arbeta med JavaScript-språkintegrerat fråge-API](javascript-query-api.md)

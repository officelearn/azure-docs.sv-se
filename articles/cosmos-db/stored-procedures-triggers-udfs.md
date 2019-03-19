---
title: Arbeta med lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB
description: Den här artikeln introducerar koncept som lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d1960fbc9fc9e8c1d672b66d3cf1f41399842059
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083206"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Lagrade procedurer, utlösare och användardefinierade funktioner

Azure Cosmos DB ger språkintegrerade, transaktionell körning av JavaScript. När du använder SQL API i Azure Cosmos DB kan du skriva **lagrade procedurer**, **utlösare**, och **användardefinierade funktioner (UDF)** i JavaScript-språket. Du kan skriva logik i JavaScript som körs i databasmotorn. Du kan skapa och köra utlösare, lagrade procedurer och UDF: er med hjälp av [Azure-portalen](https://portal.azure.com/), [JavaScript language integrerade fråge-API i Azure Cosmos DB](javascript-query-api.md) eller [Cosmos DB SQL API-klient SDK: er](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Fördelarna med att använda programmering av serversidan

Skriva lagrade procedurer, utlösare och användardefinierade funktioner (UDF) i JavaScript kan du skapa omfattande program och de har följande fördelar:

* **Procedurmässig logik:** JavaScript som ett övergripande programmeringsspråk som tillhandahåller omfattande och välbekanta gränssnitt för uttryckliga affärslogik. Du kan utföra en serie komplexa åtgärder på data.

* **Atomiska transaktioner:** Azure Cosmos DB garanterar att de databas som utförs i en lagrad procedur eller en utlösare är atomiska. Atomiska funktionen att ett program kan kombinera relaterade åtgärder i en enskild batch så att alla åtgärder lyckas eller ingen av dem lyckas.

* **Prestanda:** JSON-data mappas tack till JavaScript-typsystemet för språk. Den här mappningen möjliggör ett antal optimeringar som lazy materialisering av JSON-dokument i buffertpoolen och gör dem tillgängliga på begäran till koden som körs. Det finns andra prestandafördelarna som är associerade med leverans affärslogik till databasen, vilket innefattar:

   * *Batchbearbetning:* Du kan gruppera åtgärder som infogar och skicka dem gruppvis. Nätverkskostnader trafik svarstid och store-omkostnader för att skapa separata transaktioner minskas avsevärt.

   * *Före kompilering:* Lagrade procedurer, utlösare och UDF: er är implicit förkompilerad till formatet byte-kod för att undvika kompilering kostnaden vid tidpunkten för varje skript-anrop. På grund av före kompilering av lagrade procedurer går snabbt och har en små utrymmeskrav.

   * *Sekvensering:* Ibland operations behöver en utlösande mekanism som kan utföra en eller fler uppdateringar av data. Förutom Atomicitet finns även prestandafördelar med när du kör på serversidan.

* **Inkapsling:** Lagrade procedurer kan användas för att gruppera logiken i ett och samma ställe. Inkapsling lägger till ett Abstraktionslager ovanpå data, vilket gör det möjligt att utveckla dina program oberoende av data. Det här lagret Abstraktionslager är användbart när data är utan schema och du inte behöver hantera att lägga till ytterligare logik direkt i ditt program. Abstraktionen kan din skydda data genom att effektivisera åtkomst från skripten.

> [!TIP]
> Lagrade procedurer passar bäst för åtgärder som är skriver aktiverat. När du bestämmer var du vill använda lagrade procedurer, optimera runt kapslar in det maximala antalet skrivningar möjligt. Generellt sett är lagrade procedurer inte det effektivaste sättet för att göra stora mängder läsåtgärder så med lagrade procedurer i batch stora mängder läsningar ska returneras till klienten inte ger några önskade förmånen.

## <a name="transactions"></a>Transaktioner

Transaktionen i en typisk databasen kan definieras som en sekvens med åtgärder som utförs som en enda logisk enhet för arbete. Varje transaktion innehåller **egenskapen ACID-garantier**. ACID är en välkänd förkortning som står för: **En**tomicity, **C**onsistency, **jag**solation, och **D**urability. 

* Atomicitet garanterar att alla åtgärder utföras inuti en transaktion behandlas som en enda enhet, och antingen alla aktiviteter genomförs eller ingen av dem är. 

* Konsekvens ser till att data alltid är i ett giltigt tillstånd för transaktioner. 

* Isolering garanterar att inga två transaktioner störa varandra – många kommersiella system ger flera isoleringsnivåer som kan användas baserat på programmets behov. 

* Hållbarhet säkerställer att alla ändringar som engagerar sig i en databas alltid kommer att finnas.

I Azure Cosmos DB finns JavaScript-körning i databasmotorn. Därför köra begäranden som görs i de lagrade procedurerna och utlösare i samma omfattning som databassessionen. Den här funktionen gör det möjligt för Azure Cosmos DB att garantera ACID-egenskaper för alla åtgärder som är en del av en lagrad procedur eller en utlösare. Exempel finns i [hur du implementerar transaktioner](how-to-write-stored-procedures-triggers-udfs.md#transactions) artikeln.

### <a name="scope-of-a-transaction"></a>Omfattningen för en transaktion

Om en lagrad procedur är associerad med en Azure Cosmos-behållare, köra den lagrade proceduren i transaktionsomfång av en logisk partition-nyckel. Varje körning för lagrad procedur måste innehålla ett nyckelvärde för logisk partition som motsvarar omfattningen för transaktionen. Mer information finns i [Azure Cosmos DB partitionering](partition-data.md) artikeln.

### <a name="commit-and-rollback"></a>Commit och rollback

Transaktioner är inbyggt i Azure Cosmos DB JavaScript-programmeringsmodell. Inom en JavaScript-funktion omsluts alla åtgärder automatiskt under en enda transaktion. Om JavaScript-logik i en lagrad procedur har slutförts utan undantag, sparas alla åtgärder i transaktionen i databasen. Instruktioner som `BEGIN TRANSACTION` och `COMMIT TRANSACTION` (känner igen relationella databaser) är implicita i Azure Cosmos DB. Om det finns undantag från skriptet, återställs Azure Cosmos DB JavaScript-körning hela transaktionen. Därför måste undantagsfel motsvarar effektivt en `ROLLBACK TRANSACTION` i Azure Cosmos DB.

### <a name="data-consistency"></a>Datakonsekvens

Lagrade procedurer och utlösare körs alltid på den primära repliken av en Azure Cosmos-behållare. Den här funktionen säkerställer som läser från lagrade procedurer erbjudandet [stark konsekvens](consistency-levels-tradeoffs.md). Frågor med användardefinierade funktioner kan köras på primärt eller en sekundär replik. Lagrade procedurer och utlösare är avsedda att underlätta transaktionella skrivningar – under tiden skrivskyddad logic implementeras bäst som program sida logik och frågor med den [Azure Cosmos DB SQL API SDK](sql-api-dotnet-samples.md), hjälper dig att fylla i dataflöde för databasen. 

## <a name="bounded-execution"></a>Begränsad körning

Alla Azure Cosmos DB-åtgärder måste slutföras inom angiven tidsgräns varaktighet. Den här begränsningen gäller för funktioner i JavaScript - lagrade procedurer, utlösare och användardefinierade funktioner. Transaktionen återställs om en åtgärd inte slutförs inom tidsgränsen.

Du kan antingen se till att JavaScript-funktioner slutförs inom tidsgränsen eller implementera en fortsättning-baserad modell för att batch/återuppta körningen. För att förenkla utvecklingen av lagrade procedurer och utlösare för att hantera tidsfrister för alla funktioner i Azure Cosmos-behållaren (till exempel skapa, läsa, uppdatera och ta bort objekt) returnerar ett booleskt värde som representerar om att åtgärden Slutför. Om det här värdet är FALSKT, är det en indikation på att proceduren måste avsluta körningen eftersom skriptet förbrukar mer tid eller dataflöde än det konfigurerade värdet. Åtgärder i kö före den första värde store-åtgärden garanterat slutföra om den lagrade proceduren har slutförts i tid och inte köa inga fler begäranden. Åtgärder bör därför vara köade en i taget med hjälp av JavaScript-återanrop konvention för att hantera skriptets Kontrollflöde. Eftersom skript körs i en miljö för serversidan, regleras strikt dessa. Skript som bryter mot körning gränser upprepade gånger kan markeras som inaktiv och kan inte utföras, och de ska skapas på nytt för att respektera gränser för körning.

JavaScript-funktioner är också lyder [etablerats dataflödeskapacitet](request-units.md). JavaScript-funktioner kan potentiellt hamna med hjälp av ett stort antal enheter för programbegäran inom en kort tid och kanske rate-limited om etablerat dataflöde kapacitetsgränsen har nåtts. Det är viktigt att Observera att skript konsumerar genomflödet förutom dataflöde som har använt för att köra databasåtgärder, även om dessa databasåtgärder är något billigare än att köra samma åtgärder från klienten.

## <a name="triggers"></a>Utlösare

Det här avsnittet beskrivs två typer av utlösare:

### <a name="pre-triggers"></a>Före utlösare

Azure Cosmos DB tillhandahåller utlösare som kan anropas genom att utföra en åtgärd på ett Azure Cosmos DB-objekt. Du kan till exempel ange en före utlösare när du skapar ett objekt. I det här fallet körs före utlösaren innan objektet skapas. Förutlösare kan inte ha några indataparametrar. Om det behövs användas Begäranobjektet för att uppdatera dokumentets brödtext från den ursprungliga begäran. När utlösare är registrerade ange användare vilka åtgärder som den kan köras med. Om en utlösare har skapats med `TriggerOperation.Create`, det innebär att använda utlösaren i en ersättningsåtgärden inte tillåts. Exempel finns i [hur du skriver utlösare](how-to-write-stored-procedures-triggers-udfs.md#triggers) artikeln.

### <a name="post-triggers"></a>Efter utlösare

Liknar före utlösare, efter utlösare är också kopplad till en åtgärd på ett Azure Cosmos DB-objekt och de kräver inte någon indataparametrar. De körs *när* åtgärden har slutförts och har åtkomst till svarsmeddelandet som skickas till klienten. Exempel finns i [hur du skriver utlösare](how-to-write-stored-procedures-triggers-udfs.md#triggers) artikeln.

## <a id="udfs"></a>Användardefinierade funktioner

Användardefinierade funktioner (UDF) används för att utöka språk frågesyntaxen SQL API och enkelt implementera anpassad affärslogik. De kan anropas endast användas av frågor. Användardefinierade funktioner har inte åtkomst till context-objektet och är avsedda att användas som compute endast JavaScript. Användardefinierade funktioner kan därför köras på sekundära repliker. Exempel finns i [hur du skriver användardefinierade funktioner](how-to-write-stored-procedures-triggers-udfs.md#udfs) artikeln.

## <a id="jsqueryapi"></a>JavaScript språkintegrerade frågorna API

Förutom att utfärda frågor med SQL API-frågesyntax, den [SDK för serversidan](https://azure.github.io/azure-cosmosdb-js-server) kan du utföra frågor med hjälp av ett JavaScript-gränssnitt utan att SQL. Frågan JavaScript API kan du programmässigt skapa frågor genom att skicka predikat funktioner i sekvens av funktionsanrop. Frågor parsas av JavaScript-körning och körs effektivt i Azure Cosmos DB. Läs om JavaScript fråge-API-stöd i [arbeta med JavaScript-språket integrerade fråge-API](javascript-query-api.md) artikeln. Exempel finns i [hur du skriver lagrade procedurer och utlösare med hjälp av Javascript fråge-API](how-to-write-javascript-query-api.md) artikeln.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skriver och använda lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB med följande artiklar:

* [Hur du skriver lagrade procedurer, utlösare och användardefinierade funktioner](how-to-write-stored-procedures-triggers-udfs.md)

* [Hur du använder lagrade procedurer, utlösare och användardefinierade funktioner](how-to-use-stored-procedures-triggers-udfs.md)

* [Arbeta med JavaScript-språket integrerade fråge-API](javascript-query-api.md)

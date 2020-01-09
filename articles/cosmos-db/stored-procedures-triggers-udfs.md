---
title: 'Arbeta med lagrade procedurer, utlösare och UDF: er i Azure Cosmos DB'
description: Den här artikeln beskriver begreppen, till exempel lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 706f52a6cda2bbcb0e5ca1cfe9372600fa6709d0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441230"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Lagrade procedurer, utlösare och användardefinierade funktioner

Azure Cosmos DB tillhandahåller språkintegrerad, transaktionell körning av JavaScript. När du använder SQL API i Azure Cosmos DB kan du skriva **lagrade procedurer**, **utlösare**och **användardefinierade funktioner (UDF: er)** i JavaScript-språket. Du kan skriva logiken i JavaScript, som körs i databasmotorn. Du kan skapa och köra utlösare, lagrade procedurer och UDF: er med hjälp av [Azure Portal](https://portal.azure.com/), [java script language-API för integrerad fråga i Azure Cosmos DB](javascript-query-api.md) Cosmos DB eller klient-SDK: er för [SQL API i SQL API](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Fördelar med att använda programmering på Server Sidan

Genom att skriva lagrade procedurer, utlösare och användardefinierade funktioner (UDF: er) i Java Script kan du bygga rika program och de har följande fördelar:

* **Procedur logik:** Java Script som ett högnivå programmerings språk som innehåller omfattande och välbekanta gränssnitt för att uttrycka affärs logik. Du kan utföra en sekvens av komplexa åtgärder på data.

* **Atomiska transaktioner:** Azure Cosmos DB garanterar att databas åtgärderna som utförs inom en enda lagrad procedur eller en utlösare är atomiska. Med den här atomiska funktionen kan ett program kombinera relaterade åtgärder i en enda batch, så att antingen alla åtgärder lyckas eller ingen av dem lyckas.

* **Prestanda:** JSON-data mappas inbäddat till språk typs systemet för Java Script. Den här mappningen möjliggör ett antal optimeringar som till exempel Lazy materialization av JSON-dokument i bufferten och gör dem tillgängliga på begäran till den kod som körs. Det finns andra prestanda fördelar kopplade till-databasen med affärs logik, bland annat:

   * *Batching:* Du kan gruppera åtgärder som infogningar och skicka dem i bulk. Kostnaderna för nätverks trafik fördröjningen och butiks omkostnaderna för att skapa separata transaktioner minskas avsevärt.

   * *För kompilering:* Lagrade procedurer, utlösare och UDF: er är implicit förkompilerade till byte-kod formatet för att undvika produktionskostnad vid tidpunkten för varje skript anrop. På grund av förkompileringen är anropet av lagrade procedurer snabb och har lite utrymme.

   * *Sekvenser:* Ibland behöver åtgärder en utlösnings funktion som kan utföra en eller flera uppdateringar av data. Utöver Atomicable är det också prestanda fördelarna när de körs på Server sidan.

* **Inkapsling:** Lagrade procedurer kan användas för att gruppera logik på ett och samma ställe. Encapsulation lägger till ett abstraktions lager ovanpå data, vilket gör att du kan utveckla dina program oberoende av data. Detta skikt är användbart när data är schema-mindre och du inte behöver hantera att lägga till ytterligare logik direkt i ditt program. Abstraktionen gör att du kan skydda dina data genom att effektivisera åtkomsten från skripten.

> [!TIP]
> Lagrade procedurer lämpar sig bäst för åtgärder som är både skrivbara och kräver en transaktion i ett nyckel värde för en partition. När du bestämmer om du ska använda lagrade procedurer kan du optimera det maximala antalet skrivningar som kan göras. I allmänhet är lagrade procedurer inte det effektivaste sättet att utföra ett stort antal Läs-eller fråge åtgärder, så användningen av lagrade procedurer i ett stort antal läsningar för att återgå till klienten ger inte den önskade förmånen. För bästa prestanda bör dessa Läs-tung-åtgärder utföras på klient sidan med hjälp av Cosmos SDK. 

## <a name="transactions"></a>Transaktioner

Transaktionen i en typisk databasen kan definieras som en sekvens med åtgärder som utförs som en enda logisk enhet för arbete. Varje transaktion ger **garantier för syra egenskaper**. SYRA är en välkänd förkortning som står för: **a**tomicity, **C**onsistency, **I**solation och **D**urability. 

* Atomisk garanterar att alla åtgärder som utförs i en transaktion behandlas som en enda enhet, och att alla är antingen bekräftade eller inga av dem. 

* Konsekvens säkerställer att data alltid är i ett giltigt tillstånd för transaktioner. 

* Isolering garanterar att inga två transaktioner stör varandra – många kommersiella system ger flera isolerings nivåer som kan användas baserat på programmets behov. 

* Hållbarhet garanterar att alla ändringar som är allokerade i en databas alltid finns.

I Azure Cosmos DB finns JavaScript-körningen i databas motorn. Begär Anden som görs inom lagrade procedurer och utlösare körs därför i samma definitions område som Database-sessionen. Med den här funktionen kan Azure Cosmos DB garantera egenskaper för syra för alla åtgärder som ingår i en lagrad procedur eller en utlösare. Exempel finns i artikeln [implementera transaktioner](how-to-write-stored-procedures-triggers-udfs.md#transactions) .

### <a name="scope-of-a-transaction"></a>En transaktions omfång

Om en lagrad procedur är associerad med en Azure Cosmos-behållare körs den lagrade proceduren i transaktions omfånget för en logisk partitionsnyckel. Varje lagrad procedur körning måste innehålla ett nyckel värde för logisk partition som motsvarar omfånget för transaktionen. Mer information finns i artikeln [Azure Cosmos DB partitionering](partition-data.md) .

### <a name="commit-and-rollback"></a>Commit och rollback

Transaktioner integreras internt i Azure Cosmos DB JavaScript-programmerings modellen. I en JavaScript-funktion radbryts alla åtgärder automatiskt under en enda transaktion. Om JavaScript-logiken i en lagrad procedur slutförs utan undantag, allokeras alla åtgärder i transaktionen till-databasen. Instruktioner som `BEGIN TRANSACTION` och `COMMIT TRANSACTION` (bekanta med relations databaser) är implicita i Azure Cosmos DB. Om det finns undantag från skriptet, återställer Azure Cosmos DB JavaScript-körningsmiljön hela transaktionen. Det innebär att ett undantag i praktiken motsvarar en `ROLLBACK TRANSACTION` i Azure Cosmos DB.

### <a name="data-consistency"></a>Datakonsekvens

Lagrade procedurer och utlösare körs alltid på den primära repliken av en Azure Cosmos-behållare. Den här funktionen garanterar att läsningar från lagrade procedurer ger [stark konsekvens](consistency-levels-tradeoffs.md). Frågor som använder användardefinierade funktioner kan köras på den primära eller sekundära repliken. Lagrade procedurer och utlösare är avsedda att stödja transaktions skrivningar – ingångs bara Läs logik är bäst implementerad som logik på program sidan och frågor med hjälp av [Azure Cosmos DB SQL API SDK](sql-api-dotnet-samples.md): er, vilket hjälper dig att fylla databas data flödet. 

## <a name="bounded-execution"></a>Begränsad körning

Alla Azure Cosmos DB åtgärder måste slutföras inom den angivna tids gränsen. Den här begränsningen gäller för JavaScript-funktioner – lagrade procedurer, utlösare och användardefinierade funktioner. Om en åtgärd inte slutförs inom den tids gränsen återställs transaktionen.

Du kan antingen se till att JavaScript-funktionerna slutförs inom tids gränsen eller implementera en forts-baserad modell för körning av batch/omstart. För att förenkla utvecklingen av lagrade procedurer och utlösare för att hantera tids gränser kan alla funktioner under Azure Cosmos-behållaren (till exempel skapa, läsa, uppdatera och ta bort objekt) returnera ett booleskt värde som anger om den åtgärden kommer att full. Om det här värdet är false, är det en indikation på att proceduren måste omsluta körningen eftersom skriptet förbrukar mer tid eller ett allokerat data flöde än det konfigurerade värdet. Åtgärder i kö före den första värde store-åtgärden garanterat slutföra om den lagrade proceduren har slutförts i tid och inte köa inga fler begäranden. Därför ska åtgärder placeras i kö en i taget med hjälp av JavaScript-callback-konventionen för att hantera skriptets kontroll flöde. Eftersom skript körs i en miljö på Server sidan, styrs de strikt. Skript som upprepade gånger bryter mot körnings gränser kan vara markerade som inaktiva och kan inte utföras, och de bör återskapas för att respektera körnings gränserna.

JavaScript-funktioner omfattas också av [etablerad data flödes kapacitet](request-units.md). JavaScript-funktioner kan eventuellt komma att få ut med ett stort antal enheter för programbegäran inom en kort tid och kan vara avgiftsbelagda om den allokerade kapacitets gränsen för data flöden nås. Det är viktigt att Observera att skript använder ytterligare data flöde utöver det data flöde som krävs för att köra databas åtgärder, även om dessa databas åtgärder är något billigare än att köra samma åtgärder från klienten.

## <a name="triggers"></a>Utlösare

Azure Cosmos DB kan användas med två typer av utlösare:

### <a name="pre-triggers"></a>För utlösare

Azure Cosmos DB tillhandahåller utlösare som kan anropas genom att en åtgärd utförs på ett Azure Cosmos-objekt. Du kan till exempel ange en förutlösare när du skapar ett objekt. Då körs förutlösaren innan objektet skapas. Förutlösare kan inte ha några indataparametrar. Om det behövs kan det begärda objektet användas för att uppdatera dokumenttexten från den ursprungliga begäran. När utlösare har registrerats kan användarna ange vilka åtgärder som de kan köras med. Om en utlösare skapades med `TriggerOperation.Create`, innebär det att det inte är tillåtet att använda utlösaren i en ersättnings åtgärd. Exempel finns i artikeln om [att skriva utlösare](how-to-write-stored-procedures-triggers-udfs.md#triggers) .

### <a name="post-triggers"></a>Efter utlösare

På samma sätt som för-utlösare är post-triggers också kopplade till en åtgärd på ett Azure Cosmos-objekt och de kräver inga indataparametrar. De körs *när* åtgärden har slutförts och har åtkomst till svarsmeddelandet som skickas till klienten. Exempel finns i artikeln om [att skriva utlösare](how-to-write-stored-procedures-triggers-udfs.md#triggers) .

> [!NOTE]
> Registrerade Utlösare körs inte automatiskt när motsvarande åtgärder (skapa/ta bort/Ersätt/uppdatera) sker. De måste anropas explicit när åtgärderna utförs. Mer information finns i artikeln [om att köra utlösare](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) .

## <a id="udfs"></a>Användardefinierade funktioner

Användardefinierade funktioner (UDF: er) används för att utöka SQL API-frågesyntaxen och implementera anpassad affärs logik enkelt. De kan endast anropas inom frågor. UDF: er har inte åtkomst till Context-objektet och är avsedda att användas som enbart beräknings-Java Script. Därför kan UDF: er köras på sekundära repliker. Exempel finns i artikeln om [att skriva användardefinierade funktioner](how-to-write-stored-procedures-triggers-udfs.md#udfs) .

## <a id="jsqueryapi"></a>JavaScript-språk – integrerat fråge-API

Förutom att skicka frågor med SQL API-frågesyntax kan du köra frågor med hjälp av ett JavaScript-gränssnitt utan att du vet om SQL i [Server sidan](https://azure.github.io/azure-cosmosdb-js-server) . Med Java Script Query API kan du bygga frågor genom programmering genom att skicka predikat till sekvenser med funktions anrop. Frågor parsas av JavaScript-körningen och körs effektivt inom Azure Cosmos DB. Läs mer om stöd för Java Script Query API i [arbeta med java script language-integrering med API](javascript-query-api.md) -artikel. Exempel finns i [så här skriver du lagrade procedurer och utlösare med hjälp av Java scripts Query API](how-to-write-javascript-query-api.md) -artikel.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skriver och använder lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB med följande artiklar:

* [Skriva lagrade procedurer, utlösare och användardefinierade funktioner](how-to-write-stored-procedures-triggers-udfs.md)

* [Använda lagrade procedurer, utlösare och användardefinierade funktioner](how-to-use-stored-procedures-triggers-udfs.md)

* [Arbeta med JavaScript-språk integrerat fråge-API](javascript-query-api.md)

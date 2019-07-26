---
title: Modellerings data i Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Läs mer om datamodellering i NoSQL-databaser, skillnader mellan datamodellering i en relationsdatabas och en dokumentdatabas.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: rimman
ms.openlocfilehash: da119b2858c6b6c7bbc99b40d340f79964e0fae3
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467886"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Data modellering i Azure Cosmos DB

Medan schema fria databaser, t. ex. Azure Cosmos DB, gör det mycket enkelt att lagra och fråga ostrukturerade och delvis strukturerade data, bör du ägna lite tid åt att tänka på din data modell för att få ut mesta möjliga av tjänsten när det gäller prestanda och skalbarhet och lägsta kostnader.

Hur data ska lagras? Hur kommer programmet att hämta och fråga efter data? Är ditt program skrivskyddat eller så är det skrivskyddat?

När du har läst den här artikeln kommer du att kunna besvara följande frågor:

* Vad är datamodellering och varför ska jag bry mig?
* Hur fungerar modellerings data i Azure Cosmos DB olika för en Relations databas?
* Hur jag express datarelationer i en icke-relationell databas?
* När bäddar jag in data och när länkar jag till data?

## <a name="embedding-data"></a>Bädda in data

När du börjar att modellera data i Azure Cosmos DB försöker hantera dina entiteter som **fristående objekt** som representeras som JSON-dokument.

För jämförelse ska vi först se hur vi kan modellera data i en Relations databas. I följande exempel visas hur en person kan lagras i en relationsdatabas.

![Relationsdatabas modell](./media/sql-api-modeling-data/relational-data-model.png)

När du arbetar med relations databaser, är strategin att normalisera alla dina data. Att normalisera dina data innebär vanligt vis att ta en entitet, till exempel en person, och dela upp den i diskreta komponenter. I exemplet ovan kan en person ha flera kontakt informations poster, samt flera adress poster. Kontakt uppgifter kan delas vidare genom att ytterligare extrahera vanliga fält som en typ. Samma sak gäller för adress, varje post kan vara av typen *Start* eller *företag*.

Den guidar premise när normaliserar data ska **Undvik att lagra redundanta data** på varje post och i stället hänvisa till data. I det här exemplet, för att läsa en person, med alla sina kontakt uppgifter och adresser, måste du använda kopplingar för att effektivt skriva tillbaka (eller avnormalisera) dina data vid körning.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Uppdatering av en enskild person med deras kontaktuppgifter och adresser kräver skrivåtgärder över många enskilda tabeller.

Nu ska vi ta en titt på hur vi kan modellera samma data som en självständig entitet i Azure Cosmos DB.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "addresses": [
            {
                "line1": "100 Some Street",
                "line2": "Unit 1",
                "city": "Seattle",
                "state": "WA",
                "zip": 98012
            }
        ],
        "contactDetails": [
            {"email": "thomas@andersen.com"},
            {"phone": "+1 555 555-5555", "extension": 5555}
        ]
    }

Genom att använda metoden ovan har  vi avnormaliserat person posten, genom att **bädda in** all information som är relaterad till den här personen, till exempel kontakt uppgifter och adresser, i ett *enda JSON* -dokument.
Vi har också möjlighet att göra saker som att ha kontaktinformation på olika former helt eftersom vi inte är begränsad till ett fast schema.

Att hämta en fullständig person post från databasen är nu en **enda Läs åtgärd** mot en enda behållare och för ett enda objekt. Att uppdatera en person post, med deras kontakt information och adresser, är också en **enda Skriv åtgärd** mot ett enda objekt.

Genom att avnormalisera data, kan programmet behöva skicka färre frågor och uppdateringar för att slutföra vanliga åtgärder.

### <a name="when-to-embed"></a>När du ska bädda in

I allmänhet använder inbäddade data modeller när:

* Det finns **inneslutna** relationer mellan entiteter.
* Det finns **en till några** relationer mellan entiteter.
* Det finns inbäddade data som **ändras sällan**.
* Det finns inbäddade data som inte kommer att växa **utan bindning**.
* Det finns inbäddade data som **frågas ofta tillsammans**.

> [!NOTE]
> Normalt Avnormaliserade data som ger bättre **läsa** prestanda.

### <a name="when-not-to-embed"></a>När du inte vill bädda in

Även om en tumregel i Azure Cosmos DB är att avnormalisera allt och bädda in alla data i ett enda objekt, kan det leda till vissa situationer som bör undvikas.

Ta det här JSON-kodfragmentet.

    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "comments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            …
            {"id": 100001, "author": "jane", "comment": "and on we go ..."},
            …
            {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
            …
            {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
        ]
    }

Det kan vara vad en post-entitet med inbäddade kommentarer skulle se ut som om vi modellering en typisk blogg eller CMS-system, system. Problem med det här exemplet är att kommentarer matrisen är **obundna**, vilket innebär att det finns ingen () gräns för antal kommentarer som det enda inlägget kan ha. Detta kan bli ett problem eftersom storleken på objektet kan växa oändligt stor.

Eftersom storleken på objektet ökar möjligheten att överföra data via kabeln, samt läsning och uppdatering av objektet, i skala, kommer att påverkas.

I det här fallet är det bättre att överväga följande data modell.

    Post item:
    {
        "id": "1",
        "name": "What's new in the coolest Cloud",
        "summary": "A blog post by someone real famous",
        "recentComments": [
            {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
            {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
            {"id": 3, "author": "jane", "comment": "....."}
        ]
    }

    Comment items:
    {
        "postId": "1"
        "comments": [
            {"id": 4, "author": "anon", "comment": "more goodness"},
            {"id": 5, "author": "bob", "comment": "tails from the field"},
            ...
            {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
        ]
    },
    {
        "postId": "1"
        "comments": [
            {"id": 100, "author": "anon", "comment": "yet more"},
            ...
            {"id": 199, "author": "bored", "comment": "will this ever end?"}
        ]
    }

Den här modellen har de tre senaste kommentarerna som är inbäddade i post-behållaren, som är en matris med en fast uppsättning attribut. De andra kommentarerna är grupperade i batchar med 100 kommentarer och lagras som separata objekt. Storlek på batch har valts som 100 eftersom vårt fiktiva program används att läsa in 100 kommentarer i taget.  

Ett annat fall där inbäddning av data inte är en bra idé är när inbäddade data används ofta över-objekt och ändras ofta.

Ta det här JSON-kodfragmentet.

    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            {
                "numberHeld": 100,
                "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
            },
            {
                "numberHeld": 50,
                "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
            }
        ]
    }

Detta kan vara en persons portfölj. Vi har valt att bädda in aktie informationen i varje portfölj dokument. I en miljö där relaterade data ändras ofta, kommer som aktier handel med program, bädda in data som ändras ofta att innebära att du hela tiden uppdaterar dokumenten portfölj varje gång aktier säljs.

Lager *zaza* får säljas flera hundra gånger under en enda dag och tusentals användare kan ha *zaza* deras portfölj. Med en datamodell som ovanstående vi skulle behöva uppdatera flera tusen portfölj dokument många gånger varje dag som leder till ett system som inte bra med skalning.

## <a name="referencing-data"></a>Referens data

Inbäddning av data fungerar snyggt i många fall, men det finns scenarier när du avnormaliserar dina data, vilket leder till fler problem än det är värt. Så vad vi gör nu?

Relationsdatabaser är inte den enda plats där du kan skapa relationer mellan entiteter. I en dokument databas kan du ha information i ett dokument som relaterar till data i andra dokument. Vi rekommenderar inte att du skapar system som bättre lämpar sig för en Relations databas i Azure Cosmos DB eller andra dokument databaser, men enkla relationer är fina och kan vara användbara.

I JSON nedan som vi har valt att använda exemplet med en portfölj från tidigare men nu kan vi refererar till objektet lagerartiklar portfölj i stället för att bädda in den. Det här sättet när lagerartiklar objektet ändras ofta under dagen den enda dokument som måste uppdateras är det enda lagerartiklar dokumentet.

    Person document:
    {
        "id": "1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "holdings": [
            { "numberHeld":  100, "stockId": 1},
            { "numberHeld":  50, "stockId": 2}
        ]
    }

    Stock documents:
    {
        "id": "1",
        "symbol": "zaza",
        "open": 1,
        "high": 2,
        "low": 0.5,
        "vol": 11970000,
        "mkt-cap": 42000000,
        "pe": 5.89
    },
    {
        "id": "2",
        "symbol": "xcxc",
        "open": 89,
        "high": 93.24,
        "low": 88.87,
        "vol": 2970200,
        "mkt-cap": 1005000,
        "pe": 75.82
    }

En omedelbar Nackdelen med att den här metoden är dock om ditt program krävs för att visa information om varje lager som hålls kvar när du visar en persons portföljen. i det här fallet skulle du behöva göra flera resor till databasen för att läsa in informationen för varje lagerartiklar dokument. Här har vi gjort ett beslut att förbättra effektiviteten för skrivåtgärder, vilket sker ofta under dagen, men i sin tur komprometteras på läsåtgärder som potentiellt har mindre påverkan på prestanda hos den här specifika system.

> [!NOTE]
> Normalized datamodeller **kan kräva mer tur och RETUR** till servern.

### <a name="what-about-foreign-keys"></a>Vad gäller främmande nycklar?

Eftersom det finns för närvarande inga konceptet med en begränsning, foreign key- eller på annat sätt, relationer mellan dokument som du har i dokument är i själva verket ”svaga länkar” och kommer inte verifieras av själva databasen. Om du vill se till att de data som ett dokument refererar till faktiskt finns, måste du göra detta i ditt program eller med hjälp av serversidan utlösare eller lagrade procedurer i Azure Cosmos DB.

### <a name="when-to-reference"></a>När du ska referera till

I allmänhet använder normaliserade data modeller när:

* Som representerar **en-till-många** relationer.
* Som representerar **många-till-många** relationer.
* Relaterade data **ändras ofta**.
* Refererade data kan **obundna**.

> [!NOTE]
> Vanligtvis normaliserar ger bättre **skriva** prestanda.

### <a name="where-do-i-put-the-relationship"></a>Där placera relationen?

Tillväxt för relationen hjälper dig att avgöra i vilken dokumentet för att lagra referensen.

Om vi tittar på JSON nedan som modeller utgivare och böcker.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press",
        "books": [ 1, 2, 3, ..., 100, ..., 1000]
    }

    Book documents:
    {"id": "1", "name": "Azure Cosmos DB 101" }
    {"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "3", "name": "Taking over the world one JSON doc at a time" }
    ...
    {"id": "100", "name": "Learn about Azure Cosmos DB" }
    ...
    {"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }

Om antal böcker per utgivaren är litet med begränsad möjlighet att växa, kan det vara praktiskt att lagra boken referensen i publisher-dokument. Men om antal böcker per utgivaren är obegränsade, skulle sedan den här datamodellen leda till föränderligt och växande matriser, som i ovanstående exempel publisher dokumentet.

Växla runt lite skulle resultera i en modell som fortfarande representerar samma data men nu förhindrar dessa stora föränderliga samlingar.

    Publisher document:
    {
        "id": "mspress",
        "name": "Microsoft Press"
    }

    Book documents:
    {"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
    {"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
    {"id": "3","name": "Taking over the world one JSON doc at a time"}
    ...
    {"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
    ...
    {"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}

Vi har släppt obundna samlingen för utgivaren-dokument i exemplet ovan. I stället ha vi bara en referens till utgivaren på varje bokdokument.

### <a name="how-do-i-model-manymany-relationships"></a>Hur jag för att modellera många: många-relationer?

I en relationsdatabas *många: många* relationer modelleras ofta med anslutning till tabeller, som bara ansluta till poster från andra tabeller tillsammans.

![Ansluter till tabeller](./media/sql-api-modeling-data/join-table.png)

Du kanske att tro att replikera samma sak med hjälp av dokument och skapa en datamodell som ser ut ungefär så här.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

    Joining documents:
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Detta skulle fungera. Dock kräver läser in antingen en författare med sina böcker eller läser in en bok med dess upphovsman alltid minst två ytterligare frågor mot databasen. En fråga för att slå samman dokumentet och sedan en annan fråga för att hämta det faktiska dokumentet att det har anslutits.

Om allt det här kopplingstabell gör limma ihop två typer av data, sedan varför inte att släppa den helt?
Tänk på följande.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Om jag har en författare vet jag direkt vilka böcker de har skrivit, och omvänt om jag hade ett bok dokument inläst för att känna till ID: na för författaren. Detta sparar den mellanliggande frågor mot en anslutning till tabellen minska antalet servrar nätverksförfrågningar programmet måste göra.

## <a name="hybrid-data-models"></a>Hybrid data modeller

Nu har vi tittat bädda in (eller avnormalisera) och referera till (eller normalisering) data, var och en har sina upsides och var och en har kompromisser som vi har sett.

Det behöver inte alltid vara antingen eller inte är scared blandas saker lite.

Baserat på ditt programs specifika användningsmönster och arbetsbelastningar som det kan finnas fall där blanda inbäddad och använda data passar och kan leda till enklare programlogiken med färre server nätverksförfrågningar samtidigt en bra nivå av prestanda.

Överväg följande JSON.

    Author documents:
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",
        "countOfBooks": 3,
        "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "https://....png"}
            {"profile": "https://....png"}
            {"large": "https://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "https://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        ]
    }

Här har vi (huvudsakligen) följt inbäddad modell, där data från andra entiteter är inbäddade i översta dokumentet, men andra data refereras.

Om du tittar på bok dokumentet, ser vi några intressanta fält när vi tittar på ett antal författare. Det finns ett `id` fält som är det fält vi använder för att referera till ett författar dokument, standard praxis i en normaliserad modell, men det finns `name` även och `thumbnailUrl`. Vi kan ha fastnat `id` med och lämnat programmet för att få ytterligare information som behövs från respektive författar dokument med hjälp av länken "länk", men eftersom programmet visar författarens namn och en miniatyr bild med varje bok visat att vi kan spara en tur och retur till servern per bok i en lista genom att avnormalisera **vissa** data från författaren.

Om författarens namn har ändrats eller om han ville uppdatera sitt foto måste vi gå och uppdatera varje bok som de någonsin publicerat, men för vår app, baserat på antagandet att författare inte ändrar sina namn ofta, är detta ett acceptabelt design beslut.  

I det här exemplet finns **beräknas aggregeringar i förväg** värden för att spara dyra bearbetning på en Läsåtgärd. I det här exemplet är några av de data som är inbäddad i dokumentet författare data som beräknas vid körning. Varje gång en ny bok publiceras, skapas en bokdokument **och** countOfBooks-fältet är inställt på ett beräknat värde baserat på antalet boken dokument som finns för en viss författare. Denna optimering är bra i Läs tung system där vi har råd att utföra beräkningar på skrivningar för att optimera läsningar.

Möjlighet att aktivera en modell med förberäknade fält är möjligt eftersom Azure Cosmos DB stöder **flera dokument transaktioner**. Många NoSQL-Arkiv det går inte att göra transaktioner mellan dokument och därför förespråkar designbeslut, till exempel ”alltid bädda in allt” på grund av den här begränsningen. Med Azure Cosmos DB kan du använda serversidan utlösare eller lagrade procedurer som Infoga böcker och uppdatera författare allt inom en ACID-transaktion. Nu behöver du **inte bädda** in allt i ett dokument bara för att vara säker på att dina data är konsekventa.

## <a name="distinguishing-between-different-document-types"></a>Skilja mellan olika dokument typer

I vissa fall kanske du vill blanda olika dokument typer i samma samling. Detta är vanligt vis fallet när du vill att flera, relaterade dokument ska sitta i samma [partition](partitioning-overview.md). Du kan till exempel skicka både böcker och bok granskningar i samma samling och partitionera den av `bookId`. I sådana fall vill du vanligt vis lägga till dokument med ett fält som identifierar deras typ för att skilja dem åt.

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "bookId": "b1",
        "type": "book"
    }

    Review documents:
    {
        "id": "r1",
        "content": "This book is awesome",
        "bookId": "b1",
        "type": "review"
    },
    {
        "id": "r2",
        "content": "Best book ever!",
        "bookId": "b1",
        "type": "review"
    }

## <a name="next-steps"></a>Nästa steg

De största takeaways från den här artikeln är att förstå att datamodellering i en schemafri värld är lika viktigt som någonsin.

Precis som det är inte ett enskilt sätt att representera en typ av data på en skärm, är det inte ett enskilt sätt att modellera dina data. Du behöver att förstå ditt program och hur den skapas, använda och bearbeta data. Sedan kan du ange om hur du skapar en modell som åtgärdar omedelbara behov av ditt program genom att använda några av de riktlinjer som beskrivs här. När dina program behöver ändra kan du utnyttja flexibiliteten i en schemafri databas till att använda som ändras och enkelt kan utveckla din datamodell.

Om du vill veta mer om Azure Cosmos DB kan du referera till tjänstens [dokumentation](https://azure.microsoft.com/documentation/services/cosmos-db/) sidan.

Att förstå hur till shard dina data på flera partitioner, referera till [partitionera Data i Azure Cosmos DB](sql-api-partition-data.md).

Information om hur du kan modellera och partitionera data på Azure Cosmos DB med ett verkligt exempel finns i [data modellering och partitionering – ett verkligt exempel](how-to-model-partition-example.md).

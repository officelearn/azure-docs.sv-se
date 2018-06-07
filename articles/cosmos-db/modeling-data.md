---
title: Modeling dokumentdata för en NoSQL-databas | Microsoft Docs
description: Lär dig mer om att utforma data för NoSQL-databaser
keywords: Modeling data
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2016
ms.author: sngun
ms.openlocfilehash: ef40bcb473e4d7dbe51f9d8d9ca20265c04d0df6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34612691"
---
# <a name="modeling-document-data-for-nosql-databases"></a>Modeling dokumentdata för NoSQL-databaser
Medan schemafria databaser som Azure Cosmos DB gör det super enkelt att omfatta ändringar i datamodellen du bör fortfarande tillbringar vissa tid tro om dina data. 

Hur data ska lagras? Hur programmet ska hämta och frågar efter data? Är programmet läsa tjockt, eller Skriv tunga? 

När du har läst den här artikeln kommer du att kunna svara på följande frågor:

* Hur ska tycker om ett dokument i en dokumentdatabasen?
* Vad är datamodellering och varför ska jag hand? 
* Hur skiljer sig modellering data i en databas för dokument i en relationsdatabas?
* Hur jag express data relationer i en icke-relationell databas?
* När bädda data och när länkar jag data?

## <a name="embedding-data"></a>Bädda in data
När du startar modellerar data i ett dokumentarkiv, till exempel Azure Cosmos DB försöker behandla entiteter som **fristående dokument** representeras i JSON.

Innan vi fördjupa dig för mycket mer Låt oss ta ett par steg tillbaka och ta en titt på hur vi kan modellen något i en relationsdatabas, ett ämne som många av oss redan är bekant med. I följande exempel visas hur en person kan lagras i en relationsdatabas. 

![Relationsdatabas modellen](./media/sql-api-modeling-data/relational-data-model.png)

När du arbetar med relationsdatabaser vi har blivit vilka undervisning förekommer i år att normalisera, normalisera, normalisera.

Normaliserar data normalt omfattar tar en entitet, till exempel en person, och dela upp frågan separata delar av data. I exemplet ovan kan kan en person ha flera poster för kontakt-information samt flera poster. Vi även ta ytterligare ett steg och redovisar kontaktuppgifter genom att extrahera ytterligare vanliga fält som typen. Samma för adress, varje post här har en typ som *Start* eller *företag* 

Den guidar premise när normalisera data är att **lagra inte redundanta data** på varje registrera och i stället referera till data. I det här exemplet för att läsa en person med sina kontaktuppgifter och adresser, måste du använda kopplingar för att effektivt aggregera dina data vid körning.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Uppdatering av en person med kontaktinformation och adresser kräver skrivåtgärder över många enskilda tabeller. 

Nu ska vi ta en titt på hur vi skulle modeller för samma data som en fristående enhet i en databas för dokumentet.

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

Med metoden ovan har vi nu **Avnormaliserade** personen som registrerar var vi **inbäddade** all information som rör till den här personen som deras kontaktuppgifter och adresser i ett enda JSON-dokument.
Vi har också möjlighet att utföra åtgärder som att ha kontaktuppgifterna med olika former helt eftersom vi inte är begränsad till ett fast schema. 

Hämtar en fullständig personpost från databasen är nu en enda Läsåtgärd mot en enda samling och för ett enskilt dokument. Uppdatera en personpost med kontaktuppgifter och adresser, är också en enda skrivåtgärd mot ett enskilt dokument.

Av denormalizing data, kan programmet behöva utfärda färre frågor och uppdateringar för att slutföra vanliga åtgärder. 

### <a name="when-to-embed"></a>Bädda in
I allmänhet använder inbäddade data modeller när:

* Det finns **innehåller** relationer mellan entiteter.
* Det finns **en till några** relationer mellan entiteter.
* Det finns inbäddade data som **ändras sällan**.
* Det är inbäddat data inte växa **utan gräns**.
* Det finns inbäddade data som är **integrerad** till data i ett dokument.

> [!NOTE]
> Vanligtvis Avnormaliserade data modeller får du bättre **läsa** prestanda.
> 
> 

### <a name="when-not-to-embed"></a>När du inte att bädda in
Tumregel i dokumentet databas är att denormalize allt och bädda in alla data i ett dokument, kan detta leda till vissa situationer bör undvikas.

Ta den här JSON-fragment.

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

Det kan vara vad en post entitet med inbäddade kommentarer skulle se ut som om vi modellera en typisk blogg eller CMS, system. Problemet med det här exemplet är att kommentarer matrisen **unbounded**, vilket innebär att det finns ingen () gräns för antalet kommentarer som en enda post kan ha. Detta blir ett problem som storleken på dokumentet kan växa avsevärt.

När storleken på dokumentet växer möjlighet att överföra data under överföring samt läsning och uppdatering av dokument i skala, påverkas.

I det här fallet kan det vara bättre att tänka på följande modell.

    Post document:
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

    Comment documents:
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

Den här modellen har de senaste tre kommentarer inbäddade för sig själv, vilket är en matris med en fast bunden nu. Andra kommentarer är grupperade i till batchar av 100 kommentarer och lagras i separata dokument. Storlek på batch valdes som 100 eftersom vårt fiktiva program används att läsa in 100 kommentarer i taget.  

Ett annat fall där inbäddning data inte är en bra idé är när den inbäddade data används ofta mellan dokument och kommer att ändras ofta. 

Ta den här JSON-fragment.

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

Detta kan vara en person portfölj. Vi har valt att bädda in lager informationen i varje portfölj dokumentet. I en miljö där relaterade data ändras ofta kommer som en börs handel program, bädda in data som ändras ofta att innebära att du kontinuerligt uppdaterar varje portfölj dokumentet varje gång en börs säljs.

Börs *zaza* får säljas många hundratals gånger i en enda dag och tusentals användare kan ha *zaza* på sina portfölj. Med en datamodell som anges ovan måste vi uppdatera tusentals portfölj dokument många gånger varje dag, vilket leder till ett system som inte skala mycket bra. 

## <a id="Refer"></a>Refererar till data
Därför bädda in data fungerar bra i många fall, men det är tydligt att det finns scenarier när denormalizing data medför flera problem än lönar. Så vad vi gör nu? 

Relationsdatabaser är inte den enda plats där du kan skapa relationer mellan entiteter. I en databas i dokumentet kan du ha information i ett dokument som verkligen är kopplad till data i andra dokument. Nu kan jag inte arbeta för ett även en minut att vi bygger system som skulle vara bättre passar relationsdatabaser i Azure Cosmos DB eller andra dokumentdatabasen, men enkla relationer är skadad och kan vara användbar. 

I JSON nedan vi valde att använda exempel på en portfölj från tidigare men den här gången avser vi lager artikeln på portfölj i stället för att bädda in den. Det här sättet när objektet lager som ändras ofta under dagen endast dokument som behöver uppdateras är i ett lager dokument. 

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


En omedelbar Nackdelen med att den här metoden är dock om ditt program krävs för att visa information om varje lager som hålls kvar när du visar en persons portfölj; i det här fallet skulle du behöva göra flera resor till databasen för att läsa in informationen för varje lager dokument. Här har vi gjort ett beslut att förbättra effektiviteten för skrivåtgärder som inträffa ofta under dagen, men som i sin tur komprometteras på läsåtgärder som eventuellt få mindre påverkan på prestandan för viss systemet.

> [!NOTE]
> Normaliserade datamodeller **kan kräva mer sändningar** till servern.
> 
> 

### <a name="what-about-foreign-keys"></a>Nyheter om externa nycklar?
Eftersom det inte finns något begrepp om en begränsning, foreign key- eller på annat sätt, relationer mellan dokument som du har i dokument är effektivt ”svaga länkar” och kommer inte att verifiera genom att själva databasen. Om du vill säkerställa att data i ett dokument refererar till verkligen finns måste du göra i ditt program, eller genom att använda serversidan utlösare eller lagrade procedurer på Azure Cosmos DB.

### <a name="when-to-reference"></a>När du ska referera till
I allmänhet använder normaliserade data modeller när:

* Som representerar **en-till-många** relationer.
* Som representerar **många-till-många** relationer.
* Relaterade data **ändras ofta**.
* Refererade data kan gå **unbounded**.

> [!NOTE]
> Vanligtvis normaliserar ger bättre **skriva** prestanda.
> 
> 

### <a name="where-do-i-put-the-relationship"></a>Där placera relationen?
Tillväxten av relationen hjälper dig att avgöra i vilken dokumentet för att lagra referensen.

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
    {"id": "1000", "name": "Deep Dive in to Azure Cosmos DB" }

Om antalet böcker per utgivaren är liten med begränsad tillväxt, kan det vara användbart att lagra book referens i publisher-dokument. Men om antal böcker per utgivaren är unbounded skulle sedan denna datamodell leda till föränderliga, växande matriser, som i ovanstående exempel publisher dokumentet. 

Växla runt lite skulle resultera i en modell som representerar fortfarande samma data, men nu undviker dessa stora föränderliga samlingar.

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
    {"id": "1000","name": "Deep Dive in to Azure Cosmos DB", "pub-id": "mspress"}

I exemplet ovan har vi bort samlingen unbounded på utgivaren dokumentet. I stället vi bara har en en referens till en utgivare på varje bokdokument.

### <a name="how-do-i-model-manymany-relationships"></a>Hur jag för att modellera många: många-relationer?
I en relationsdatabas *många: många* relationer ofta modelleras med anslutning till tabeller som bara ansluta poster från andra tabeller tillsammans. 

![Koppla tabeller](./media/sql-api-modeling-data/join-table.png)

Du kanske tro att replikera samma sak med hjälp av dokument och skapa en datamodell som liknar följande.

    Author documents: 
    {"id": "a1", "name": "Thomas Andersen" }
    {"id": "a2", "name": "William Wakefield" }

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101" }
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
    {"id": "b3", "name": "Taking over the world one JSON doc at a time" }
    {"id": "b4", "name": "Learn about Azure Cosmos DB" }
    {"id": "b5", "name": "Deep Dive in to Azure Cosmos DB" }

    Joining documents: 
    {"authorId": "a1", "bookId": "b1" }
    {"authorId": "a2", "bookId": "b1" }
    {"authorId": "a1", "bookId": "b2" }
    {"authorId": "a1", "bookId": "b3" }

Detta kan fungera. Dock kräver inläsning av antingen en författare med deras böcker eller läsa in en bok med dess upphovsman alltid minst två ytterligare frågor mot databasen. En fråga till anslutande dokumentet och sedan en annan fråga för att hämta det faktiska dokumentet är ansluten. 

Om allt anslutning till tabellen gör fäster ihop två typer av data, varför inte släppa den helt?
Tänk på följande.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents: 
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive in to Azure Cosmos DB", "authors": ["a2"]}

Nu om jag har en författare jag vet vilka böcker som de har skrivit omedelbart och om jag hade ett book dokument läsas in I motsats vet ID innehålla. Detta sparar den mellanliggande frågan mot anslutning till tabellen minskar antalet server förfrågningar programmet måste göra. 

## <a id="WrapUp"></a>Hybrid datamodeller
Vi nu har tittat bädda in (eller denormalizing) och refererande (eller normaliserar) data har sina upsides och kompromisser har vi har sett. 

Det behöver inte alltid vara antingen eller inte scared blanda saker lite. 

Baserat på ditt programs specifika användningsmönster och arbetsbelastningar som det kan finnas fall där blanda inbäddad refererade klokt och kan leda till enklare programlogik med färre server förfrågningar men har ändå en bra nivå av prestanda.

Överväg följande JSON. 

    Author documents: 
    {
        "id": "a1",
        "firstName": "Thomas",
        "lastName": "Andersen",        
        "countOfBooks": 3,
         "books": ["b1", "b2", "b3"],
        "images": [
            {"thumbnail": "http://....png"}
            {"profile": "http://....png"}
            {"large": "http://....png"}
        ]
    },
    {
        "id": "a2",
        "firstName": "William",
        "lastName": "Wakefield",
        "countOfBooks": 1,
        "books": ["b1"],
        "images": [
            {"thumbnail": "http://....png"}
        ]
    }

    Book documents:
    {
        "id": "b1",
        "name": "Azure Cosmos DB 101",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
            {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "http://....png"}
        ]
    },
    {
        "id": "b2",
        "name": "Azure Cosmos DB for RDBMS Users",
        "authors": [
            {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "http://....png"},
        ]
    }

Vi har här (mest) följt inbäddad modell, där data från andra enheter som är inbäddade i det översta dokumentet, men andra data refereras. 

Om du tittar på book dokumentet ser vi några intressanta fält när vi tittar på matrisen för författare. Det finns en *id* fält som är det fält som vi använder för att referera tillbaka till ett författare dokument, standard Övning i en normaliserade modell, men sedan vi har också *namn* och *thumbnailUrl*. Vi kunde har precis har fastnat med *id* och lämnas programmet för att hämta ytterligare information den behövs från respektive författare dokumentet med hjälp av länken ””, men eftersom vårt program visar författarens namn och en miniatyrbild med alla böcker visas kan vi spara onödig kommunikation till servern per bok i en lista av denormalizing **vissa** data från författare.

Kontrollera, om författarens namn ändras eller de ville uppdatera sina foto vi skulle behöva gå en uppdatering alla böcker de publicerade någonsin men detta är en godkänd beslut för vårt program baserat på antagandet att författare inte ändrar deras namn så ofta.  

Det finns i exemplet **före beräknade mängder** värden för att spara dyra bearbetning på en Läsåtgärd. I det här exemplet är några av data som är inbäddade i dokumentet författare data som beräknas vid körning. Varje gång en ny bok publiceras, skapas en bokdokument **och** countOfBooks fältet är inställt på ett beräknat värde baserat på antalet book dokument som finns för en viss författare. Denna optimering är bra i skrivskyddade tunga system där vi har råd att utföra beräkningar på skrivningar för att optimera läsningar.

Möjlighet att ha en modell med före beräknade fält är möjligt eftersom Azure Cosmos DB stöder **flera dokument transaktioner**. Många NoSQL-lagringsplatser kan inte göra transaktioner mellan dokument och därför gör designbeslut, till exempel ”inkludera alltid allt”, på grund av den här begränsningen. Du kan använda serversidan utlösare eller lagrade procedurer som Infoga böcker och uppdatera författarna alla inom en ACID-transaktion med Azure Cosmos DB. Nu du inte **har** att bädda in allt i ett dokument bara för att se till att dina data förblir konsekventa.

## <a name="NextSteps"></a>Nästa steg
De största takeaways från den här artikeln är att förstå att datamodeller i en schemafria värld är precis lika viktig som någonsin. 

Precis som det är inte ett enskilt sätt att representera en bit data på en skärm, är det inte ett enskilt sätt att modellera dina data. Du behöver förstå ditt program och hur produceras, använda och bearbeta data. Genom att använda några av de riktlinjer som visas här kan du ange om hur du skapar en modell som åtgärdar omedelbara behov av ditt program. När dina program behöver ändra kan du utnyttja flexibiliteten hos en schemafria databas att omfatta som ändras och utvecklas datamodellen enkelt. 

Om du vill veta mer om Azure Cosmos DB kan referera till tjänstens [dokumentationen](https://azure.microsoft.com/documentation/services/cosmos-db/) sidan. 

Att förstå hur till Fragmentera data över flera partitioner avser [partitionering Data i Azure Cosmos DB](sql-api-partition-data.md). 

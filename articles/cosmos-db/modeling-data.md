---
title: Modelleringsdokumentdata i en NoSQL-databas
titleSuffix: Azure Cosmos DB
description: Läs mer om datamodellering i NoSQL-databaser, skillnader mellan datamodellering i en relationsdatabas och en dokumentdatabas.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: andrl
ms.custom: seodec18
ms.openlocfilehash: 0197c11673f49214dc2cea09b53290993a00c6b3
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744946"
---
# <a name="modeling-document-data-for-nosql-databases"></a>Modelleringsdokumentdata för NoSQL-databaser

Medan schemafria databaser, som Azure Cosmos DB, gör det mycket enkelt att omfatta ändringar i datamodellen det bör fortfarande ta lite tid tänka om dina data. 

Hur data ska lagras? Hur kommer programmet att hämta och fråga efter data? Är programmet att läsa tjockt, eller Skriv tunga? 

När du har läst den här artikeln kommer du att kunna besvara följande frågor:

* Hur ska jag tänka på ett dokument i en dokumentdatabas?
* Vad är datamodellering och varför ska jag bry mig? 
* Hur skiljer sig modellering av finansdata i en dokumentdatabas en relationsdatabas?
* Hur jag express datarelationer i en icke-relationell databas?
* När bäddar jag in data och när länkar jag till data?

## <a name="embedding-data"></a>Bädda in data
När du startar datamodellering i ett dokumentarkiv, till exempel Azure Cosmos DB, försöker hantera dina entiteter som **självständigt dokument** representeras i JSON.

Innan vi fördjupar oss för mycket ytterligare, låt oss ta tillbaka några få steg och ta en titt på hur vi kan modellera något i en relationsdatabas, ett ämne som många redan är bekant med. I följande exempel visas hur en person kan lagras i en relationsdatabas. 

![Relationsdatabas modell](./media/sql-api-modeling-data/relational-data-model.png)

När du arbetar med relationsdatabaser, har vi varit undervisats i flera år för att normalisera, normalisera, normalisera.

Normaliserar dina data vanligtvis innebär att en enhet, till exempel en person och förklara i diskreta delar av data. I exemplet ovan är kan en person ha flera kontakta poster samt flera poster. Vi även gå ett steg längre och bryter ned kontaktinformation genom att extrahera ytterligare vanliga fält som en typ. På samma sätt som för adress, här varje post har en typ som *Start* eller *företag* 

Den guidar premise när normaliserar data ska **Undvik att lagra redundanta data** på varje post och i stället hänvisa till data. I det här exemplet för att läsa en person med deras kontaktuppgifter och adresser, behöver du använda kopplingar för att effektivt aggregera dina data vid körning.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType on cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

Uppdatering av en enskild person med deras kontaktuppgifter och adresser kräver skrivåtgärder över många enskilda tabeller. 

Nu ska vi ta en titt på hur vi skulle modeller för samma data som en fristående enhet i en dokumentdatabas.

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

Med hjälp av metoden ovan har vi nu **Avnormaliserade** personen som registrerar var vi **embedded** all information som är relaterad till den här personen som deras kontaktuppgifter och adresser, till ett enda JSON dokumentet.
Vi har också möjlighet att göra saker som att ha kontaktinformation på olika former helt eftersom vi inte är begränsad till ett fast schema. 

Hämta en fullständig personpost från databasen är nu en enda Läsåtgärd mot en enda samling och för ett enskilt dokument. Uppdatera en personpost med deras kontaktuppgifter och adresser, är också en enskild skrivåtgärd mot ett enskilt dokument.

Genom att avnormalisera data, kan programmet behöva skicka färre frågor och uppdateringar för att slutföra vanliga åtgärder. 

### <a name="when-to-embed"></a>När du ska bädda in
I allmänhet använder inbäddade data modeller när:

* Det finns ** relationer mellan entiteter.
* Det finns **en till några** relationer mellan entiteter.
* Det finns inbäddade data som **ändras sällan**.
* Det är inbäddade data inte växa **utan gräns**.
* Det finns inbäddade data som är **integrerad** till data i ett dokument.

> [!NOTE]
> Normalt Avnormaliserade data som ger bättre **läsa** prestanda.
> 
> 

### <a name="when-not-to-embed"></a>När du inte vill bädda in
Tumregel i en dokumentdatabas är att avnormalisera allt och bädda in alla data i ett enda dokument, kan detta leda till vissa situationer som bör undvikas.

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

Det kan vara vad en post-entitet med inbäddade kommentarer skulle se ut som om vi modellering en typisk blogg eller CMS-system, system. Problem med det här exemplet är att kommentarer matrisen är **obundna**, vilket innebär att det finns ingen () gräns för antal kommentarer som det enda inlägget kan ha. Detta blir ett problem Eftersom storleken på dokumentet kan växa avsevärt.

När storleken på dokumentet ökar möjligheten att överföra data via under överföring samt läsa och uppdatera dokumentet, i skala, kommer att påverkas.

I så fall skulle det vara bättre att tänka på följande modell.

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

Den här modellen har de senaste tre kommentarer inbäddad på inlägget, vilket är en matris med en fast bunden nu. Andra kommentarerna är grupperade i batchar med 100 kommentarer och lagras i separata dokument. Storlek på batch har valts som 100 eftersom vårt fiktiva program används att läsa in 100 kommentarer i taget.  

Ett annat fall där bädda in data inte är en bra idé är när inbäddade data används ofta i dokument och ändras ofta. 

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

Detta kan vara en persons portfölj. Vi har valt att bädda in lagerartiklar informationen till varje portfölj dokument. I en miljö där relaterade data ändras ofta, kommer som aktier handel med program, bädda in data som ändras ofta att innebära att du hela tiden uppdaterar dokumenten portfölj varje gång aktier säljs.

Lager *zaza* får säljas flera hundra gånger under en enda dag och tusentals användare kan ha *zaza* deras portfölj. Med en datamodell som ovanstående vi skulle behöva uppdatera flera tusen portfölj dokument många gånger varje dag som leder till ett system som inte bra med skalning. 

## <a id="Refer"></a>Refererar till data
Därför bädda in data fungerar bra för många fall, men det är tydligt att det finns scenarier när avnormalisera data medför flera problem än vad det är värt att. Så vad vi gör nu? 

Relationsdatabaser är inte den enda plats där du kan skapa relationer mellan entiteter. Du kan ha information i ett dokument som faktiskt är kopplat till data i andra dokument i en dokumentdatabas. Nu kan jag inte utvecklarrådgivning även en minut att vi bygger system som skulle vara bättre lämpade för en relationsdatabas i Azure Cosmos DB eller någon annan dokumentdatabas, men enkla relationer fungerar bra och kan vara användbart. 

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
> 
> 

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
> 
> 

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

Nu om jag har haft en författare, jag vet omedelbart vilka böcker som de har skrivit och omvänt om jag hade ett boken dokument som är inlästa jag skulle känner till ID: n innehålla. Detta sparar den mellanliggande frågor mot en anslutning till tabellen minska antalet servrar nätverksförfrågningar programmet måste göra. 

## <a id="WrapUp"></a>Hybriddatamodeller
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

Om du tittar på bok dokumentet, ser vi några intressanta fält när vi tittar på ett antal författare. Det finns en *id* fältet det vill säga de fält som vi använder för att referera tillbaka till ett dokument för författare, praxis i en normaliserad modell, men sedan vi har också *namn* och *thumbnailUrl*. Vi kan ha fastnat med *id* och programmet för att hämta eventuell ytterligare information som den behövs från respektive redigera dokumentet med hjälp av ”länken”, men eftersom appen visar författarens namn och en miniatyrbild med varje bok visas kan vi spara en tur och RETUR till servern per boken i en lista med avnormalisera **vissa** data från författare.

Visst, om författarens namn ändras eller de ville uppdatera sina foto vi skulle behöva gå en uppdatering varje bok de någonsin publicerats men för vårt program baserat på antagandet att författare inte ändrar namnen ofta, det här är en godtagbar designbeslut.  

I det här exemplet finns **beräknas aggregeringar i förväg** värden för att spara dyra bearbetning på en Läsåtgärd. I det här exemplet är några av de data som är inbäddad i dokumentet författare data som beräknas vid körning. Varje gång en ny bok publiceras, skapas en bokdokument **och** countOfBooks-fältet är inställt på ett beräknat värde baserat på antalet boken dokument som finns för en viss författare. Denna optimering är bra i Läs tung system där vi har råd att utföra beräkningar på skrivningar för att optimera läsningar.

Möjlighet att aktivera en modell med förberäknade fält är möjligt eftersom Azure Cosmos DB stöder **flera dokument transaktioner**. Många NoSQL-Arkiv det går inte att göra transaktioner mellan dokument och därför förespråkar designbeslut, till exempel ”alltid bädda in allt” på grund av den här begränsningen. Med Azure Cosmos DB kan du använda serversidan utlösare eller lagrade procedurer som Infoga böcker och uppdatera författare allt inom en ACID-transaktion. Nu du inte **har** att bädda in allt i ett dokument bara för att se till att dina data förblir konsekvent.

## <a name="NextSteps"></a>Nästa steg
De största takeaways från den här artikeln är att förstå att datamodellering i en schemafri värld är lika viktigt som någonsin. 

Precis som det är inte ett enskilt sätt att representera en typ av data på en skärm, är det inte ett enskilt sätt att modellera dina data. Du behöver att förstå ditt program och hur den skapas, använda och bearbeta data. Sedan kan du ange om hur du skapar en modell som åtgärdar omedelbara behov av ditt program genom att använda några av de riktlinjer som beskrivs här. När dina program behöver ändra kan du utnyttja flexibiliteten i en schemafri databas till att använda som ändras och enkelt kan utveckla din datamodell. 

Om du vill veta mer om Azure Cosmos DB kan du referera till tjänstens [dokumentation](https://azure.microsoft.com/documentation/services/cosmos-db/) sidan. 

Att förstå hur till shard dina data på flera partitioner, referera till [partitionera Data i Azure Cosmos DB](sql-api-partition-data.md). 

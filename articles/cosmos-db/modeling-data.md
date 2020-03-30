---
title: Modellering av data i Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Lär dig mer om datamodellering i NoSQL-databaser, skillnader mellan modelleringsdata i en relationsdatabas och en dokumentdatabas.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 523049ea3286445117f41147f3dd12a2c911d1ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72755019"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Datamodellering i Azure Cosmos DB

Även om schemafria databaser, som Azure Cosmos DB, gör det super enkelt att lagra och fråga ostrukturerade och halvstrukturerade data, bör du ägna lite tid åt att tänka på din datamodell för att få ut det mesta av tjänsten när det gäller prestanda och skalbarhet och lägsta Kostnad.

Hur ska data lagras? Hur ska ditt program hämta och fråga data? Är din ansökan läs-tung, eller skriv-tung?

Efter att ha läst den här artikeln kommer du att kunna svara på följande frågor:

* Vad är datamodellering och varför ska jag bry mig?
* Hur skiljer sig modelleringsdata i Azure Cosmos DB från en relationsdatabas?
* Hur uttrycker jag datarelationer i en icke-relationsdatabas?
* När bäddar jag in data och när länkar jag till data?

## <a name="embedding-data"></a>Bädda in data

När du börjar modellera data i Azure Cosmos DB försöka behandla dina entiteter som fristående objekt som representeras som **JSON-dokument.**

Som jämförelse, låt oss först se hur vi kan modellera data i en relationsdatabas. Följande exempel visar hur en person kan lagras i en relationsdatabas.

![Relationsdatabasmodell](./media/sql-api-modeling-data/relational-data-model.png)

När du arbetar med relationsdatabaser är strategin att normalisera alla dina data. Att normalisera dina data innebär vanligtvis att du tar en entitet, till exempel en person, och att dela upp dem i diskreta komponenter. I exemplet ovan kan en person ha flera kontaktinformationsposter samt flera adressposter. Kontaktuppgifter kan delas upp ytterligare genom att ytterligare extrahera vanliga fält som en typ. Detsamma gäller för adress, varje post kan vara av typen *Hem* eller *Företag*.

Den vägledande premissen vid normalisering av data är att undvika att **lagra överflödiga data** på varje post och snarare referera till data. I det här exemplet måste du använda JOINS för att effektivt skriva tillbaka (eller denormalisera) dina data vid körning om du vill läsa en person, med alla deras kontaktuppgifter och adresser.

    SELECT p.FirstName, p.LastName, a.City, cd.Detail
    FROM Person p
    JOIN ContactDetail cd ON cd.PersonId = p.Id
    JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
    JOIN Address a ON a.PersonId = p.Id

För att uppdatera en enskild person med deras kontaktuppgifter och adresser krävs skrivåtgärder i många enskilda tabeller.

Nu ska vi ta en titt på hur vi skulle modellera samma data som en fristående entitet i Azure Cosmos DB.

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

Med hjälp av metoden ovan har vi **denormaliserat** personposten genom att **bädda in** all information som rör den här personen, till exempel deras kontaktuppgifter och adresser, i ett *enda JSON-dokument.*
Dessutom, eftersom vi inte är begränsade till ett fast schema har vi flexibiliteten att göra saker som att ha kontaktuppgifter till olika former helt.

Att hämta en fullständig personpost från databasen är nu en **enda läsåtgärd** mot en enda behållare och för ett enda objekt. Att uppdatera en personpost, med deras kontaktuppgifter och adresser, är också en **enda skrivåtgärd** mot ett enda objekt.

Genom att denormalisera data kan ditt program behöva utfärda färre frågor och uppdateringar för att slutföra vanliga åtgärder.

### <a name="when-to-embed"></a>När du ska bädda in

I allmänhet använder du inbäddade datamodeller när:

* Det finns relationer mellan **entiteter.**
* Det finns **en-till-få** relationer mellan entiteter.
* Det finns inbäddade data som **ändras sällan**.
* Det finns inbäddade data som inte kommer att växa **utan bunden**.
* Det finns inbäddade data som **efterfrågas ofta tillsammans**.

> [!NOTE]
> Vanligtvis denormaliserade datamodeller ger bättre **läsprestanda.**

### <a name="when-not-to-embed"></a>När du inte ska bädda in

Även om tumregeln i Azure Cosmos DB är att denormalisera allt och bädda in alla data i ett enda objekt, kan detta leda till vissa situationer som bör undvikas.

Ta det här JSON-utdraget.

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

Detta kan vara vad ett inlägg enhet med inbäddade kommentarer skulle se ut om vi var modellering en typisk blogg, eller CMS, system. Problemet med det här exemplet är att kommentarsmatrisen är **obegränsad,** vilket innebär att det inte finns någon (praktisk) gräns för hur många kommentarer ett enskilt inlägg kan ha. Detta kan bli ett problem eftersom storleken på objektet kan växa oändligt stor.

När objektets storlek ökar påverkas möjligheten att överföra data över kabeln samt att läsa och uppdatera objektet i stor skala.

I det här fallet skulle det vara bättre att överväga följande datamodell.

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

Den här modellen har de tre senaste kommentarerna inbäddade i inläggsbehållaren, som är en matris med en fast uppsättning attribut. De andra kommentarerna är grupperade i grupperade i grupper om 100 kommentarer och lagras som separata objekt. Storleken på partiet valdes som 100 eftersom vår fiktiva ansökan tillåter användaren att ladda 100 kommentarer åt gången.  

Ett annat fall där inbäddning av data inte är en bra idé är när inbäddade data används ofta över objekt och ändras ofta.

Ta det här JSON-utdraget.

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

Detta kan representera en persons aktieportfölj. Vi har valt att bädda in aktieinformationen i varje portföljdokument. I en miljö där relaterade data förändras ofta, som en aktiehandel program, bädda in data som ändras ofta kommer att innebära att du ständigt uppdaterar varje portfölj dokument varje gång en aktie handlas.

Stock *zaza* kan handlas många hundra gånger på en enda dag och tusentals användare kan ha *zaza* på sin portfölj. Med en datamodell som ovanstående skulle vi behöva uppdatera tusentals portföljdokument många gånger varje dag som leder till ett system som inte kommer att skala bra.

## <a name="referencing-data"></a>Referera till data

Inbäddning av data fungerar bra i många fall, men det finns scenarier när denormaling dina data kommer att orsaka fler problem än det är värt. Så vad gör vi nu?

Relationsdatabaser är inte den enda plats där du kan skapa relationer mellan entiteter. I en dokumentdatabas kan du ha information i ett dokument som relaterar till data i andra dokument. Vi rekommenderar inte att skapa system som skulle vara bättre lämpade för en relationsdatabas i Azure Cosmos DB, eller någon annan dokumentdatabas, men enkla relationer är bra och kan vara användbara.

I JSON nedan valde vi att använda exemplet med en aktieportfölj från tidigare men den här gången hänvisar vi till aktieposten på portföljen istället för att bädda in den. På så sätt, när lagerartikeln ändras ofta under dagen, är det enda dokument som behöver uppdateras det enda lagerdokumentet.

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

En omedelbar nackdel med detta tillvägagångssätt är dock om din ansökan är skyldig att visa information om varje aktie som innehas när du visar en persons portfölj; I det här fallet måste du göra flera resor till databasen för att läsa in informationen för varje lagerdokument. Här har vi fattat ett beslut om att förbättra effektiviteten i skrivoperationer, vilket sker ofta under hela dagen, men i sin tur äventyras på läsåtgärder som potentiellt har mindre inverkan på prestanda för just detta system.

> [!NOTE]
> Normaliserade datamodeller **kan kräva fler tur- och returresor** till servern.

### <a name="what-about-foreign-keys"></a>Hur är det med utländska nycklar?

Eftersom det för närvarande inte finns något begrepp om en begränsning, sekundär nyckel eller på annat sätt, några mellandokument relationer som du har i dokument är effektivt "svaga länkar" och kommer inte att verifieras av databasen själv. Om du vill vara medveten om att de data som ett dokument refererar till faktiskt finns måste du göra detta i ditt program eller med hjälp av utlösare på serversidan eller lagrade procedurer på Azure Cosmos DB.

### <a name="when-to-reference"></a>När ska referens

Använd i allmänhet normaliserade datamodeller när:

* Representerar **en-till-många** relationer.
* Representerar **många-till-många relationer.**
* Relaterade data **ändras ofta**.
* Refererade data kan vara **obegränsade**.

> [!NOTE]
> Normalt normalisering ger bättre **skrivprestanda.**

### <a name="where-do-i-put-the-relationship"></a>Var lägger jag relationen?

Tillväxten av relationen hjälper till att avgöra i vilket dokument som referensen ska lagras.

Om vi tittar på JSON nedan som modeller förlag och böcker.

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

Om antalet böcker per utgivare är litet med begränsad tillväxt kan det vara användbart att lagra bokreferensen i förlaget. Men om antalet böcker per utgivare är obegränsat, skulle den här datamodellen leda till föränderliga, växande matriser, som i exemplet utgivaren dokumentet ovan.

Att växla runt saker lite skulle resultera i en modell som fortfarande representerar samma data men nu undviker dessa stora föränderliga samlingar.

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

I exemplet ovan har vi tappat den obegränsade samlingen i utgivardokumentet. Istället har vi bara en hänvisning till förlaget på varje bok dokument.

### <a name="how-do-i-model-manymany-relationships"></a>Hur modellerar jag många:många relationer?

I en relationsdatabas modelleras *många:många* relationer ofta med kopplingstabeller, som bara sammanfogar poster från andra tabeller tillsammans.

![Kopplingstabeller](./media/sql-api-modeling-data/join-table.png)

Du kan frestas att replikera samma sak med hjälp av dokument och producera en datamodell som liknar följande.

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

Det här skulle fungera. Men att läsa in antingen en författare med sina böcker, eller läsa in en bok med författaren, skulle alltid kräva minst två ytterligare frågor mot databasen. En fråga till det kopplade dokumentet och sedan en annan fråga för att hämta det faktiska dokumentet som kopplas.

Om allt detta kopplingstabell gör är limning ihop två bitar av data, så varför inte släppa den helt?
Tänk på följande.

    Author documents:
    {"id": "a1", "name": "Thomas Andersen", "books": ["b1, "b2", "b3"]}
    {"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

    Book documents:
    {"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
    {"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
    {"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
    {"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}

Nu, om jag hade en författare, jag vet genast vilka böcker de har skrivit, och omvänt om jag hade en bok dokument laddad jag skulle veta ID: er av författaren (s). Detta sparar den mellanliggande frågan mot kopplingstabellen vilket minskar antalet tidsresor som programmet måste göra.

## <a name="hybrid-data-models"></a>Hybriddatamodeller

Vi har nu tittat inbäddning (eller denormalizing) och refererar (eller normalisera) data, var och en har sina uppsidor och var och en har kompromisser som vi har sett.

Det behöver inte alltid vara antingen eller, var inte rädd för att blanda ihop saker lite.

Baserat på programmets specifika användningsmönster och arbetsbelastningar kan det finnas fall där det är meningsfullt att blanda inbäddade och refererade data och kan leda till enklare programlogik med färre serverrundningsturer samtidigt som en bra prestanda bibehålls.

Tänk på följande JSON.

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

Här har vi (mestadels) följt den inbäddade modellen, där data från andra entiteter är inbäddade i dokumentet på den översta nivån, men andra data refereras.

Om man tittar på bokdokumentet kan vi se några intressanta fält när vi tittar på utbudet av författare. Det finns `id` ett fält som är det område vi använder för att hänvisa tillbaka till ett `name` `thumbnailUrl`författardokument, standardpraxis i en normaliserad modell, men då har vi också och . Vi kunde ha `id` fastnat med och lämnade ansökan för att få ytterligare information som behövs från respektive författare dokument med hjälp av "länk", men eftersom vår ansökan visar författarens namn och en miniatyrbild med varje bok som visas kan vi spara en rundresa till servern per bok i en lista genom att denormalisera **vissa** data från författaren.

Visst, om författarens namn ändrats eller de ville uppdatera sitt foto vi måste gå och uppdatera varje bok de någonsin publicerats, men för vår ansökan, baserat på antagandet att författarna inte ändrar sina namn ofta, är detta ett acceptabelt designbeslut.  

I exemplet finns det **förberäknade aggregatvärden** för att spara dyr bearbetning på en läsåtgärd. I exemplet är vissa av de data som är inbäddade i författardokumentet data som beräknas vid körning. Varje gång en ny bok publiceras skapas ett bokdokument **och** fältet countOfBooks anges till ett beräknat värde baserat på antalet bokdokument som finns för en viss författare. Denna optimering skulle vara bra i läsa tunga system där vi har råd att göra beräkningar på skriver för att optimera läser.

Möjligheten att ha en modell med förberäknade fält är möjlig eftersom Azure Cosmos DB stöder **transaktioner med flera dokument**. Många NoSQL-butiker kan inte göra transaktioner mellan dokument och förespråkar därför designbeslut, till exempel "bädda alltid in allt", på grund av denna begränsning. Med Azure Cosmos DB kan du använda utlösare på serversidan eller lagrade procedurer som infogar böcker och uppdaterar författare i en ACID-transaktion. Nu behöver du **inte** bädda in allt i ett dokument bara för att vara säker på att dina data förblir konsekventa.

## <a name="distinguishing-between-different-document-types"></a>Skilja mellan olika dokumenttyper

I vissa fall kanske du vill blanda olika dokumenttyper i samma samling. Detta är vanligtvis fallet när du vill att flera, relaterade dokument ska sitta i samma [partition](partitioning-overview.md). Du kan till exempel placera både böcker och bokrecensioner `bookId`i samma samling och partitionera den med . I en sådan situation vill du vanligtvis lägga till dina dokument med ett fält som identifierar deras typ för att skilja dem åt.

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

Precis som det inte finns något enda sätt att representera en bit data på en skärm, finns det inget enda sätt att modellera dina data. Du måste förstå ditt program och hur det kommer att producera, konsumera och bearbeta data. Sedan, genom att tillämpa några av de riktlinjer som presenteras här kan du ställa in om att skapa en modell som tillgodoser de omedelbara behoven hos ditt program. När dina program behöver ändras kan du utnyttja flexibiliteten i en schemafri databas för att enkelt omfamna den ändringen och utveckla din datamodell.

Mer information om Azure Cosmos DB finns [documentation](https://azure.microsoft.com/documentation/services/cosmos-db/) på tjänstens dokumentationssida.

Mer information om hur du fragmenterar dina data över flera partitioner finns [i Partitioneringsdata i Azure Cosmos DB](sql-api-partition-data.md).

Mer information om hur du modellerar och partitionerar data på Azure Cosmos DB med ett verkligt exempel finns [i Datamodellering och partitionering – ett verkligt exempel](how-to-model-partition-example.md).

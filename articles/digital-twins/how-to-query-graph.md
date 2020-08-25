---
title: Fråga tvillingdiagrammet
titleSuffix: Azure Digital Twins
description: Mer information finns i så här frågar du efter information i Azure Digitals dubbla diagram.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e7be96fcab0807ac8c6500c3b360f9380b4d2b28
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824958"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Skicka frågor till Azure Digitals dubbla grafer

Den här artikeln innehåller exempel och mer information om hur du använder [Azures digitala dubbla frågespråk](concepts-query-language.md) för att skicka frågor till den [dubbla grafen](concepts-twins-graph.md) . Du kör frågor i grafen med hjälp av Azures digitala dubbla [**API: er för frågor**](how-to-use-apis-sdks.md).

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

Resten av den här artikeln innehåller exempel på hur du använder dessa åtgärder.

## <a name="query-syntax"></a>Frågesyntax

Det här avsnittet innehåller exempel frågor som illustrerar frågans språk struktur och utför möjliga fråge åtgärder.

Hämta [digitala dubbla](concepts-twins-graph.md) med egenskaper (inklusive ID och metadata):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> ID: t för en digital delad frågas med hjälp av fältet metadata `$dtId` .

Du kan också få dubbla på sina *Taggegenskaper* genom att följa anvisningarna i [lägga till taggar till digitala dubbla](how-to-use-tags.md):
```sql
select * from digitaltwins where is_defined(tags.red) 
```

### <a name="select-top-items"></a>Markera de översta objekten

Du kan välja flera "Top"-objekt i en fråga med hjälp av- `Select TOP` satsen.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE property = 42
```

### <a name="query-by-model"></a>Fråga efter modell

`IS_OF_MODEL`Operatorn kan användas för att filtrera baserat på den dubbla [modellen](concepts-models.md). Det stöder arv och har flera alternativ för överlagring.

Den enklaste användningen av `IS_OF_MODEL` tar bara en `twinTypeName` parameter: `IS_OF_MODEL(twinTypeName)` .
Här är ett exempel på en fråga som skickar ett värde i den här parametern:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

Om du vill ange en dubbel samling att söka i när det finns mer än en (t. ex. När en `JOIN` används) lägger du till `twinCollection` parametern: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Här är ett exempel på en fråga som lägger till ett värde för den här parametern:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

Om du vill göra en exakt matchning lägger du till `exact` parametern: `IS_OF_MODEL(twinTypeName, exact)` .
Här är ett exempel på en fråga som lägger till ett värde för den här parametern:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

Du kan också skicka alla tre argumenten tillsammans: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Här är ett exempel på en fråga som anger ett värde för alla tre parametrar:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>Fråga baserat på relationer

När du frågar baserat på digitala dubbla relationer, har Azure Digitals interservers frågespråk en speciell syntax.

Relationerna hämtas till fråge omfånget i- `FROM` satsen. En viktig skillnad från "klassiska" SQL-typ språk är att varje uttryck i den här `FROM` satsen inte är en tabell. i stället `FROM` uttrycker satsen en relation mellan olika enheter och är skriven med en digital Azure-version av `JOIN` . 

Kom ihåg att med Azure Digitals dubbla [modell](concepts-models.md) funktioner, finns det inga relationer oberoende av varandra. Det innebär att Azure Digitals-frågespråket `JOIN` är lite annorlunda än den allmänna SQL `JOIN` , eftersom relationer här inte kan frågas oberoende och måste vara knutna till ett dubbel.
För att ta med den här skillnaden `RELATED` används nyckelordet i- `JOIN` satsen för att referera till en grupp med dubbla relationer. 

Följande avsnitt innehåller flera exempel på hur det ser ut.

> [!TIP]
> Den här funktionen imiterar den här funktionen de dokumentbaserade funktionerna i CosmosDB, där `JOIN` kan utföras på underordnade objekt i ett dokument. CosmosDB använder `IN` nyckelordet för att ange att `JOIN` är avsett att iterera över mat ris element i det aktuella Sammanhangs dokumentet.

#### <a name="relationship-based-query-examples"></a>Exempel på Relations hip-baserade frågor

Om du vill hämta en data uppsättning som inkluderar relationer använder `FROM` du ett enda uttryck följt av N `JOIN` -instruktioner, där `JOIN` uttrycken uttrycker relationer för resultatet av en tidigare `FROM` or- `JOIN` instruktion.

Här är en exempel Relations hip-baserad fråga. Det här kodfragmentet väljer alla digitala, dubbla med *ID-* egenskapen för "ABC", och alla digitala garn som är relaterade till dessa Digitala flätar via en *contains* -relation. 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> Utvecklaren behöver inte korrelera detta `JOIN` med ett nyckel värde i `WHERE` -satsen (eller ange ett nyckel värde infogat med `JOIN` definitionen). Den här korrelationen beräknas automatiskt av systemet, eftersom Relations egenskaperna identifierar målentiteten.

#### <a name="query-the-properties-of-a-relationship"></a>Fråga egenskaperna för en relation

På samma sätt som digitala dubbla har egenskaper som beskrivs via DTDL, kan relationer också ha egenskaper. Med Azures digitala Flätaa frågespråk kan du filtrera och projicera relationer genom att tilldela ett alias till relationen i- `JOIN` satsen. 

Anta till exempel en *servicedBy* -relation som har en *reportedCondition* -egenskap. I nedanstående fråga får den här relationen ett alias för R för att referera till egenskapen.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

I exemplet ovan noterar du hur *reportedCondition* är en egenskap hos *servicedBy* -relationen (inte av någon digital, som har en *servicedBy* -relation).

### <a name="query-with-multiple-joins"></a>Fråga med flera kopplingar

I för hands versionen stöds för närvarande upp till fem `JOIN` s i en enda fråga. På så sätt kan du förflytta flera nivåer av relationer samtidigt.

Här är ett exempel på en fråga med flera kopplingar, som hämtar alla lampor som finns på ljus panelerna i rum 1 och 2.

```sql
SELECT LightBulb 
FROM DIGITALTWINS Room 
JOIN LightPanel RELATED Room.contains 
JOIN LightBulb RELATED LightPanel.contains 
WHERE IS_OF_MODEL(LightPanel, ‘dtmi:contoso:com:lightpanel;1’) 
AND IS_OF_MODEL(LightBulb, ‘dtmi:contoso:com:lightbulb ;1’) 
AND Room.$dtId IN [‘room1’, ‘room2’] 
```

## <a name="run-queries-with-an-api-call"></a>Köra frågor med ett API-anrop

När du har bestämt dig för en frågesträng kör du den genom att anropa API: et för **frågor**.
Följande kodfragment visar det här anropet från klient appen:

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

Anropet returnerar frågeresultat i form av ett QueryResult-objekt. 

Fråge anrop stöder sid indelning. Här är ett komplett exempel med fel hantering och växling:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>Frågornas begränsningar

Det kan finnas en fördröjning på upp till 10 sekunder innan ändringarna i instansen återspeglas i frågor. Om du till exempel utför en åtgärd som att skapa eller ta bort dubbla med DigitalTwins-API: t kan det hända att resultatet inte omedelbart avspeglas i frågor som API-begäranden. Det bör finnas tillräckligt med väntan på en kort period för att lösa problemet.

Det finns ytterligare begränsningar för att använda `JOIN` under för hands versionen.
* Det finns inte stöd för under frågor i `FROM` instruktionen.
* `OUTER JOIN` semantik stöds inte, vilket innebär att om relationen har en rangordning på noll, elimineras hela raden rad från resultat uppsättningen utdata.
* I för hands versionen begränsas diagrammets djup till fem `JOIN` nivåer per fråga.
* Källan för `JOIN` åtgärder är begränsad: frågan måste deklarera de dubbla platser där frågan börjar.

## <a name="query-best-practices"></a>Bästa metoderna för frågor

Nedan visas några tips för att fråga med Azure Digital-dubbla.

* Tänk på fråge mönstret under modell design fasen. Försök att se till att relationer som behöver besvaras i en enskild fråga modelleras som en relation med en nivå.
* Utforma egenskaper på ett sätt som gör det möjligt att undvika stora resultat uppsättningar från graf Traversal.
* Du kan avsevärt minska antalet frågor som du behöver genom att skapa en matris med dubbla och frågor med `IN` operatorn. Anta till exempel ett scenario där *byggnader* som innehåller *golv* och *golv* innehåller *rum*. Om du vill söka efter rum i en byggnad som är frekventa kan du:

    1. Hitta golv i byggnaden baserat på `contains` relation
        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ``` 
    2. Om du vill hitta rum, i stället för att ta med golv en-i-ett och köra en `JOIN` fråga för att hitta rummen för var och en, kan du fråga med en samling våningar i byggnaden (med namnet *våning* i frågan nedan).

        I klient program:
        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```
        I fråga:
        ```sql
        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
        ```
* Egenskaps namn och-värden är Skift läges känsliga, så var noga med att använda de exakta namnen som definieras i modellerna. Om egenskaps namnen är felstavade eller felaktigt bokstäver, är resultat uppsättningen tom utan att några fel returneras.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure Digitals dubbla API: er och SDK: er](how-to-use-apis-sdks.md), inklusive fråge-API som används för att köra frågorna från den här artikeln.

---
title: Fråga tvillingdiagrammet
titleSuffix: Azure Digital Twins
description: Mer information finns i så här frågar du efter information i Azure Digitals dubbla diagram.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8aad0d9fde30a235903364d57a73c1c53f08ecce
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145794"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Skicka frågor till Azure Digitals dubbla grafer

Den här artikeln innehåller exempel och mer information om hur du använder [Azures digitala dubbla frågespråk](concepts-query-language.md) för att skicka frågor till den [dubbla grafen](concepts-twins-graph.md) . Du kör frågor i grafen med hjälp av Azures digitala dubbla [**API: er för frågor**](/rest/api/digital-twins/dataplane/query).

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

Resten av den här artikeln innehåller exempel på hur du använder dessa åtgärder.

## <a name="query-syntax"></a>Frågesyntax

Det här avsnittet innehåller exempel frågor som illustrerar frågans språk struktur och utför möjliga frågor på [digitala dubbla](concepts-twins-graph.md).

### <a name="show-all-existing-digital-twins"></a>Visa alla befintliga digitala dubbla

Här är den grundläggande fråga som returnerar en lista över alla digitala enheter i instansen:

```sql
SELECT *
FROM DIGITALTWINS
```

### <a name="select-top-items"></a>Markera de översta objekten

Du kan välja flera "Top"-objekt i en fråga med hjälp av- `Select TOP` satsen.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

### <a name="count-items"></a>Antal objekt

Du kan räkna antalet objekt i en resultat uppsättning med hjälp av- `Select COUNT` satsen:

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

Lägg till en `WHERE` sats för att räkna antalet objekt som uppfyller ett visst kriterium. Här följer några exempel på hur du kan räkna med ett applicerat filter baserat på typen av dubbel modell (mer information om den här syntaxen finns i [*fråga efter modell*](#query-by-model) nedan):

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

Du kan också använda `COUNT` tillsammans med- `JOIN` satsen. Här är en fråga som räknar alla lampor som finns på de ljusa panelerna i rum 1 och 2:

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

### <a name="specify-return-set-with-projections"></a>Ange retur uppsättning med projektioner

Med hjälp av projektioner kan du välja vilka kolumner som en fråga ska returnera.

>[!NOTE]
>För tillfället stöds inte komplexa egenskaper. För att se till att projektions egenskaperna är giltiga kombinerar du projektionerna med en `IS_PRIMITIVE` bock.

Här är ett exempel på en fråga som använder projektion för att returnera dubbla och relationer. Följande fråga projekterar *konsumenten* , *fabriken* och *Edge* från ett scenario där en *fabrik* med ID: t *ABC* är relaterad till *konsumenten* via en relation av *fabriken. kunden* och relationen presenteras som en *gräns* .

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

Du kan också använda projektion för att returnera en dubbels egenskap. I följande fråga visas *namn* egenskapen för de *konsumenter* som är relaterade till *fabriken* med ID: t *ABC* via en relation av *Factory. Custom* .

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

Du kan också använda projektion för att returnera en egenskap för en relation. Som i föregående exempel projekt i följande fråga projektets *namn* egenskap för de *konsumenter* som är relaterade till *fabriken* med ID: t *ABC* genom en relation mellan *fabrik. kund* ; men nu returnerar den även två egenskaper för relationen, *prop1* och *prop2* . Det gör detta genom att namnge Relations *kanten* och samla in dess egenskaper.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Du kan också använda alias för att förenkla frågor med projektion.

Följande fråga utför samma åtgärder som i föregående exempel, men ger alias för egenskaps namnen till `consumerName` , `first` `second` och `factoryArea` .

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)"
```

Här är en liknande fråga som frågar samma uppsättning som ovan, men projekterar endast egenskapen *Consumer.name* som `consumerName` , och projekterar hela *fabriken* som en dubbel.

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

### <a name="query-by-property"></a>Fråga efter egenskap

Hämta digitala dubbla med **Egenskaper** (inklusive ID och metadata):

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> ID: t för en digital delad frågas med hjälp av fältet metadata `$dtId` .

Du kan också skapa dubbla baserat på **om en viss egenskap har definierats** . Här är en fråga som hämtar dubbla med en definierad *plats* egenskap:

```sql
SELECT *
FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Detta kan hjälpa dig att få en upplösning med hjälp av taggarnas *egenskaper,* enligt beskrivningen i [lägga till taggar till digitala dubbla](how-to-use-tags.md). Här är en fråga som hämtar alla dubbla Taggar med *rött* :

```sql
select * from digitaltwins where is_defined(tags.red)
```

Du kan också få dubbla baserat på **typen av egenskap** . Här är en fråga som sammanfaller vars *temperatur* egenskap är ett tal:

```sql
SELECT * FROM DIGITALTWINS T
WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>Fråga efter modell

`IS_OF_MODEL`Operatorn kan användas för att filtrera baserat på den dubbla [**modellen**](concepts-models.md).

Det tar hänsyn till [arv](concepts-models.md#model-inheritance) och [versions sortering](how-to-manage-model.md#update-models) , och utvärderas till **Sant** för en bestämd mellan om den dubbla uppfyller något av följande villkor:

* Den dubbla implementerar modellen direkt `IS_OF_MODEL()` och versions numret för modellen på den dubbla är *större än eller lika* med versions numret för den angivna modellen
* Den dubbla implementerar en modell som *utökar* modellen till `IS_OF_MODEL()` och den dubblans utökade modell versions nummer är *större än eller lika* med versions numret för den angivna modellen

Den här metoden har flera alternativ för överlagring.

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

När du frågar baserat på digitala dubbla **relationer** , har Azures digitala dubbla frågespråket frågespråk en speciell syntax.

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

På samma sätt som digitala dubbla har egenskaper som beskrivs via DTDL, kan relationer också ha egenskaper. Du kan fråga efter varandra **utifrån egenskaperna för deras relationer** .
Med Azures digitala Flätaa frågespråk kan du filtrera och projicera relationer genom att tilldela ett alias till relationen i- `JOIN` satsen.

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

Upp till fem `JOIN` s stöds i en enda fråga. På så sätt kan du förflytta flera nivåer av relationer samtidigt.

Här är ett exempel på en fråga med flera kopplingar, som hämtar alla lampor som finns på ljus panelerna i rum 1 och 2.

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

### <a name="other-compound-query-examples"></a>Exempel på andra sammansatta frågor

Du kan **kombinera** någon av ovanstående typer av fråga med hjälp av kombinations operatorer för att inkludera mer information i en enskild fråga. Här följer några ytterligare exempel på sammansatta frågor som frågar efter fler än en typ av dubbel beskrivare på en gång.

| Beskrivning | Söka i data |
| --- | --- |
| Från de enheter som *Room 123* har kan du returnera de MxChip-enheter som hanterar rollen operatör | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Hämta dubbla som har en relation som heter *innehåller* med en annan som har ID: t *id1* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Hämta alla rum för den här rums modellen som finns i *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="reference-expressions-and-conditions"></a>Referens: uttryck och villkor

Det här avsnittet innehåller en referens för de operatörer och funktioner som är tillgängliga när du skriver Azure Digitals dubbla frågor.

### <a name="operators"></a>Operatorer

Följande operatorer stöds:

| Familj | Operatorer |
| --- | --- |
| Logisk |AND, OR, NOT (och, eller, inte) |
| Jämförelse |=,! =, <, >, <=, >= |
| Innehåller | I, NOM |

### <a name="functions"></a>Funktioner

Följande typ av kontroll och data typs funktioner stöds:

| Funktion | Beskrivning |
| -------- | ----------- |
| IS_DEFINED | Returnerar ett booleskt värde som anger huruvida egenskapen har tilldelats ett värde. Detta stöds endast när värdet är en primitiv typ. Primitiva typer är sträng, boolesk, numerisk eller `null` . DateTime, objekt typer och matriser stöds inte. |
| IS_OF_MODEL | Returnerar ett booleskt värde som anger om den angivna dubbla matchar den angivna modell typen |
| IS_BOOL | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är ett booleskt värde. |
| IS_NUMBER | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är ett tal. |
| IS_STRING | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är en sträng. |
| IS_NULL | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är null. |
| IS_PRIMITIVE | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är en primitiv (sträng, boolesk, numerisk eller `null` ). |
| IS_OBJECT | Returnerar ett booleskt värde som anger om typen för det angivna uttrycket är ett JSON-objekt. |

Följande sträng funktioner stöds:

| Funktion | Beskrivning |
| -------- | ----------- |
| STARTSWITH (x, y) | Returnerar ett booleskt värde som anger om det första sträng uttrycket börjar med det andra. |
| ENDSWITH (x, y) | Returnerar ett booleskt värde som anger om det första sträng uttrycket slutar med det andra. |

## <a name="run-queries-with-an-api-call"></a>Köra frågor med ett API-anrop

När du har bestämt dig för en frågesträng kör du den genom att anropa API: et för **frågor** .
Följande kodfragment visar det här anropet från klient appen:

```csharp

var adtInstanceEndpoint = new Uri(your-Azure-Digital-Twins-instance-URL>);
var tokenCredential = new DefaultAzureCredential();

var client = new DigitalTwinsClient(adtInstanceEndpoint, tokenCredential);

string query = "SELECT * FROM digitaltwins";
AsyncPageable<string> result = await client.QueryAsync<string>(query);
```

Anropet returnerar frågeresultat i form av ett String-objekt.

Fråge anrop stöder sid indelning. Här är ett fullständigt exempel som använder sig av `BasicDigitalTwin` typen frågeresultat med fel hantering och växling:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<BasicDigitalTwin> qresult = client.QueryAsync<BasicDigitalTwin>(query);
    await foreach (BasicDigitalTwin item in qresult)
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="query-limitations"></a>Frågornas begränsningar

Det kan finnas en fördröjning på upp till 10 sekunder innan ändringarna i instansen återspeglas i frågor. Om du till exempel utför en åtgärd som att skapa eller ta bort dubbla med DigitalTwins-API: t kan det hända att resultatet inte omedelbart avspeglas i frågor som API-begäranden. Det bör finnas tillräckligt med väntan på en kort period för att lösa problemet.

Det finns ytterligare begränsningar för att använda `JOIN` .

* Det finns inte stöd för under frågor i `FROM` instruktionen.
* `OUTER JOIN` semantik stöds inte, vilket innebär att om relationen har en rangordning på noll, elimineras hela raden rad från resultat uppsättningen utdata.
* Diagram över gångs djupet är begränsat till fem `JOIN` nivåer per fråga.
* Källan för `JOIN` åtgärder är begränsad: frågan måste deklarera de dubbla platser där frågan börjar.

## <a name="query-best-practices"></a>Bästa metoderna för frågor

Nedan visas några tips för att fråga med Azure Digital-dubbla.

* Tänk på fråge mönstret under modell design fasen. Försök att se till att relationer som behöver besvaras i en enskild fråga modelleras som en relation med en nivå.
* Utforma egenskaper på ett sätt som gör det möjligt att undvika stora resultat uppsättningar från graf Traversal.
* Du kan avsevärt minska antalet frågor som du behöver genom att skapa en matris med dubbla och frågor med `IN` operatorn. Anta till exempel ett scenario där *byggnader* som innehåller *golv* och *golv* innehåller *rum* . Om du vill söka efter rum i en byggnad som är frekventa kan du:

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

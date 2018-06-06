---
title: SQL-frågor för Azure Cosmos DB | Microsoft Docs
description: Lär dig mer om SQL-syntax, databasbegrepp och SQL-frågor för Azure Cosmos DB. SQL kan användas som en JSON-frågespråket i Azure Cosmos-databasen.
keywords: SQL-syntax, sql-fråga, sql-frågor, json-frågespråket, databasbegrepp och sql-frågor, mängdfunktioner
services: cosmos-db
author: LalithaMV
manager: kfile
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: laviswa
ms.openlocfilehash: f0fd1b57be07eda13655b5a6c0dcb5b412e8a248
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798329"
---
# <a name="sql-queries-for-azure-cosmos-db"></a>SQL-frågor för Azure Cosmos DB

Microsoft Azure Cosmos DB stöder förfrågningar till dokument med SQL (Structured Query Language) som ett JSON-frågespråk på SQL-API-konton. Azure Cosmos-DB är verkligen schemafria. Tack vare dess åtagande att JSON-datamodell direkt i databasmotorn ger automatisk indexering av JSON-dokument utan explicita schema eller att sekundärindex.

När du utformar frågespråket för Cosmos DB hade vi två mål i åtanke:

* I stället för inventing nya JSON frågespråk som vi ville har stöd för SQL. SQL är ett av de mest välkända och populära frågespråk. Cosmos-Databasens SQL är en formell programmeringsmodell för komplexa frågor via JSON-dokument.
* Vi ville använda Javascripts programmeringsmodell som grund för våra frågespråk som JSON-dokument-databas kan utföra JavaScript direkt i databasmotorn. SQL-API finns i typsystemet i Javascript's, utvärdering av uttryck och funktionsanrop. Detta i sin tur är en fysisk programmeringsmodell för relationella projektioner, hierarkisk navigering i JSON-dokument, self kopplingar, spatial frågor och anrop av användardefinierade funktioner (UDF) helt skrivna i JavaScript, bland andra funktioner. 

Vi tror att dessa funktioner är nyckeln till att minska friktionen mellan programmet och databasen och är avgörande för utvecklarproduktivitet.

Vi rekommenderar att komma igång med att titta på nedanstående video, där Azure Cosmos DB Programhanteraren Andrew Liu visar Azure Cosmos DB frågar funktioner och visar online [Query Playground](http://www.documentdb.com/sql/demo), där du kan prova Azure Cosmos DB och kör SQL-frågor mot vår datauppsättning som visas i videon.

> [!VIDEO https://www.youtube.com/embed/1LqUQRpHfFI]
>
>

Mer avancerade frågor metoder visas i den här uppföljning video:

> [!VIDEO https://www.youtube.com/embed/kASU9NOIR90]
>
>

Återvänd sedan till den här artikeln, där vi börjar med en SQL-fråga självstudiekurs som vägleder dig igenom några enkla JSON-dokument och SQL-kommandon.

## <a id="GettingStarted"></a>Komma igång med SQL-kommandon i Cosmos DB
Om du vill se Cosmos-Databasens SQL på arbetet vi börjar med några enkla JSON-dokument och gå igenom några enkla frågor mot den. Överväg att dessa två JSON-dokument om två serier. Vi behöver inte skapa någon scheman eller sekundärindex explicit med Cosmos DB. Vi behöver bara infoga JSON-dokument till en Cosmos-DB-samling och sedan fråga. Här är ett enkelt JSON-dokumentet för familjen Andersen, överordnade, underordnade (och deras husdjur) adress och registreringsinformation. Dokumentet har strängar, siffror, booleska värden, matriser och kapslade egenskaper. 

**Dokumentet**  

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow", 
         "gender": "female", 
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Här är ett andra dokument med en skillnaden mellan – `givenName` och `familyName` används i stället för `firstName` och `lastName`.

**Dokumentet**  

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

Nu ska vi prova några frågor mot dessa data för att förstå några viktiga aspekter av Azure Cosmos DB SQL-frågespråket. Följande fråga returnerar exempelvis dokument där fältet id matchar `AndersenFamily`. Eftersom det är en `SELECT *`, utdata från frågan är klar JSON-dokumentet:

**Fråga**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Nu föreställa dig när vi behöver formatera om JSON-utdata i en annan form. Den här frågan genererar ett nytt JSON-objekt med två valda fält, namn och ort, när den adressen stad har samma namn som tillstånd. I det här fallet matchar ”NY, NY”.

**Fråga**    

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Results**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


Nästa fråga returnerar alla angivna namnen på underordnade i familjen vars id matchar `WakefieldFamily` sorterade efter ort där du bor.

**Fråga**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Results**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Vi vill att uppmärksamma några anmärkningsvärda aspekter av Cosmos-DB-frågespråket igenom de exempel som vi har sett:  

* Eftersom SQL API fungerar på JSON-värden, behandlar trädet Formats entiteter i stället för rader och kolumner. Därför språk kan du referera till noder i trädet på varje godtycklig djup som `Node1.Node2.Node3…..Nodem`, liknande relationella SQL hänvisar till två del-referens för `<table>.<column>`.   
* Structured query language fungerar med schemat mindre data. Typsystemet måste bindas dynamiskt. Samma uttryck kan ge olika typer på olika dokument. Resultatet av en fråga är ett giltigt JSON-värde, men är inte säkert att vara i ett fast schema.  
* Cosmos DB stöder endast strikt JSON-dokument. Det innebär att typsystemet och uttryck begränsas till endast behandlar JSON-typer. Referera till den [JSON-specifikationen](http://www.json.org/) för mer information.  
* En Cosmos-DB-samling är en schemafria behållare för JSON-dokument. Relationer i data enheter inom och mellan dokument i en samling fångas implicit av inneslutning och inte av primärnyckel och sekundärnyckel viktiga relationer. Det här är en viktig aspekt värt pekar hänsyn intra-dokument-kopplingar som beskrivs senare i den här artikeln.

## <a id="Indexing"></a> Cosmos DB indexering
Innan vi går in SQL-syntaxen är det vara värt att utforska indexering designen i Azure Cosmos-databasen. 

Syftet med-databasindex är att hantera frågor i olika formulär och former med minsta resursförbrukning (t.ex. CPU och in-/ utdata) och ger bra genomflöde och låg latens. Valet av rätt index för att fråga en databas kräver ofta mycket planering och experiment. Den här metoden innebär en utmaning för schemat mindre databaser där data stämmer inte överens med ett strikt schema och utvecklas snabbt samtidigt. 

När vi undersystemet Cosmos DB indexering, ange vi därför följande mål:

* Indexera dokument utan schemat: undersystemet indexering inte kräver någon schemainformation eller göra några antaganden om schemat dokument. 
* Stöd för effektiv och omfattande hierarkiska och relationella: indexet stöder Cosmos-DB-frågespråket effektivt, inklusive stöd för hierarkiska och relationella projektioner.
* Stöd för konsekvent frågor in face of en varaktig volym av skrivningar: för hög skrivåtgärder genomströmning arbetsbelastningar med konsekvent frågor, indexet uppdateras inkrementellt effektivt och online i händelse av en varaktig volym för skrivningar. Uppdateringen konsekvent index är avgörande för att hantera frågor på nivån konsekvenskontroll där användaren konfigurerade dokumenttjänsten.
* Stöd för flera innehavare: reservation-baserade modellen för resursen styrning angivna mellan klienter, utförs index uppdateringar budgeten systemresurser (processor, minne och i/o-åtgärder per sekund) fördelas per replik. 
* Lagringseffektivitet: för kostnadseffektivitet, på disklagring kostnader för indexet är begränsad och förutsägbara. Detta är viktigt eftersom Cosmos DB kan utvecklare kompromissa kostnaden baserad mellan index kostnader i förhållande till prestanda för frågor.  

Referera till den [Azure Cosmos DB prover](https://github.com/Azure/azure-documentdb-net) på MSDN efter exempel som visar hur du konfigurerar indexprincip för en samling. Nu är dags detaljer om Azure Cosmos-Databasens SQL-syntax.

## <a id="Basics"></a>Grunderna i Azure Cosmos-Databasens SQL-fråga
Varje fråga består av en SELECT-satsen och valfria FROM och WHERE-satser per ANSI SQL-standarder. Vanligtvis för varje fråga räknas källan i FROM-satsen upp. Sedan har filter i WHERE-satsen tillämpats på källan för att hämta en delmängd av JSON-dokument. Slutligen används SELECT-satsen för att projicera begärda JSON-värdena i select-listan.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a id="FromClause"></a>FROM-satsen
Den `FROM <from_specification>` satsen är valfri såvida källan filtreras eller planerat senare i frågan. Syftet med den här satsen är att ange den datakälla som frågan måste fungera. Hela samlingen är ofta källan, men ett kan du ange en delmängd av samlingen i stället. 

En fråga som `SELECT * FROM Families` anger att hela familjer samlingen är källan att räkna upp. En särskild identifierare rot kan användas för att representera samlingen istället för att använda namnet på samlingen. Följande lista innehåller de regler som tillämpas per fråga:

* Samlingen kan vara ett alias, t.ex `SELECT f.id FROM Families AS f` eller helt enkelt `SELECT f.id FROM Families f`. Här `f` motsvarar `Families`. `AS` är ett valfritt nyckelord för alias identifierare.
* När ett alias, den ursprungliga källan kan inte bindas. Till exempel `SELECT Families.id FROM Families f` är syntaktiskt felaktig eftersom inte går att matcha identifieraren ”familjer” längre.
* Alla egenskaper som behöver refereras måste vara fullständigt kvalificerad. Detta gäller i frånvaron av strikt schema för att undvika eventuella tvetydig bindningar. Därför `SELECT id FROM Families f` är syntaktiskt felaktig eftersom egenskapen `id` är inte bunden.

### <a name="subdocuments"></a>Underdokument
Källan kan också reduceras till en mindre deluppsättning. Till exempel att räkna upp endast en underkatalog i varje dokument bli på subroot sedan källan, som visas i följande exempel:

**Fråga**

    SELECT * 
    FROM Families.children

**Results**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

När ovanstående exempel används en matris som källa, ett objekt även ska användas som källa, vilket är vad som visas i följande exempel: någon giltig JSON-värde (inte Odefinierad) som finns i källan betraktas som ska ingå i resultatet av frågan. Om vissa familjer inte har en `address.state` värde, de ingår inte i frågeresultatet.

**Fråga**

    SELECT * 
    FROM Families.address.state

**Results**

    [
      "WA", 
      "NY"
    ]


## <a id="WhereClause"></a>WHERE-satsen
WHERE-satsen (**`WHERE <filter_condition>`**) är valfritt. Anger de villkor som JSON-dokument som tillhandahålls av källan måste uppfylla för att vara med i resultatet. JSON-dokument måste utvärdera de angivna villkoren på ”true” beaktas för resultatet. WHERE-satsen används av indexet lagret för att fastställa den absoluta minsta delmängden källdokument som kan ingå i resultatet. 

Följande fråga begär dokument som innehåller en namnegenskapen vars värde är `AndersenFamily`. Alla dokument som inte har en namnegenskap, eller där värdet inte matchar `AndersenFamily` utesluts. 

**Fråga**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


Föregående exempel visade en enkel likheten fråga. SQL-API: et stöder också en mängd skalära uttryck. Det vanligaste är binär och unära uttryck. Egenskapsreferenser från källan JSON-objekt är också giltigt uttryck. 

Följande binära operatorer stöds för närvarande och kan användas i frågor som visas i följande exempel:  

<table>
<tr>
<td>Aritmetiska</td>    
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Binärt</td>    
<td>|, &, ^, <<>>,, >>> (noll fill högerskift)</td>
</tr>
<tr>
<td>Logiska</td>
<td>OCH, ELLER INTE</td>
</tr>
<tr>
<td>Jämförelse</td>    
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Sträng</td>    
<td>|| (sammanfoga)</td>
</tr>
</table>  


Låt oss ta en titt på några frågor med binära operatorer.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Unära operatorer +,-, ~ inte stöds också och kan användas inuti frågor som visas i följande exempel:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Förutom binär och unära operatorer tillåts egenskapsreferenser. Till exempel `SELECT * FROM Families f WHERE f.isRegistered` returnerar JSON-dokument som innehåller egenskapen `isRegistered` där egenskapens värde är lika med JSON `true` värde. Andra värden (FALSKT, null, Odefinierad, `<number>`, `<string>`, `<object>`, `<array>`osv) leder till källdokument som ska uteslutas från resultatet. 

### <a name="equality-and-comparison-operators"></a>Jämförelse av och likhetsfrågor operatörer
Följande tabell visar resultatet av likheten jämförelser i SQL-API mellan två typer som JSON.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Odefinierad</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Booleskt värde</strong>
         </td>
         <td valign="top">
            <strong>Antal</strong>
         </td>
         <td valign="top">
            <strong>Sträng</strong>
         </td>
         <td valign="top">
            <strong>Objektet</strong>
         </td>
         <td valign="top">
            <strong>matris</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Odefinierad<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Booleskt värde<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Antal<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Sträng<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objektet<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
         <td valign="top">
Odefinierat </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>matris<strong>
         </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
Odefinierat </td>
         <td valign="top">
            <strong>OKEJ</strong>
         </td>
      </tr>
   </tbody>
</table>

För andra jämförelseoperatorer som >, > =,! =, < och < =, följande regler gäller:   

* Jämförelse mellan typer resulterar i Undefined.
* Jämförelse mellan två objekt eller två matriser resulterar i Undefined.   

Om resultatet av det skalära uttrycket i filtret är odefinierad, motsvarande dokumentet inte tas med i resultatet, eftersom Undefined inte logiskt del ”true”.

### <a name="between-keyword"></a>MELLAN nyckelord
Du kan också använda nyckelordet mellan för att uttrycka frågor mot intervall med värden som i ANSI SQL. MELLAN kan användas mot strängar eller siffror.

Den här frågan returnerar till exempel alla family dokument som den första underordnade klass är mellan 1-5 (båda inkluderande). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Till skillnad från i ANSI-SQL, kan du också använda BETWEEN-satsen i FROM-satsen som i följande exempel.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Kom ihåg att skapa en indexprincip som använder en intervallet Indextypen mot alla numeriska egenskaper/sökvägar som är filtrerade i instruktionen BETWEEN för snabbare frågan körningstider. 

Den största skillnaden mellan att använda BETWEEN i SQL-API och ANSI SQL är att du kan ange intervallet frågor mot egenskaper för olika typer – du kan till exempel ha ”klass” vara ett tal (5) i vissa dokument och strängar i andra (”grade4”). I dessa fall, som i JavaScript, en jämförelse mellan två olika typer resultaten i ”Odefinierad” och dokumentet kommer att hoppas över.

### <a name="logical-and-or-and-not-operators"></a>Logisk (AND, OR och inte) operatorer
Logiska operatorer fungerar med booleska värden. De logiska tabellerna sanningen för de här operatorerna visas i följande tabeller.

| ELLER | True | False | Odefinierat |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Odefinierat |
| Odefinierat |True |Odefinierat |Odefinierat |

| OCH | True | False | Odefinierat |
| --- | --- | --- | --- |
| True |True |False |Odefinierat |
| False |False |False |False |
| Odefinierat |Odefinierat |False |Odefinierat |

| INTE |  |
| --- | --- |
| True |False |
| False |True |
| Odefinierat |Odefinierat |

### <a name="in-keyword"></a>I nyckelord
IN-nyckelordet kan användas för att kontrollera om ett angivet värde matchar något värde i en lista. Den här frågan returnerar till exempel alla family dokument där id är ”WakefieldFamily” eller ”AndersenFamily”. 

    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Det här exemplet returnerar alla dokument om tillståndet är någon av de angivna värdena.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Ternär (?) och ansvariga för Coalesce (?)
Ternär och Coalesce operatörer kan användas för att skapa villkorsuttryck liknar populära programmeringsspråk som C# och JavaScript. 

Operatorn Ternär (?) kan vara väldigt användbar när man skapar nya JSON-egenskaper direkt. Till exempel kan nu du skriva frågor för att klassificera klassen nivåer i en mänsklig läsbar form som nybörjare/mellanliggande/Avancerat enligt nedan.

     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Du kan också kapsla anrop till operatorn som i frågan nedan.

    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Som med andra frågeoperatorer ingår om refererade egenskaper i villkorsuttrycket saknas i ett dokument, eller om de typer som jämförs är olika, sedan dessa dokument inte i frågeresultatet.

Operatorn Coalesce (?) kan användas för att effektivt kontrollera om finns för en egenskap (kallas även har definierats) i ett dokument. Detta är användbart när frågor körs mot halvstrukturerade data av olika typer eller. Den här frågan returnerar till exempel ”efternamn” om den finns, eller ”efternamn” om det inte finns.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a id="EscapingReservedKeywords"></a>Angiven egenskapsaccessor
Du kan också komma åt egenskaper med hjälp av operatorn inom citattecken egenskapen `[]`. Till exempel `SELECT c.grade` och `SELECT c["grade"]` är likvärdiga. Den här syntaxen är användbart när du vill undanta en egenskap som innehåller blanksteg, specialtecken, eller delar samma namn som en SQL-nyckelord eller reserverat ord som händer.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a id="SelectClause"></a>SELECT-satsen
SELECT-satsen (**`SELECT <select_list>`**) är obligatoriskt och anger vilka värden hämtas från frågan, precis som i ANSI SQL. Den delmängd som har filtrerats ovanpå källdokument har skickats till Projektionsfasen, där de angivna JSON-värdena hämtas och ett nytt JSON-objekt har skapats, för varje inmatningen som skickades till den. 

I följande exempel visas en typisk urvalsfråga. 

**Fråga**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Kapslade egenskaper
I exemplet nedan vi projicerar två kapslade egenskaper `f.address.state` och `f.address.city`.

**Fråga**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Projektion stöder också JSON-uttryck som visas i följande exempel:

**Fråga**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Nu ska vi titta på rollen för `$1` här. Den `SELECT` satsen behöver skapa en JSON-objekt och eftersom ingen nyckel tillhandahålls vi använder implicit argumentet variabelnamn som börjar med `$1`. Den här frågan returnerar till exempel två implicit argumentvariabler, etikett `$1` och `$2`.

**Fråga**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Alias
Nu ska vi utöka exemplet ovan med explicit alias med värden. Nyckelordet används för alias är. Det är valfritt som visas vid användning av det andra värdet som `NameInfo`. 

Om en fråga har två egenskaper med samma namn, användas alias för att byta namn på en eller båda egenskaperna så att de skiljas åt i planerade resultatet.

**Fråga**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Skalära uttryck
Förutom egenskapsreferenser till stöder SELECT-satsen också skaläruttryck konstanter, aritmetiska uttryck, logiska uttryck osv. Här är till exempel en enkel ”Hello World”-fråga.

**Fråga**

    SELECT "Hello World"

**Results**

    [{
      "$1": "Hello World"
    }]


Här är ett mer avancerat exempel som använder ett skalärt uttryck.

**Fråga**

    SELECT ((2 + 11 % 7)-2)/3    

**Results**

    [{
      "$1": 1.33333
    }]


I följande exempel är resultatet av det skalära uttrycket som ett booleskt värde.

**Fråga**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    

**Results**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Skapa en objekt och matris
En annan nyckelfunktion i SQL-API är array-objekt skapas. Observera att vi har skapat ett nytt JSON-objekt i det förra exemplet. På samma sätt kan kan en också skapa matriser som visas i följande exempel:

**Fråga**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    

**Results**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a id="ValueKeyword"></a>VÄRDET nyckelord
Den **värdet** nyckelord är ett sätt att returnera JSON-värde. Till exempel returnerar frågan nedan skalären `"Hello World"` i stället för `{$1: "Hello World"}`.

**Fråga**

    SELECT VALUE "Hello World"

**Results**

    [
      "Hello World"
    ]


Följande fråga returnerar JSON-värde utan den `"address"` etikett i resultaten.

**Fråga**

    SELECT VALUE f.address
    FROM Families f    

**Results**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

I följande exempel utökar här om du vill visa hur du returnerar JSON primitiva värden (lövnivån av JSON-träd). 

**Fråga**

    SELECT VALUE f.address.state
    FROM Families f    

**Results**

    [
      "WA",
      "NY"
    ]


### <a name="-operator"></a>* Operatorn
Särskilda operatorn (*) går för att projicera dokumentet-är. När den används, måste det vara det enda beräknade fältet. När en fråga som `SELECT * FROM Families f` är giltigt, `SELECT VALUE * FROM Families f ` och `SELECT *, f.id FROM Families f ` är inte giltiga.

**Fråga**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Results**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

### <a id="TopKeyword"></a>Operatorn TOP
Nyckelordet TOP kan användas för att begränsa antalet värden från en fråga. När upp används tillsammans med ORDER BY-satsen är resultatmängden begränsad till första N antalet beställda värden. Annars returneras första N antalet resultat i en odefinierad ordning. Som bästa praxis, i en SELECT-instruktion alltid Använd en ORDER BY-sats med TOP-instruktion. Detta är det enda sättet att förutsägbart indikera vilka rader som påverkas av längst upp. 

**Fråga**

    SELECT TOP 1 * 
    FROM Families f 

**Results**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

TOP kan användas med ett konstant värde (som visas ovan) eller med ett variabelvärde med hjälp av frågor med parametrar. Mer information finns i parameterfrågor nedan.

### <a id="Aggregates"></a>Mängdfunktioner
Du kan också utföra aggregeringar i den `SELECT` satsen. Mängdfunktioner utför en beräkning på en uppsättning värden och returnera ett enstaka värde. Följande fråga returnerar till exempel antal family dokument i samlingen.

**Fråga**

    SELECT COUNT(1) 
    FROM Families f 

**Results**

    [{
        "$1": 2
    }]

Du kan också returnera mängdfunktionen skalära värde med hjälp av den `VALUE` nyckelord. Följande fråga returnerar till exempel antalet värden som ett tal:

**Fråga**

    SELECT VALUE COUNT(1) 
    FROM Families f 

**Results**

    [ 2 ]

Du kan också utföra aggregeringar i kombination med filter. Följande fråga returnerar till exempel antalet dokument med adressen i delstaten Washington.

**Fråga**

    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 

**Results**

    [ 1 ]

I följande tabell listas stöds mängdfunktioner i SQL-API. `SUM` och `AVG` utförs via numeriska värden, medan `COUNT`, `MIN`, och `MAX` kan utföras via siffror, strängar, booleska värden och null-värden. 

| Användning | Beskrivning |
|-------|-------------|
| ANTAL | Returnerar antalet objekt i uttrycket. |
| SUMMA   | Returnerar summan av alla värden i uttrycket. |
| MIN   | Returnerar det minsta värdet i uttrycket. |
| MAX   | Returnerar det största värdet i uttrycket. |
| MEDEL   | Returnerar medelvärdet av värdena i uttrycket. |

Aggregeringar kan också utföras via resultatet av en matris iteration. Mer information finns i [matris Iteration i frågor](#Iteration).

> [!NOTE]
> När du använder Azure portal Data Explorer, Observera att aggregering frågor kan returnera delvis sammanlagda resultat via en fråga sida. SDK: erna producerar en enda ackumulerade värdet på alla sidor. 
> 
> För att kunna utföra aggregering frågor med kod du behöver .NET SDK 1.12.0, .NET Core SDK 1.1.0 eller Java SDK 1.9.5 eller senare.    
>

## <a id="OrderByClause"></a>ORDER BY-sats
Som i ANSI-SQL, kan du använda en valfri Order By-sats samtidigt som frågor körs. Instruktionen kan innehålla ett valfritt ASC/DESC-argument för att ange ordningen som resultat måste hämtas.

Här är till exempel en fråga som hämtar familjer i den ordning de fasta Ortnamn.

**Fråga**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city

**Results**

    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"    
      }
    ]

Och här är en fråga som hämtar familjer efter skapandedatum som lagras som ett tal som representerar epok tid, dvs, förfluten tid sedan den 1 januari 1970 i sekunder.

**Fråga**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC

**Results**

    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472    
      }
    ]

## <a id="Advanced"></a>Avancerade begrepp och SQL-frågor

### <a id="Iteration"></a>Upprepning
En ny konstruktion har lagts till den **IN** nyckelord i SQL-API för att ge stöd för att iterera över JSON-matriser. FRÅN-källa har stöd för iteration. Vi börjar med följande exempel:

**Fråga**

    SELECT * 
    FROM Families.children

**Results**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Nu ska vi titta på en annan fråga som utför iteration över underordnade objekt i samlingen. Observera skillnaden i matrisen utdata. Det här exemplet delar upp `children` och förenklas resultaten till en matris.  

**Fråga**

    SELECT * 
    FROM c IN Families.children

**Results**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Detta kan ytterligare användas för att filtrera på varje enskild post i matrisen som visas i följande exempel:

**Fråga**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Results**  

    [{
      "givenName": "Lisa"
    }]

Du kan också utföra sammanställning över resultatet av matrisen iteration. Till exempel räknar följande fråga antalet underordnade bland alla serier.

**Fråga**

    SELECT COUNT(child) 
    FROM child IN Families.children

**Results**  

    [
      { 
        "$1": 3
      }
    ]

### <a id="Joins"></a>Kopplingar
Du behöver ansluta över tabeller är viktigt i en relationsdatabas. Det är den logiska naturlig följd att designa normaliserade scheman. Strider mot det behandlar till SQL API Avnormaliserade datamodellen schemafria dokument. Det här är den logiska motsvarigheten till en ”självkoppling”.

Den syntax som språket stöder är < from_source1 > < from_source2 > Anslut till koppling... Anslut < from_sourceN >. Generellt sett detta returnerar en uppsättning **N**- tupplar (tuppel med **N** värden). Varje tuppel har värden som genereras av alla samling alias iterera över sina respektive uppsättningar. Detta är med andra ord en fullständig kryssprodukten av mängderna deltar i kopplingen.

I följande exempel visas hur JOIN-satsen fungerar. I följande exempel visas resultatet är tom eftersom kryssprodukten av varje dokument från källan och tomma är tom.

**Fråga**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Results**  

    [{
    }]


I följande exempel är kopplingen mellan dokumentroten och `children` subroot. Det är en kryssprodukten mellan två JSON-objekt. Att underordnade är en matris är inte giltiga i KOPPLINGEN eftersom vi arbetar med en enda rot som är underordnade matrisen. Därför innehåller resultatet bara två resultat eftersom kryssprodukten av varje dokument med matrisen ger exakt bara ett dokument.

**Fråga**

    SELECT f.id
    FROM Families f
    JOIN f.children

**Results**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


I följande exempel visas en mer konventionella koppling:

**Fråga**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Results**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



Det första du Observera är att den `from_source` av den **ansluta** -satsen är en iterator. Därför är flödet i det här fallet följande:  

* Expandera varje element i underordnade **c** i matrisen.
* Tillämpa en kryssprodukten med roten för dokumentet **f** med varje underordnat element **c** som har förenklad i det första steget.
* Slutligen projektet rotobjektet **f** namnegenskap enbart. 

Det första dokumentet (`AndersenFamily`) innehåller endast ett underordnat element, så resultatet innehåller bara ett enda objekt som motsvarar det här dokumentet. Det andra dokumentet (`WakefieldFamily`) innehåller två underordnade. Därför genererar kryssprodukten ett separat objekt för varje underordnad, vilket ledde till två objekt, en för varje underordnad som motsvarar det här dokumentet. Fälten rot i båda dessa dokument är desamma, precis som du förväntar dig i en kryssprodukten.

Verktyget verkliga för anslutning till är att formuläret tupplar från kryssprodukten i en form som annars är svår att projektet. Du kan dessutom som visas i exemplet nedan, filtrera på kombinationen av en tuppel som låter: användaren valde ett villkor uppfylls av tuppeln övergripande.

**Fråga**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**Results**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Det här exemplet är en naturlig förlängning av föregående exempel och utför en dubbel koppling. Därför kan du visa kryssprodukten som genererat följande kod:

    for-each(Family f in Families)
    {    
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily` har ett underordnat objekt som har en husdjur. Så här ger en rad i kryssprodukten (1\*1\*1) från den här serien. WakefieldFamily men har två underordnade objekt, men endast ett underordnat objekt ”Jesse” har husdjur. Jesse har två husdjur om. Därför kryssprodukten ger 1\*1\*2 = 2 rader från den här serien.

I nästa exempel finns ett filter på `pet`. Detta omfattar inte alla tupplar där husdjur namnet inte är ”skuggkopia”. Observera att vi kan skapa tupplar från matriser, filter på något av elementen i tuppeln, och projektet valfri kombination av elementen. 

**Fråga**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Results**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a id="JavaScriptIntegration"></a>JavaScript-integrering
Azure Cosmos-DB är en programmeringsmodell för att köra JavaScript-baserade programlogik direkt på samlingar som lagrade procedurer och utlösare. Detta möjliggör både:

* Möjligheten att utföra CRUD-åtgärder för högpresterande transaktionell och frågor mot dokument i en samling tack vare djupgående integration av JavaScript-körning direkt i databasmotorn. 
* En fysisk modellering av Kontrollflöde, variabel omfång och tilldelning och integration av undantagshantering primitiver med databastransaktioner. Mer information om Azure DB som Cosmos-stöd för JavaScript-integrering finns i dokumentationen för programmering av serversidan JavaScript.

### <a id="UserDefinedFunctions"></a>Användardefinierade funktioner (UDF)
SQL-API ger stöd för användaren definierat funktioner (UDF) tillsammans med de typer som redan har definierats i den här artikeln. I synnerhet stöds skalära UDF: er där utvecklare kan skicka in noll eller flera argument och returnera ett enda argument resultat tillbaka. Var och en av de här argumenten kontrolleras för att vara giltiga JSON-värdena.  

SQL-syntaxen utökas så att den stöder anpassad programlogik med hjälp av dessa användardefinierade funktioner. UDF: er kan registreras med SQL-API och sedan refereras som en del av en SQL-fråga. I praktiken som de UDF: er exquisitely anropas av frågor. Följd att detta val har UDF: er inte åtkomst till context-objektet som har andra JavaScript typer (lagrade procedurer och utlösare). Eftersom frågor körs i skrivskyddat läge kan köras de på primära eller sekundära repliker. Därför är UDF: er avsedd att köras på sekundära repliker till skillnad från andra typer av JavaScript.

Nedan visas ett exempel på hur en UDF kan registreras för Cosmos-DB-databasen, speciellt under en dokumentsamling.

       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  

I föregående exempel skapas en UDF-fil som heter `REGEX_MATCH`. Den accepterar två strängvärden som JSON `input` och `pattern` och kontrollerar om de första matchar mönstret som anges i andra använder Javascript's string.match()-funktionen.

Vi kan nu använda den här UDF i en fråga i en projektion. UDF: er måste vara kvalificerat med skiftlägeskänsliga prefixet ”udf”. När anropas från frågor. 

> [!NOTE]
> Innan 3/17/2015 stöds Cosmos DB UDF anrop utan ”udf”. prefix som väljer REGEX_MATCH(). Det här anropa mönstret är föråldrad.  
> 
> 

**Fråga**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Results**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

UDF-filen kan även användas i ett filter som visas i exemplet nedan är också kvalificerad med ”udf”. prefix:

**Fråga**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Results**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


I princip UDF: er är giltig skalära uttryck och kan användas i både projektioner och filter. 

För att expandera med UDF: er ska vi titta på ett annat exempel med villkorlig logik:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);


Nedan visas ett exempel som utnyttjar en användardefinierad funktion.

**Fråga**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    

**Results**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Som i föregående exempel demonstrerar integrera kraften i JavaScript-språket med SQL-API för att tillhandahålla en omfattande programmerbara gränssnitt för att göra komplex procedurmässig, villkorlig logik med hjälp av inbyggda funktioner för körning av JavaScript UDF: er.

SQL-API ger argumenten till de UDF: er för varje dokument i källan för den aktuella etappen (WHERE-satsen eller SELECT-satsen) bearbetning av en användardefinierad funktion. Resultatet är inbyggda i övergripande körning pipelinen sömlöst. Om egenskaperna som anges av en användardefinierad funktion parametrar finns inte i JSON-värde, parametern anses Odefinierad och därför UDF-anrop är helt hoppas över. På liknande sätt om resultatet av en användardefinierad funktion är odefinierad ingår den inte i resultatet. 

Sammanfattningsvis är UDF: er bra verktyg för att göra komplicerad affärslogik som en del av frågan.

### <a name="operator-evaluation"></a>Operatorn utvärdering
Cosmos DB, ritar bredd med JavaScript-operatörer och dess utvärdering semantik miljöpåverkan som JSON-databas. Medan Cosmos DB försöker bevara JavaScript-semantik i JSON-support, avviker åtgärden utvärdering i vissa fall.

I SQL-API, är till skillnad från i traditionella SQL typer av värden ofta inte känd tills värdena som hämtas från databasen. För att effektivt köra frågor, har de flesta av operatörerna strikt krav. 

SQL-API utförs inte implicita konverteringar, till skillnad från JavaScript. Till exempel en fråga som `SELECT * FROM Person p WHERE p.Age = 21` matchar dokument som innehåller en ålder egenskap vars värde är 21. Andra dokument vars ålder-egenskap stämmer med strängen ”21” eller andra eventuellt oändlig variationer som ”021”, ”21.0”, ”0021”, ”00021”, etc. matchas inte. Detta är däremot att JavaScript där strängvärden är implicit omvandlas till siffror (baserat på operator, ex: ==). Det här alternativet är avgörande för effektiv index som matchar i SQL-API. 

## <a name="parameterized-sql-queries"></a>Parametriserade SQL-frågor
Cosmos DB stöder frågor med parametrar som anges med @ notation. Parametriserade SQL ger stabil hantering och undantagstecken användarindata, förhindra oavsiktlig exponering av data via SQL injection. 

Du kan till exempel skriva en fråga som tar efternamn och region som parametrar och sedan exekverar för olika värden för efternamn och adressläge baserat på indata från användaren.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Denna begäran kan sedan skickas till Cosmos-databas som en JSON-fråga som visas nedan.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

Argumentet TOP kan anges med frågor med parametrar som visas nedan.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Parametervärden kan vara en giltig JSON (strängar, siffror, booleska värden, null, även matriser eller kapslade JSON). Även eftersom Cosmos-DB-schema mindre, valideras parametrar inte mot alla typer.

## <a id="BuiltinFunctions"></a>Inbyggda funktioner
Cosmos DB stöder också ett antal inbyggda funktioner för vanliga åtgärder som kan användas i frågor som användardefinierade funktioner (UDF).

| Funktionen grupp          | Åtgärder                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Matematiska funktioner  | ABS, TAKET, EXP, VÅNING, LOG, LOG10, POWER, runda, LOGGA, SQRT, ruta, AVKORTA, ARCCOS, ARCSIN, ARCTAN, ATN2, COS, bet, grader, PI, radianer, SIN och TAN |
| Ange kontrollerar funktioner | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED och IS_PRIMITIVE                                                           |
| Strängfunktioner        | CONCAT, innehåller, ENDSWITH, INDEX_OF, vänster, längd, nedre, LTRIM, Ersätt, REPLIKERA, OMVÄND, höger, RTRIM, STARTSWITH, DELSTRÄNGEN och övre       |
| Matrisfunktioner         | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH och ARRAY_SLICE                                                                                         |
| Spatial funktioner       | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID och ST_ISVALIDDETAILED                                                                           | 

Om du använder en användardefinierad funktion (UDF) som en inbyggd funktion är nu tillgänglig, bör du använda motsvarande inbyggd funktion som det ska gå snabbare att köra och mer effektivt. 

### <a name="mathematical-functions"></a>Matematiska funktioner
Matematiska funktioner utför en beräkning, baserat på värden som har angetts som argument och returnerar ett numeriskt värde. Här är en tabell med inbyggda matematiska funktioner som stöds.


| Användning | Beskrivning |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [[ABS (num_expr)](#bk_abs) | Returnerar det absoluta (positiva) värdet av uttryck. |
| [TAKET (num_expr)](#bk_ceiling) | Returnerar det minsta heltalsvärdet större än eller lika med det angivna numeriska uttrycket. |
| [VÅNING (num_expr)](#bk_floor) | Returnerar det största heltalet mindre än eller lika med det angivna numeriska uttrycket. |
| [EXP (num_expr)](#bk_exp) | Returnerar e upphöjt till uttryck. |
| [LOGGEN (num_expr [, base])](#bk_log) | Returnerar den naturliga logaritmen av uttryck eller logaritmen med hjälp av den angivna basen |
| [Log10 (num_expr)](#bk_log10) | Returnerar 10-logaritmiska värdet för det angivna numeriska uttrycket. |
| [AVRUNDA (num_expr)](#bk_round) | Returnerar ett tal avrundat till närmaste heltal. |
| [AVKORTA (num_expr)](#bk_trunc) | Returnerar ett numeriskt värde trunkeras till närmaste heltal. |
| [SQRT (num_expr)](#bk_sqrt) | Returnerar kvadratroten av uttryck. |
| [RUTA (num_expr)](#bk_square) | Returnerar kvadratroten av uttryck. |
| [STRÖMFÖRSÖRJNING (num_expr, num_expr)](#bk_power) | Returnerar kraften i uttryck till det angivna värdet. |
| [LOGGA (num_expr)](#bk_sign) | Returnerar tecknet värdet (-1, 0, 1) för uttryck. |
| [ARCCOS (num_expr)](#bk_acos) | Returnerar vinkeln i radianer, vars cosinus är det angivna numeriska uttrycket; kallas även cosinus. |
| [ARCSIN (num_expr)](#bk_asin) | Returnerar vinkeln i radianer, vars sinus är ett uttryck. Detta kallas också arcsinus. |
| [ARCTAN (num_expr)](#bk_atan) | Returnerar vinkeln i radianer, vars tangens är ett uttryck. Detta kallas också tangens. |
| [ATN2 (num_expr)](#bk_atn2) | Returnerar vinkeln i radianer mellan positiva x-axeln och ray från ursprunget till punkt (y, x), där x och y är värdena för två angivna float uttryck. |
| [COS (num_expr)](#bk_cos) | Returnerar trigonometriska värden cosinus för den angivna vinkeln i radianer i det angivna uttrycket. |
| [BET (num_expr)](#bk_cot) | Returnerar trigonometriska värden cotangens för den angivna vinkeln i radianer i uttryck. |
| [GRADER (num_expr)](#bk_degrees) | Returnerar en motsvarande vinkel i grader för en vinkel angiven i radianer. |
| [PI)](#bk_pi) | Returnerar värdet PI konstant. |
| [RADIANER (num_expr)](#bk_radians) | Returnerar radianer när ett numeriskt uttryck i grader anges. |
| [SIN (num_expr)](#bk_sin) | Returnerar trigonometriska värden sinus för den angivna vinkeln i radianer i det angivna uttrycket. |
| [TAN (num_expr)](#bk_tan) | Returnerar tangens för input-uttryck i det angivna uttrycket. |

Du kan till exempel nu köra frågor som liknar följande:

**Fråga**

    SELECT VALUE ABS(-4)

**Results**

    [4]

Den största skillnaden mellan Cosmos DB funktioner jämfört med ANSI SQL är att de har utformats för att fungera med schemat mindre och blandad schemadata. Till exempel om du har ett dokument där egenskapen Size saknas eller har ett icke-numeriska värde som ”okänt” och sedan dokumentet hoppas över, i stället för att returnera ett fel.

### <a name="type-checking-functions"></a>Ange kontrollerar funktioner
Typen kontrollerar funktioner kan du kontrollera vilken typ av ett uttryck i SQL-frågor. Typen kontrollerar funktioner kan användas för att avgöra vilken typ av egenskaper i dokument direkt när den variabel eller okänd. Här är en tabell med stöds inbyggd typ kontrollerar funktioner.

<table>
<tr>
  <td><strong>Användning</strong></td>
  <td><strong>Beskrivning</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är en matris.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är ett booleskt värde.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är ett tal.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är ett JSON-objekt.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är en sträng.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om egenskapen har tilldelats ett värde.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (uttryck)</a></td>
  <td>Returnerar ett booleskt värde som anger om värdet är en sträng, siffra, Boolean eller null.</td>
</tr>

</table>

Med dessa funktioner kan köra du nu frågor som liknar följande:

**Fråga**

    SELECT VALUE IS_NUMBER(-4)

**Results**

    [true]

### <a name="string-functions"></a>Strängfunktioner
Följande skalärfunktioner utföra en åtgärd på ett inkommande värde och returnerar en sträng, numeriskt eller booleskt värde. Här är en tabell med inbyggda strängfunktioner:

| Användning | Beskrivning |
| --- | --- |
| [LÄNGDEN (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |Returnerar antalet tecken i angivet stränguttryck |
| [SAMMANFOGA (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Returnerar en sträng som är resultatet av att sammanfoga två eller flera strängvärden. |
| [DELSTRÄNGEN (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Returnerar en del av ett stränguttryck. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Returnerar ett booleskt värde som anger om först stränguttryck börjar med andra |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Returnerar ett booleskt värde som anger om först stränguttryck slutar med andra |
| [INNEHÅLLER (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Returnerar ett booleskt värde som anger om först stränguttryck innehåller andra. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |Returnerar startpositionen för den första förekomsten av andra stränguttryck inom det första uttrycket i strängen, eller -1 om strängen inte hittas. |
| [VÄNSTER (str_expr num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |Returnerar en sträng med det angivna antalet tecken vänstra del. |
| [RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |Returnerar den högra delen av en sträng med det angivna antalet tecken. |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Returnerar ett stränguttryck när den tar bort inledande blanksteg. |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Returnerar ett stränguttryck efter trunkering av alla avslutande blanksteg. |
| [NEDRE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Returnerar ett stränguttryck när versal data har konverterats till gemener. |
| [ÖVRE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Returnerar ett stränguttryck när gemen data har konverterats till versaler. |
| [Ersätt (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |Ersätter alla förekomster av ett angivet strängvärde med ett annat värde. |
| [REPLIKERA (str_expr, num_expr)](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-reference#bk_replicate) |Upprepar ett strängvärde till ett angivet antal gånger. |
| [OMVÄND (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |Returnerar ett strängvärde omvänd ordning. |

Med dessa funktioner kan köra du nu frågor som liknar följande. Exempelvis returnerar gruppnamnet versaler på följande sätt:

**Fråga**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Results**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Eller sammanfoga strängar som i det här exemplet:

**Fråga**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Results**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Strängfunktioner kan också användas i WHERE-satsen för att filtrera resultat, som i följande exempel:

**Fråga**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Results**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Matrisfunktioner
Följande skalärfunktioner utföra en åtgärd på en matris indatavärdet och returnera numeriska, Boolean eller matrisen värde. Här är en tabell med inbyggda matrisfunktioner:

| Användning | Beskrivning |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |Returnerar antal element i matrisen uttrycket. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Returnerar en matris som är resultatet av sammanfogar två eller flera matrisvärden. |
| [ARRAY_CONTAINS (arr_expr uttryck [, bool_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Returnerar ett booleskt värde som anger om matrisen innehåller det angivna värdet. Ange om matchningen är fullständigt eller partiellt. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Returnerar en del av ett matrisuttryck. |

Matrisfunktioner kan användas för att manipulera matriser i JSON. Här är till exempel en fråga som returnerar alla dokument där en av överordnade är ”Robin Wakefield”. 

**Fråga**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Du kan ange ett partiellt fragment för motsvarande element i matrisen. Följande fråga returnerar alla överordnade med den `givenName` av `Robin`.

**Fråga**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)

**Results**

    [{
      "id": "WakefieldFamily"
    }]


Här är ett annat exempel som använder ARRAY_LENGTH för att få antalet underordnade per familj.

**Fråga**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Results**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Spatial funktioner
Cosmos DB stöder följande öppna geospatiala Consortium (OGC) inbyggda funktioner för geospatial frågor. 

<table>
<tr>
  <td><strong>Användning</strong></td>
  <td><strong>Beskrivning</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Returnerar avståndet mellan de två GeoJSON punkt, Polygon eller LineString-uttrycken.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Returnerar ett booleskt uttryck som anger om det första GeoJSON-objektet (Point, LineString eller Polygon) är i andra GeoJSON-objektet (Point, LineString eller Polygon).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Returnerar ett booleskt uttryck som anger om de två angivna GeoJSON-objekt (Point, LineString eller Polygon) intersect.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Returnerar ett booleskt värde som anger om det angivna uttrycket GeoJSON punkt, Polygon eller LineString är giltig.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Returnerar ett JSON-värde som innehåller ett booleskt värde värdet om det angivna uttrycket för GeoJSON punkt, Polygon eller LineString är giltiga och ogiltiga, dessutom orsak som ett strängvärde.</td>
</tr>
</table>

Spatial funktioner kan användas för att utföra närhet frågor mot spatialdata. Här är till exempel en fråga som returnerar alla family dokument som ligger inom 30 km för den angivna platsen med hjälp av funktionen ST_DISTANCE inbyggda. 

**Fråga**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Mer information om geospatiala stöds i Cosmos-databasen finns [arbeta med geospatiala data i Azure Cosmos DB](geospatial.md). Som packar upp spatial funktioner och SQL-syntaxen för Cosmos DB. Nu ska vi ta en titt på hur LINQ-frågor fungerar och hur den interagerar med syntaxen vi sett hittills.

## <a id="Linq"></a>LINQ till SQL API
LINQ är en programmeringsmodell för .NET som representerar beräkning som frågor för dataströmmar med objekt. Cosmos DB innehåller ett klientsidan bibliotek gränssnittet med LINQ genom att underlätta konvertering mellan JSON och .NET-objekt och mappning från en delmängd av LINQ-frågor till Cosmos-DB-frågor. 

Bilden nedan visar arkitekturen för LINQ-frågor med Cosmos DB.  Med Cosmos-DB-klient kan utvecklare skapa ett **IQueryable** objekt som frågar direkt Cosmos DB frågan provider, som sedan översätter LINQ-fråga till en Cosmos-DB-fråga. Frågan skickas sedan till Cosmos-DB-servern för att hämta en uppsättning resultat i JSON-format. Returnerade resultat avserialiseras till en dataström med .NET-objekt på klientsidan.

![Arkitektur för LINQ-frågor med SQL-API - SQL-syntaxen, JSON-frågespråket, databasbegrepp och SQL-frågor][1]

### <a name="net-and-json-mapping"></a>.NET och JSON-mappning
Mappningen mellan .NET-objekt och JSON-dokument är naturlig - varje medlemsfält har mappats till ett JSON-objekt, där fältnamnet är mappad till den ”key”-delen av objektet och ”värde”-delen är rekursivt mappas till värdedelen för objektet. Exempel: I familjen-objekt som skapas är mappad till JSON-dokumentet som visas nedan. Och vice versa JSON-dokumentet är mappad till en .NET-objekt.

**C#-klass**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ till SQL-översättning
Fråga Cosmos-DB-provider utför en bästa prestanda-mappning från en LINQ-fråga till en Cosmos-Databasens SQL-fråga. I följande beskrivning förutsätter vi att läsaren har en grundläggande kunskaper inom LINQ.

Vi kan först stöder alla JSON primitiva typer – numeriska typer, boolean, sträng eller null för typsystemet. Endast typerna JSON stöds. Följande skalära uttryck stöds.

* Konstanta värden – dessa inkluderar konstanta värden för primitiva datatyper när frågan ska utvärderas.
* Egenskapen/matrisen indexuttryck – dessa uttryck finns i egenskapen för ett objekt eller ett matriselement.
  
     familj. ID;    Family.children[0].familyName;    Family.children[0].grade;    Family.children[n].grade; n är en int-variabel
* Aritmetiska uttryck - dessa inkluderar vanliga aritmetiska uttryck på numeriska och booleska värden. För en fullständig lista finns i SQL-specifikationen.
  
     2 * family.children[0].grade;    x + y;
* Jämförelse av stränguttryck - dessa inkluderar jämför ett strängvärde till vissa konstant värde.  
  
     mother.familyName == ”Smith”;    child.givenName == s. s är en string-variabel
* / Objektmatris skapa uttryck - uttrycken returnerade ett objekt av sammansatta värde eller en anonym typ eller en matris med sådana objekt. Dessa värden kan vara kapslade.
  
     ny överordnad {familyName = ”Smith”, givenName = ”Johan”}; nya {först = 1, andra = 2}; en anonym typ med två fält              
     nya int [] {3, child.grade, 5};

### <a id="SupportedLinqOperators"></a>Lista över stöds LINQ-operatorer
Här är en lista över stöds LINQ operatorer i LINQ-providern ingår i SQL .NET SDK.

* **Välj**: projektioner översätta till SQL SELECT inklusive objektkonstruktion
* **Där**: filter översätta till SQL WHERE och stöd för översättningen mellan & &, || och! för SQL-operatorerna
* **SelectMany**: tillåter återgång av matriser för att ansluta till SQL-satsen. Kan användas för att kedjan/kapsla uttryck för att filtrera på matriselement
* **OrderBy och OrderByDescending**: översätts till ORDER BY stigande/fallande
* **Antal**, **summan**, **Min**, **Max**, och **genomsnittlig** operatorer för aggregering och motsvarigheterna asynkrona **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync**, och **AverageAsync**.
* **CompareTo**: översätts till intervallet jämförelser. Används vanligtvis för strängar eftersom de inte jämförbar i .NET
* **Ta**: översätter SQL längst upp för att begränsa resultaten av en fråga
* **Matematiska funktioner**: har stöd för översättning av. NET'S Abs ARCCOS, ARCSIN ARCTAN, taket, Cos, Exp, våning, Log, Log10, Pow, Round, signera, Sin, Sqrt, Tan, Truncate till motsvarande SQL inbyggda funktioner.
* **Sträng funktioner**: har stöd för översättning av. NET'S Concat innehåller EndsWith, IndexOf, Count, ToLower, TrimStart, Ersätt, omvänd, TrimEnd, StartsWith, delsträngen, ToUpper till motsvarande SQL inbyggda funktioner.
* **Matrisen funktioner**: har stöd för översättning av. NET'S Concat innehåller och antal till motsvarande SQL inbyggda funktioner.
* **Tilläggsfunktioner geospatiala**: har stöd för översättning av stub-metoder avståndet i IsValid och IsValidDetailed till motsvarande SQL inbyggda funktioner.
* **Användardefinierade funktionen tilläggsfunktionen**: har stöd för översättning av stub-metoden UserDefinedFunctionProvider.Invoke till motsvarande användardefinierad funktion.
* **Diverse**: har stöd för översättning av coalesce och villkorlig operatörer. Kan översätta innehåller strängen innehåller, ARRAY_CONTAINS eller SQL-IN beroende på kontext.

### <a name="sql-query-operators"></a>SQL-frågeoperatorer
Här följer några exempel som visar hur vissa standard LINQ-frågeoperatorer översätts till Cosmos-DB-frågor.

#### <a name="select-operator"></a>Välj Operator
Syntaxen är `input.Select(x => f(x))`, där `f` är ett skalärt uttryck.

**LINQ lambda-uttryck**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**LINQ lambda-uttryck**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**LINQ lambda-uttryck**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>SelectMany operator
Syntaxen är `input.SelectMany(x => f(x))`, där `f` är ett skalärt uttryck som returnerar en Mängdtyp.

**LINQ lambda-uttryck**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Där operator
Syntaxen är `input.Where(x => f(x))`, där `f` är ett skalärt uttryck som returnerar ett booleskt värde.

**LINQ lambda-uttryck**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**LINQ lambda-uttryck**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Sammansatta SQL-frågor
Ovanstående operatörer kan bestå för att bilda mer kraftfulla frågor. Eftersom Cosmos DB stöder kapslade samlingar, kan sammansättning vara antingen sammanfogas eller kapslade.

#### <a name="concatenation"></a>Sammanfogning
Syntaxen är `input(.|.SelectMany())(.Select()|.Where())*`. En sammanfogad fråga kan börja med en valfri `SelectMany` frågan följt av flera `Select` eller `Where` operatörer.

**LINQ lambda-uttryck**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**LINQ lambda-uttryck**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**LINQ lambda-uttryck**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**LINQ lambda-uttryck**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>För många kapslade
Syntaxen är `input.SelectMany(x=>x.Q())` där Q är en `Select`, `SelectMany`, eller `Where` operator.

I en kapslad fråga används den inre frågan till varje element i samlingen yttre. En viktig funktion är att den inre frågan kan referera till fält element i samlingen yttre som Självkopplingar.

**LINQ lambda-uttryck**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**LINQ lambda-uttryck**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**LINQ lambda-uttryck**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a id="ExecutingSqlQueries"></a>Kör SQL-frågor
Cosmos DB visar resurser via ett REST-API som kan anropas med valfritt språk som kan göra HTTP/HTTPS-förfrågningar. Dessutom erbjuder Cosmos DB programmeringsbibliotek för flera populära språk som .NET, Node.js, JavaScript och Python. REST-API och de olika biblioteken alla stöd för frågor via SQL. .NET SDK stöder LINQ frågar utöver SQL.

Följande exempel visar hur du skapar en fråga och skicka den mot en Cosmos-DB-databaskonto.

### <a id="RestAPI"></a>REST-API
Cosmos DB erbjuder en öppen RESTful-programmeringsmiljö via HTTP. Databasen konton kan etableras med hjälp av en Azure-prenumeration. Resursmodell Cosmos DB består av en uppsättning resurser under ett databaskonto som är adresserbara via en logisk och stabil URI. En uppsättning resurser kallas för en feed i det här dokumentet. Ett databaskonto består av en uppsättning databaser som alla innehåller flera samlingar, var och en av vilka i sin tur innehåller dokument, UDF: er och andra typer av resurser.

Modellen grundläggande interaktion med dessa resurser är via HTTP-verb som GET, PUT, POST och DELETE med sina standard tolkning. Verbet POST används för att skapa en ny resurs, för att köra en lagrad procedur eller för att utfärda en Cosmos-DB-fråga. Frågor är alltid skrivskyddade åtgärder med inga sidoeffekter.

I följande exempel visas en POST för en SQL-API-fråga som görs mot en samling som innehåller två exempeldokument vi har granskat hittills. Frågan har ett enkelt filter på namnegenskapen JSON. Observera användningen av den `x-ms-documentdb-isquery` och Content-Type: `application/query+json` rubriker för att ange att åtgärden en fråga.

**Förfrågan**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }


**Results**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


Det andra exemplet visar en mer komplex fråga som returnerar flera resultat från kopplingen.

**Förfrågan**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Results**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Om en frågas resultat får inte plats i en enskild sida med resultat REST-API och returnerar sedan en fortsättningstoken via den `x-ms-continuation-token` Svarsrubrik. Klienter kan sidbryta resultat genom att inkludera rubriken i efterföljande resultat. Antalet resultat per sida kan också kontrolleras via den `x-ms-max-item-count` nummer sidhuvud. Om den angivna frågan har en Aggregeringsfunktion som `COUNT`, och sedan sidan frågan kan returnera en delvis insamlat värde över sidan av resultaten. Klienterna måste utföra en andra nivå sammanställning över dessa resultat för att skapa de slutliga resultaten, till exempel, sum över räknas som returneras i enskilda sidor för att returnera det totala antalet.

Använd för att hantera data konsekvent princip för frågor i `x-ms-consistency-level` huvud som alla REST API-begäranden. För sessionskonsekvens, behöver också echo senast `x-ms-session-token` Cookie-huvud i frågebegäran. Samlingen efterfrågade indexprincip kan även påverka konsekvenskontroll av frågeresultatet. Med standardvärdet indexering principinställningar, för samlingar indexet är alltid aktuell med innehållet i dokumentet och frågeresultaten matchar konsekvenskontrollen valt för data. Om indexprincip är möjligt att Lazy kan frågor returnera inaktuella resultat. Mer information finns i [Azure Cosmos DB Konsekvensnivåer][consistency-levels].

Om den konfigurerade indexprincip på samlingen inte stöder den angivna frågan, returnerar servern Azure Cosmos DB 400 ”Felaktig begäran”. Returneras för intervallet frågor mot sökvägar som konfigurerats för sökningar hash (likhetsfrågor) och för sökvägar som uttryckligen är undantagen från indexering. Den `x-ms-documentdb-query-enable-scan` huvudet kan anges för att tillåta frågan för att utföra en genomsökning när ett index inte är tillgänglig.

Du kan få detaljerad mått på Frågekörningen genom att ange `x-ms-documentdb-populatequerymetrics` sidhuvud till `True`. Mer information finns i [SQL-frågan mätvärden för Azure Cosmos DB](sql-api-sql-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET) SDK
.NET SDK stöder både LINQ och SQL frågor. I följande exempel visas hur du utför enkelt filter-frågan som introduceras tidigare i det här dokumentet.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Det här exemplet jämför två egenskaper för likhetsfrågor inom varje dokument och använder anonym projektioner. 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


I nästa exempel visas kopplingar, uttryckt genom SelectMany för LINQ.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



.NET-klienten går automatiskt igenom alla sidor i resultatet av frågan i foreach-block som ovan. Frågealternativen introducerades i avsnittet REST API är också tillgängliga i .NET SDK med hjälp av `FeedOptions` och `FeedResponse` klasser i metoden CreateDocumentQuery. Antalet sidor som kan kontrolleras med hjälp av den `MaxItemCount` inställningen. 

Du kan också explicit styra sidindelning genom att skapa `IDocumentQueryable` med hjälp av den `IQueryable` objekt sedan genom att läsa den` ResponseContinuationToken` värden och skicka dem tillbaka som `RequestContinuationToken` i `FeedOptions`. `EnableScanInQuery` kan ställas in för att aktivera genomsökningar när frågan inte stöds av den konfigurerade indexprincip. För partitionerade samlingar kan du använda `PartitionKey` frågan ska köras mot en enskild partition (även om Cosmos DB extraherar det automatiskt från frågetexten) och `EnableCrossPartitionQuery` köra frågor som kan behöva köras mot flera partitioner. 

Referera till [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-documentdb-net) för flera sampel som innehåller frågor. 

### <a id="JavaScriptServerSideApi"></a>JavaScript API för serversidan
Cosmos DB är en programmeringsmodell för att köra JavaScript-baserade programlogik direkt på samlingar med lagrade procedurer och utlösare. JavaScript-logik som registrerats på en samlingsnivå kan sedan utfärda databasåtgärder i åtgärder på dokument i den angivna samlingen. Dessa åtgärder är kapslas in i omgivande ACID-transaktioner.

I följande exempel visas hur du använder den Frågedokument i JavaScript-server API för att göra förfrågningar från innanför lagrade procedurer och utlösare.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>Referenser
1. [Introduktion till Azure Cosmos DB][introduction]
2. [Azure Cosmos-Databasens SQL-specifikationen](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-documentdb-net)
4. [Konsekvensnivåer för Azure Cosmos DB][consistency-levels]
5. ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. JavaScript-specifikationen [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. Utvärdering av frågetekniker för stora databaser [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Bearbetning av frågor i parallella relationsdatabassystem, IEEE datorn Society Press, 1994
11. Lu Ooi, Tan, bearbetning av frågor i parallella relationsdatabassystem, IEEE datorn Society Press, 1994.
12. Kitts Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Report Anders Tomkins: Pig Latin: en inte så främmande språk för databehandling, SIGMOD 2008.
13. G. Graefe. Kaskadspridas ramverk för optimering av frågan. IEEE Data Eng. Bull., 18.3: 1995.

[1]: ./media/sql-api-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md

---
title: Anpassad sökning i kognitiva kunskaper – Azure Search
description: 'Utöka funktioner för kognitiv sökning kompetens genom att anropa till webb-API: er'
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: luisca
ms.custom: seojan2018
ms.openlocfilehash: 1fcb12fc2cfae98376210e1924a670cce444f4f2
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757670"
---
# <a name="custom-web-api-skill"></a>Anpassade webb-API-kunskaper

Den **anpassat webb-API** färdighet låter dig utöka kognitiv sökning genom att anropa till en webb-API-slutpunkt att tillhandahålla anpassade åtgärder. Liknar inbyggda kunskaper en **anpassade webb-API** färdighet har indata och utdata. Beroende på indata och får en JSON-nyttolast i ditt webb-API när indexeraren körs och som returnerar en JSON-nyttolast som ett svar, tillsammans med statuskoden lyckades. Svaret förväntas ha utdata som anges av dina anpassade kunskaper. Svaret är något annat betraktas som ett fel och ingen enrichments utförs.

Strukturen för JSON-nyttolaster beskrivs ytterligare ned i det här dokumentet.

> [!NOTE]
> Indexeraren kommer att försöka igen två gånger för vissa standard HTTP-statuskoder som returnerades från webb-API. De här HTTP-statuskoder är: 
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Färdighet parametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| URI | URI: N webb-api som den _JSON_ nyttolasten skickas. Endast **https** URI-schemat är tillåtet |
| httpMethod | Metoden som ska användas för att skicka nyttolasten. Tillåtna metoder är `PUT` eller `POST` |
| httpHeaders | En samling nyckel / värde-par där nycklarna som representerar rubriknamn och värden representerar värden i huvudet som skickas till ditt webb-API tillsammans med nyttolasten. Följande huvuden får inte vara i den här samlingen: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| timeout | (Valfritt) När du anger du tidsgränsen för http-klienten som gör API-anrop. Den måste vara formaterad som en XSD-värde för ”dayTimeDuration” (en begränsad delmängd av en [varaktighet i ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) värde). Till exempel `PT60S` i 60 sekunder. Om inte set, väljs ett standardvärde på 30 sekunder. Timeout-värdet kan anges till högst 90 sekunder och minst 1 sekund. |
| batchSize | (Valfritt) Anger hur många ”dataposter” (se _JSON_ nyttolast för strukturen nedan) kommer att skickas per API-anrop. Om inte set, ett standardvärde på 1 000 väljs. Vi rekommenderar att du använder den här parametern för att uppnå en lämplig kompromiss mellan indexeringsflöde och belastningen på ditt API |

## <a name="skill-inputs"></a>Färdighet indata

Det finns ingen ”fördefinierade” indata för den här färdighet. Du kan välja ett eller flera fält som är redan tillgänglig vid tidpunkten för den här färdighet körningen som indata och _JSON_ nyttolasten skickas till webb-API har olika fält.

## <a name="skill-outputs"></a>Färdighet utdata

Det finns inga ”fördefinierade” utdata för kompetensen. Lägg till fält för tabellutdata beroende på svaret returnerar ditt webb-API, så att de kan hämtas från den _JSON_ svar.


## <a name="sample-definition"></a>Exempeldefinition

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can count the number of words or characters or lines in text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "countOf",
            "source": "/document/propertyToCount"
          }
        ],
        "outputs": [
          {
            "name": "count",
            "targetName": "countOfThings"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Exemplet indata JSON-struktur

Detta _JSON_ struktur som representerar den nyttolast som skickas till ditt webb-API.
Det kommer alltid att följa dessa begränsningar:

* Den översta entitet kallas `values` och blir en matris med objekt. Antalet sådana objekt ska vara högst den `batchSize`
* Varje objekt i den `values` matris har
    * En `recordId` egenskap som är en **unika** sträng som används för att identifiera posten.
    * En `data` egenskap som är en _JSON_ objekt. Fälten för den `data` egenskapen motsvarar ”namn”-anges i den `inputs` avsnitt i färdighet-definitionen. Värdet för dessa fält kommer från den `source` fälten (som kan vara från ett fält i dokumentet eller potentiellt från en annan färdighet)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "countOf": "words"
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "countOf": "characters"
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world \r\n Hi World",
             "language": "en",
             "countOf": "lines"
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "countOf": null
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Exemplet utdata JSON-struktur

”Utdata” motsvarar svaret som returnerades från ditt webb-api. Web api endast ska returnera en _JSON_ nyttolast (verifieras genom att titta på den `Content-Type` svarshuvud) och bör uppfylla följande villkor:

* Det bör finnas en översta entitet som kallas `values` som ska vara en matris med objekt.
* Antalet objekt i matrisen ska vara samma som antalet objekt som skickas till webb-api.
* Varje objekt ska ha:
   * En `recordId` egenskap
   * En `data` egenskapen, vilket är ett objekt där fälten är enrichments matchande ”namn” i den `output` och vars värde betraktas berikande.
   * En `errors` egenskap, en matris med eventuella fel som upptäcks som kommer att läggas till indexeraren körningshistorik. Den här egenskapen måste anges, men kan ha en `null` värde.
   * En `warnings` egenskap, en matris som listar alla varningar påträffades som kommer att läggas till indexeraren körningshistorik. Den här egenskapen måste anges, men kan ha en `null` värde.
* Objekten i den `values` matrisen måste vara i samma ordning som objekten i den `values` matris som skickas som en begäran till webb-API. Men den `recordId` används för korrelation så någon post i svar som innehåller en `recordId` som inte ingick i den ursprungliga begäran till webb-API kommer att tas bort.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "Cannot understand what needs to be counted"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "count": 2
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "count": 6
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "count": 11
            },
            "errors": null,
            "warnings": null
        },
    ]
}

```

## <a name="error-cases"></a>Felhändelser
Förutom ditt webb-API som är otillgänglig eller skicka ut inte lyckas statuskoder betraktas följande som felaktiga fall:

* Om webb-API returnerar statuskoden lyckades men svaret anger att det inte är `application/json` svaret är ogiltiga och inga enrichments kommer att utföras.
* Om det finns **ogiltigt** (med `recordId` inte i den ursprungliga begäran eller med dubblettvärden) poster i svaret `values` matris, inga berikande ska utföras för **dessa** poster.

I de fall när webb-API är tillgängligt eller returnerar ett HTTP-fel, läggs ett eget fel med alla tillgängliga information om HTTP-felet till indexeraren körningshistorik.

## <a name="see-also"></a>Se också

+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Lägg till anpassad kompetens att kognitiv sökning](cognitive-search-custom-skill-interface.md)
+ [Skapa en anpassad kompetens med hjälp av översätta Text-API](cognitive-search-create-custom-skill-example.md)
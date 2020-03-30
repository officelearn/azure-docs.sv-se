---
title: Anpassad webb-API-färdighet i skillsets
titleSuffix: Azure Cognitive Search
description: Utöka funktionerna i Azure Cognitive Search skillsets genom att ringa ut till webb-API:er. Använd den anpassade webb-API-färdigheten för att integrera din anpassade kod.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74484109"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Anpassad webb-API-färdighet i en Azure Cognitive Search-anrikningspipeline

Med den **anpassade webb-API-färdigheten** kan du utöka AI-anrikning genom att ringa ut till en webb-API-slutpunkt som tillhandahåller anpassade åtgärder. I likhet med inbyggda kunskaper har en **anpassad webb-API-färdighet** indata och utdata. Beroende på indata får webb-API:et en JSON-nyttolast när indexeraren körs och matar ut en JSON-nyttolast som ett svar, tillsammans med en statuskod för lyckade svar. Svaret förväntas ha de utdata som anges av din anpassade färdighet. Alla andra svar betraktas som ett fel och inga anrikningar utförs.

Strukturen på JSON nyttolaster beskrivs längre ner i detta dokument.

> [!NOTE]
> Indexeraren försöker igen två gånger för vissa standard-HTTP-statuskoder som returneras från webb-API:et. Dessa HTTP-statuskoder är: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.webapiskill

## <a name="skill-parameters"></a>Färdighetsparametrar

Parametrar är skiftlägeskänsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| Uri | URI för webb-API:et som _JSON-nyttolasten_ ska skickas till. Endast **https** URI-schema är tillåtet |
| httpMethod (på) | Den metod som ska användas när nyttolasten skickas. Tillåtna `PUT` metoder är eller`POST` |
| httpHeaders (på) | En samling nyckel-värde-par där nycklarna representerar rubriknamn och värden representerar rubrikvärden som ska skickas till webb-API:et tillsammans med nyttolasten. Följande rubriker är förbjudna att vara `Accept` `Accept-Charset`i `Accept-Encoding` `Content-Length`den `Content-Type` `Cookie`här `Host` `TE`samlingen: , , , , , , , , , , , , , , , `Upgrade``Via` |
| timeout | (Valfritt) När det anges anger timeout för http-klienten som ringer API-anropet. Det måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett [VARAKTIGHETSVÄRDE FÖR ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Till exempel `PT60S` i 60 sekunder. Om den inte är inställd väljs ett standardvärde på 30 sekunder. Tidsgränsen kan ställas in på högst 230 sekunder och minst 1 sekund. |
| batchSize | (Valfritt) Anger hur många "dataposter" _JSON_ (se JSON-nyttolaststrukturen nedan) som kommer att skickas per API-anrop. Om den inte är inställd väljs en standard på 1000. Vi rekommenderar att du använder den här parametern för att uppnå en lämplig avvägning mellan indexeringsdataflöde och belastning på ditt API |
| gradAvParallelism | (Valfritt) När det anges anger antalet samtal som indexeraren kommer att ringa parallellt med den slutpunkt du har angett. Du kan minska det här värdet om slutpunkten misslyckas under för hög av en begäran belastning, eller höja den om din slutpunkt kan acceptera fler begäranden och du vill ha en ökning av prestanda för indexeraren.  Om inte inställt används ett standardvärde på 5. Graden Avparallelism kan ställas in på högst 10 och minst 1. |

## <a name="skill-inputs"></a>Indata för färdighet

Det finns inga "fördefinierade" indata för den här färdigheten. Du kan välja ett eller flera fält som redan är tillgängliga när den här färdigheten körs som indata och _den JSON-nyttolasten_ som skickas till webb-API:et har olika fält.

## <a name="skill-outputs"></a>Utdata för färdighet

Det finns inga "fördefinierade" utdata för den här färdigheten. Beroende på vilket svar webb-API:et kommer tillbaka lägger du till utdatafält så att de kan hämtas från _JSON-svaret._


## <a name="sample-definition"></a>Exempeldefinition

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
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
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Exempel på JSON-struktur för provinmatning

Den här _JSON-strukturen_ representerar nyttolasten som ska skickas till webb-API:et.
Det kommer alltid att följa dessa begränsningar:

* Entiteten på den `values` översta nivån anropas och kommer att vara en matris med objekt. Antalet sådana objekt kommer på sin höjd att vara`batchSize`
* Varje objekt `values` i matrisen kommer att ha
    * En `recordId` egenskap som är en **unik** sträng som används för att identifiera posten.
    * En `data` egenskap som är ett _JSON-objekt._ Fälten i `data` egenskapen motsvarar de "namn" `inputs` som anges i avsnittet i färdighetsdefinitionen. Värdet för dessa fält kommer `source` att komma från dessa fält (som kan komma från ett fält i dokumentet eller eventuellt från en annan färdighet)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Exempel på JSON-struktur

"Utdata" motsvarar svaret som returneras från webb-API:et. Webb-API:et ska endast returnera en _JSON-nyttolast_ (verifierad genom att titta på `Content-Type` svarshuvudet) och bör uppfylla följande begränsningar:

* Det bör finnas en enhet `values` på den översta nivån som ska vara en matris med objekt.
* Antalet objekt i matrisen ska vara detsamma som antalet objekt som skickas till webb-API:et.
* Varje objekt bör ha:
   * En `recordId` fastighet
   * En `data` egenskap, som är ett objekt där fälten är `output` berikande som matchar "namnen" i och vars värde anses vara anrikningen.
   * En `errors` egenskap, en matris med alla fel som påträffats och som läggs till i indexeringskörningshistoriken. Den här egenskapen krävs, `null` men kan ha ett värde.
   * En `warnings` egenskap, en matris med alla varningar som påträffats och som läggs till i indexeringskörningshistoriken. Den här egenskapen krävs, `null` men kan ha ett värde.
* Objekten i `values` matrisen behöver inte vara i samma `values` ordning som objekten i matrisen som skickas som en begäran till webb-API:et. Emellertid `recordId` används det för korrelation, så någon `recordId` post i svaret som innehåller en som inte var delen av den original- förfråganen till webb-API:et, ska kasseras.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Felfall
Förutom att webb-API:et inte är tillgängligt eller att skicka ut statuskoder som inte är lyckade betraktas följande som felaktiga:

* Om webb-API:et returnerar en statuskod för `application/json` lyckade försök men svaret anger att det inte är det anses svaret ogiltigt och inga anrikningar utförs.
* Om det finns `recordId` **ogiltiga** (med inte i den ursprungliga begäran `values` eller med dubblettvärden) poster i svarsmatrisen, kommer ingen anrikning att utföras för **dessa** poster.

I de fall då webb-API:et inte är tillgängligt eller returnerar ett HTTP-fel läggs ett användarvänligt fel med all tillgänglig information om HTTP-felet till i körningshistoriken för indexeraren.

## <a name="see-also"></a>Se även

+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Lägga till anpassad färdighet i en AI-anrikningspipeline](cognitive-search-custom-skill-interface.md)
+ [Exempel: Skapa en anpassad färdighet för AI-anrikning](cognitive-search-create-custom-skill-example.md)

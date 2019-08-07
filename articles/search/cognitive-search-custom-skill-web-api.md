---
title: Anpassad kognitiv Sök kompetens – Azure Search
description: 'Utöka funktionerna i kognitiv search-färdighetsuppsättningar genom att anropa ut till webb-API: er'
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 0451778d9b3bb29d06551c881b9f674ef7a74ab3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841222"
---
# <a name="custom-web-api-skill"></a>Anpassad webb-API-kunskap

Med den **anpassade webb-API-** kunskapen kan du utöka kognitiv sökning genom att anropa till en webb-API-slutpunkt som tillhandahåller anpassade åtgärder. På liknande sätt som inbyggda kunskaper har en **anpassad webb-API-** kunskap indata och utdata. Beroende på indata får ditt webb-API en JSON-nyttolast när indexeraren körs, och utvärderar en JSON-nyttolast som svar, tillsammans med status koden lyckades. Svaret förväntas ha de utdata som anges av din anpassade färdighet. Andra svar betraktas som ett fel och inga berikningar utförs.

Strukturen för JSON-nyttolasterna beskrivs ytterligare ned i det här dokumentet.

> [!NOTE]
> Indexeraren försöker igen två gånger för vissa standard-HTTP-statuskod som returneras från webb-API: et. HTTP-status koderna är: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Kunskaps parametrar

Parametrar är Skift läges känsliga.

| Parameternamn     | Beskrivning |
|--------------------|-------------|
| URI | URI för webb-API: t som _JSON_ -nyttolasten ska skickas till. Endast **https** URI-schema tillåts |
| httpMethod | Den metod som ska användas vid sändning av nytto lasten. Tillåtna metoder är `PUT` eller`POST` |
| httpHeaders | En samling nyckel/värde-par där nycklarna representerar rubrik namn och värden representerar huvud värden som skickas till ditt webb-API tillsammans med nytto lasten. Följande rubriker `Accept`är förbjudna att tas med i samlingen: `Content-Type` `Accept-Encoding`, `Content-Length` `Accept-Charset` `Cookie` ,,`Host`,,,, `TE` `Upgrade``Via` |
| timeout | Valfritt Anger tids gränsen för http-klienten som gör API-anropet. Det måste formateras som ett XSD "dayTimeDuration"-värde (en begränsad delmängd av ett varaktighets värde på [ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Till exempel `PT60S` i 60 sekunder. Om inget värde anges väljs ett standardvärde på 30 sekunder. Tids gränsen kan anges till högst 230 sekunder och minst 1 sekund. |
| batchSize | Valfritt Anger hur många "data poster" (se _JSON_ nytto Last strukturen nedan) som ska skickas per API-anrop. Om den inte anges väljs standardvärdet 1000. Vi rekommenderar att du använder den här parametern för att uppnå en lämplig kompromiss mellan indexering av data flödet och belastningen på ditt API |

## <a name="skill-inputs"></a>Kompetens inmatningar

Det finns inga fördefinierade indata för den här kunskapen. Du kan välja ett eller flera fält som redan är tillgängliga när du kör den här färdighets körningen som indata och _JSON_ -nyttolasten som skickas till webb-API: t har olika fält.

## <a name="skill-outputs"></a>Kunskaps utmatningar

Det finns inga fördefinierade utdata för den här kunskapen. Beroende på vilket svar som webb-API: t kommer att returnera, lägger du till utdatakolumner så att de kan hämtas från _JSON_ -svaret.


## <a name="sample-definition"></a>Exempel definition

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
## <a name="sample-input-json-structure"></a>Exempel-JSON-struktur

Den här _JSON_ -strukturen representerar den nytto last som ska skickas till ditt webb-API.
Den kommer alltid att följa dessa begränsningar:

* Entiteten på den översta nivån anropas `values` och är en matris med objekt. Antalet sådana objekt är högst`batchSize`
* Varje objekt i `values` matrisen kommer att ha
    * En `recordId` egenskap som är en **unik** sträng som används för att identifiera posten.
    * En `data` egenskap som är ett _JSON_ -objekt. Fälten i `data` egenskapen motsvarar de "namn" som anges `inputs` i avsnittet i kunskaps definitionen. Värdet för dessa fält kommer från `source` dessa fält (som kan komma från ett fält i dokumentet eller som kan komma från en annan kunskap)

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

## <a name="sample-output-json-structure"></a>Exempel-JSON-struktur för utdata

"Utdata" motsvarar det svar som returneras från ditt webb-API. Webb-API: et ska bara returnera en _JSON_ -nyttolast (verifierad `Content-Type` genom att titta på svars huvudet) och bör uppfylla följande begränsningar:

* Det bör finnas en entitet `values` på den översta nivån som ska vara en matris med objekt.
* Antalet objekt i matrisen måste vara samma som antalet objekt som skickas till webb-API: et.
* Varje objekt bör ha:
   * En `recordId` egenskap
   * En `data` egenskap, som är ett objekt där fälten är anrikninger som matchar "namn" `output` i och vars värde betraktas som berikning.
   * En `errors` egenskap, en matris som visar eventuella fel som kommer att läggas till i körnings historiken för indexeraren. Den här egenskapen är obligatorisk, men kan ha `null` ett värde.
   * En `warnings` egenskap, en matris som visar alla varningar som kommer att läggas till i körnings historiken för indexeraren. Den här egenskapen är obligatorisk, men kan ha `null` ett värde.
* Objekt i `values` matrisen behöver inte vara i samma ordning som de objekt `values` i matrisen som skickas som en begäran till webb-API: et. Men används för korrelation så att alla poster i svaret som innehåller en som inte `recordId` var en del av den ursprungliga begäran till webb-API: et ignoreras. `recordId`

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

## <a name="error-cases"></a>Fel fall
Förutom att ditt webb-API är otillgängligt, eller om du skickar ut status koder som inte lyckades, anses följande vara felaktiga fall:

* Om webb-API: t returnerar en status kod för lyckad status men svaret visar att `application/json` svaret inte är det, anses inte svaret vara ogiltigt och inga berikningar utförs.
* Om det finns **ogiltiga** (med `recordId` inte i den ursprungliga begäran eller med dubblettvärden) poster i svars `values` mat ris, utförs ingen berikning för **dessa** poster.

När webb-API: t inte är tillgängligt eller returnerar ett HTTP-fel, läggs ett fel meddelande med tillgänglig information om HTTP-felet till i körnings historiken för Indexer.

## <a name="see-also"></a>Se också

+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Lägg till anpassad färdighet i kognitiv sökning](cognitive-search-custom-skill-interface.md)
+ [Exempel: Skapa en anpassad färdighet för kognitiv sökning](cognitive-search-create-custom-skill-example.md)
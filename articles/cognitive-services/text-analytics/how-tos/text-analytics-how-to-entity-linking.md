---
title: Använda entitetsigenkänning med API:et för textanalys
titleSuffix: Azure Cognitive Services
description: Lär dig hur du identifierar och disambiguererar identiteten för en entitet som finns i text med REST-API:et för textanalys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203499"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Så här använder du namngiven entitetsigenkänning i textanalys

Med API:et för textanalys kan du ta ostrukturerad text och returnera en lista över oenyderade entiteter, med länkar till mer information på webben. API:et stöder både namngiven enhetsigenkänning (NER) och entitetslänkning.

### <a name="entity-linking"></a>Länkning av entiteter

Entitetslänkning är förmågan att identifiera och disambiguate identiteten på en enhet `Mars` som finns i text (till exempel för att avgöra om en förekomst av ordet hänvisar till planeten, eller till den romerska krigsguden). Den här processen kräver att det finns en kunskapsbas på ett lämpligt språk för att länka erkända entiteter i text. Entity Linking använder [Wikipedia](https://www.wikipedia.org/) som denna kunskapsbas.


### <a name="named-entity-recognition-ner"></a>Namngiven entitetsigenkänning (NER)

Namngiven entitetsigenkänning (NER) är möjligheten att identifiera olika entiteter i text och kategorisera dem i fördefinierade klasser eller typer som: person, plats, händelse, produkt och organisation.  

Med början i version 3 kan den här funktionen i API:et för textanalys också identifiera personliga och känsliga informationstyper som: telefonnummer, personnummer, e-postadress och bankkontonummer.  Identifiera dessa entiteter kan hjälpa till att klassificera känsliga dokument och redigera personlig information.

## <a name="named-entity-recognition-versions-and-features"></a>Namngivna versioner och funktioner för entitetsigenkänning

Api:et för textanalys erbjuder två versioner av namngiven entitetsigenkänning – v2 och v3. Version 3 (Offentlig förhandsversion) innehåller ökad information i de entiteter som kan identifieras och kategoriseras.

| Funktion                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Metoder för enstaka och batchbegäranden                          | X      | X      |
| Grundläggande enhetsigenkänning i flera kategorier              | X      | X      |
| Utökad klassificering för erkända entiteter                 |        | X      |
| Separata slutpunkter för att skicka entitetslänkning och NER-begäranden. |        | X      |
| Versionshantering av modell                                                |        | X      |

Mer [information finns i språkstöd.](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)


#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

### <a name="entity-types"></a>Entitetstyper

Namngiven entitetsigenkänning v3 ger utökad identifiering mellan flera typer. För närvarande kan NER v3 känna igen följande kategorier av entiteter:

* Allmänt
* Personlig information 

En detaljerad lista över enheter och språk som stöds finns i artikeln [NER v3-stödda entitetstyper.](../named-entity-types.md)

### <a name="request-endpoints"></a>Begär slutpunkter

Namngivna entitetsigenkänning v3 använder separata slutpunkter för NER och entitetslänkningsbegäranden. Använd ett URL-format nedan baserat på din begäran:

NER (NER)
* Allmänna enheter -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Personlig information -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entitetslänkning
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Versionshantering av modell

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

### <a name="entity-types"></a>Entitetstyper

> [!NOTE]
> Named Entity Recognition(NER) version 2 stöder endast följande entiteter. NER v3 är i offentlig förhandsversion och utökar antalet och djupet för de entiteter som känns igen i text.   

| Typ  | Subtyp | Exempel |
|:-----------   |:------------- |:---------|
| Person        | Ej tillämpligt\*         | "Jeff", "Bill Gates"     |
| Location      | Ej tillämpligt\*         | "Redmond, Washington", "Paris"  |
| Organisation  | Ej tillämpligt\*         | "Microsoft"   |
| Kvantitet      | Tal        | "6", "sex"     |
| Kvantitet      | Procent    | "50%", "femtio procent"|
| Kvantitet      | Ordinal       | "2:a", "tvåa"     |
| Kvantitet      | Ålder           | "90 dagar gammal", "30 år gammal"    |
| Kvantitet      | Valuta      | "$10.99"     |
| Kvantitet      | Dimension     | "10 miles", "40 cm"     |
| Kvantitet      | Temperatur   | "32 grader"    |
| DateTime      | Ej tillämpligt\*         | "18:30 4 februari 2012"      |
| DateTime      | Datum          | "2 maj 2017", "2017-05-02"   |
| DateTime      | Tid          | "08:00", "8:00"  |
| DateTime      | DatumRange     | "2 maj till 5 maj"    |
| DateTime      | TimeRange (Tidsföring)     | "18:00 till 19:00"     |
| DateTime      | Varaktighet      | "1 minut och 45 sekunder"   |
| DateTime      | Ange           | "varje tisdag"     |
| URL           | Ej tillämpligt\*         | "https:\//www.bing.com"    |
| E-post         | Ej tillämpligt\*         | "support@contoso.com" |
| Amerikanskt telefonnummer  | Ej tillämpligt\*         | (Endast telefonnummer i USA) "(312) 555-0176" |
| IP-adress    | Ej tillämpligt\*         | "10.0.0.100" |

\*Beroende på indata och extraherade enheter kan vissa `SubType`enheter utelämna .  Alla listade enhetstyper som stöds är endast tillgängliga för de engelska, kinesisk-förenklade, franska, tyska och spanska språken.

### <a name="request-endpoints"></a>Begär slutpunkter

Named Entity Recognition v2 använder en enda slutpunkt för NER- och entitetslänkningsbegäranden:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Skicka en REST API-begäran

### <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: ID, text, språk.

Varje dokument måste vara under 5 120 tecken och du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten.

### <a name="structure-the-request"></a>Strukturera begäran

Skicka en POST-begäran. Du kan [använda Postman](text-analytics-how-to-call-api.md) eller **API-testkonsolen** i följande länkar för att snabbt strukturera och skicka en. 

> [!NOTE]
> Du hittar din nyckel och slutpunkt för din Text Analytics-resurs på Azure-portalen. De kommer att finnas på resursens **snabbstartssida** under **resurshantering**. 

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning](#tab/version-3)

[Namngiven entitetsigenkänning v3-referens](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

Version 3 använder separata slutpunkter för NER- och entitetslänkningsbegäranden. Använd ett URL-format nedan baserat på din begäran:

NER (NER)
* Allmänna enheter -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Enheter för personuppgifter -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entitetslänkning
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

[Namngiven enhetsigenkänning (NER) v2-referens](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

Version 2 använder följande slutpunkt för entitetslänkning och NER-begäranden: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Ange att en begäran ska innehålla API-nyckeln för textanalys. I förfrågelsorganet ska du tillhandahålla de JSON-dokument som du har förberett.

### <a name="example-ner-request"></a>Exempel på NER-begäran 

Följande är ett exempel på innehåll som du kan skicka till API: et. Formatet för begäran är detsamma för båda versionerna av API:et.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>Publicera begäran

Analysen utförs när begäran har tagits emot. Se avsnittet [datagränser](../overview.md#data-limits) i översikten för information om storleken och antalet förfrågningar som du kan skicka per minut och sekund.

API:et för textanalys är tillståndslöst. Inga data lagras i ditt konto och resultaten returneras omedelbart i svaret.

## <a name="view-results"></a>Visa resultat

Alla POST-begäranden returnerar ett JSON-formaterat svar med ID:na och identifierade entitetsegenskaper.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör så att du kan sortera, söka och hantera data. På grund av stöd för flerspråkiga och emoji kan svaret innehålla textförskjutningar. Se [hur du bearbetar textförskjutningar](../concepts/text-offsets.md) för mer information.

#### <a name="version-30-preview"></a>[Version 3.0-förhandsvisning)](#tab/version-3)

### <a name="example-v3-responses"></a>Exempel v3 svar

Version 3 innehåller separata slutpunkter för NER- och entitetslänkning. Svaren för båda verksamheterna finns nedan. 

#### <a name="example-ner-response"></a>Exempel PÅ NER-svar

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>Exempel på svar om entitetslänkning

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21"></a>[Version 2.1](#tab/version-2)

### <a name="example-ner-v2-response"></a>Exempel på NER v2-svar
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöden för entitetslänkning med textanalys i Cognitive Services. Sammanfattningsvis:

* Namngiven entitetsigenkänning är tillgänglig för valda språk i två versioner.
* JSON-dokument i begäran innehåller ett ID, text och språkkod.
* POST-begäranden skickas till en eller flera slutpunkter med hjälp av en anpassad [åtkomstnyckel och en slutpunkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som är giltig för din prenumeration.
* Svarsutdata, som består av länkade enheter (inklusive konfidenspoäng, förskjutningar och webblänkar, för varje dokument-ID) kan användas i alla program

## <a name="next-steps"></a>Nästa steg

* [Översikt över Textanalys](../overview.md)
* [Använda klientbiblioteket Text Analytics](../quickstarts/text-analytics-sdk.md)
* [Nyheter](../whats-new.md)

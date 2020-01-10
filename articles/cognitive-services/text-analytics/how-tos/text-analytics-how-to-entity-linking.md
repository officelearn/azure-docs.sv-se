---
title: Använd entitets igenkänning med API för textanalys
titleSuffix: Azure Cognitive Services
description: Lär dig hur du identifierar och disambiguate identiteten för en entitet som finns i text med Textanalys REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/06/2019
ms.author: aahi
ms.openlocfilehash: a9bfa2a51341bf60f92378f5d96a390bd1d92db1
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732786"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Använda namngiven enhets igenkänning i Textanalys

Med API för textanalys kan du ta ostrukturerad text och returnera en lista med disambiguated-entiteter, med länkar till mer information på webben. API: et stöder både NER (Named Entity Recognition) och entitet länkning.

### <a name="entity-linking"></a>Entitetslänkning

Länkning av entitet är möjligheten att identifiera och disambiguate identiteten för en entitet som finns i text (till exempel att avgöra om en förekomst av ordet `Mars` refererar till planet eller den latinska god). Den här processen kräver att det finns en kunskaps bas på ett lämpligt språk för att länka identifierade entiteter i text. 


### <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)

Med namngiven enhets igenkänning (NER) kan du identifiera olika entiteter i text och kategorisera dem i fördefinierade klasser eller typer. Till exempel: personer, platser och organisationer.

## <a name="named-entity-recognition-versions-and-features"></a>Versioner och funktioner för namngiven enhets igenkänning

API för textanalys erbjuder två versioner av namngiven enhets igenkänning – v2 och v3. Version 3 (offentlig för hands version) ger ökad information i de entiteter som kan identifieras och kategoriseras.

| Funktion                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Metoder för enkel-och batch-begäranden                          | X      | X      |
| Grundläggande enhets igenkänning över flera kategorier              | X      | X      |
| Utökad klassificering för identifierade entiteter                 |        | X      |
| Separata slut punkter för att skicka enhets länknings-och NER-begäranden. |        | X      |
| Modell version                                                |        | X      |

Mer information finns i [språk stöd](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) .

#### <a name="version-2tabversion-2"></a>[Version 2](#tab/version-2)

### <a name="entity-types"></a>Entitetstyper

> [!NOTE]
> NER (Named Entity Recognition) version 2 stöder endast följande entiteter. NER v3 finns i en offentlig för hands version och utökar avsevärt antalet och djupet i entiteterna som identifieras i text.   

| Typ  | Undertyp | Exempel |
|:-----------   |:------------- |:---------|
| Person        | Ej tillämpligt\*         | "Jeff", "Bill Gates"     |
| Location      | Ej tillämpligt\*         | ”Redmond, Washington”, ”Paris”  |
| Organisation  | Ej tillämpligt\*         | ”Microsoft”   |
| Kvantitet      | Tal        | ”6”, ”sex”     |
| Kvantitet      | Procent    | ”50 %”, ”femtio procent”|
| Kvantitet      | Ordningstal       | ”2:a”, ”andra”     |
| Kvantitet      | Ålder           | "90 dag gammal", "30 år gammal"    |
| Kvantitet      | Valuta      | ”10,99 USD”     |
| Kvantitet      | Dimension     | ”10 miles”, ”40 cm”     |
| Kvantitet      | Temperatur   | ”32 grader”    |
| DateTime      | Ej tillämpligt\*         | ”18.30 den 4 februari 2012”      |
| DateTime      | Datum          | ”2 maj 2017”, ”2017-05-02”   |
| DateTime      | Tid          | "8.00", "8:00"  |
| DateTime      | DateRange     | ”2 maj till 5 maj”    |
| DateTime      | TimeRange     | ”18 till 19”     |
| DateTime      | Längd      | ”1 minut och 45 sekunder”   |
| DateTime      | Ange           | ”varje tisdag”     |
| URL           | Ej tillämpligt\*         | "https:\//www.bing.com"    |
| E-post         | Ej tillämpligt\*         | "support@contoso.com" |
| Telefonnummer till USA  | Ej tillämpligt\*         | (Endast telefonnummer till USA) "(312) 555-0176" |
| IP-adress    | Ej tillämpligt\*         | "10.0.0.100" |

\* beroende på inmatade och extraherade entiteter kan vissa entiteter utelämna `SubType`.  Alla entitetstyper som stöds visas endast på engelska, kinesiska (förenklad), franska, tyska och spanska språk.

### <a name="request-endpoints"></a>Begär slut punkter

Namngiven entitets igenkännings version v2 använder en enda slut punkt för NER och begär Anden om att länka entiteter:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

#### <a name="version-3-public-previewtabversion-3"></a>[Version 3 (offentlig för hands version)](#tab/version-3)

### <a name="entity-types"></a>Entitetstyper

Med namnet entitets igenkänning v3 får du utökad identifiering över flera typer. För närvarande kan NER v3 identifiera följande kategorier av entiteter:

* Allmänt
* Personlig information 

En detaljerad lista över entiteter och språk som stöds finns i artikeln [ner v3-typer som stöds](../named-entity-types.md) .

### <a name="request-endpoints"></a>Begär slut punkter

Med namnet entitets igenkänning v3 används separata slut punkter för NER och begär Anden om att länka entiteter. Använd ett URL-format nedan baserat på din begäran:

NER
* Allmänna entiteter – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Personlig information – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Länkning av entitet
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Modell version

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

---

## <a name="sending-a-rest-api-request"></a>Skicka en REST API-begäran

### <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: ID, text, språk.

Varje dokument måste vara under 5 120 tecken och du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten.

### <a name="structure-the-request"></a>Strukturera begäran

Skapa en POST-begäran. Du kan [använda Postman](text-analytics-how-to-call-api.md) eller **API test-konsolen** i följande länkar för att snabbt strukturera och skicka en. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

#### <a name="version-2tabversion-2"></a>[Version 2](#tab/version-2)

[Referens för namngiven enhets igenkänning (NER) v2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

Version 2 använder följande slut punkt för entitet länkning och NER-begär Anden: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

#### <a name="version-3tabversion-3"></a>[Version 3](#tab/version-3)

[Referens för namngiven enhets igenkänning v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

Version 3 använder separata slut punkter för NER och begär Anden om enhets länkning. Använd ett URL-format nedan baserat på din begäran:

NER
* Allmänna entiteter – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entiteter med personlig information – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Länkning av entitet
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

---

Ange ett rubrik för begäran som ska innehålla din API för textanalys-nyckel. I begär ande texten anger du de JSON-dokument som du har för berett.

### <a name="example-ner-request"></a>Exempel på NER-begäran 

Följande är ett exempel på innehåll som du kan skicka till API: et. Formatet för begäran är detsamma för båda versionerna av API: et.

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

Analysen utförs när begäran har tagits emot. I avsnittet [data begränsningar](../overview.md#data-limits) i översikt finns information om storlek och antal begär Anden som du kan skicka per minut och sekund.

API för textanalys är tillstånds lös. Inga data lagras i ditt konto och resultaten returneras omedelbart i svaret.

## <a name="view-results"></a>Visa resultat

Alla POST-förfrågningar returnerar ett JSON-formaterat svar med ID och identifierade egenskaper för entiteten.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör så att du kan sortera, söka och hantera data.

#### <a name="version-2tabversion-2"></a>[Version 2](#tab/version-2)

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

#### <a name="version-3-public-previewtabversion-3"></a>[Version 3 (offentlig för hands version)](#tab/version-3)

### <a name="example-v3-responses"></a>Exempel på v3-svar

Version 3 innehåller separata slut punkter för NER-och enhets länkning. Svaren för båda åtgärderna nedan.

#### <a name="example-ner-response"></a>Exempel på NER-svar

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

#### <a name="example-entity-linking-response"></a>Exempel på länkat svar för entitet

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

---

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för entitets länkning med Textanalys i Cognitive Services. Sammanfattningsvis:

* Identifiering av namngivna enheter är tillgängligt för valda språk i två versioner.
* JSON-dokument i begär ande texten innehåller ID, text och språkkod.
* POST-begäranden skickas till en eller flera slut punkter, med hjälp av en anpassad [åtkomst nyckel och en slut punkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som är giltig för din prenumeration.
* Svars utdata, som består av länkade entiteter (inklusive förtroende poäng, förskjutningar och webb Länkar för varje dokument-ID) kan användas i alla program

## <a name="next-steps"></a>Nästa steg

* [Översikt över Textanalys](../overview.md)
* [Använda klient biblioteket för Textanalys](../quickstarts/text-analytics-sdk.md)
* [Nyheter](../whats-new.md)

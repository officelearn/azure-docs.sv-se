---
title: Använd entitets igenkänning med API för textanalys
titleSuffix: Azure Cognitive Services
description: Lär dig att identifiera entiteter med hjälp av Textanalys REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: e51e5945df8b08ec81db0c85416b31b3ec788ffd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488632"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Använda namngiven enhets igenkänning i Textanalys

Det [namngivna identifierings-API: et för entiteten](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) tar ostrukturerad text och returnerar en lista med disambiguated-entiteter med länkar till mer information på webben (Wikipedia och Bing) för varje JSON-dokument.

## <a name="entity-linking-and-named-entity-recognition"></a>Enhets länkning och identifiering av namngiven entitet

Textanalyss `entities`-slutpunkt stöder både NER-och entitets igenkänning.

### <a name="entity-linking"></a>Länkning av entiteter
Enhets länkning är möjligheten att identifiera och disambiguate identiteten för en entitet som påträffas i text (till exempel att avgöra om "mars" används som planet eller som den romerska god). Den här processen kräver att det finns en kunskaps bas för vilka kända entiteter är länkade – Wikipedia används som kunskaps bas för `entities` slut punkts Textanalys.

### <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)
Med namngiven enhets igenkänning (NER) kan du identifiera olika entiteter i text och kategorisera dem i fördefinierade klasser, eller typer. 

## <a name="named-entity-recognition-v3-public-preview"></a>Namngiven enhets igenkänning v3 offentlig för hands version

[Nästa version av namngiven enhets igenkänning](https://cognitiveusw2ppe.portal.azure-api.net/docs/services/TextAnalytics-v3-0-Preview-1/operations/56f30ceeeda5650db055a3c7/console) är nu tillgänglig för offentlig för hands version. Den ger uppdateringar för både entitets-och enhets identifiering. 

:::row:::
    :::column span="":::
        **Funktion**
    :::column-end:::
    ::: column span="":::
        **Beskrivning** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        Expanderade entitetstyper och under typer
    :::column-end:::
    :::column span="":::
     Utökad klassificering och identifiering för flera namngivna enhets typer.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Separata slut punkter för begäran 
    :::column-end:::
    :::column span="":::
        Separata slut punkter för att skicka enhets länknings-och NER-begäranden.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` parameter
    :::column-end:::
    :::column span="":::
        En valfri parameter för att välja en version av Textanalyss modellen. För närvarande är standard modellen tillgänglig för användning.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Entitetstyper

Med namnet entitets igenkänning v3 får du utökad identifiering över flera typer. För närvarande kan NER v3 identifiera följande kategorier av entiteter. En detaljerad lista över vilka entiteter och språk som stöds finns i artikeln [namngivna enhets typer](../named-entity-types.md) .

* Allmänt
* Personlig information 

### <a name="request-endpoints"></a>Begär slut punkter

Med namnet entitets igenkänning v3 används separata slut punkter för NER och begär Anden om att länka entiteter. Använd ett URL-format nedan baserat på din begäran:

NER
* Allmänna entiteter – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entiteter med personlig information – `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Länkning av entitet
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Modell version

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Typer som stöds för namngiven entitets igenkänning v2

> [!NOTE]
> Följande entiteter stöds av den namngivna enhets igenkänningen (NER) version 2. [Ner v3](#named-entity-recognition-v3-public-preview) finns i en offentlig för hands version och utökar avsevärt antalet och djupet i entiteterna som identifieras i text.   

| Typ  | Undertyp | Exempel |
|:-----------   |:------------- |:---------|
| Person        | Ej tillämpligt\*         | "Jeff", "Bill Gates"     |
| Plats      | Ej tillämpligt\*         | ”Redmond, Washington”, ”Paris”  |
| Organisation  | Ej tillämpligt\*         | ”Microsoft”   |
| Kvantitet      | Tal        | ”6”, ”sex”     |
| Kvantitet      | Procent    | ”50 %”, ”femtio procent”|
| Kvantitet      | Ordningstal       | ”2:a”, ”andra”     |
| Kvantitet      | Ålder           | "90 dag gammal", "30 år gammal"    |
| Kvantitet      | Valuta      | ”10,99 USD”     |
| Kvantitet      | Dimension     | ”10 miles”, ”40 cm”     |
| Kvantitet      | Temperatur   | ”32 grader”    |
| DateTime      | Ej tillämpligt\*         | ”18.30 den 4 februari 2012”      |
| DateTime      | Date          | ”2 maj 2017”, ”2017-05-02”   |
| DateTime      | Tid          | "8.00", "8:00"  |
| DateTime      | DateRange     | ”2 maj till 5 maj”    |
| DateTime      | TimeRange     | ”18 till 19”     |
| DateTime      | Varaktighet      | ”1 minut och 45 sekunder”   |
| DateTime      | Ange           | ”varje tisdag”     |
| URL           | Ej tillämpligt\*         | "https:\//www.bing.com"    |
| E-post         | Ej tillämpligt\*         | "support@contoso.com" |

\* beroende på inmatade och extraherade entiteter kan vissa entiteter utelämna `SubType`.  Alla entitetstyper som stöds visas endast på engelska, kinesiska (förenklad), franska, tyska och spanska språk.

### <a name="language-support"></a>Stöd för språk

Om du använder enhets länkning på olika språk måste du använda en motsvarande kunskaps bas på varje språk. För entitets länkning i Textanalys innebär detta att varje språk som stöds av `entities` slut punkten länkar till motsvarande Wikipedia-sökkorpus på det språket. Eftersom storleken på Korpus varierar mellan olika språk, förväntas det att återställningen av enhetens länknings funktion också kan variera. Mer information finns i artikeln om [språk support](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) .

## <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: ID, text, språk

Information om språk som stöds för närvarande finns i [den här listan](../text-analytics-supported-languages.md).

Dokumentstorleken måste vara under 5 120 tecken per dokument, och du kan ha upp till 1 000 objekt (ID:n) per samling. Samlingen skickas i begäranstexten. Följande exempel är en illustration av innehåll som du kan skicka till entitetens länknings slut.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Steg 1: Strukturera begäran

Information om begäransdefinitionen finns i [Hur anropar man textanalys API:et](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skicka en **POST**-begäran. Läs API-dokumentationen för denna begäran: [entitets-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Ange HTTP-slutpunkten för extrahering av nyckel fraser genom att antingen använda en Textanalys-resurs på Azure eller en instansierad [textanalys-behållare](text-analytics-how-to-install-containers.md). Du måste inkludera `/text/analytics/v2.1/entities`. Till exempel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Ange ett rubrik för begäran för att inkludera [åtkomst nyckeln](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) för textanalys åtgärder.

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna **API-testkonsolen** i [dokumentationen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) för att strukturera en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analysen utförs när begäran har tagits emot. I avsnittet [data begränsningar](../overview.md#data-limits) i översikt finns information om storlek och antal begär Anden som du kan skicka per minut och sekund.

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.

## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Alla POST-begäranden returnerar ett JSON-formaterat svar med ID:n och identifierade egenskaper.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör så att du kan sortera, söka och hantera data.

Ett exempel på utdata för enhets länkning visas härnäst:

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för entitets länkning med Textanalys i Cognitive Services. Sammanfattningsvis:

+ [Entitets-API: et](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) är tillgängligt för valda språk.
+ JSON-dokument i begär ande texten innehåller ID, text och språkkod.
+ POST-begäran riktas till en `/entities`-slutpunkt med hjälp av en personligt anpassad [åtkomstnyckel och en slutpunkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som är giltig för din prenumeration.
+ Svars utdata, som består av länkade entiteter (inklusive förtroende poäng, förskjutningar och webb Länkar för varje dokument-ID) kan användas i alla program

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API för textanalys](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Översikt över Textanalys](../overview.md)
* [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
* [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712)

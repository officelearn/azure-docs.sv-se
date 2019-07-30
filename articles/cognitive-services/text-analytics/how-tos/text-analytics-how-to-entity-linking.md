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
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: 5a5787504d72259354f9c5eba2e2f4e22402ef0b
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619726"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Använda namngiven enhets igenkänning i Textanalys

Det [namngivna identifierings-API: et](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) för entiteten tar ostrukturerad text och returnerar en lista med disambiguated-entiteter med länkar till mer information på webben (Wikipedia och Bing) för varje JSON-dokument.

## <a name="entity-linking-and-named-entity-recognition"></a>Enhets länkning och identifiering av namngiven entitet

`entities` Slut punkten för textanalys stöder både ner-och entitets igenkänning.

### <a name="entity-linking"></a>Entity Linking
Enhets länkning är möjligheten att identifiera och disambiguate identiteten för en entitet som påträffas i text (till exempel att avgöra om "mars" används som planet eller som den romerska god). Den här processen kräver att det finns en kunskaps bas där kända entiteter är länkade – Wikipedia används som kunskaps bas för `entities` slut punkten textanalys.

### <a name="named-entity-recognition-ner"></a>Igenkänning av namngivna enheter (NER)
Med namngiven enhets igenkänning (NER) kan du identifiera olika entiteter i text och kategorisera dem i fördefinierade klasser. De klasser av entiteter som stöds visas nedan.

I Textanalys [Version 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)är både enhets länkning och namngiven entitets IGENKÄNNING (ner) tillgängliga för flera språk. Mer information finns i artikeln om [språk support](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) .

### <a name="language-support"></a>Stöd för språk

Om du använder enhets länkning på olika språk måste du använda en motsvarande kunskaps bas på varje språk. För entitets länkning i textanalys innebär detta att varje språk som stöds av `entities` slut punkten länkar till motsvarande Wikipedia-sökkorpus på det språket. Eftersom storleken på Korpus varierar mellan olika språk, förväntas det att återställningen av enhetens länknings funktion också kan variera.

## <a name="supported-types-for-named-entity-recognition"></a>Typer som stöds för identifiering av namngiven entitet

| type  | Undertyp | Exempel |
|:-----------   |:------------- |:---------|
| Person        | EJ TILLÄMPLIGT\*         | "Jeff", "Bill Gates"     |
| Location      | EJ TILLÄMPLIGT\*         | ”Redmond, Washington”, ”Paris”  |
| Organisation  | EJ TILLÄMPLIGT\*         | "Microsoft"   |
| Antal      | Tal        | "6", "six"     |
| Antal      | Procent    | ”50 %”, ”femtio procent”|
| Antal      | Ordningstal       | ”2:a”, ”andra”     |
| Antal      | NumberRange   | ”4 till 8”     |
| Antal      | Ålder           | "90 dag gammal", "30 år gammal"    |
| Antal      | Currency      | ”10,99 USD”     |
| Antal      | Dimension     | ”10 miles”, ”40 cm”     |
| Antal      | Temperatur   | ”32 grader”    |
| DateTime      | EJ TILLÄMPLIGT\*         | ”18.30 den 4 februari 2012”      |
| DateTime      | Date          | ”2 maj 2017”, ”2017-05-02”   |
| DateTime      | Time          | "8.00", "8:00"  |
| DateTime      | DateRange     | ”2 maj till 5 maj”    |
| DateTime      | TimeRange     | ”18 till 19”     |
| DateTime      | Duration      | ”1 minut och 45 sekunder”   |
| DateTime      | Ange           | ”varje tisdag”     |
| DateTime      | Tidszon      |    |
| URL           | EJ TILLÄMPLIGT\*         | "https:\//www.Bing.com"    |
| Email         | EJ TILLÄMPLIGT\*         | "support@contoso.com" |

\*Beroende på inmatade och extraherade entiteter kan vissa entiteter `SubType`utelämna.  Alla entitetstyper som stöds visas bara för de engelska, kinesiska, kinesiska, franska, tyska och spanska språken.



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

+ Skicka en **POST**-begäran. Läs API-dokumentationen för denna begäran: [API för entity Linking](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Ange HTTP-slutpunkten för enhets extrahering. Den måste innehålla `/entities`-resursen: `https://[your-region].api.cognitive.microsoft.com/text/analytics/v2.1/entities`

+ Ange en begäransrubrik som inkluderar åtkomstnyckeln för textanalysåtgärder. Mer information finns i [Hitta slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

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
+ POST-begäran riktas till en `/entities`-slutpunkt med hjälp av en personligt anpassad [åtkomstnyckel och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för din prenumeration.
+ Svars utdata, som består av länkade entiteter (inklusive förtroende poäng, förskjutningar och webb Länkar för varje dokument-ID) kan användas i alla program

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API för textanalys](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Översikt över Textanalys](../overview.md)
* [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
* [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712)

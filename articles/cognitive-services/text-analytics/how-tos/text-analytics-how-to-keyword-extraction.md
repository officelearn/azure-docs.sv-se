---
title: Extrahering av nyckelfraser med REST-API:et för textanalys | Microsoft Docs
description: Så här extraherar du nyckelfraser med hjälp av REST-API:et för textanalys från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: 9cb6a40e6ce53a6d82d4129db876a1b24269f166
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997724"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exempel: Så här extraherar du nyckelfraser med textanalys

[API:et för extrahering av nyckelfras](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) utvärderar ostrukturerad text och returnerar en lista med nyckelfraser för varje JSON-dokument. 

Den här funktionen är användbar om du snabbt behöver identifiera de viktigaste punkterna i en samling av dokument. Exempel: med den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”.

För tillfället stöder extraktion av nyckelfraser engelska, tyska, spanska och japanska. Övriga språk är i förhandsversion. Mer information finns i [språk som stöds](../text-analytics-supported-languages.md).

> [!TIP]
> Textanalys ger även en Linux-baserad Docker-containeravbildning för nyckelfrasextrahering, så att du kan [installera och köra Textanalys-containern](text-analytics-how-to-install-containers.md) nära dina data.

## <a name="preparation"></a>Förberedelse

Extrahering av nyckelfraser fungerar bäst när du ger den en större mängd text att arbeta med. Det här är motsatt från känsloanalys som fungerar bättre på mindre mängder text. För att få bästa resultat från båda åtgärder kan du överväga att omstrukturera indata därefter.

Du måste ha JSON-dokument i följande format: id, text, språk

Dokumentstorleken måste vara under 5 120 tecken per dokument, och du kan ha upp till 1 000 objekt (ID:n) per samling. Samlingen skickas i begäranstexten. Följande exempel är en illustration av innehåll som du kan skicka in för extrahering av nyckelfraser.

```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```    
    
## <a name="step-1-structure-the-request"></a>Steg 1: Strukturera begäran

Information om begäransdefinitionen finns i [Hur anropar man textanalys API:et](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skicka en **POST**-begäran. Läs API-dokumentationen för denna begäran: [API för nyckelfraser](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)

+ Ange HTTP-slutpunkten för nyckelfrasextrahering, med hjälp av en Textanalys-resurs på Azure eller en instansierad [Textanalys-container](text-analytics-how-to-install-containers.md). Den måste innehålla `/keyPhrases`-resursen: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`

+ Ange en begäransrubrik som inkluderar åtkomstnyckeln för textanalysåtgärder. Mer information finns i [Hitta slutpunkter och åtkomstnycklar](text-analytics-how-to-access-key.md).

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna **API-testkonsolen** i [dokumentationen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) för att strukturera en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analysen utförs när begäran har tagits emot. Tjänsten accepterar upp till 100 begäranden per minut. Varje begäran kan vara högst 1 MB.

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.

## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Alla POST-begäranden returnerar ett JSON-formaterat svar med ID:n och identifierade egenskaper.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör så att du kan sortera, söka och hantera data.

Ett exempel på utdata för extrahering av nyckelfraser visas här:

```json
    "documents": [
        {
            "keyPhrases": [
                "year",
                "trail",
                "trip",
                "views"
            ],
            "id": "1"
        },
        {
            "keyPhrases": [
                "marked trails",
                "Worst hike",
                "goners"
            ],
            "id": "2"
        },
        {
            "keyPhrases": [
                "trail",
                "small children",
                "family"
            ],
            "id": "3"
        },
        {
            "keyPhrases": [
                "spectacular views",
                "trail",
                "area"
            ],
            "id": "4"
        },
        {
            "keyPhrases": [
                "places",
                "beautiful views",
                "favorite trail"
            ],
            "id": "5"
        }
```

Enligt vad som anges, hittar och ignorerar analysatorn ord som inte behövs och behåller enskilda termer eller fraser som verkar vara subjektet eller objektet i en mening. 

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt känna begrepp och arbetsflöden för extrahering av nyckelfraser med hjälp av textanalys i Cognitive Services. Sammanfattningsvis:

+ [API för extrahering av nyckelfraser](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) är tillgängligt för utvalda språk.
+ JSON-dokument i begärandetexten innehåller ID, text och språkkod.
+ POST-begäran riktas till en `/keyphrases`-slutpunkt med hjälp av en personligt anpassad [åtkomstnyckel och en slutpunkt](text-analytics-how-to-access-key.md) som är giltig för din prenumeration.
+ Svarsutdata, som består av nyckelord och fraser för varje dokument-ID, kan strömmas till valfri app som tar emot JSON, inklusive Excel och Power BI bara för att nämna några.

## <a name="see-also"></a>Se också 

 [Översikt över Textanalys](../overview.md)  
 [Vanliga frågor och svar (FAQ)](../text-analytics-resource-faq.md)</br>
 [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API för textanalys](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6)

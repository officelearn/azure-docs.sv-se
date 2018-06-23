---
title: Vad är Bing enheten sökningen? | Microsoft Docs
description: Lär dig använda Bing enheten Sök API för att söka på webben för entiteter och platser.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0B54E747-61BF-42AA-8788-E25D63F625FC
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: f1b87c07d5b56307fd6b3fc68999598aeab6eb82
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354897"
---
# <a name="what-is-bing-entity-search"></a>Vad är Bing enheten sökningen?

Sök-API Bing enheten skickar en fråga till Bing och hämtar resultat som innehåller entiteter och platser. Det inkluderar plats resultaten hotell, hotell eller andra lokala företag. Bing returnerar platser om frågan anger namnet på det lokala företaget eller ber om en typ av verksamhet (till exempel hotell närheten). Bing returnerar entiteter om frågan anger välkända personer, platser (turistattraktioner, tillstånd, land, etc.) eller saker.

## <a name="suggesting--using-search-terms"></a>Föreslå & med ett sökvillkor

Om du anger en sökruta där användaren anger sitt sökterm kan använda den [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) att förbättra upplevelsen. API: N returnerar föreslagna frågan strängar utifrån partiella söktermer som användartyper.

När användaren anger sitt sökterm, URL koda termen innan du anger den [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) Frågeparametern. Till exempel om användaren anger *Marcus Appel*, ange `q` till *Marcus + Appel* eller *Marcus % 20Appel*.

Om sökordet innehåller fel stavning, Sök svaret innehåller en [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) objekt. Objektet innehåller ursprungliga stavning och korrigerade stavningen Bing används för sökningen.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>Begär entiteter

Ett på exempelbegäran finns i [gör din första begäran](./quick-start.md).

## <a name="the-response"></a>Svaret

Svaret innehåller en [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse) objekt. Om Bing hittar en entitet eller en plats som är relevant, objektet innehåller de `entities` fältet `places` , eller båda. Annars innehåller objektet response inte något av fälten.

Den `entities` fältet är en [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer) objekt som innehåller en lista över [entiteten](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) objekt (finns i `value` fält). Listan kan innehålla en enda företag enhet, flera disambiguation entiteter eller båda. 

Ett företag entiteten är en entitet som Bing tror är den enda entiteten som uppfyller begäran (det finns inga tvetydighet om vilken enhet som uppfyller begäran). Om flera entiteter kan uppfylla begäran innehåller fler än en disambiguation entitet. Om begäran använder film franchisingrätt allmän rubrik, innehåller sannolikt listan disambiguation entiteter. Men om begäran anger ett visst program från franchisen, sannolikt listan innehåller en enda företag entitet.

Enheterna omfattar välkända personligheter singers, aktörer, athletes, modeller, t.ex.; platser och landmärken, till exempel Mount Rainier eller Lincoln Memorial; och saker, till exempel en bananer, goldendoodle, bok eller film rubrik. Den [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) fältet innehåller tips som identifierar den entitetstypen. Till exempel om det är en person, filmer, djur eller finns. En lista över möjliga typer finns [entitetstyper](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Nedan visas ett svar som innehåller en bestämmande och disambiguation entitet.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "http://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

Entiteten innehåller en `name`, `description`, och `image` fält. När du visar dessa fält i användarupplevelsen attributet du dem. Den `contractualRules` fältet innehåller en lista över uppgift som du måste tillämpa. Avtal regeln identifierar det fält som information som gäller för. Information om hur du kopplar information finns i [tillskrivningar](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "http://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

När du visar informationen entitet (namn, beskrivning och bild), måste du också använda URL-Adressen i det `webSearchUrl` fält som du vill länka till Bing-sökningen resultatsida som innehåller entiteten.


Den `places` fältet är en [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) objekt som innehåller en lista över [plats](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) objekt (finns i `value` fält). Listan innehåller en eller flera lokala enheter som uppfyller begäran.

Platser är restaurang, hotell eller lokala företag. Den [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) fältet innehåller tips som identifierar den lokala entitetstypen. Listan innehåller en lista över tips som plats, LocalBusiness, restaurang. Varje efterföljande tips i matrisen begränsas den entitetstypen. En lista över möjliga typer finns [entitetstyper](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```

Lokala medveten entitet som frågar *restaurang närheten* kräver användarens plats att tillhandahålla korrekta resultat. Din begäran bör alltid använda X sökplats och X-MSEdge-ClientIP rubriker för att ange användarens plats. Om Bing tror frågan skulle dra nytta av användarens plats, anger den `askUserForLocation` i [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) till **SANT**. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

En plats omfattar resultatet på plats namn, adress, telefonnummer och URL: en till entitetens webbplats. När du visar informationen om enheten, måste du också använda URL-Adressen i det `webSearchUrl` fält som du vill länka till Bing-sökningen resultatsida som innehåller entiteten.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Du eller någon tredje part å dina vägnar kan inte använda, behålla, lagra, cache, dela, eller distribuera alla data från enheter-API: et för testning, utveckling, utbildning, distribuera eller gör alla icke-Microsoft-tjänster eller funktion.  

## <a name="data-attribution"></a>Fördelning av data

Bing enheten API-svar innehåller information som ägs av tredje part. Du är ansvarig för att se till att du använder är lämpligt, till exempel genom att följa kreativa commons licens användarupplevelsen kan förlita sig på.

Om ett svar eller resultatet innehåller den `contractualRules`, `attributions`, eller `provider` fält, du måste attributet data. Om svaret inte innehåller några av dessa fält krävs ingen information. Om svaret innehåller den `contractualRules` fält och `attributions` och/eller `provider` fält, måste du använda avtal reglerna för att attributet data.

I följande exempel visas en entitet som innehåller en MediaAttribution avtal regel och en avbildning som omfattar en `provider` fältet. MediaAttribution regeln identifierar bilden som mål för regeln, så du kan ignorera bilden `provider` fältet och Använd i stället MediaAttribution regel för att ange tillskrivningar.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Om en regel för avtal innehåller den `targetPropertyName` fältet regeln gäller endast för det aktuella fältet. Annars regeln gäller för det överordnade objektet som innehåller den `contractualRules` fältet.

I följande exempel visas den `LinkAttribution` regeln innehåller den `targetPropertyName` fältet, så att regeln gäller för den `description` fältet. För regler som gäller för specifika fält, måste du inkludera en rad direkt efter måldata som innehåller en hyperlänk till leverantörens webbplats. Till exempel för att attributet beskrivningen innehåller en rad omedelbart efter en beskrivande text som innehåller en hyperlänk till data på leverantörens webbplats i det här fallet att skapa en länk till contoso.com.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Licens-information

Om listan över regler som avtal innehåller en [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) regeln, måste du visa meddelandet på raden omedelbart efter det innehåll som licensvillkoren gäller för. Den `LicenseAttribution` regel använder den `targetPropertyName` fältet för att identifiera den egenskap som licensvillkoren gäller för.

Följande är ett exempel som innehåller en `LicenseAttribution` regel.

![Licens-information](./media/cognitive-services-bing-entities-api/licenseattribution.png)

Licens-meddelande som visas måste innehålla en hyperlänk till den webbplats som innehåller information om licensen. Normalt du namnet på licensen som en hyperlänk. Om meddelandet är till exempel **Text under kopia av SA licens** och kopia av SA är namnet på licensen, du kan göra en kopia av SA en hyperlänk.

### <a name="link-and-text-attribution"></a>Information om länken och text

Den [LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) och [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) regler används vanligtvis för att identifiera leverantören av data. Den `targetPropertyName` fältet identifierar det fält som regeln gäller för.

Innehåller en rad direkt efter det innehåll som gäller för uppgift till (till exempel riktade fältet) för att attributet providers. Raden ska stå klart för att visa att providers är källan för data. Till exempel ”Data från: contoso.com”. För `LinkAttribution` regler, måste du skapa en hyperlänk till leverantörens webbplats.

Följande är ett exempel som innehåller `LinkAttribution` och `TextAttribution` regler.

![Länken text information](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Media-information

Om entiteten innehåller en avbildning och du visa det, måste du ange en klicka igenom länk till leverantörens webbplats. Om entiteten innehåller en [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution) regel, använda regelns URL för att skapa länken klicka igenom. Annars använder den URL som ingår i bildens `provider` fältet för att skapa länken klicka igenom.

Följande är ett exempel som innehåller en bild `provider` fältet och avtal regler. Eftersom exemplet inkluderar avtal regeln kan du ignorera bilden `provider` och Använd den `MediaAttribution` regeln.

![Media-information](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Sökning eller Sök-liknande upplevelse

Precis som med Bing Web Sök API kan Bing enheten Sök-API endast användas på grund av en direkt användarfrågan eller Sök eller på grund av en åtgärd i en app eller en upplevelse som logiskt kan tolkas som en användare sökbegäran. En illustration följer några exempel på godkända sökning eller Sök-liknande upplevelser.

- Användaren anger en fråga direkt i en sökning i en app
- Användaren väljer viss text eller avbildningen och begäranden ”mer information” eller ”mer information”
- Användaren begär en sökning bot om ett visst ämne
- Användaren dwells på ett visst objekt eller en enhet i ett scenario med visual Sök typ

Om du inte är säker på om din upplevelse kan anses vara en sökning-liknande miljö bör du kontrollera med Microsoft.

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

Kom igång snabbt med din första begäran, se [gör din första begäran](./quick-start.md).

Bekanta dig med de [Bing enheten Sök API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) referens. Referensen innehåller rubriker och frågeparametrar som används för att begära sökresultat. Den omfattar också definitioner av objekt som svar. 

För att förbättra användarupplevelsen rutan Sök finns [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md). När användaren sina frågeterm, kan du anropa denna API för att få relevanta sökord som används av andra.

Se till att läsa [Bing användas och visa krav](./use-display-requirements.md) så att du inte delar någon av reglerna om hur du använder sökresultatet.
---
title: Vad är entitetssökning i Bing? | Microsoft Docs
description: Lär dig hur du använder den Entitetssökning i Bing för att söka på webben för entiteter och platser.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 0B54E747-61BF-42AA-8788-E25D63F625FC
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2016
ms.author: scottwhi
ms.openlocfilehash: 275430bc6ee8f935978243e61f68713974648189
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008118"
---
# <a name="what-is-bing-entity-search"></a>Vad är entitetssökning i Bing?

Bing-Entitetssökning skickar en sökfråga till Bing och hämtar resultat som innehåller entiteter och platser. Plats resultat omfattar restauranger, hotell eller andra lokala företag. Bing returnerar platser om frågan anger namnet på det lokala företaget eller begär en typ av verksamhet (exempelvis restauranger i närheten). Bing returnerar entiteter om frågan anger välkända personer, platser (turistattraktioner, tillstånd, länder/regioner, osv.) eller saker.

## <a name="suggesting--using-search-terms"></a>Föreslå och använda söktermer

Om du tillhandahåller en sökruta där användaren anger sin sökterm bör du använda [API för automatiska förslag i Bing ](../bing-autosuggest/get-suggested-search-terms.md) för att ge bättre funktioner. API:t returnerar föreslagna frågesträngar baserat på partiella söktermer som användaren skriver in.

När användaren har angett sin sökterm kodar URL:en termen innan den ställer in [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) frågeparametern. Exempel: om användaren anger *Marcus Appel*anger `q` till *Marcus + Appel* eller *Marcus % 20Appel*.

Om söktermen som innehåller en felstavning, Sök svaret innehåller en [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) objekt. Objektet visas den ursprungliga stavningen och korrigerade stavningen som Bing används för sökningen.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="requesting-entities"></a>Begär entiteter

En exempelbegäran, se [gör din första begäran](./quick-start.md).

## <a name="the-response"></a>Svaret

Svaret innehåller en [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse) objekt. Om Bing hittar en entitet eller plats som är relevant, objektet innehåller de `entities` fältet `places` fält eller båda. I annat fall innehåller objektet response inte något av fälten.
> [!NOTE]
> Entiteten svar stöder flera marknader, men svaret platser stöder endast oss företag platser. 

Den `entities` fältet är en [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entityanswer) objekt som innehåller en lista över [entitet](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity) objekt (se den `value` fältet). Listan kan innehålla en enda dominerande enhet, flera tvetydigheter entiteter eller båda. 

En dominerande enhet är en entitet som Bing tror är den enda entitet som uppfyller begäran (det finns inga tveksamheter om vilka entitet uppfyller begäran). Om flera entiteter kan utföra den begärda åtgärden, innehåller listan mer än en tvetydigheter entitet. Om denna begäran använder generisk rubrik för en film franchise, innehåller sannolikt listan tvetydigheter entiteter. Men om begäran anger ett visst program från franchisen, sannolikt listan innehåller en enda dominerande enhet.

Entiteter innehåller välkända personligheter singers, aktörer, tävlande, modeller, t.ex.; platser och landmärken, till exempel montera Rainier eller Lincoln Memorial; och sådant som en rubrik för bananer, goldendoodle, bok eller film. Den [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) fältet innehåller tips som identifierar den enhetstyp. Exempel: om det är en person, film, djur eller finns. En lista över möjliga typer finns i [entitetstyper](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Nedan visas ett svar som innehåller en dominerande ställning och tvetydigheter entitet.

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

Entiteten innehåller en `name`, `description`, och `image` fält. När du visar de här fälten i din användarupplevelse, måste du attributet dem. Den `contractualRules` fältet innehåller en lista över upphovsrätter som du måste tillämpa. Avtalsenliga regeln identifierar det fält som information som gäller för. Information om hur du tillämpar attribution finns i [Attribution](#data-attribution).

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

När du visar entitetsinformation (namn, beskrivning och avbildningen), måste du också använda URL: en i den `webSearchUrl` fält för att länka till Bing search resultatsida som innehåller entiteten.


Den `places` fältet är en [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#localentityanswer) objekt som innehåller en lista över [plats](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#place) objekt (se den `value` fältet). Listan innehåller en eller flera lokala enheter som uppfyller begäran.

Platser är restaurang, hotell eller lokala företag. Den [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entitypresentationinfo) fältet innehåller tips som identifierar den lokala entitetstypen. Listan innehåller en lista över tips som plats, LocalBusiness, restaurang. Varje efterföljande tipset i matrisen begränsar entitetens typ. En lista över möjliga typer finns i [entitetstyper](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Entiteten svar stöder flera marknader, men svaret platser stöder endast oss företag platser. 

Lokala medveten entitet frågor som *restaurang närheten* kräver användarens plats att tillhandahålla korrekta resultat. Dina begäranden bör alltid använda X sökplats och rubriker för X-MSEdge-ClientIP för att ange användarens plats. Om Bing tror frågan skulle med fördel användarens plats, anger den `askUserForLocation` i [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#querycontext) till **SANT**. 

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

Resultatet för en plats innehåller platsen namn, adress, telefonnummer och URL: en till dess webbplats. När du visar entitetsinformationen, måste du också använda URL: en i den `webSearchUrl` fält för att länka till Bing search resultatsida som innehåller entiteten.

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
> Du eller en tredje part för din räkning kan inte använda, behålla, lagra, cache, delar, eller distribuera alla data från API för entiteter för testning, utveckling, utbildning, distribuerar eller gör tillgänglig någon icke-Microsoft-tjänst eller funktion.  

## <a name="data-attribution"></a>Data attribution

I Bing svar innehåller information som ägs av tredje part. Du ansvarar för att se till att din användning är lämpligt, till exempel uppfyller en creative commons-licens användarupplevelsen kan förlita sig på.

Om ett svar eller ett resultat som innehåller den `contractualRules`, `attributions`, eller `provider` fält, du måste attributet data. Om svaret inte innehåller några av de här fälten, krävs ingen information. Om svaret innehåller den `contractualRules` fält och `attributions` och/eller `provider` fält, måste du använda avtalsenliga reglerna för att attributet data.

I följande exempel visas en entitet som innehåller en MediaAttribution avtalsenliga regel och en avbildning som innehåller en `provider` fält. MediaAttribution regeln identifierar bilden som mål för regeln, så att du vill ignorera en bilds `provider` fältet och i stället använda MediaAttribution regeln för att ge information.  

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

Om en avtalsenliga regel innehåller den `targetPropertyName` fältet regeln gäller endast för fältet riktade. I annat fall regeln gäller för det överordnade objektet som innehåller den `contractualRules` fält.

I följande exempel visas den `LinkAttribution` regeln innehåller den `targetPropertyName` fältet har regeln gäller för den `description` fält. Du måste inkludera en rad direkt efter den aktuella data som innehåller en hyperlänk till leverantörens webbplats för regler som gäller för specifika fält. Till exempel för att attributet beskrivning, inkludera en rad omedelbart efter en beskrivande text som innehåller en hyperlänk till data på leverantörens webbplats, i det här fallet att skapa en länk till contoso.com.

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

### <a name="license-attribution"></a>Licens attribution

Om listan över regler som avtalsenliga innehåller en [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) regeln, måste du visa meddelandet på raden direkt efter det innehåll som licensen gäller för. Den `LicenseAttribution` regel använder den `targetPropertyName` fält för att identifiera den egenskap som licensen gäller för.

Följande visar ett exempel som innehåller en `LicenseAttribution` regel.

![Licens attribution](./media/cognitive-services-bing-entities-api/licenseattribution.png)

Licens-meddelande som du visar måste innehålla en hyperlänk till webbplatsen som innehåller information om licensen. Normalt kan göra du namnet på licensen som en hyperlänk. Om meddelandet är till exempel **Text under kopia av SA licens** och kopia av SA är namnet på licensen, du kan göra en kopia av SA en hyperlänk.

### <a name="link-and-text-attribution"></a>Länken och text attribution

Den [LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#linkattribution) och [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#textattribution) regler används vanligtvis för att identifiera leverantören av data. Den `targetPropertyName` fältet identifierar fältet som regeln gäller för.

Inkludera en rad som omedelbart efter det innehåll som gäller för uppgift till (till exempel riktade fältet) för att attributet providers. Raden ska stå tydligt för att visa att providers är källan för data. Till exempel ”Data från: contoso.com”. För `LinkAttribution` regler, måste du skapa en hyperlänk till leverantörens webbplats.

Följande visar ett exempel som innehåller `LinkAttribution` och `TextAttribution` regler.

![Länken text attribution](./media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Media attribution

Om entiteten tas en bild och du visar det, måste du ange en klicka igenom länk till leverantörens webbplats. Om entiteten innehåller en [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mediaattribution) regel, använder regelns URL för att skapa klicka igenom länk. Annars kan du använda URL: en som ingår i en bilds `provider` fält som du vill skapa klicka igenom länk.

Följande visar ett exempel som innehåller en bild `provider` fält och avtalsenliga regler. Eftersom exemplet inkluderar avtalsenliga regeln kan du ignorera avbildningens `provider` och Använd den `MediaAttribution` regeln.

![Media attribution](./media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Sök i eller Sök-liknande upplevelse

Precis som med API för webbsökning i Bing, kan Bing-Entitetssökning bara användas på grund av en direkt användarfråga eller Sök eller på grund av en åtgärd i en app eller en upplevelse som logiskt kan tolkas som en användares sökbegäran. För tydlighetens skull här följer några exempel på godkända Sök- eller Sök-liknande upplevelser.

- Användaren anger en fråga direkt i en sökruta i en app
- Användaren väljer viss text eller avbildning och begäranden ”mer information” eller ”mer information”
- Användaren begär en sökning-robot om ett visst ämne
- Användaren dwells på ett visst objekt eller en enhet i ett scenario för typ av visuell sökning

Om du inte är säker på om din upplevelse kan betraktas som en sökning-liknande upplevelse, rekommenderar vi att du kontrollerar med Microsoft.

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

Kom igång snabbt med din första begäran, se [gör din första begäran](./quick-start.md).

Bekanta dig med den [Entitetssökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference) referens. Referensen innehåller rubriker och frågeparametrar som används för att begära sökresultat. Den omfattar även definitioner av svarsobjekten. 

För att förbättra användarfunktionen för sökrutan läser du [API för automatiska förslag i Bing](../bing-autosuggest/get-suggested-search-terms.md). När användaren anger sina frågetermer kan du anropa det här API:t för att få relevanta frågetermer som har använts av andra användare.

Se till att läsa [Bing Use and Display Requirements](./use-display-requirements.md) (Krav för användning och visning i Bing) så att du inte bryter mot någon av reglerna om användning av sökresultat.
---
title: Granska utdata för Azure Video indexerare som produceras av v2 API | Microsoft Docs
description: Det här avsnittet behandlar Video indexeraren utdata som genereras av v2 API.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cfowler
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 6c410b054ba98961d15a4db0ff7eaa2804245cb0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355872"
---
# <a name="examine-the-video-indexer-output-produced-by-v2-api"></a>Granska Video indexeraren utdata som genereras av v2 API

> [!Note]
> API: er för Video indexeraren V1 är nu föråldrade och kommer att tas bort på 1 augusti 2018. Du bör börja använda API: er för Video indexeraren v2 för att undvika avbrott.
>
> För att utveckla med Video indexeraren v2 API: er finns i anvisningarna [här](https://api-portal.videoindexer.ai/). 

När du anropar den **hämta Video Index** API och response-status är OK får du en detaljerad JSON-utdata som svar-innehåll. Innehållet i JSON innehåller information om de angivna video insikter. Insikter som inkluderar dimensioner som: betyg, ocrs, ytor, ämnen, block och så vidare. Dimensionerna har instanser av tidsintervall som visar när varje dimension visades i videon.  

Du kan också visuellt undersöka videons sammanfattade insikter genom att trycka på **spela upp** video i Video indexeraren portal-knappen. Mer information finns i [visa och redigera video insikter](video-indexer-view-edit.md).

![Insikter](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Den här artikeln går igenom innehållet i JSON som returneras av den **hämta Video Index** API. 

> [!NOTE]
> Förfallodatum för alla åtkomst-token i videon indexerare är en timme.


## <a name="root-elements"></a>Rotelement

|Namn|Beskrivning|
|---|---|
|accountId|Den spelningslista VI konto-ID.|
|id|Den spelningslista-ID.|
|namn|Namn på den spelningslista.|
|description|Beskrivning av den spelningslista.|
|Användarnamn|Namnet på användaren som skapade listan.|
|skapad|Skapandetid för den spelningslista.|
|privacyMode|Den spelningslista sekretess (privata och offentliga).|
|state|Spelningslistan (överförda, bearbetning, bearbetade, misslyckades, i karantän).|
|isOwned|Om listan har skapats med den aktuella användaren.|
|isEditable|Om den aktuella användaren har behörighet att redigera spelningslistan.|
|isBase|Om listan är en grundläggande spelningslista (en video) eller en spellista som gjorts av andra videor (Härlett).|
|durationInSeconds|Den totala varaktigheten för listan.|
|summarizedInsights|Innehåller en [summarizedInsights](#summarizedinsights).
|videor|En lista över [videor](#videos) konstruera listan.<br/>Om den här spelningslista konstruerade av tidsintervall för andra videor (härledda), videor i den här listan innehåller endast data från de inkluderade tidsintervall.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

Det här avsnittet visas en sammanfattning av insikter.

|Attribut | Beskrivning|
|---|---|
|namn|Namnet på videon. Till exempel Azure-Monitor.|
|shortId|ID för videon. Till exempel 63c6d532ff.|
|privacyMode|Din analys på detaljnivå kan ha en av följande lägen: **privata**, **offentliga**. **Offentliga** -videon är synlig för alla i ditt konto och vem som helst som har en länk till videon. **Privata** -videon är synlig för alla i ditt konto.|
|varaktighet|Innehåller en varaktighet som beskriver den tid som en förklaring inträffade. Det är varaktighet i sekunder.|
|thumbnailUrl|Miniatyr av video fullständig URL. Till exempel ”https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO.”. Observera att om videon är privat kan URL: en innehåller en åtkomst-token med en timme. Efter en timme URL: en kommer inte längre och du behöver hämta uppdelning igen med en ny url i den eller anropa GetAccessToken för att få en ny åtkomsttoken och skapa hela webbadressen manuellt (”https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken]').|
|ytor|Kan innehålla en eller flera ytor. Mer information finns i [ytor](#faces).|
|Avsnitt om|Kan innehålla en eller flera artiklar. Mer information finns i [avsnitt](#topics).|
|våra|Kan innehålla en eller flera våra. Mer information finns i [våra](#sentiments).|
|audioEffects| Kan innehålla en eller flera audioEffects. Mer information finns i [audioEffects](#audioeffects).|
|varumärken| Kan innehålla noll eller flera varumärken. Mer information finns i [märken](#brands).|
|Statistik | Mer information finns i [statistik](#statistics).|

### <a name="statistics"></a>Statistik

|Namn|Beskrivning|
|---|---|
|CorrespondenceCount|Antal svaren i videon.|
|WordCount|Antal ord per högtalaren.|
|SpeakerNumberOfFragments|Mängden fragment högtalaren har i en video.|
|SpeakerLongestMonolog|På högtalaren längsta monolog. Om högtalaren har silences inuti monolog ingår den. Tystnad i början och slutet av monolog tas bort.| 
|SpeakerTalkToListenRatio|Beräkningen baseras på tidsåtgång på högtalaren monolog (utan tystnad mellan) dividerat med den totala tiden av videon. Tiden avrundas till tredje decimaltecknet.|

## <a name="videos"></a>videor

|Namn|Beskrivning|
|---|---|
|accountId|Videons VI konto-ID.|
|id|Den video-ID.|
|namn|Namnet på video.
|state|Videons tillstånd (överförda, bearbetning, bearbetade, misslyckades, i karantän).|
|processingProgress|Status för bearbetning under bearbetningen (till exempel 20%).|
|failureCode|Felkod om kunde inte bearbeta (till exempel UnsupportedFileType).|
|failureMessage|Felmeddelande om kunde inte bearbeta.|
|externalId|Videons externa id (om det angetts av användaren).|
|externalUrl|Videons extern url (om det angetts av användaren).|
|metadata|Videons externa metadata (om det angetts av användaren).|
|isAdult|Om videon var manuellt granskat och identifieras som en vuxen video.|
|insikter|Insights-objekt.|
|thumbnailUrl|Miniatyr av video fullständig URL. Till exempel ”https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO.”. Observera att om videon är privat kan URL: en innehåller en åtkomst-token med en timme. Efter en timme URL: en kommer inte längre och du behöver hämta uppdelning igen med en ny url i den eller anropa GetAccessToken för att få en ny åtkomsttoken och skapa hela webbadressen manuellt (”https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken]').|
|publishedUrl|En url att strömma videon.|
|publishedUrlProxy|En url för direktuppspelning av video från (för Apple-enheter).|
|viewToken|En kort livslängd visa token för direktuppspelning av video.|
|sourceLanguage|Videons källa språk.|
|språk|Videons språkspecifika (translation).|
|indexingPreset|Den förinställning som används för att indexera videon.|
|streamingPreset|Den förinställning som används för att publicera videon.|
|linguisticModelId|Används för att transkribera videon CRI modellen.|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>insikter

Insikter som är en uppsättning dimensioner (exempelvis betyg rader, ytor, varumärken, etc.), där varje dimension är en lista över unika element (till exempel face1, bild2, -bild3), och varje element har sin egen metadata och en lista över instanser (som är tidsintervall med ytterligare valfria metadata).

En yta kan ha ett ID, ett namn, en miniatyrbild, andra metadata och en lista med dess temporal instanser (till exempel: 00:00:05 – 00:00:10, 00:01:00 - 00:02:30 och 00:41:21 – 00:41:49.) Varje temporal instans kan ha ytterligare metadata. Till exempel samordnar de står inför rektangel (20,230,60,60).

|Version|Code-versionen|
|---|---|
|sourceLanguage|Videons källa språk (förutsatt att ett master språk). I form av en [BCP 47](https://tools.ietf.org/html/bcp47) sträng.|
|språk|Insikter språk (översättas från käll-språk). I form av en [BCP 47](https://tools.ietf.org/html/bcp47) sträng.|
|betyg|Den [betyg](#transcript) dimension.|
|OCR|Den [ocr](#ocr) dimension.|
|Nyckelord|Den [nyckelord](#keywords) dimension.|
|ytor|Den [ytor](#faces) dimension.|
|Etiketter|Den [etiketter](#labels) dimension.|
|bilderna|Den [bilderna](#shots) dimension.|
|varumärken|Den [märken](#brands) dimension.|
|audioEffects|Den [audioEffects](#audioEffects) dimension.|
|våra|Den [våra](#sentiments) dimension.|
|visualContentModeration|Den [visualContentModeration](#visualcontentmoderation) dimension.|
|textualConentModeration|Den [textualConentModeration](#textualconentmoderation) dimension.|

Exempel:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualConentModeration": ...
}
```

#### <a name="transcript"></a>betyg

|Namn|Beskrivning|
|---|---|
|id|Rad-ID.|
|text|Betyg sig själv.|
|språk|Språket som betyg. Avsett att stödja betyg där varje rad kan ha ett annat språk.|
|instanser|En lista över tidsintervall där den här raden visades. Om-instansen är betyg, får endast 1 instans.|

Exempel:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

#### <a name="ocr"></a>OCR

|Namn|Beskrivning|
|---|---|
|id|OCR rad-ID.|
|text|Texten som OCR.|
|förtroende|Recognition förtroende.|
|språk|OCR-språk.|
|instanser|En lista över tidsintervall där den här OCR fanns (samma OCR kan visas flera gånger).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>Nyckelord

|Namn|Beskrivning|
|---|---|
|id|Nyckelordet id.|
|text|Nyckelordstexten som.|
|förtroende|Det nyckelordet recognition förtroende.|
|språk|Nyckelordet språk (vid översättning).|
|instanser|En lista över tidsintervall där det här nyckelordet fanns (ett nyckelord kan visas flera gånger).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 

```

#### <a name="faces"></a>ytor

|Namn|Beskrivning|
|---|---|
|id|Ansikts-ID.|
|namn|Ansikts-namn. Det kan vara ”okänt #0”, en identifierade ryktbarhet eller en kund utbildade person.|
|förtroende|Ansikts identifiering förtroende.|
|description|Om det är en ryktbarhet dess beskrivning kan vara ”: Satya Nadella föddes med...”. |
|thumbnalId|Id för på miniatyrbilden för den sida.|
|knownPersonId|Om det är en känd person, ett internt ID.|
|ReferenceId|Om det är en Bing-ryktbarhet dess Bing-ID.|
|referenceType|För närvarande bara Bing.|
|rubrik|Om det finns en ryktbarhet rubriken (till exempel ”Microsofts VD”).|
|imageUrl|Om det finns en ryktbarhet dess bild-url.|
|instanser|Dessa är instanser av där de står inför fanns i det angivna tidsintervallet. Varje instans har också en thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

#### <a name="labels"></a>Etiketter

|Namn|Beskrivning|
|---|---|
|id|Etikett-ID.|
|namn|Etikettnamn (till exempel ”dator”, ”TV').|
|språk|Etiketten namn språk (vid översättning). BCP-47|
|instanser|En lista över tidsintervall där den här etiketten fanns (en etikett kan visas flera gånger). Varje instans har ett förtroende-fält. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

#### <a name="shots"></a>bilderna

|Namn|Beskrivning|
|---|---|
|id|Som ID.|
|Nyckelrutor|En lista över viktiga ramar inom det bild (var och en har ett ID och en lista över instanser tidsintervall). Viktiga ramar instanser har ett thumbnailId fält med den keyFrame miniatyr-ID.|
|instanser|En lista över tidsintervall för den här som visar (bilderna har endast 1 instans).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
          "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
       "thumbnailId": "00000000-0000-0000-0000-000000000000",   
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
          "thumbnailId": "00000000-0000-0000-0000-000000000000",        
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

#### <a name="brands"></a>varumärken

Företag och produktens namn upptäcktes i tal till text betyg och/eller Video OCR. Detta inkluderar inte visual igenkänning av varumärken och logotyper identifiering.

|Namn|Beskrivning|
|---|---|
|id|Varumärken-ID.|
|namn|Märken namn.|
|wikiId | Suffix för varumärken wikipedia url. Till exempel ”Target_Corporation” är suffixet för [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
|wikiUrl | Varumärke har Wikipedia url, om det finns. Till exempel [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description|Märken beskrivning.|
|tags|En lista över fördefinierade taggar som var associerade med detta.|
|förtroende|Förtroende-värdet för Video indexeraren varumärken detektorn (0-1).|
|instanser|En lista över tidsintervall för detta. Varje instans har en brandType som anger om detta fanns i betyg eller OCR.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "wikiId": "Microsoft",
    "wikiUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="audioeffects"></a>audioEffects

|Namn|Beskrivning|
|---|---|
|id|Ljud effekt-ID.|
|typ|Ljud Effekttyp (till exempel applåder tal, tystnad).|
|instanser|En lista över tidsintervall där detta ljud visades.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>våra

Våra samman efter deras sentimentType (positiv/Neutral/negativ). Till exempel 0 0.1, 0,1 0,2.

|Namn|Beskrivning|
|---|---|
|id|Sentiment-ID.|
|Medel |Medelvärdet av alla resultat för alla instanser av den typen sentiment - Neutral/positiv/negativ|
|instanser|En lista över tidsintervall där den här sentiment visades.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

VisualContentModeration blocket innehåller tidsintervall som Video indexeraren hittas potentiellt ha innehåll för vuxna. Om visualContentModeration är tom, finns det inget innehåll för vuxna som identifierades.

Videor som hittas för innehåll för vuxna eller dyr kan vara tillgängliga för privata vyn. Användare har möjlighet att skicka en begäran om en mänsklig granskning av innehållet, där fallet attributet IsAdult innehåller resultatet av mänsklig granska.

|Namn|Beskrivning|
|---|---|
|id|Den visuella innehåll avbrottsmoderering-ID.|
|adultScore|Vuxna poäng (från innehåll kontrollant).|
|racyScore|Dyr poäng (från innehåll avbrottsmoderering).|
|instanser|En lista över tidsintervall där den här visuella innehåll avbrottsmoderering visades.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualconentmoderation"></a>textualConentModeration 

|Namn|Beskrivning|
|---|---|
|id|Textrepresentation innehåll avbrottsmoderering-ID.|
|bannedWordsCount |Antal förbjudna ord.|
|bannedWordsRatio |Förhållandet mellan från totala antalet ord.|


## <a name="next-steps"></a>Nästa steg

[Video indexeraren API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

Information om hur du bäddar in widgetar i ditt program finns [bädda in Video indexeraren widgetar i dina program](video-indexer-embed-widgets.md). 


---
title: Granska Azure Video Indexer-utdata som genereras av v2 API | Microsoft Docs
description: Det här avsnittet undersöker Video Indexer-utdata som genereras av v2 API.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cfowler
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 43cc02417fad8a2fa46bd309235951393cd55b8a
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "41987973"
---
# <a name="examine-the-video-indexer-output-produced-by-v2-api"></a>Granska Video Indexer-utdata som genereras av v2 API

> [!Note]
> Video Indexer V1 API upphörde den 1 augusti 2018. Nu bör du använda Video Indexer v2 API: et. <br/>Om du vill utveckla med API: er för Video Indexer-v2, se instruktionerna [här](https://api-portal.videoindexer.ai/). 

När du anropar den **hämta Video Index** API och svarsstatusen är OK, du får detaljerad JSON-utdata som svarsinnehållet. JSON-innehållet innehåller information om de angivna videoinsikter. Insikterna som inkluderar dimensioner som: avskrifter, ocrs, ansikten, ämnen, block, osv. Dimensionerna har instanser av tidsintervall som visar när varje dimension som visades i videon.  

Du kan också visuellt undersöka videons sammanfattade insikter genom att trycka på den **spela upp** knappen för videon i Video Indexer-portalen. Mer information finns i [visa och redigera videoinsikter](video-indexer-view-edit.md).

![Insikter](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Den här artikeln undersöker JSON-innehåll som returneras av den **hämta Video Index** API. 

> [!NOTE]
> Förfallodatum för åtkomsttoken i Video Indexer är en timme.


## <a name="root-elements"></a>Rotelement

|Namn|Beskrivning|
|---|---|
|accountId|Till spellistan VI konto-ID.|
|id|Till spellistan-ID.|
|namn|Till spellistan namn.|
|beskrivning|Till spellistan beskrivning.|
|Användarnamn|Namnet på användaren som skapade listan.|
|skapad|Skapandetid för till spellistan.|
|privacyMode|Till spellistan sekretess-läge (privata/offentliga).|
|state|Till spellistan (överförda, bearbetning, bearbetade, misslyckades, har satts i karantän).|
|isOwned|Anger om listan har skapats av den aktuella användaren.|
|isEditable|Anger om den aktuella användaren har behörighet att redigera spelningslistan.|
|isBase|Anger om listan är en grundläggande spelningslista (en video) eller en spellista som består av andra videor (härledd).|
|durationInSeconds|Den totala varaktigheten för listan.|
|summarizedInsights|Innehåller en [summarizedInsights](#summarizedinsights).
|videor|En lista över [videor](#videos) konstruera spelningslistan.<br/>Om du skapar den här spelningslista av tidsintervall för andra videoklipp (härledd), videor i den här listan innehåller endast data från de inkluderade tidsintervall.|

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

Det här avsnittet visas en sammanfattning av insikterna.

|Attribut | Beskrivning|
|---|---|
|namn|Namnet på videon. Till exempel Azure Monitor.|
|shortId|ID för videon. Till exempel 63c6d532ff.|
|privacyMode|Din analys på detaljnivå kan ha ett av följande lägen: **privata**, **offentliga**. **Offentliga** -videon är synlig för alla i ditt konto och vem som helst som har en länk till videon. **Privata** -videon är synlig för alla i ditt konto.|
|varaktighet|Innehåller en varaktighet som beskriver den tid som en insikt inträffade. Varaktighet är i sekunder.|
|thumbnailVideoId|ID för videon som miniatyren togs.
|thumbnailId|Videons miniatyr-ID. Att hämta det faktiska miniatyr anropet Get-miniatyr (https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-thumbnail) och skickar den thumbnailVideoId och thumbnailId.|
|ansikten|Kan innehålla noll eller flera ansikten. Mer information finns i [ansikten](#faces).|
|nyckelord|Kan innehålla noll eller flera nyckelord. Mer information finns i [nyckelord](#keywords).|
|sentiment|Kan innehålla noll eller flera sentiment. Mer information finns i [sentiment](#sentiments).|
|audioEffects| Kan innehålla noll eller flera audioEffects. Mer information finns i [audioEffects](#audioeffects).|
|etiketter| Kan innehålla noll eller fler etiketter. Detaljerad information finns i [etiketter](#labels).|
|varumärken| Kan innehålla noll eller flera varumärken. Mer information finns i [varumärken](#brands).|
|statistik | Mer information finns i [statistik](#statistics).|

## <a name="videos"></a>videor

|Namn|Beskrivning|
|---|---|
|accountId|Videons VI konto-ID.|
|id|Videons-ID.|
|namn|Videons namn.
|state|Videons tillstånd (överförda, bearbetning, bearbetade, misslyckades, har satts i karantän).|
|processingProgress|Bearbetningsförlopp under bearbetningen (t.ex, 20%).|
|failureCode|Felkod om kunde inte bearbeta (till exempel UnsupportedFileType).|
|failureMessage|Felmeddelande om det gick inte att bearbeta.|
|externalId|Videons externt ID (om det angetts av användaren).|
|externalUrl|Videons extern url (om det angetts av användaren).|
|metadata|Videons externa metadata (om det angetts av användaren).|
|isAdult|Anger om videon manuellt har granskat och identifieras som en video som är olämpligt för barn.|
|insikter|Insights-objekt. Mer information finns i [insights](#insights).|
|thumbnailId|Videons miniatyr-ID. Att hämta det faktiska miniatyr anropet Get-miniatyr (https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-thumbnail) och skickar den video-ID och thumbnailId.|
|publishedUrl|En url som används för att strömma videon.|
|publishedUrlProxy|En url som används för att strömma video från (för Apple-enheter).|
|viewToken|En kort livslängd visa token för strömning av videon.|
|sourceLanguage|Videons källspråk.|
|Språk|Videons språkspecifika (translation).|
|indexingPreset|Den förinställning som används för att indexera videon.|
|streamingPreset|Den förinställning som används för att publicera videon.|
|linguisticModelId|CRIS modellen används för att transkribera videon.|
|statistik | Mer information finns i [statistik](#statistics).|

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

Insikterna är en uppsättning dimensioner (till exempel avskrift rader, ansikten, varumärken, osv.), där varje dimension är en lista över unika element (till exempel face1, bild2, -bild3), och varje element har sin egen metadata och en lista över dess instanser (som är tidsintervall med ytterligare valfria metadata).

Ett ansikte kan ha ett ID, ett namn, en miniatyrbild, andra metadata och en lista över dess temporala instanser (till exempel: 00:00:05 – 00:00:10, 00:01:00 - 00:02:30 och 00:41:21 – 00:41:49.) Varje temporala instans kan ha ytterligare metadata. Till exempel samordnar den ansiktsrektangeln (20,230,60,60).

|Version|Code-versionen|
|---|---|
|sourceLanguage|Videons källspråk (förutsatt att ett master språk). I form av en [BCP-47](https://tools.ietf.org/html/bcp47) sträng.|
|Språk|Insights-språk (översätts från källspråk). I form av en [BCP-47](https://tools.ietf.org/html/bcp47) sträng.|
|avskrift|Den [avskrift](#transcript) dimension.|
|OCR|Den [ocr](#ocr) dimension.|
|nyckelord|Den [nyckelord](#keywords) dimension.|
|block|Kan innehålla en eller flera [block](#blocks)|
|ansikten|Den [ansikten](#faces) dimension.|
|etiketter|Den [etiketter](#labels) dimension.|
|skärmbilder|Den [skärmbilder](#shots) dimension.|
|varumärken|Den [varumärken](#brands) dimension.|
|audioEffects|Den [audioEffects](#audioEffects) dimension.|
|sentiment|Den [sentiment](#sentiments) dimension.|
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

#### <a name="blocks"></a>block

Attribut | Beskrivning
---|---
id|ID för blocket.|
instanser|En lista över tidsintervall i det här blocket.|

#### <a name="transcript"></a>avskrift

|Namn|Beskrivning|
|---|---|
|id|Rad-ID.|
|text|Avskriften.|
|Språk|Avskriften-språk. Avser att stödja avskrift där varje rad kan ha ett annat språk.|
|instanser|En lista över tidsintervall där den här raden visas. Om-instansen är avskrift, har endast 1 instans.|

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
|text|OCR-text.|
|förtroende|Igenkänning av förtroende.|
|Språk|OCR-språk.|
|instanser|En lista över tidsintervall där den här OCR visades (samma OCR kan visas flera gånger).|

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

#### <a name="keywords"></a>nyckelord

|Namn|Beskrivning|
|---|---|
|id|Nyckelordet-ID.|
|text|Nyckelordstexten.|
|förtroende|Den nyckelordet igenkänning av förtroende.|
|Språk|Nyckelordet språk (vid översättning).|
|instanser|En lista över tidsintervall där det här nyckelordet visas (ett nyckelord kan visas flera gånger).|

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

#### <a name="faces"></a>ansikten

|Namn|Beskrivning|
|---|---|
|id|Ansikts-ID.|
|namn|Ansikts-namnet. Det kan vara ”okänt #0”, en identifierade kändisar eller en kund utbildad person.|
|förtroende|Face ID förtroende.|
|beskrivning|En beskrivning av kändisar. |
|thumbnalId|ID för miniatyrbilden för den sida.|
|knownPersonId|Om det är en känd person, dess interna ID.|
|Tjänsten|Om det är en Bing kändisar, dess Bing-ID.|
|referenceType|För närvarande bara Bing.|
|rubrik|Om det är en kändisar, dess rubrik (till exempel ”Microsofts VD”).|
|imageUrl|Om det är en kändisar, dess bild-url.|
|instanser|Dessa är instanser av där ansiktet visas i det angivna tidsintervallet. Varje instans har också en thumbnailsId. |

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

#### <a name="labels"></a>etiketter

|Namn|Beskrivning|
|---|---|
|id|Etikett-ID.|
|namn|Etikettnamn (till exempel ”dator”, ”TV”).|
|Språk|Etiketten namn språk (vid översättning). BCP-47|
|instanser|En lista över tidsintervall där den här etiketten visas (en etikett kan visas flera gånger). Varje instans har ett förtroende-fält. |


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

#### <a name="shots"></a>skärmbilder

|Namn|Beskrivning|
|---|---|
|id|Som ID.|
|Nyckelrutor|En lista över viktiga ramarna i på nedan (var och en har ett ID och en lista över instanser tidsintervall). Viktiga bildrutor instanser har ett thumbnailId fält med den bildrutan miniatyr-ID.|
|instanser|En lista över tidsintervall för den här som visar (skärmbilder har endast 1 instans).|

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

Företag och produkten namn har identifierats i tal till textavskrift och/eller Video OCR. Detta inkluderar inte visuell igenkänning av varumärken eller logotyp identifiering.

|Namn|Beskrivning|
|---|---|
|id|Varumärke-ID.|
|namn|Namnet varumärken.|
|Tjänsten | Suffix för varumärke wikipedias url. Till exempel ”Target_Corporation” är suffixet för [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | Varumärket är Wikipedias url, om det finns. Till exempel [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|beskrivning|Varumärken beskrivning.|
|tags|En lista över fördefinierade taggar som är kopplade till den här varumärke.|
|förtroende|Förtroende-värdet för Video Indexer varumärke detektor (0-1).|
|instanser|En lista över tidsintervall för den här varumärke. Varje instans har en brandType som anger om den här varumärke visades i avskriften eller OCR.|

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
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
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

#### <a name="statistics"></a>statistik

|Namn|Beskrivning|
|---|---|
|CorrespondenceCount|Antal svaren i videon.|
|WordCount|Antalet ord per talare.|
|SpeakerNumberOfFragments|Mängden fragment talaren har i en video.|
|SpeakerLongestMonolog|Talarens längsta monolog. Om talaren har silences inuti monolog ingår den. Åsidosatt inaktivitet i början och slutet av monolog tas bort.| 
|SpeakerTalkToListenRatio|Beräkningen baseras på den tid som ägnats åt talarens monolog (utan intervallet mellan) dividerat med den totala tiden för videon. Tiden avrundas till tredje decimaltecknet.|

#### <a name="audioeffects"></a>audioEffects

|Namn|Beskrivning|
|---|---|
|id|Ljud effekt-ID.|
|typ|Typ av ljud effekt (till exempel applåder, tal, tystnad).|
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

#### <a name="sentiments"></a>sentiment

Sentiment sammanställs efter deras sentimentType fält (positiv/Neutral/negativ). Till exempel 0 0.1, 0.1 0.2.

|Namn|Beskrivning|
|---|---|
|id|Sentiment-ID.|
|Medel |Medelvärdet för samtliga värden i alla instanser av den typ av sentiment – positiv/Neutral/negativ|
|instanser|En lista över tidsintervall där den här sentiment visades.|
|sentimentType |Typen kan vara ”positiva', 'Neutral' eller 'Negativt”.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
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

VisualContentModeration blocket innehåller tidsintervall som Video Indexer hittas ha potentiellt vuxet innehåll. Om visualContentModeration är tom, finns det inga vuxet innehåll som har identifierats.

Videor som finns för vuxet eller olämpligt innehåll kan vara tillgängliga för privata vyn. Användare har möjlighet att skicka en begäran om en mänsklig granskning av innehållet, i vilket fall IsAdult attributet innehåller resultatet av mänsklig granskning.

|Namn|Beskrivning|
|---|---|
|id|Visual innehållsmoderering-ID.|
|adultScore|Poäng för Vuxeninnehåll (från content moderator).|
|racyScore|Poäng för vågat (från innehållsmoderering).|
|instanser|En lista över tidsintervall där den här visual innehållsmoderering visades.|

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
|id|Textbaserade innehållsmoderering-ID.|
|bannedWordsCount |Antal otillåtna orden.|
|bannedWordsRatio |Förhållandet från totala antalet ord.|


## <a name="next-steps"></a>Nästa steg

[API för Videoindexering](https://api-portal.videoindexer.ai)

Information om hur du bäddar in widgetar i ditt program finns i [bädda in Video Indexer widgetar i dina program](video-indexer-embed-widgets.md). 


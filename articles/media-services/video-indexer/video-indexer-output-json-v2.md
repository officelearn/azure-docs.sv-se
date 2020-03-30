---
title: Undersök videoindexeringsutdata som produceras av v2 API - Azure
titleSuffix: Azure Media Services
description: Det här avsnittet undersöker Azure Media Services Video Indexer-utdata som produceras av v2 API.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/09/2019
ms.author: juliako
ms.openlocfilehash: 2fac5e07f9646c4fc0fac7b1be53b5a5ac1ea803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245933"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>Undersök videoindexeringsutdata som produceras av API

När du anropar **Api:et hämta videoindex** och svarsstatusen är OK får du en detaljerad JSON-utdata som svarsinnehåll. JSON-innehållet innehåller information om de angivna videoinsikterna. Insikterna inkluderar: utskrifter, OCRs, ansikten, ämnen, block, etc. Varje insiktstyp innehåller förekomster av tidsintervall som visas när insikten visas i videon. 

1. Om du vill hämta JSON-filen anropar du [Get Video Index API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. Om du också är intresserad av specifika artefakter, ring [Get Video Artifact Download URL API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    I API-anropet anger du den begärda artefakttypen (OCR, Ansikten, Nyckelbildrutor etc.)

Du kan också visuellt undersöka videons sammanfattade insikter genom att trycka på **play-knappen** på videon på [videoindexerarens](https://www.videoindexer.ai/) webbplats. Mer information finns i [Visa och redigera videoinsikter](video-indexer-view-edit.md).

![Insikter](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

I den här artikeln undersöks JSON-innehållet som returneras av **Api:et get video index.** 

> [!NOTE]
> Förfallodatum för alla åtkomsttoken i Video Indexer är en timme.


## <a name="root-elements"></a>Rotelement

|Namn|Beskrivning|
|---|---|
|accountId|Spellistans VI-konto-ID.|
|id|Spellistans ID.|
|namn|Spellistans namn.|
|description|Spellistans beskrivning.|
|userName|Namnet på den användare som skapade spellistan.|
|Skapad|Spellistans skapandetid.|
|sekretessMode|Spellistans sekretessläge (Privat/Offentlig).|
|state|Spellistans (uppladdade, bearbetade, bearbetade, misslyckade, i karantän).|
|ärägd|Anger om spellistan har skapats av den aktuella användaren.|
|ärredigerbar|Anger om den aktuella användaren har behörighet att redigera spellistan.|
|isBase (isBase)|Anger om spellistan är en basspellista (en video) eller en spellista som består av andra videor (härledda).|
|durationInSeconds|Den totala varaktigheten av spellistan.|
|sammanfattade Ljus|Innehåller en [summarizedInsights](#summarizedinsights).
|videor|En lista med [videor](#videos) som skapar spellistan.<br/>Om den här spellistan med konstruerade tidsintervall för andra videor (härledda) innehåller videorna i den här listan endast data från de medföljande tidsintervallen.|

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

## <a name="summarizedinsights"></a>sammanfattade Ljus

I det här avsnittet visas sammanfattningen av insikterna.

|Attribut | Beskrivning|
|---|---|
|namn|Namnet på videon. Till exempel Azure Monitor.|
|id|ID:et för videon. Till exempel 63c6d532ff.|
|sekretessMode|Uppdelningen kan ha något av följande lägen: **Privat**, **Offentlig**. **Offentlig** - videon är synlig för alla i ditt konto och alla som har en länk till videon. **Privat** - videon är synlig för alla i ditt konto.|
|varaktighet|Innehåller en varaktighet som beskriver hur en insikt inträffade. Varaktigheten är i sekunder.|
|thumbnailVideoId|ID:et för videon som miniatyren togs från.
|thumbnailId (thumbnailId)|Videons miniatyr-ID. För att få den faktiska miniatyren, ring [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) och skicka den thumbnailVideoId och thumbnailId.|
|Ansikten|Kan innehålla noll eller fler ansikten. Mer detaljerad information finns i [ansikten](#faces).|
|nyckelord|Kan innehålla noll eller fler sökord. Mer detaljerad information finns i [nyckelord](#keywords).|
|Känslor|Kan innehålla noll eller fler sentiment. Mer detaljerad information finns i [sentiment .](#sentiments)|
|audioEffects| Kan innehålla noll eller fler audioEffects. Mer detaljerad information finns i [audioEffects](#audioEffects).|
|Etiketter| Kan innehålla noll eller fler etiketter. Mer information finns i [etiketter](#labels).|
|Märken| Kan innehålla noll eller fler märken. Mer detaljerad information finns i [varumärken](#brands).|
|statistik | Mer detaljerad information finns i [statistik](#statistics).|
|Känslor| Kan innehålla noll eller fler känslor. Mer detaljerad information finns i [känslor](#emotions).|
|Ämnen|Kan innehålla noll eller fler ämnen. [Ämnena](#topics) insikt.|

## <a name="videos"></a>videor

|Namn|Beskrivning|
|---|---|
|accountId|Videons VI-konto-ID.|
|id|Videons ID.|
|namn|Videons namn.
|state|Videons tillstånd (uppladdat, bearbetat, bearbetat, misslyckats, i karantän).|
|bearbetningProgress|Bearbetningen fortskrider under bearbetningen (till exempel 20 %).|
|felKoda|Felkoden om det inte gick att bearbeta (till exempel "Un supportfileType").|
|felMessage|Felmeddelandet om det inte gick att bearbeta.|
|externt|Videons externa ID (om det anges av användaren).|
|externUrl|Videons externa url (om den anges av användaren).|
|metadata|Videons externa metadata (om den anges av användaren).|
|ärAdult|Anger om videon granskades manuellt och identifierades som en vuxenvideo.|
|Insikter|Insights-objektet. Mer information finns i [insikter](#insights).|
|thumbnailId (thumbnailId)|Videons miniatyr-ID. För att få den faktiska miniatyrsamtal [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) och skicka den video-ID och thumbnailId.|
|publiceradeUrl|En url för att strömma videon.|
|publishedUrlProxy|En url för att strömma videon från (för Apple-enheter).|
|visaToken|En kortlivad vytoken för att strömma videon.|
|källaSpråk|Videons källspråk.|
|language|Videons faktiska språk (översättning).|
|indexingPreset|Den förinställning som används för att indexera videon.|
|streamingPreset|Den förinställning som används för att publicera videon.|
|språkModellId|CRIS-modellen som används för att transkribera videon.|
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
### <a name="insights"></a>Insikter

Varje insikt (till exempel avskriftsrader, ansikten, varumärken osv.) innehåller en lista över unika element (till exempel face1, face2, face3) och varje element har sina egna metadata och en lista över sina förekomster (som är tidsintervall med ytterligare valfria metadata).

Ett ansikte kan ha ett ID, ett namn, en miniatyr, andra metadata och en lista över dess temporala förekomster (till exempel: 00:00:05 – 00:00:10, 00:01:00 - 00:02:30 och 00:41:21 – 00:41:49.) Varje temporal instans kan ha ytterligare metadata. Till exempel är ansiktets rektangelkoordinater (20 230 60,60).

|Version|Kodversionen|
|---|---|
|källaSpråk|Videons källspråk (förutsatt att ett huvudspråk). I form av en [BCP-47](https://tools.ietf.org/html/bcp47) sträng.|
|language|Insiktsspråket (översatt från källspråket). I form av en [BCP-47](https://tools.ietf.org/html/bcp47) sträng.|
|Utskrift|[Avskriften](#transcript) insikt.|
|Ocr|[OCR-insikten.](#ocr)|
|nyckelord|Nyckelorden [insikt.](#keywords)|
|block|Kan innehålla ett eller flera [block](#blocks)|
|Ansikten|Ansikten [insikt.](#faces)|
|Etiketter|[Etiketterna](#labels) insikt.|
|Skott|[Skotten](#shots) insikt.|
|Märken|[Varumärkena](#brands) insikt.|
|audioEffects|[AudioEffects](#audioEffects) insikt.|
|Känslor|[Känslorna insikt.](#sentiments)|
|visualContentModeration|[VisualContentModeration](#visualcontentmoderation) insikt.|
|textKontentmoderation|Den [textuellaContentModeration](#textualcontentmoderation) insikt.|
|Känslor| [Känslorna](#emotions) insikt.|
|Ämnen|[Ämnena](#topics) insikt.|

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
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>block

Attribut | Beskrivning
---|---
id|ID för blocket.|
Instanser|En lista över tidsintervall för det här blocket.|

#### <a name="transcript"></a>Utskrift

|Namn|Beskrivning|
|---|---|
|id|Rad-ID.|
|text|Själva utskriften.|
|language|Avskriftsspråket. Avsedd att stödja avskrift där varje rad kan ha ett annat språk.|
|Instanser|En lista över tidsintervall där den här raden visades. Om instansen är avskrift har den bara en instans.|

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

#### <a name="ocr"></a>Ocr

|Namn|Beskrivning|
|---|---|
|id|OCR-linje-ID.|
|text|OCR-texten.|
|konfidensbedömning|Erkännandet förtroende.|
|language|OCR-språket.|
|Instanser|En lista över tidsintervall där denna OCR visades (samma OCR kan visas flera gånger).|
|höjd|Höjden på OCR-rektangeln|
|överst|Den översta platsen i px|
|vänster| Den vänstra platsen i px|
|bredd|Bredden på OCR-rektangeln|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>nyckelord

|Namn|Beskrivning|
|---|---|
|id|Nyckelords-ID.|
|text|Nyckelordstexten.|
|konfidensbedömning|Nyckelordets erkännande förtroende.|
|language|Nyckelordsspråket (när det översätts).|
|Instanser|En lista över tidsintervall där nyckelordet visades (ett nyckelord kan visas flera gånger).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="faces"></a>Ansikten

|Namn|Beskrivning|
|---|---|
|id|Ansikts-ID.|
|namn|Namnet på ansiktet. Det kan vara "Okänd #0, en identifierad kändis eller en kund utbildad person.|
|konfidensbedömning|Ansiktsidentifieringens förtroende.|
|description|En beskrivning av kändisen. |
|thumbnailId (thumbnailId)|ID:et för miniatyrbilden av det ansiktet.|
|knownPersonId|Om det är en känd person, dess interna ID.|
|referenceId|Om det är en Bing kändis, dess Bing ID.|
|referenceType (referenstyp)|För närvarande bara Bing.|
|title|Om det är en kändis, dess titel (till exempel "Microsofts VD").|
|imageUrl|Om det är en kändis, dess bild url.|
|Instanser|Dessa är fall där ansiktet dök upp i det angivna tidsintervallet. Varje instans har också en thumbnailsId. |

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
|namn|Etikettnamnet (till exempel "Dator", "TV").|
|language|Etikettnamnet (när det översätts). BCP-47|
|Instanser|En lista över tidsintervall där den här etiketten visades (en etikett kan visas flera gånger). Varje instans har ett konfidensfält. |


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

#### <a name="scenes"></a>Scener

|Namn|Beskrivning|
|---|---|
|id|Scen-ID.|
|Instanser|En lista över tidsintervall för den här scenen (en scen kan bara ha 1 instans).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>Skott

|Namn|Beskrivning|
|---|---|
|id|Skottet id.|
|Nyckelrutor|En lista över keyFrames inom bilden (var och en har ett ID och en lista över instanser tidsintervall). Varje keyFrame-förekomst har ett thumbnailId-fält som innehåller keyFrames miniatyr-ID.|
|Instanser|En lista över tidsintervall för detta skott (ett skott kan bara ha 1 instans).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>Märken

Affärs- och produktvarumärken som upptäckts i talet till textavskrift och/eller Video OCR. Detta inkluderar inte visuell igenkänning av varumärken eller logotypidentifiering.

|Namn|Beskrivning|
|---|---|
|id|Varumärket ID.|
|namn|Varumärkena namn.|
|referenceId | Suffixet av varumärket wikipedia url. Till exempel är "Target_Corporation" suffixet [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation)i .
|referensUrl | Varumärkets Wikipedia url, om det finns. Till exempel [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description|Varumärkesbeskrivningen.|
|tags|En lista över fördefinierade taggar som var associerade med det här varumärket.|
|konfidensbedömning|Konfidensvärdet för varumärket Video Indexer (0-1).|
|Instanser|En lista över tidsintervall för detta varumärke. Varje instans har en brandType, som anger om detta märke dök upp i avskriften eller i OCR.|

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
|KorrespondensCount|Antal korrespondenser i videon.|
|SpeakerWordCount|Antalet ord per talare.|
|HögtalareNummerFragments|Mängden fragment som högtalaren har i en video.|
|HögtalareLångasteMonolog|Talarens längsta monolog. Om talaren har tystnader inuti monolog det ingår. Tystnad i början och slutet av monolog tas bort.| 
|SpeakerTalkToListenRatio|Beräkningen baseras på den tid som spenderas på talarens monolog (utan tystnaden däremellan) dividerat med den totala tiden för videon. Tiden avrundas till det tredje decimaltecknet.|

#### <a name="audioeffects"></a><a id="audioEffects"/>audioEffects

|Namn|Beskrivning|
|---|---|
|id|Ljudeffekt-ID.|
|typ|Ljudeffekttypen (till exempel Klappa, Tal, Tystnad).|
|Instanser|En lista över tidsintervall där den här ljudeffekten visades.|

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

#### <a name="sentiments"></a>Känslor

Sentiments aggregeras av deras sentimentType-fält (Positivt/Neutralt/Negativt). Till exempel 0-0,1, 0,1-0,2.

|Namn|Beskrivning|
|---|---|
|id|Sentiment-ID.|
|genomsnittligTcore |Medelvärdet av alla poäng för alla instanser av den sentimenttypen - Positiv/Neutral/Negativ|
|Instanser|En lista över tidsintervall där den här känslan visades.|
|sentimentType |Typen kan vara "Positiv", "Neutral" eller "Negativ".|

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

VisualContentModeration-blocket innehåller tidsintervall som Video Indexer fann potentiellt ha barnförbjudet innehåll. Om visualContentModeration är tomt finns det inget barnförbjudet innehåll som har identifierats.

Videor som visar sig innehålla barn eller racy innehåll kan vara tillgängliga för privat visning bara. Användare har möjlighet att skicka en begäran om en mänsklig granskning av innehållet, i vilket fall attributet IsAdult kommer att innehålla resultatet av den mänskliga granskningen.

|Namn|Beskrivning|
|---|---|
|id|Det visuella innehållsmoderations-ID:et.|
|vuxenScore|Den vuxna poängen (från innehållsmoderator).|
|racyScore (racyScore)|Den racy poäng (från innehåll moderering).|
|Instanser|En lista över tidsintervall där den här visuella innehållsmoderationen visades.|

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

#### <a name="textualcontentmoderation"></a>textKontentmoderation 

|Namn|Beskrivning|
|---|---|
|id|Textinnehåll moderering ID.|
|bannedWordsCount |Antalet förbjudna ord.|
|bannedWordsRatio |Förhållandet från det totala antalet ord.|

#### <a name="emotions"></a>Känslor

Video Indexer identifierar känslor baserat på tal och ljudsignaler. Den identifierade känslor kan vara: glädje, sorg, ilska, eller rädsla.

|Namn|Beskrivning|
|---|---|
|id|Känslo-ID:t.|
|typ|Den känsla ögonblick som identifierades baserat på tal och ljud ledtrådar. Känslan kan vara: glädje, sorg, ilska eller rädsla.|
|Instanser|En lista över tidsintervall där denna känsla dök upp.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>Ämnen

Video Indexer drar slutsatser av huvudämnen från transkriptioner. När det är möjligt ingår [IPTC-taxonomi](https://iptc.org/standards/media-topics/) på 2:a nivån. 

|Namn|Beskrivning|
|---|---|
|id|Ämnes-ID.|
|namn|Ämnesnamnet, till exempel: "Läkemedel".|
|referenceId|Brödsmulor som återspeglar ämneshierarkin. Till exempel: "Hälsa och välbefinnande / Medicin och sjukvård / Läkemedel".|
|konfidensbedömning|Förtroendet poäng i intervallet [0,1]. Högre är mer självsäker.|
|language|Språket som används i ämnet.|
|iptcName (iptcName)|IPTC-mediekodnamnet, om det upptäcks.|
|Instanser |För närvarande indexerar videoindexeraren inte ett ämne till tidsintervall, så hela videon används som intervall.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>Nästa steg

[Utvecklarportal för videoindexerare](https://api-portal.videoindexer.ai)

Information om hur du bäddar in widgetar i programmet finns i [Bädda in videoindexeringswidgetar i dina program](video-indexer-embed-widgets.md). 


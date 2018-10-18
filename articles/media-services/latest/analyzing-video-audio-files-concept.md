---
title: Analysera video och ljud filer med Azure Media Services | Microsoft Docs
description: När du använder Azure Media Services kan analysera du dina ljud- och contnet med AudioAnalyzerPreset och VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 36c30e11e8b7bd8b3e8ae3e424649998dcfff5bb
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49381207"
---
# <a name="analyzing-video-and-audio-files"></a>Analysera video-och ljudfiler

Azure Media Services v3 kan du extrahera insikter från din video och ljud filer med Video Indexer via AMS v3 analyzer förinställningar (beskrivs i den här artikeln). Om du vill mer detaljerade insikter, använda Video Indexer direkt. Att förstå när du vill använda i stället för Video Indexer. Media Services analyzer förinställningar, Kolla in den [jämförelsedokumentet](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Om du vill analysera ditt innehåll med Media Services v3 förinställningar kan du skapa en **transformera** och skicka en **jobbet** som använder en av dessa förinställningar: **AudioAnalyzerPreset** eller **VideoAnalyzerPreset**. I följande artikel visar hur du använder **VideoAnalyzerPreset**: [självstudie: analysera videoklipp med Azure Media Services](analyze-videos-tutorial-with-api.md).

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** kan du ta fram flera ljud insikter från en ljud- eller video-fil. Utdata innehåller en JSON-fil (med insikterna) och VTT-filen för ljudavskrifter. Den här förinställningen accepterar en egenskap som anger språket i indatafilen i form av en [BCP47](https://tools.ietf.org/html/bcp47) sträng. Ljud insikterna är:

* Ljudutskrift – avskrifter av orden med tidsstämplar. Flera språk som stöds
* Talare indexering – en mappning av talare och motsvarande talade ord
* Tal attitydanalys – utdata för attitydanalys som utförs på ljudutskrift
* Nyckelord – nyckelord som extraheras från ljudutskrift.

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** kan du ta fram flera ljud- och insikter från en videofil. Utdata innehåller en JSON-fil (med insikterna), en VTT-fil för videoavskriften och en samling av miniatyrbilder. Den här förinställningen också godkänner en [BCP47](https://tools.ietf.org/html/bcp47) sträng (som motsvarar språket i videon) som en egenskap. Videoinsikter inkluderar alla ljud insikter som nämns ovan och följande funktioner:

* Ansikte spårning – den tid under vilken ansikten finns i videon. Varje ansikte har ett ansikts-id och en motsvarande samling av miniatyrbilder
* Visual text – den text som har identifierats via optisk teckenläsning. Texten är stämplad och används också för att extrahera nyckelord (utöver ljudavskrifter)
* Nyckelrutor – en samling nyckelrutor som extraheras från videon
* Visual innehållsmoderering – delen av videor som har flaggats som vuxet eller olämpligt sin natur
* Anteckningens – ett resultat av att kommentera videor baserat på en fördefinierad objektmodell

##  <a name="insightsjson-elements"></a>Insights.JSON element

Utdata innehåller en JSON-fil (insights.json) med alla de insikter som hittades i video eller ljud. Json kan innehålla följande element:

### <a name="transcript"></a>avskrift

|Namn|Beskrivning|
|---|---|
|id|Rad-ID.|
|text|Avskriften.|
|language|Avskriften-språk. Avser att stödja avskrift där varje rad kan ha ett annat språk.|
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

### <a name="ocr"></a>OCR

|Namn|Beskrivning|
|---|---|
|id|OCR rad-ID.|
|text|OCR-text.|
|förtroende|Igenkänning av förtroende.|
|language|OCR-språk.|
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

### <a name="faces"></a>ansikten

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

### <a name="shots"></a>skärmbilder

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

### <a name="statistics"></a>statistik

|Namn|Beskrivning|
|---|---|
|CorrespondenceCount|Antal svaren i videon.|
|WordCount|Antalet ord per talare.|
|SpeakerNumberOfFragments|Mängden fragment talaren har i en video.|
|SpeakerLongestMonolog|Talarens längsta monolog. Om talaren har silences inuti monolog ingår den. Åsidosatt inaktivitet i början och slutet av monolog tas bort.| 
|SpeakerTalkToListenRatio|Beräkningen baseras på den tid som ägnats åt talarens monolog (utan intervallet mellan) dividerat med den totala tiden för videon. Tiden avrundas till tredje decimaltecknet.|


### <a name="sentiments"></a>sentiment

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

### <a name="labels"></a>etiketter

|Namn|Beskrivning|
|---|---|
|id|Etikett-ID.|
|namn|Etikettnamn (till exempel ”dator”, ”TV”).|
|language|Etiketten namn språk (vid översättning). BCP-47|
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

### <a name="keywords"></a>nyckelord

|Namn|Beskrivning|
|---|---|
|id|Nyckelordet-ID.|
|text|Nyckelordstexten.|
|förtroende|Den nyckelordet igenkänning av förtroende.|
|language|Nyckelordet språk (vid översättning).|
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
## <a name="next-steps"></a>Nästa steg

[Självstudie: Analysera videoklipp med Azure Media Services](analyze-videos-tutorial-with-api.md)

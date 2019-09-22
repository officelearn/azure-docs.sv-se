---
title: Analysera video-och ljudfiler med Azure Media Services | Microsoft Docs
description: När du använder Azure Media Services kan du analysera ljud-och video innehåll med hjälp av AudioAnalyzerPreset och VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/21/2019
ms.author: juliako
ms.openlocfilehash: bc4be8eaafe805e5d9a985b005efe80bc4af1d21
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2019
ms.locfileid: "71177990"
---
# <a name="analyzing-video-and-audio-files"></a>Analysera video-och ljudfiler

Med Azure Media Services v3 kan du extrahera insikter från dina video-och ljudfiler med Video Indexer via Media Services v3 Analyzer-för hands inställningar (beskrivs i den här artikeln). Använd Video Indexer direkt för att indexera mer detaljerad information. Mer information om när du bör använda analysförinställningar för Video Indexer eller Media Services finns i [jämförelsedokumentet](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Om du vill analysera ditt innehåll med Media Services v3-för inställningar skapar du en **transformering** och skickar ett **jobb** som använder någon av följande för inställningar: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) eller **AudioAnalyzerPreset**. Följande artikel visar hur du använder **VideoAnalyzerPreset**: [Gång Analysera videor med Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> När du använder en Video- eller Audio Analyzer-förinställningen använder du Azure-portalen för att ställa in ditt konto på att ha 10 mediereserverade S3-enheter. Mer information finns i [Skala mediebearbetning](media-reserved-units-cli-how-to.md).

## <a name="built-in-presets"></a>Inbyggda förinställningar

Media Services stöder för närvarande följande inbyggda Analyzer-för hands inställningar:  

|**Förinställda namnet**|**Scenario**|**Detaljer**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analysera ljud|För inställningen används en fördefinierad uppsättning av AI-baserade analys åtgärder, inklusive tal avskrifter. För närvarande stöder för inställningen bearbetning av innehåll med ett enda ljud spår som innehåller tal på ett och samma språk. Du kan ange språket för ljud nytto lasten i indata med BCP-47-formatet för ' language tag-region '. Språk som stöds är engelska ("en-US" och "en-GB"), spanska ("es-ES" och "es-MX"), franska (fr-FR). italienska ("IT-IT"), japanska (' ja-JP '), portugisiska (' pt-BR '), kinesiska (' zh-CN '), tyska ("de-DE"), arabiska ("ar-tex"), ryska (' ru-RU '), hindi ("Hi-IN" ) och koreanska ("ko-KR").<br/><br/> Om språket inte har angetts eller är inställt på null, kommer automatisk språk identifiering att välja det första språket som identifierats och bearbeta det valda språket under filens varaktighet. Funktionen för automatisk språk identifiering stöder för närvarande engelska, kinesiska, franska, tyska, italienska, japanska, spanska, ryska och portugisiska. Det stöder för närvarande inte dynamisk växling mellan språk när det första språket har identifierats. Funktionen för automatisk språk identifiering fungerar bäst med ljud inspelningar med tydligt discernible tal. Om automatisk språk identifiering inte kan hitta språket, kommer avskriften att återgå till engelska.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analysera ljud och video|Extraherar insikter (avancerade metadata) från både ljud och video och matar ut en JSON-format fil. Du kan ange om du bara vill extrahera ljud insikter när du bearbetar en videofil. Mer information finns i [Analysera video](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Identifiera alla ansikten som finns i videon|Beskriver de inställningar som ska användas när du analyserar en video för att identifiera alla ytor som finns.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

För inställningen gör att du kan extrahera flera ljud insikter från en ljud-eller video fil. Utdata innehåller en JSON-fil (med alla insikter) och VTT-filen för ljud avskriften. Den här för inställningen accepterar en egenskap som anger språket för indatafilen i form av en [BCP47](https://tools.ietf.org/html/bcp47) -sträng. Ljud insikten innehåller:

* Ljud avskrift – en avskrift av talade ord med tidsstämplar. Flera språk stöds
* Talare-indexering – en mappning av högtalarna och motsvarande talade ord
* Analys av tal sentiment – resultatet av sentiment-analysen utförs på ljud avskriften
* Nyckelord – Nyckelord som extraheras från ljud avskriften.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

För inställningen kan du extrahera flera ljud-och video insikter från en videofil. Utdata innehåller en JSON-fil (med alla insikter), en VTT-fil för video avskriften och en samling med miniatyrer. Den här för hands inställningen accepterar också en [BCP47](https://tools.ietf.org/html/bcp47) sträng (som representerar videons språk) som en egenskap. Video insikten innehåller alla ljud insikter som nämns ovan och följande ytterligare objekt:

* Ansikts spårning – den tid under vilken ansikten finns i videon. Varje ansikte har ett ansikts-ID och en motsvarande samling med miniatyrer
* Visuell text – den text som identifieras via optisk tecken igenkänning. Texten är tidsstämplad och används även för att extrahera nyckelord (förutom ljud avskriften)
* Nyckel rutor – en samling nyckel rutor som extraheras från videon
* Redaktör för visuellt innehåll – den del av videor som har flaggats som vuxen eller vågat i naturen
* Anteckning – ett resultat av att kommentera videor baserat på en fördefinierad objekt modell

##  <a name="insightsjson-elements"></a>Insights. JSON-element

Utdata innehåller en JSON-fil (Insights. JSON) med alla insikter som hittades i videon eller ljudet. JSON kan innehålla följande element:

### <a name="transcript"></a>avskrifts

|Name|Beskrivning|
|---|---|
|id|Rad-ID.|
|text|Själva avskriften.|
|language|Avskrifts språket. Avsett att stödja avskrifter där varje rad kan ha ett annat språk.|
|instanser|En lista med tidsintervaller där denna rad visades. Om instansen avskrifts har den bara en instans.|

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

### <a name="ocr"></a>stöd

|Name|Beskrivning|
|---|---|
|id|ID för OCR-linje.|
|text|OCR-text.|
|tillit|Igenkännings förtroendet.|
|language|OCR-språket.|
|instanser|En lista med tidsintervall där denna OCR visades (samma OCR kan förekomma flera gånger).|

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

### <a name="faces"></a>ytor

|Name|Beskrivning|
|---|---|
|id|Ansikts-ID.|
|name|Ansikts namnet. Det kan vara okänd #0, en identifierad kändis eller en kundutbildad person.|
|tillit|Förtroende för ansikts identifiering.|
|description|En beskrivning av kändis. |
|thumbnailId|ID för miniatyr bilden för den aktuella ytan.|
|knownPersonId|Om det är en känd person, dess interna ID.|
|referenceId|Om det är en Bing-kändis, dess Bing-ID.|
|referenceType|Just Bing.|
|title|Om det är en kändis, dess titel (till exempel "Microsofts VD").|
|imageUrl|Om det är en kändis, dess bild-URL.|
|instanser|Detta är instanser av där ytan fanns inom det aktuella tidsintervallet. Varje instans har också en thumbnailsId. |

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

### <a name="shots"></a>bilder

|Name|Beskrivning|
|---|---|
|id|Bild-ID.|
|Nyckel rutor|En lista över nyckel bild rutor i instansen (var och en har ett ID och en lista över instanser av instans intervallet). Nyckel bilds instanser har ett thumbnailId-fält med nyckel rutans miniatyr-ID.|
|instanser|En lista över tidsintervallen för den här instansen (dum par har bara en instans).|

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

### <a name="statistics"></a>uppgifterna

|Name|Beskrivning|
|---|---|
|CorrespondenceCount|Antal korrespondens i videon.|
|WordCount|Antalet ord per talare.|
|SpeakerNumberOfFragments|Mängden fragment som talare har i en video.|
|SpeakerLongestMonolog|Föredragets längsta monolog. Om högtalaren har tystnad i monolog är den inkluderad. Tystnad i början och slutet av monolog tas bort.| 
|SpeakerTalkToListenRatio|Beräkningen baseras på den tid som ägnats åt högtalar monolog (utan tystnad mellan) dividerat med videons totala tid. Tiden avrundas till det tredje decimal tecknet.|


### <a name="sentiments"></a>sentiment

Sentiment sammanställs av deras sentimentType-fält (positiv/neutral/negativ). Till exempel 0-0,1, 0,1-0,2.

|Name|Beskrivning|
|---|---|
|id|Sentiment-ID.|
|averageScore |Medelvärdet av alla resultat från alla instanser av sentiment-typ positiv/neutral/negativ|
|instanser|En lista med tidsintervaller där denna sentiment visades.|
|sentimentType |Typen kan vara "positiv", "neutral" eller "negativ".|

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

|Name|Beskrivning|
|---|---|
|id|Etikett-ID: t.|
|name|Etikett namnet (till exempel "dator", "TV").|
|language|Etikettens namn språk (vid översättning). BCP-47|
|instanser|En lista med tidsintervall där etiketten visas (en etikett kan visas flera gånger). Varje instans har ett konfidens fält. |


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

|Name|Beskrivning|
|---|---|
|id|Nyckelords-ID: t.|
|text|Nyckelords texten.|
|tillit|Nyckelordets tolknings säkerhet.|
|language|Nyckelords språket (vid översättning).|
|instanser|En lista med tidsintervall där det här nyckelordet visades (ett nyckelord kan visas flera gånger).|

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

VisualContentModeration-blocket innehåller tidsintervall som Video Indexer hittade för att potentiellt ha vuxen innehåll. Om visualContentModeration är tomt finns det inget olämpligt innehåll som identifierats.

Videor som innehåller vuxen eller vågat innehåll kan endast vara tillgängliga för privat vy. Användare har möjlighet att skicka en begäran om en mänsklig granskning av innehållet, i så fall kan IsAdult-attributet innehålla resultatet av mänsklig granskning.

|Name|Beskrivning|
|---|---|
|id|ID för moderator för visuellt innehåll.|
|adultScore|Den vuxen poängen (från Content moderator).|
|racyScore|Vågat-poängen (från innehålls redigering).|
|instanser|En lista med tidsintervaller där den här visuella innehålls kontrollanten visades.|

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

[Självstudier: Analysera videor med Azure Media Services](analyze-videos-tutorial-with-api.md)

---
title: Analysera video- och ljudfiler
titleSuffix: Azure Media Services
description: Lär dig hur du analyserar ljud- och videoinnehåll med AudioAnalyzerPreset och VideoAnalyzerPreset i Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/30/2020
ms.author: juliako
ms.openlocfilehash: 1d28fc37b98493322b9e201ac899b7911dd1d705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269892"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analysera video- och ljudfiler med Azure Media Services

Med Azure Media Services v3 kan du extrahera insikter från dina video- och ljudfiler med Video Indexer. I den här artikeln beskrivs förinställningarna för Media Services v3-analysator som används för att extrahera dessa insikter. Använd Video Indexer direkt för att indexera mer detaljerad information. Om du vill förstå när videoindexerare kontra Media Services-analysatorförinställningar ska användas finns i [jämförelsedokumentet](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Om du vill analysera innehållet med media services v3-förinställningar skapar du en **Transform och** skickar in ett **jobb** som använder någon av dessa förinställningar: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) eller **AudioAnalyzerPreset**. En självstudiekurs som visar hur du använder **VideoAnalyzerPreset**finns i [Analysera videor med Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> När du använder en Video- eller Audio Analyzer-förinställningen använder du Azure-portalen för att ställa in ditt konto på att ha 10 mediereserverade S3-enheter. Mer information finns i [Skala mediebearbetning](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Efterlevnad, sekretess och säkerhet

Som en viktig påminnelse måste du följa alla tillämpliga lagar i din användning av Video Indexer, och du får inte använda Video Indexer eller någon annan Azure-tjänst på ett sätt som bryter mot andras rättigheter eller kan vara skadligt för andra. Innan du laddar upp några videor, inklusive biometriska data, till videoindexertjänsten för bearbetning och lagring måste du ha alla rätt rättigheter, inklusive alla lämpliga medgivanden, från personen/individerna i videon. Om du vill veta mer om efterlevnad, sekretess och säkerhet i Video Indexer, Microsoft [Cognitive Services Terms](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Om du vill ha Microsofts sekretessskyldigheter och hantering av dina data läser du Microsofts [sekretesspolicy,](https://privacy.microsoft.com/PrivacyStatement) [villkoren för onlinetjänster](https://www.microsoft.com/licensing/product-licensing/products) ("OST") och [tillägget för databehandling](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Ytterligare sekretessinformation, inklusive om lagring av data, radering/destruktion, finns i OST och [här](../video-indexer/faq.md). Genom att använda Video Indexer samtycker du till att vara bunden av villkoren för kognitiva tjänster, OST, DPA och sekretesspolicyn.

## <a name="built-in-presets"></a>Inbyggda förinställningar

Media Services stöder för närvarande följande inbyggda analysverktygsförinställningar:  

|**Förinställt namn**|**Scenario**|**Detaljer**|
|---|---|---|
|[AudioAnalyzerFörbeka](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analysera ljud|Förinställningen tillämpar en fördefinierad uppsättning AI-baserade analysåtgärder, inklusive talavskrift. För närvarande stöder förinställningen bearbetning av innehåll med ett enda ljudspår som innehåller tal på ett enda språk. Du kan ange språket för ljudnyttolasten i indata med bcp-47-formatet "språktagg-region". Språk som stöds är engelska ("en-US" och "en-GB"), spanska ("es-ES" och "es-MX"), franska ("fr-FR"), italienska ("it-IT"),japanska ("ja-JP), portugisiska (pt-BR), kinesiska ("zh-CN"),tyska (de-DE), arabiska (ar-EG och "ar-SY"), ryska (ru-RU), hindi ("hi-IN" och koreanska ("ko-KR").<br/><br/> Om språket inte anges eller är inställt på null väljer automatisk språkidentifiering det första språket som identifieras och fortsätter med det valda språket under filens varaktighet. Den automatiska språkdetekteringsfunktionen stöder för närvarande engelska, kinesiska, franska, tyska, italienska, japanska, spanska, ryska och portugisiska. Det stöder inte dynamiskt växling mellan språk efter att det första språket har upptäckts. Den automatiska språkidentifieringsfunktionen fungerar bäst med ljudinspelningar med tydligt urskiljbart tal. Om automatisk språkidentifiering inte hittar språket återgår transkriptionen till engelska.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analysera ljud och video|Extraherar insikter (rich metadata) från både ljud och video och matar ut en JSON-formatfil. Du kan ange om du bara vill extrahera ljudinsikter när du bearbetar en videofil. Mer information finns i [Analysera video](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Identifiera ansikten som finns i video|Beskriver de inställningar som ska användas när du analyserar en video för att identifiera alla ansikten som finns.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerFörbeka

Förinställningen gör att du kan extrahera flera ljudinsikter från en ljud- eller videofil. Utdata innehåller en JSON-fil (med alla insikter) och VTT-fil för ljudavskriften. Den här förinställningen accepterar en egenskap som anger språket för indatafilen i form av en [BCP47-sträng.](https://tools.ietf.org/html/bcp47) Ljudinsikterna inkluderar:

* **Ljud transkription**: En utskrift av de talade orden med tidsstämplar. Flera språk stöds.
* **Högtalarindexering**: En kartläggning av högtalarna och motsvarande talade ord.
* **Tal känsla analys:** Produktionen av sentiment analys utförs på ljud transkription.
* **Nyckelord**: Nyckelord som extraheras från ljud transkriptionen.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Förinställningen gör att du kan extrahera flera ljud- och videoinsikter från en videofil. Utdata innehåller en JSON-fil (med alla insikter), en VTT-fil för videoavskriften och en samling miniatyrer. Den här förinställningen accepterar också en [BCP47-sträng](https://tools.ietf.org/html/bcp47) (som representerar videons språk) som en egenskap. Videoinsikterna innehåller alla ljudinsikter som nämns ovan och följande ytterligare objekt:

* **Ansiktsspårning**: Den tid under vilken ansikten finns i videon. Varje ansikte har ett ansikts-ID och en motsvarande samling miniatyrer.
* **Visuell text**: Texten som identifieras via optisk teckenigenkänning. Texten är tidsstämplad och används även för att extrahera nyckelord (förutom ljudavskriften).
* **Nyckelrutor:** En samling nyckelrutor som extraherats från videon.
* **Visuell innehållsmoderering**: Den del av videorna som flaggas som vuxna eller vågade till sin natur.
* **Anteckning:** Ett resultat av att du kommenterar videorna baserat på en fördefinierad objektmodell

## <a name="insightsjson-elements"></a>insights.json element

Utdata innehåller en JSON-fil (insights.json) med alla insikter som finns i videon eller ljudet. JSON kan innehålla följande element:

### <a name="transcript"></a>Utskrift

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

### <a name="ocr"></a>Ocr

|Namn|Beskrivning|
|---|---|
|id|OCR-linje-ID.|
|text|OCR-texten.|
|konfidensbedömning|Erkännandet förtroende.|
|language|OCR-språket.|
|Instanser|En lista över tidsintervall där denna OCR visades (samma OCR kan visas flera gånger).|

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

### <a name="faces"></a>Ansikten

|Namn|Beskrivning|
|---|---|
|id|Ansikts-ID.|
|namn|Ansiktsnamnet. Det kan vara "Okänd #0", en identifierad kändis eller en kundutbildad person.|
|konfidensbedömning|Ansiktsidentifieringens förtroende.|
|description|En beskrivning av kändisen. |
|thumbnailId (thumbnailId)|ID:et för miniatyrbilden av det ansiktet.|
|knownPersonId|Det interna ID:t (om det är en känd person).|
|referenceId|Bing-ID (om det är en Bing kändis).|
|referenceType (referenstyp)|För närvarande bara Bing.|
|title|Titeln (om det är en kändis, till exempel "Microsofts VD").|
|imageUrl|Bildadressen, om det är en kändis.|
|Instanser|Fall där ansiktet dök upp i det angivna tidsintervallet. Varje instans har också en thumbnailsId. |

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

### <a name="shots"></a>Skott

|Namn|Beskrivning|
|---|---|
|id|Skottet id.|
|Nyckelrutor|En lista över nyckelbildrutor inom bilden (var och en har ett ID och en lista över instanser tidsintervall). Nyckelbildrutor har ett thumbnailId-fält med keyFrames miniatyr-ID.|
|Instanser|En lista över tidsintervall för detta skott (skott har bara 1 instans).|

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
|KorrespondensCount|Antal korrespondenser i videon.|
|WordCount (Ort)|Antalet ord per talare.|
|HögtalareNummerFragments|Mängden fragment som högtalaren har i en video.|
|HögtalareLångasteMonolog|Talarens längsta monolog. Om talaren har tystnad inuti monologen ingår det. Tystnad i början och slutet av monolog tas bort.|
|SpeakerTalkToListenRatio|Beräkningen baseras på den tid som spenderas på talarens monolog (utan tystnaden däremellan) dividerat med den totala tiden för videon. Tiden avrundas till det tredje decimaltecknet.|


### <a name="sentiments"></a>Känslor

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

### <a name="labels"></a>Etiketter

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

### <a name="keywords"></a>nyckelord

|Namn|Beskrivning|
|---|---|
|id|Nyckelords-ID.|
|text|Nyckelordstexten.|
|konfidensbedömning|Nyckelordets erkännande förtroende.|
|language|Nyckelordsspråket (när det översätts).|
|Instanser|En lista över tidsintervall där nyckelordet visades (ett nyckelord kan visas flera gånger).|

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

VisualContentModeration-blocket innehåller tidsintervall som Video Indexer fann potentiellt ha barnförbjudet innehåll. Om visualContentModeration är tomt finns det inget barnförbjudet innehåll som har identifierats.

Videor som visar sig innehålla barn eller racy innehåll kan vara tillgängliga för privat visning bara. Användare kan skicka en begäran om en mänsklig granskning `IsAdult` av innehållet, i vilket fall attributet kommer att innehålla resultatet av den mänskliga granskningen.

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
## <a name="next-steps"></a>Nästa steg

[Självstudie: Analysera videor med Azure Media Services](analyze-videos-tutorial-with-api.md)

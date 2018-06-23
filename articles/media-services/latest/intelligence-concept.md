---
title: Azure media intelligence | Microsoft Docs
description: När du använder Azure Media Services kan analysera du din ljud och video contnet med AudioAnalyzerPreset och VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2018
ms.author: juliako
ms.openlocfilehash: c488060b9db0ba482d12eee2394e5149b918950e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331528"
---
# <a name="media-intelligence"></a>Medieintelligens

Azure Media Services REST v3-API kan du analysera ljud och video. För att analysera ditt innehåll kan du skapa en **transformera** och skicka en **jobbet** som använder en av dessa förinställningar: **AudioAnalyzerPreset** eller **VideoAnalyzerPreset** . 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** kan du ta fram flera ljud insikter från en ljud- eller fil. Utdata innehåller en JSON-fil (med denna kunskap) och VTT-filen för ljud betyg. Den här förinställningen accepterar en egenskap som anger språket i indatafilen i form av en [BCP47](https://tools.ietf.org/html/bcp47) sträng. Ljud insikter inkluderar:

* Ljud skrivfel – ett betyg av tal med tidsstämplar. Flera språk som stöds
* Talare indexering – en mappning av talare och motsvarande orden
* Tal sentiment analys – utdata från sentiment analys utförs på ljud skrivfel
* Nyckelord – nyckelord som extraheras från ljud skrivfel.

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** kan du ta fram flera ljud- och insikter från en videofil. Utdata innehåller en JSON-fil (med denna kunskap), en VTT-fil för video betyg och en samling av miniatyrbilder. Denna inställning kan också användas med en [BCP47](https://tools.ietf.org/html/bcp47) sträng (som motsvarar språket i videon) som en egenskap. Video insikter inkluderar alla ljud insikter som nämns ovan och följande ytterligare funktioner:

* Står inför spårning – den tid under vilken ytor finns i videon. Varje yta har ett ansikte-id och en motsvarande mängd miniatyrer
* Visual text – den text som har identifierats via OCR. Texten är stämplad och används också för att extrahera nyckelord (förutom ljud betyg)
* Nyckelrutor – en samling nyckelrutor som extraheras från videon
* Visual innehåll avbrottsmoderering – del videor som har flaggats som vuxen eller dyr till sin natur
* Anteckningen – ett resultat av kommentera videor baserat på en fördefinierad objektmodell

##  <a name="insightsjson-elements"></a>Insights.JSON element

Utdata innehåller en JSON-fil (insights.json) med de insikter som hittades i video eller ljud. Json kan innehålla följande element:

### <a name="transcript"></a>betyg

|Namn|Beskrivning|
|---|---|
|id|Rad-ID.|
|text|Betyg sig själv.|
|Språk|Språket som betyg. Avsett att stödja betyg där varje rad kan ha ett annat språk.|
|instanser|En lista över tidsintervall där den här raden visades. Om-instansen är ett betyg, får endast 1 instans.|

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
|text|Texten som OCR.|
|Förtroende|Recognition förtroende.|
|Språk|OCR-språk.|
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

### <a name="keywords"></a>nyckelord

|Namn|Beskrivning|
|---|---|
|id|Nyckelordet-ID.|
|text|Nyckelordstexten som.|
|Förtroende|Det nyckelordet recognition förtroende.|
|Språk|Nyckelordet språk (vid översättning).|
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

### <a name="faces"></a>ytor

|Namn|Beskrivning|
|---|---|
|id|Ansikts-ID.|
|namn|Ansikts-namn. Det kan vara ”okänt #0”, en identifierade ryktbarhet eller en kund utbildade person.|
|Förtroende|Ansikts identifiering förtroende.|
|description|Vid en ryktbarhet dess beskrivning. |
|thumbnalId|Id för på miniatyrbilden för den sida.|
|knownPersonId|Vid en känd person, ett internt ID.|
|referenceId|Vid en Bing-ryktbarhet dess Bing-ID.|
|referenceType|För närvarande bara Bing.|
|rubrik|Vid en ryktbarhet rubriken (till exempel ”Microsofts VD”).|
|imageUrl|Vid en ryktbarhet dess bild-url.|
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

### <a name="labels"></a>etiketter

|Namn|Beskrivning|
|---|---|
|id|Etikett-ID.|
|namn|Etikettnamn (till exempel ”dator”, ”TV').|
|Språk|Etiketten namn språk (vid översättning). BCP-47|
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

### <a name="shots"></a>bilderna

|Namn|Beskrivning|
|---|---|
|id|Som ID.|
|Nyckelrutor|En lista över viktiga ramar inom det bild (var och en har ett ID och en lista över instanser tidsintervall).|
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
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
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
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```


### <a name="sentiments"></a>Våra

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

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudier: Analysera videor med Azure Media Services](analyze-videos-tutorial-with-api.md)

---
title: Analysera direktsänd video med AI som du väljer – Azure
description: I den här artikeln får du lära dig hur du skapar en IoT Edge-modul som kan integreras med real tids analys på IoT Edge för att analysera direktsänd video med en valfri modell för visuellt innehåll.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 0ac2af280eefd5ce293a8be422551d5ee6f6d3f3
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261263"
---
# <a name="analyze-live-video-with-ai-of-your-choice"></a>Analysera direktsänd video med valfri AI

I den här artikeln får du lära dig hur du skapar en IoT Edge-modul som kan integreras med real tids analys på IoT Edge för att analysera direktsänd video med en valfri modell för visuellt innehåll. 

## <a name="pre-reading"></a>För läsning

[Media diagram-koncept](media-graph-concept.md)

## <a name="media-graph-extension"></a>Media Graph-tillägg

Live video analys på IoT Edge definierar en utöknings modell som gör att du kan utöka medie diagramets bearbetnings funktioner till dina egna Media Analytics-komponenter via ett Graph-tillägg. Analys komponenten kan använda traditionella bild bearbetnings tekniker eller AI-modeller för visuellt innehåll. Graph-tillägg är aktiverade genom att inkludera noden [http-tilläggsbegäranden](media-graph-concept.md#http-extension-processor) i ett medie diagram. HTTP-tillägget kan vidarebefordra video bild rutor till en HTTP-slutpunkt som anges av dig och fungerar som gränssnitt för din komponent via det. Anslutningen kan göras till en lokal eller fjärrslutpunkt och den kan skyddas av autentisering och TLS-kryptering, om det behövs. Dessutom tillåter processorn valfri skalning och kodning av video bild rutorna innan de vidarebefordras.

Du kan välja att köra en valfri utgångs metod på alla tillgängliga distributions körningar, till exempel ONNX, TensorFlow, PyTorch eller andra på din egen Docker-behållare. Du kan också använda programspråket och de bibliotek som du väljer för att exponera avbildningens inferencing-funktioner, även om en HTTP-server används på din egen behållare. Inferencing-behållaren bör distribueras tillsammans med Live Video Analytics Edge-modulen för bästa prestanda och kommer sedan att anropas via HTTP-tilläggs processorn i din graf-topologi.
Dessutom kan anrops frekvensen till din anpassade modul begränsas genom att du kan lägga till en [rörelse detektor processor](media-graph-concept.md#motion-detection-processor) och ett [ram hastighets filter processor](media-graph-concept.md#frame-rate-filter-processor) överströms till http-tilläggsbegäranden.

Diagrammet nedan visar data flödet på hög nivå:

![Gräns enhet](./media/analyze-live-video-with-ai-your-choice-how-to/edge-device.png)

På så sätt kan du analysera video med hjälp av de AI-modeller som du väljer för att uppfylla dina unika affärs behov. AI-modellerna kan vara från communityn med öppen källkod eller från dina egna data forskare eller från en specialiserad AI-Provider.

## <a name="media-graph-http-extension-contract-definitions"></a>Kontrakts definitioner för HTTP-tillägg för medie diagram

Det här avsnittet definierar det HTTP-kontrakt som definierar data flödet.

### <a name="http-extensibility-protocol"></a>Protokoll för HTTP-utökning  

HTTP-kontraktet definieras enligt följande:

* Modulen fungerar som HTTP-server.
* Live video analys i IoT Edge-modulen fungerar som HTTP-klient.

### <a name="request"></a>Förfrågan

Förfrågningar från Live Video Analytics-modulen till modulen är följande:

|||
|---|---|
|POST| `https://hostname/optional-path?optional-query`|
|Acceptera|Application/JSON,*/*|
|Auktorisering| Basic, Digest, Bearer (via anpassad huvud support)|
|Content-Type|bild/jpeg<br/>bild/png<br/>bild/bmp<br/>bild/x – RAW|
|Innehålls längd|Längd på brödtext, i byte|
|User-Agent|Azure Media Services|
|Brödtext|Bild byte, binärt kodat i en av de innehålls typer som stöds.|

#### <a name="example"></a>Exempel

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Svar

Svar från modulen till video analys modulen i real tid bör vara följande

|||
|---|---|
|Statuskoder|200 OK-Härlednings resultat hittades<br/>204 inget innehåll – inget innehåll hittades av AI-filen<br/>400 Felaktig begäran-inte förväntades<br/>500 internt Server fel-ej förväntat<br/>503 servern är upptagen – AMS kommer att stängas av baserat på sidhuvudet "retry-after" eller baserat på en standard tids period i fall rubriken inte är förinställd.|
|Content-Type|application/json|
|Innehålls längd|    Längd på brödtext, i byte|
|Brödtext|JSON-objekt med enskild "inferences"-egenskap.|

#### <a name="example"></a>Exempel

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Vi rekommenderar starkt att svaren returneras med giltiga JSON-dokument efter det förinställda schema som definierats nedan. Detta säkerställer bättre samverkan med andra komponenter och möjliga framtida funktioner som läggs till i modulen för video analys i real tid.

Om din modul returnerar ett svar där innehålls typen inte är "Application/JSON", kodar live video analys meddelandet som ett bas 64-innehåll och serialiserar det som en ogenomskinlig JSON-nyttolast.

Om din modul returnerar ett svar med innehålls typen "Application/JSON", men JSON-schemat inte följer det [schema för härlednings-metadata som beskrivs nedan](#inference-metadata-schema), vidarebefordras meddelande nytto lasten via pipelinen, men interoperabiliteten kommer att minska.

> [!NOTE]
> Om modulen inte genererar något resultat ska den returnera HTTP 204-statuskod (inget innehåll) med en tom svars text. Live Video Analytics kommer att förstå detta som ett tomt resultat och vidarebefordrar inte händelsen i pipelinen.

### <a name="inference-metadata-schema"></a>Schema för härledning av metadata

Varje Härlednings objekt följer detta supermängds schema:

```
{
  "type": "[classification|motion|entity|text|other]",
  "subtype": "",              // Free form subtype id

  // Object has *at most one of* the following
  "classification":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ]
  },

  "motion":
  {
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
    // "regionId": ""
  },

  "entity":
  {
    "tag": 
    {
      "value": "",            // Tag value
      "confidence": 0.0       // Tag confidence
    },
    "attributes":[            // Optional attributes
      {
        "name": "",           
        "value": "",          
        "confidence": 0.0     
      }
    ],
    "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
  },

  "text":
  {
    "value": "",              // Text value
    "language": "",           // Optional BCP47 Language Code (https://tools.ietf.org/html/bcp47)
    "startTimestamp": 0,      // Optional start timestamp
    "endTimestamp": 0         // Optional end timestamp
  },

  // Plus every object can have zero or more extensions
  "extensions":
  {
    "name1": "value1",
    "name2": "value2"
    // ...
  }
}
```
 
### <a name="data-contracts---class-hierarchy"></a>Data kontrakt – hierarki för hierarki

![Data kontrakt – hierarki för hierarki](./media/analyze-live-video-with-ai-your-choice-how-to/data-contracts.png)

### <a name="examples"></a>Exempel  

Exemplet nedan innehåller en enskild händelse med alla typer av härledning som stöds:

```
{
  "inferences": [
    // Light detection
    {
      "type": "classification",
      "subtype": "lightDetection",
      "classification": {
        "tag": { "value": "daylight", "confidence": 0.86 },
        "attributes": [
            { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 }
        ]
      }
    },

    // Motion detection
    {
      "type": "motion",
      "subtype": "motionDetection",
      "motion":
      {
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Object detection
    {
      "type": "entity",
      "subtype": "objectDetection",    
      "entity":
      {
        "tag": { "value": "dog", "confidence": 0.97 },
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // Vehicle identification
    {
      "type": "entity",
      "subtype": "vehicleIdentification",    
      "entity":
      {
        "tag": { "value": "007-SPY", "confidence": 0.82 },
        "attributes":[  
          { "name": "color", "value": "black", "confidence": 0.90 },
          { "name": "body", "value": "coupe", "confidence": 0.87 },
          { "name": "make", "value": "Aston Martin", "confidence": 0.35 },
          { "name": "model", "value": "DBS V12", "confidence": 0.33 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      }
    },

    // People identification
    {
      "type": "entity",
      "subtype": "peopleIdentification",    
      "entity":
      {
        "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 },
        "attributes":[  
          { "name": "age", "value": "73", "confidence": 0.87 },
          { "name": "glasses", "value": "yes", "confidence": 0.94 }
        ],
        "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 }
      },
    }
  ]
}
```
 
## <a name="sample-http-extension-modules"></a>Testa moduler för HTTP-tillägg

Några exempel på moduler för HTTP-tillägg finns i [Live Video Analytics-GitHub lagrings platsen](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis). En av dessa [video analys exempel](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) visar hur du använder [Yolov3](https://pjreddie.com/darknet/yolo/) [ONNX](http://onnx.ai/) -modellen för att bygga en IoT Edge-modul för objekt identifiering. Du kan använda samma metod för att bygga en egen modul med en valfri AI-modell.

## <a name="next-steps"></a>Nästa steg

[Felsöka](troubleshoot-how-to.md)

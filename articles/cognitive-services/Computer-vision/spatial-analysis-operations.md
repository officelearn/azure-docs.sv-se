---
title: Åtgärder för rums analys
titleSuffix: Azure Cognitive Services
description: Åtgärder för rums analys.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 9d1b6e20bf2decfe051e79e073736f71181260fa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014551"
---
# <a name="spatial-analysis-operations"></a>Åtgärder för rums analys

Med rumslig analys kan du analysera strömningsvideo i realtid från kameraenheter. För varje kameraenhet som du konfigurerar genererar åtgärder för rumslig analys en utdataström med JSON-meddelanden som skickas till din instans av Azure IoT Hub. 

Behållaren för rums analys implementerar följande åtgärder:

| Åtgärds identifierare| Description|
|---------|---------|
| cognitiveservices. vision. spatialanalysis-personcount | Räknar personer i en angiven zon i kamerans visnings fält. <br> Utvärderar en inledande _personCountEvent_ -händelse och _personCountEvent_ händelser när antalet ändras.  |
| cognitiveservices. vision. spatialanalysis-personcrossingline | Spårar när en person korsar en angiven linje i kamerans visnings fält. <br>Avger en _personLineEvent_ -händelse när personen korsar linjen och ger riktad information. 
| cognitiveservices. vision. spatialanalysis-personcrossingpolygon | Spårar när en person korsar en angiven linje i kamerans visnings fält. <br> Genererar en _personLineEvent_ -händelse när personen korsar zonen och ger riktad information. |
| cognitiveservices. vision. spatialanalysis-persondistance | Spårar när personer bryter mot en avstånds regel. <br> Avger en _personDistanceEvent_ regelbundet med platsen för varje avstånds överträdelse. |

Alla ovanstående åtgärder är också tillgängliga i `.debug` versionen, som har möjlighet att visualisera video bild rutorna när de bearbetas. Du måste köra `xhost +` på värddatorn för att aktivera visualiseringen av video bild rutor och händelser.

| Åtgärds identifierare| Description|
|---------|---------|
| cognitiveservices. vision. spatialanalysis-personcount. debug | Räknar personer i en angiven zon i kamerans visnings fält. <br> Utvärderar en inledande _personCountEvent_ -händelse och _personCountEvent_ händelser när antalet ändras.  |
| cognitiveservices. vision. spatialanalysis-personcrossingline. debug | Spårar när en person korsar en angiven linje i kamerans visnings fält. <br>Avger en _personLineEvent_ -händelse när personen korsar linjen och ger riktad information. 
| cognitiveservices. vision. spatialanalysis-personcrossingpolygon. debug | Spårar när en person korsar en angiven linje i kamerans visnings fält. <br> Genererar en _personLineEvent_ -händelse när personen korsar zonen och ger riktad information. |
| cognitiveservices. vision. spatialanalysis-persondistance. debug | Spårar när personer bryter mot en avstånds regel. <br> Avger en _personDistanceEvent_ regelbundet med platsen för varje avstånds överträdelse. |

Rums analys kan också köras med [Live Video Analytics](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) som sin video AI-modul. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Åtgärds identifierare| Description|
|---------|---------|
| cognitiveservices. vision. spatialanalysis-personcount. livevideoanalytics | Räknar personer i en angiven zon i kamerans visnings fält. <br> Utvärderar en inledande _personCountEvent_ -händelse och _personCountEvent_ händelser när antalet ändras.  |
| cognitiveservices. vision. spatialanalysis-personcrossingline. livevideoanalytics | Spårar när en person korsar en angiven linje i kamerans visnings fält. <br>Avger en _personLineEvent_ -händelse när personen korsar linjen och ger riktad information. 
| cognitiveservices. vision. spatialanalysis-personcrossingpolygon. livevideoanalytics | Spårar när en person korsar en angiven linje i kamerans visnings fält. <br> Genererar en _personLineEvent_ -händelse när personen korsar zonen och ger riktad information. |
| cognitiveservices. vision. spatialanalysis-persondistance. livevideoanalytics | Spårar när personer bryter mot en avstånds regel. <br> Avger en _personDistanceEvent_ regelbundet med platsen för varje avstånds överträdelse. |

Live Video Analytics-åtgärder är också tillgängliga i `.debug` versionen (t. ex. cognitiveservices. vision. spatialanalysis-personcount. livevideoanalytics. debug) som har möjlighet att visualisera video bild rutorna som bearbetas. Du måste köra `xhost +` på värddatorn för att aktivera visualiseringen av video bild rutor och händelser

> [!IMPORTANT]
> AI-modeller för visuellt innehåll identifierar och hittar mänsklig närvaro i video filmer och utdata genom att använda en avgränsnings ruta runt ett mänskligt objekt. AI-modeller försöker inte identifiera ansikten eller identifiera identiteter eller demografiska individer.

Dessa är de parametrar som krävs för var och en av dessa åtgärder för att utföra den här typen av

| Åtgärdsparametrar| Description|
|---------|---------|
| Åtgärds-ID | Åtgärds identifieraren från tabellen ovan.|
| enabled | Boolean: true eller false|
| VIDEO_URL| RTSP-URL: en för kamera enheten (exempel: `rtsp://username:password@url` ). Rums analys stöder H. 264-kodad data ström, antingen via RTSP, http eller MP4 |
| VIDEO_SOURCE_ID | Ett eget namn för kamera enheten eller video strömmen. Detta kommer att returneras med händelse-JSON-utdata.|
| VIDEO_IS_LIVE| Sant för kamera enheter; falskt för inspelade videor.|
| VIDEO_DECODE_GPU_INDEX| Vilken GPU som ska avkoda video ramen. Som standard är det 0. Ska vara samma som `gpu_index` i andra Node config som `VICA_NODE_CONFIG` , `DETECTOR_NODE_CONFIG` .|
| DETECTOR_NODE_CONFIG | JSON som anger vilken GPU som ska köra detektor-noden på. Ska ha följande format: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | JSON-konfiguration för zonen och raden enligt beskrivningen nedan.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Zon konfiguration för cognitiveservices. vision. spatialanalysis-personcount

 Detta är ett exempel på en JSON-ineffekt för den SPACEANALYTICS_CONFIG parameter som konfigurerar en zon. Du kan konfigurera flera zoner för den här åtgärden.

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| Namn | Typ| Description|
|---------|---------|---------|
| `zones` | lista| Lista över zoner. |
| `name` | sträng| Eget namn för zonen.|
| `polygon` | lista| Varje värde par representerar x, y för hörnen i en polygon. Polygonen representerar de områden där människor spåras eller räknas och polygoner är baserade på normaliserade koordinater (0-1), där det övre vänstra hörnet är (0,0, 0,0) och det nedre högra hörnet är (1,0, 1,0).   
| `threshold` | flyt| Händelser utsätts när AI-modellernas tillförlitlighet är större eller lika med det här värdet. |
| `type` | sträng| För **cognitiveservices. vision. spatialanalysis-personcount** ska detta vara `count` .|
| `trigger` | sträng| Typ av utlösare för att skicka en händelse. Värden som stöds är `event` för att skicka händelser när antalet ändras eller `interval` för att skicka händelser med jämna mellanrum, oavsett om antalet har ändrats eller inte.
| `interval` | sträng| En tid i sekunder som antalet personer ska aggregeras innan en händelse utlöses. Åtgärden fortsätter att analysera scenen med konstant hastighet och returnerar det vanligaste antalet under intervallet. Samlings intervallet gäller för både `event` och `interval` .|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Linje konfiguration för cognitiveservices. vision. spatialanalysis-personcrossingline

Detta är ett exempel på en JSON-ineffekt för den SPACEANALYTICS_CONFIG parameter som konfigurerar en rad. Du kan konfigurera flera korsande linjer för den här åtgärden.

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Namn | Typ| Description|
|---------|---------|---------|
| `lines` | lista| Lista med rader.|
| `name` | sträng| Eget namn för den här raden.|
| `line` | lista| Definitionen av raden. Det här är en riktad linje som gör att du kan förstå "post" eller "avsluta".|
| `start` | värdepar| x, y-koordinaterna för linjens start punkt. Värdena för float representerar placeringen av hörnen i förhållande till det övre, vänstra hörnet. För att beräkna de absoluta x-, y-värdena multiplicerar du värdena med ram storleken. |
| `end` | värdepar| x, y-koordinaterna för linjens slut punkt. Värdena för float representerar placeringen av hörnen i förhållande till det övre, vänstra hörnet. För att beräkna de absoluta x-, y-värdena multiplicerar du värdena med ram storleken. |
| `threshold` | flyt| Händelser utsätts när AI-modellernas tillförlitlighet är större eller lika med det här värdet. |
| `type` | sträng| För **cognitiveservices. vision. spatialanalysis-personcrossingline** ska detta vara `linecrossing` .|
|`trigger`|sträng|Typ av utlösare för att skicka en händelse.<br>Värden som stöds: "event": Utlös när någon korsar linjen.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Zon konfiguration för cognitiveservices. vision. spatialanalysis-personcrossingpolygon

Detta är ett exempel på en JSON-ineffekt för den SPACEANALYTICS_CONFIG parameter som konfigurerar en zon. Du kan konfigurera flera zoner för den här åtgärden.

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Namn | Typ| Description|
|---------|---------|---------|
| `zones` | lista| Lista över zoner. |
| `name` | sträng| Eget namn för zonen.|
| `polygon` | lista| Varje värde par representerar x, y för hörn i polygonen. Polygonen representerar de områden där människor spåras eller räknas. Värdena för float representerar placeringen av hörnen i förhållande till det övre, vänstra hörnet. För att beräkna de absoluta x-, y-värdena multiplicerar du värdena med ram storleken. 
| `threshold` | flyt| Händelser utsätts när AI-modellernas tillförlitlighet är större eller lika med det här värdet. |
| `type` | sträng| För **cognitiveservices. vision. spatialanalysis-personcrossingpolygon** ska detta vara `enter` eller `exit` .|
| `trigger`|sträng|Typ av utlösare för att skicka en händelse<br>Värden som stöds: "event": Utlös när någon anger eller avslutar zonen.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Zon konfiguration för cognitiveservices. vision. spatialanalysis-persondistance

Detta är ett exempel på en JSON-ineffekt för SPACEANALYTICS_CONFIG-parametern som konfigurerar en zon för **cognitiveservices. vision. spatialanalysis-persondistance**. Du kan konfigurera flera zoner för den här åtgärden.

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| Namn | Typ| Description|
|---------|---------|---------|
| `zones` | lista| Lista över zoner. |
| `name` | sträng| Eget namn för zonen.|
| `polygon` | lista| Varje värde par representerar x, y för hörn i polygonen. Polygonen representerar de områden där människor räknas och avståndet mellan personer mäts. Värdena för float representerar placeringen av hörnen i förhållande till det övre, vänstra hörnet. För att beräkna de absoluta x-, y-värdena multiplicerar du värdena med ram storleken. 
| `threshold` | flyt| Händelser utsätts när AI-modellernas tillförlitlighet är större eller lika med det här värdet. |
| `type` | sträng| För **cognitiveservices. vision. spatialanalysis-persondistance** ska detta vara `people_distance` .|
| `trigger` | sträng| Typ av utlösare för att skicka en händelse. Värden som stöds är `event` för att skicka händelser när antalet ändras eller `interval` för att skicka händelser med jämna mellanrum, oavsett om antalet har ändrats eller inte.
| `interval` | sträng | En tid i sekunder som överträdelsen ska aggregeras innan en händelse utlöses. Samlings intervallet gäller för både `event` och `interval` .|
| `output_frequency` | int | Den hastighet med vilken händelser utgående. När `output_frequency` = x tas varje x-händelse ut, t. ex. `output_frequency` = 2 innebär att alla andra händelser är utdata. Output_frequency gäller både `event` och `interval` .|
| `minimum_distance_threshold` | flyt| Ett avstånd i fot som utlöser en "TooClose"-händelse när människor är mindre än avståndet mellan varandra.|
| `maximum_distance_threshold` | flyt| Ett avstånd i fot som utlöser en "TooFar"-händelse när människor är större än det avståndet.|

Detta är ett exempel på en JSON-ineffekt för parametern DETECTOR_NODE_CONFIG som konfigurerar en **cognitiveservices. vision. spatialanalysis-persondistance-** zon.

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| Namn | Typ| Description|
|---------|---------|---------|
| `gpu_index` | sträng| Det GPU-index som den här åtgärden ska köras på.|
| `do_calibration` | sträng | Anger att kalibreringen är påslagen. `do_calibration` måste vara true för att **cognitiveservices. vision. spatialanalysis-persondistance** ska fungera korrekt.|

Se rikt linjerna för [kamera placering](spatial-analysis-camera-placement.md)  för att lära dig mer om zon-och linje konfigurationer.

## <a name="spatial-analysis-operation-output"></a>Utdata för spatial analys åtgärd

Händelserna från varje åtgärd skickas till Azure IoT Hub i JSON-format.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>JSON-format för cognitiveservices. vision. spatialanalysis-personcount AI Insights

Exempel-JSON för händelse utdata i den här åtgärden.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Händelse fält namn | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Händelse-ID|
| `type` | sträng| Händelsetyp|
| `detectionsId` | matris| Matris med storlek 1 av unik identifierare för identifiering av person som utlöste händelsen|
| `properties` | samling| Samling med värden|
| `trackinId` | sträng| Unikt ID för den person som identifierats|
| `status` | sträng| "Retur" eller "avsluta"|
| `side` | int| Numret på den sida av polygonen som personen korsar|
| `zone` | sträng | Fältet "namn" för polygonen som representerar den zon som korsades|
| `trigger` | sträng| Utlösarens typ är event eller Interval beroende på värdet för `trigger` i SPACEANALYTICS_CONFIG|

| Namn på identifierings fält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Identifierings-ID|
| `type` | sträng| Identifierings typ|
| `region` | samling| Samling med värden|
| `type` | sträng| Typ av region|
| `points` | samling| Övre vänstra och nedre högra punkter när region typen är REKTANGEL |
| `confidence` | flyt| Förtroende för algoritm|

| Fält namn för SourceInfo | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Kamera-ID|
| `timestamp` | datum| UTC-datum när JSON-nyttolasten genererades|
| `width` | int | Bredd på video RAM|
| `height` | int | Video ramens höjd|
| `frameId` | int | Ram-ID|
| `cameraCallibrationInfo` | samling | Samling med värden|
| `status` | sträng | Anger om kamera kalibrering till jord planet är "fullständig"|
| `cameraHeight` | flyt | Kamerans höjd över marken i fot. Detta härleds från automatisk kalibrering. |
| `focalLength` | flyt | Kamerans zoomnings tid i bild punkter. Detta härleds från automatisk kalibrering. |
| `tiltUpAngle` | flyt | Kamerans lutnings vinkel från lodrätt. Detta härleds från automatisk kalibrering.|

| Fält namn för SourceInfo | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Kamera-ID|
| `timestamp` | datum| UTC-datum när JSON-nyttolasten genererades|
| `width` | int | Bredd på video RAM|
| `height` | int | Video ramens höjd|
| `frameId` | int | Ram-ID|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>JSON-format för cognitiveservices. vision. spatialanalysis-personcrossingline AI Insights

Exempel-JSON för identifiering av utdata för den här åtgärden.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| Händelse fält namn | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Händelse-ID|
| `type` | sträng| Händelsetyp|
| `detectionsId` | matris| Matris med storlek 1 av unik identifierare för identifiering av person som utlöste händelsen|
| `properties` | samling| Samling med värden|
| `trackinId` | sträng| Unikt ID för den person som identifierats|
| `status` | sträng| Riktningen för linje korsning, antingen ' CrossLeft ' eller ' CrossRight '|
| `zone` | sträng | Fältet "namn" på raden som har korsats|

| Namn på identifierings fält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Identifierings-ID|
| `type` | sträng| Identifierings typ|
| `region` | samling| Samling med värden|
| `type` | sträng| Typ av region|
| `points` | samling| Övre vänstra och nedre högra punkter när region typen är REKTANGEL |
| `confidence` | flyt| Förtroende för algoritm|

| Fält namn för SourceInfo | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Kamera-ID|
| `timestamp` | datum| UTC-datum när JSON-nyttolasten genererades|
| `width` | int | Bredd på video RAM|
| `height` | int | Video ramens höjd|
| `frameId` | int | Ram-ID|


> [!IMPORTANT]
> AI-modellen identifierar en person oavsett om personen är riktad mot eller bort från kameran. AI-modellen kör inte ansikts igenkänning eller igenkänning och genererar inte bio metrisk information. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>JSON-format för cognitiveservices. vision. spatialanalysis-personcrossingpolygon AI Insights

Exempel-JSON för identifiering av utdata för den här åtgärden.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Händelse fält namn | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Händelse-ID|
| `type` | sträng| Händelsetyp|
| `detectionsId` | matris| Matris med storlek 1 av unik identifierare för identifiering av person som utlöste händelsen|
| `properties` | samling| Samling med värden|
| `trackinId` | sträng| Unikt ID för den person som identifierats|
| `status` | sträng| Riktningen för polygon-korsningar, antingen "Enter" eller "Exit"|
| `zone` | sträng | Fältet "namn" för polygonen som representerar den zon som korsades|

| Namn på identifierings fält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Identifierings-ID|
| `type` | sträng| Identifierings typ|
| `region` | samling| Samling med värden|
| `type` | sträng| Typ av region|
| `points` | samling| Övre vänstra och nedre högra punkter när region typen är REKTANGEL |
| `confidence` | flyt| Förtroende för algoritm|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>JSON-format för cognitiveservices. vision. spatialanalysis-persondistance AI Insights

Exempel-JSON för identifiering av utdata för den här åtgärden.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Händelse fält namn | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Händelse-ID|
| `type` | sträng| Händelsetyp|
| `detectionsId` | matris| Matris med storlek 1 av unik identifierare för identifiering av person som utlöste händelsen|
| `properties` | samling| Samling med värden|
| `personCount` | int| Antal personer som identifierats när händelsen skickades|
| `averageDistance` | flyt| Det genomsnittliga avståndet mellan alla identifierade personer i fot|
| `minimumDistanceThreshold` | flyt| Avståndet i fot som utlöser en "TooClose"-händelse när människor är mindre än avståndet mellan varandra.|
| `maximumDistanceThreshold` | flyt| Avståndet i fot som utlöser en "TooFar"-händelse när människor är större än avståndet mellan varandra.|
| `eventName` | sträng| Händelse namnet är vid `TooClose` `minimumDistanceThreshold` överträdelse av, `TooFar` vid överträdelse av `maximumDistanceThreshold` eller `unknown` när automatisk kalibrering inte har slutförts|
| `distanceViolationPersonCount` | int| Antal personer som identifierats i överträdelse av `minimumDistanceThreshold` eller `maximumDistanceThreshold`|
| `zone` | sträng | Fältet "namn" för polygonen som representerar den zon som övervakades för distancing mellan personer|
| `trigger` | sträng| Utlösarens typ är event eller Interval beroende på värdet för `trigger` i SPACEANALYTICS_CONFIG|

| Namn på identifierings fält | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Identifierings-ID|
| `type` | sträng| Identifierings typ|
| `region` | samling| Samling med värden|
| `type` | sträng| Typ av region|
| `points` | samling| Övre vänstra och nedre högra punkter när region typen är REKTANGEL |
| `confidence` | flyt| Förtroende för algoritm|
| `centerGroundPoint` | 2 flytt ALS värden| `x`, `y` värden med koordinaterna för personens härledda plats på marken i fot. `x` avståndet från kameran vinkel rätt mot kamerans bild plan projiceras på marken i fot. `y` avståndet från kameran parallellt med bild planet projiceras på marken i fot.|

| Fält namn för SourceInfo | Typ| Description|
|---------|---------|---------|
| `id` | sträng| Kamera-ID|
| `timestamp` | datum| UTC-datum när JSON-nyttolasten genererades|
| `width` | int | Bredd på video RAM|
| `height` | int | Video ramens höjd|
| `frameId` | int | Ram-ID|
| `cameraCallibrationInfo` | samling | Samling med värden|
| `status` | sträng | Anger om kamera kalibrering till jord planet är "fullständig"|
| `cameraHeight` | flyt | Kamerans höjd över marken i fot. Detta härleds från automatisk kalibrering. |
| `focalLength` | flyt | Kamerans zoomnings tid i bild punkter. Detta härleds från automatisk kalibrering. |
| `tiltUpAngle` | flyt | Kamerans lutnings vinkel från lodrätt. Detta härleds från automatisk kalibrering.|


## <a name="use-the-output-generated-by-the-container"></a>Använd de utdata som genereras av behållaren

Du kanske vill integrera identifiering eller händelser för rums analys i ditt program. Här följer några metoder att tänka på: 

* Använd Azure Event Hub SDK för ditt valda programmeringsspråk för att ansluta till Azure IoT Hub-slutpunkten och ta emot händelserna. Mer information finns i [läsa meddelanden från enhet till moln från den inbyggda slut punkten](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) . 
* Konfigurera **meddelanderoutning** på Azure-IoT Hub för att skicka händelserna till andra slut punkter eller spara händelserna i data lagringen. Mer information finns i [IoT Hub](../../iot-hub/iot-hub-devguide-messages-d2c.md) meddelanderoutning. 
* Konfigurera ett Azure Stream Analytics jobb för att bearbeta händelserna i real tid när de anländer och skapa visualiseringar. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Distribuera åtgärder för rums analys i skala (flera kameror)

För att få bästa möjliga prestanda och användning av GPU: er kan du distribuera alla avstånds analys åtgärder på flera kameror med diagram instanser. Nedan visas ett exempel på hur du kör `cognitiveservices.vision.spatialanalysis-personcount` åtgärden på fem kameror.

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| Namn | Typ| Description|
|---------|---------|---------|
| `batch_size` | int | Anger antalet kameror som ska användas i åtgärden. |

## <a name="next-steps"></a>Nästa steg

* [Distribuera en webb program inventering](spatial-analysis-web-app.md)
* [Loggning och felsökning](spatial-analysis-logging.md)
* [Guide för kamera placering](spatial-analysis-camera-placement.md)
* [Guide för zon-och linje placering](spatial-analysis-zone-line-placement.md)
---
title: Uppgradera till Read v 3.0 i API för visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig hur du uppgraderar till Visuellt innehåll v 3.0 Read API från v 2.0/v 2.1.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: c3394156b073df54d6582dc43571137b21df29cd
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968947"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Uppgradera från läsa v2. x för att läsa v3. x

Den här guiden visar hur du uppgraderar din befintliga behållare eller Cloud API Code från Read v2. x för att läsa v 3.0 och v 3.1 Preview.

## <a name="determine-your-api-path"></a>Ta reda på din API-sökväg
Använd följande tabell för att fastställa **versions strängen** i API-sökvägen baserat på den läsning 3. x-version som du migrerar till.

|Produkt typ| Version | Versions sträng i API-sökväg för 3. x |
|:-----|:----|:----|
|Tjänst | Läs 3,0 | **v3.0** |
|Container | Läs 3,0 för hands version | **v3.0** |
|Tjänst/container | Läs 3,1 för hands version | **v 3.1 – för hands version. 2** |

Använd sedan följande avsnitt för att begränsa dina åtgärder och ersätta **versions strängen** i din API-sökväg med värdet från tabellen. Till exempel för för hands versionen av Cloud och container i **Read v 3.1** uppdaterar du API-sökvägen till **https://{Endpoint}/vision/v 3.1 – för hands version. 2/läsa/analysera [? Language]**.

## <a name="servicecontainer"></a>Tjänst/container

### `Batch Read File`

|Läs 2. x |Läs 3. x  |
|----------|-----------|
|https://{Endpoint}/vision/**v 2.0/läsa/Core/asyncBatchAnalyze**     |https://{Endpoint}/vision/<**version sträng**>/Read/Analyze [? Language]|
    
Det finns en ny valfri _språk_ parameter. Om du inte känner till språket i ditt dokument, eller om det kan vara flerspråkigt, ska du inte ta med det. 

### `Get Read Results`

|Läs 2. x |Läs 3. x  |
|----------|-----------|
|https://{Endpoint}/vision/**v 2.0/Read/Operations**/{operationId}     |https://{Endpoint}/vision/<**versions sträng**>/Read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` status flagga

När anropet till `Get Read Operation Result` lyckas returneras ett status Strängs fält i JSON-texten.
 
|Läs 2. x |Läs 3. x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>API-svar (JSON) 

Observera följande ändringar i JSON:
* I v2. x `Get Read Operation Result` returneras JSON för OCR-igenkänning när statusen är `Succeeded"` . I v 3.0 är det här fältet `succeeded` .
* Om du vill hämta roten för sid mat ris ändrar du JSON-hierarkin från `recognitionResults` till `analyzeResult` / `readResults` . Rad-och ord-JSON-hierarkin är oförändrade, så inga kod ändringar krävs.
* Sid vinkeln `clockwiseOrientation` har bytt namn till `angle` och intervallet har ändrats från 0-360 grader till-180 till 180 grader. Beroende på din kod kan det hända att du inte behöver göra några ändringar eftersom de flesta matematik funktioner kan hantera båda områdena.

API: et för v 3.0 introducerar även följande förbättringar som du kan använda för att utnyttja:
* `createdDateTime` och `lastUpdatedDateTime` läggs till så att du kan spåra bearbetningens längd. Mer information finns i dokumentationen. 
* `version` visar vilken version av API som används för att generera resultat
* Ett per ord `confidence` har lagts till. Det här värdet är kalibrerat så att ett värde 0,95 innebär att det finns en chans på 95% som igenkänningen är korrekt. Förtroende poängen kan användas för att välja vilken text som ska skickas till mänsklig granskning. 
    
    
I 2. X är utdataformatet följande: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
I v 3.0 har den justerats:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Endast tjänst

### `Recognize Text`
`Recognize Text` är en *förhands gransknings* åtgärd som är *föråldrad i alla versioner av API för visuellt innehåll*. Du måste migrera från `Recognize Text` till `Read` (v 3.0) eller `Batch Read File` (v 2.0, v 2.1). v 3.0 av `Read` innehåller nyare, bättre modeller för text igenkänning och ytterligare funktioner, så det rekommenderas. Så här uppgraderar du från `Recognize Text` till `Read` :

|Identifiera text 2. x |Läs 3. x  |
|----------|-----------|
|https://{slut punkt}/vision/**v 2.0/recognizeText [? mode]**|https://{Endpoint}/vision/<**version sträng**>/Read/Analyze [? Language]|
    
_Läges_ parametern stöds inte i `Read` . Både handskriven och utskriven text kommer automatiskt att stödjas.
    
En ny valfri _språk_ parameter är tillgänglig i v 3.0. Om du inte känner till språket i ditt dokument, eller om det kan vara flerspråkigt, ska du inte ta med det. 

### `Get Recognize Text Operation Result`

|Identifiera text 2. x |Läs 3. x  |
|----------|-----------|
|https://{Endpoint}/vision/**v 2.0/textOperations/**{operationId}|https://{Endpoint}/vision/<**versions sträng**>/Read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` status flaggor
När anropet till `Get Recognize Text Operation Result` lyckas returneras ett status Strängs fält i JSON-texten. 
 
|Identifiera text 2. x |Läs 3. x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>API-svar (JSON)

Observera följande ändringar i JSON:    
* I v2. x `Get Read Operation Result` returneras JSON för OCR-igenkänning när statusen är `Succeeded` . I v3. x är det här fältet `succeeded` .
* Om du vill hämta roten för sid mat ris ändrar du JSON-hierarkin från `recognitionResult` till `analyzeResult` / `readResults` . Rad-och ord-JSON-hierarkin är oförändrade, så inga kod ändringar krävs.

API: et för v 3.0 introducerar även följande förbättringar som du kan använda. Mer information finns i API-referensen:
* `createdDateTime` och `lastUpdatedDateTime` läggs till så att du kan spåra bearbetningens längd. Mer information finns i dokumentationen. 
* `version` visar vilken version av API som används för att generera resultat
* Ett per ord `confidence` har lagts till. Det här värdet är kalibrerat så att ett värde 0,95 innebär att det finns en chans på 95% som igenkänningen är korrekt. Förtroende poängen kan användas för att välja vilken text som ska skickas till mänsklig granskning. 
* `angle` den allmänna orienteringen för texten i medsols riktning, mätt i grader mellan (-180, 180].
* `width` och ger `"height"` dig måtten för ditt dokument och `"unit"` tillhandahåller mått enheten (pixlar eller tum, beroende på dokument typ).
* `page` flersidiga dokument stöds
* `language` det inmatade språket för dokumentet (från den valfria _språk_ parametern.)


I 2. X är utdataformatet följande: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
I v3. x har den justerats:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Endast behållare

### `Synchronous Read`

|Läs 2,0 |Läs 3. x  |
|----------|-----------|
|https://{Endpoint}/vision/**v 2.0/läsa/Core/analysera**     |https://{Endpoint}/vision/<**version sträng**>/Read/syncAnalyze [? Language]|

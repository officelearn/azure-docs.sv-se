---
title: Uppgradera till v 3.0 av API för visuellt innehåll
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
ms.openlocfilehash: 6e695fcfacac19ca82273d84d049bdb2afe14b54
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214184"
---
# <a name="upgrade-to-computer-vision-v30-read-api-from-v20v21"></a>Uppgradera till Visuellt innehåll v 3.0 Read API från v 2.0/v 2.1

Den här guiden visar hur du uppgraderar din befintliga Visuellt innehåll v 2.0-eller v 2.1 REST API kod till v 3.0 Läs åtgärder. 

## <a name="upgrade-batch-read-file-to-read"></a>Uppgradera `Batch Read File` till `Read`


1. Ändra API-sökvägen för `Batch Read File` 2. x enligt följande:


    |Läs 2. x |Läs 3,0  |
    |----------|-----------|
    |https://{Endpoint}/vision/**v 2.0/läsa/Core/asyncBatchAnalyze**     |https://{Endpoint}/vision/**v 3.0/läsa/analysera**[? Language]|
    
    Det finns en ny valfri _språk_ parameter. Om du inte känner till språket i ditt dokument, eller om det kan vara flerspråkigt, ska du inte ta med det. 

2. Ändra API-sökvägen för `Get Read Results` i 2. x enligt följande:

    |Läs 2. x |Läs 3,0  |
    |----------|-----------|
    |https://{Endpoint}/vision/**v 2.0**/Read/-**åtgärder**/{operationId}     |https://{Endpoint}/vision/**v 3.0**/Read/**analyzeResults**/{operationId}|

3. Ändra koden för att kontrol lera JSON-resultatet från `Get Read Operation Result` . När anropet till `Get Read Operation Result` lyckas returneras ett status Strängs fält i JSON-texten. Följande värden från v 2.0 har ändrats för att passa bättre med andra kognitiva tjänst REST-API: er. 
 
    |Läs 2. x |Läs 3,0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. Ändra koden för att tolka det slutliga tolknings resultatet JSON från `Get Read Operation Result` . 

    Observera följande ändringar i JSON:
    
    - I v2. x `"Get Read Operation Result"` returneras JSON för OCR-igenkänning när statusen är `"Succeeded"` . I v 3.0 är det här fältet `"succeeded"` .
    - Om du vill hämta roten för sid mat ris ändrar du JSON-hierarkin från `"recognitionResults"` till `"analyzeResult"` / `"readResults"` . Rad-och ord-JSON-hierarkin är oförändrade, så inga kod ändringar krävs.
    -   Sid vinkeln `"clockwiseOrientation"` har bytt namn till `"angle"` och intervallet har ändrats från 0-360 grader till-180 till 180 grader. Beroende på din kod kan det hända att du inte behöver göra några ändringar eftersom de flesta matematik funktioner kan hantera båda områdena.
    -   V 3.0-API: et introducerar även följande förbättringar som du kan välja mellan:- `"createdDateTime"` och `"lastUpdatedDateTime"` läggs till så att du kan spåra bearbetningens längd. Mer information finns i dokumentationen. 
        - `"version"` visar vilken version av API som används för att generera resultat
        - Ett per ord `"confidence"` har lagts till. Det här värdet är kalibrerat så att ett värde 0,95 innebär att det finns en chans på 95% som igenkänningen är korrekt. Förtroende poängen kan användas för att välja vilken text som ska skickas till mänsklig granskning. 
    
    
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

## <a name="upgrade-from-recognize-text-to-read"></a>Uppgradera från `Recognize Text` till `Read`
`Recognize Text` är en *förhands gransknings* åtgärd som är *föråldrad i alla versioner av API för visuellt innehåll*. Du måste migrera från `Recognize Text` till `Read` (v 3.0) eller `Batch Read File` (v 2.0, v 2.1). v 3.0 av `Read` innehåller nyare, bättre modeller för text igenkänning och ytterligare funktioner, så det rekommenderas. Så här uppgraderar du från `Recognize Text` till `Read` :

1. Ändra API-sökvägen för `Recognize Text` v2. x enligt följande:


    |Identifiera text 2. x |Läs 3,0  |
    |----------|-----------|
    |https://{slut punkt}/vision/**v 2.0/recognizeText [? mode]**|https://{Endpoint}/vision/**v 3.0/läsa/analysera**[? Language]|
    
    _Läges_ parametern stöds inte i `Read` . Både handskriven och utskriven text kommer automatiskt att stödjas.
    
    En ny valfri _språk_ parameter är tillgänglig i v 3.0. Om du inte känner till språket i ditt dokument, eller om det kan vara flerspråkigt, ska du inte ta med det. 

2. Ändra API-sökvägen för `Get Recognize Text Operation Result` v2. x enligt följande:

    |Identifiera text 2. x |Läs 3,0  |
    |----------|-----------|
    |https://{Endpoint}/vision/**v 2.0/textOperations/**{operationId}|https://{Endpoint}/vision/**v 3.0/läsa/analyzeResults**/{operationId}|

3. Ändra koden för att kontrol lera JSON-resultatet från `Get Recognize Text Operation Result` . När anropet till `Get Read Operation Result` lyckas returneras ett status Strängs fält i JSON-texten. 
 
    |Identifiera text 2. x |Läs 3,0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. Ändra koden för att tolka det slutliga tolknings resultatet JSON från `Get Recognize Text Operation Result` som stöd `Get Read Operation Result` .

    Observera följande ändringar i JSON:    

    - I v2. x `"Get Read Operation Result"` returneras JSON för OCR-igenkänning när statusen är `"Succeeded"` . I v 3.0 är det här fältet `"succeeded"` .
    - Om du vill hämta roten för sid mat ris ändrar du JSON-hierarkin från `"recognitionResult"` till `"analyzeResult"` / `"readResults"` . Rad-och ord-JSON-hierarkin är oförändrade, så inga kod ändringar krävs.
    -   API: et för v 3.0 introducerar även följande förbättringar som du kan använda. Mer information finns i API-referensen:- `"createdDateTime"` och `"lastUpdatedDateTime"` läggs till så att du kan spåra bearbetningens längd. Mer information finns i dokumentationen. 
        - `"version"` visar vilken version av API som används för att generera resultat
        - Ett per ord `"confidence"` har lagts till. Det här värdet är kalibrerat så att ett värde 0,95 innebär att det finns en chans på 95% som igenkänningen är korrekt. Förtroende poängen kan användas för att välja vilken text som ska skickas till mänsklig granskning. 
        - `"angle"` den allmänna orienteringen för texten i medsols riktning, mätt i grader mellan (-180, 180].
        -  `"width"` och ger `"height"` dig måtten för ditt dokument och `"unit"` tillhandahåller mått enheten (pixlar eller tum, beroende på dokument typ).
        - `"page"` flersidiga dokument stöds
        - `"language"` det inmatade språket för dokumentet (från den valfria _språk_ parametern.)


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
    
## <a name="all-other-operations"></a>Alla andra åtgärder

Det finns inga andra bryta ändringar mellan v2. X och v 3.0 av API för visuellt innehåll. Du kan bara ändra API-sökvägen som ska ersättas `v2.0` med `v3.0` .

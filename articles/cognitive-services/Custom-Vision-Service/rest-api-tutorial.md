---
title: 'Självstudier: Använda REST-API:et för Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Använda REST API för att skapa, utbilda, testa och exportera en Custom Vision-modell.
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: 2f28af14c0579a7a6b514d66d2b66a540adb8fac
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226527"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>Självstudier: Använda REST-API:ET FÖR Custom Vision

Lär dig att använda REST API för att skapa, utbilda, testa och exportera en Custom Vision-modell.

Informationen i det här dokumentet visar hur du använder en REST-klient för att arbeta med REST API för att träna Custom Vision-tjänsten. Exemplen visar hur du använder ett API med hjälp av verktyget `curl` från en bash-miljö och `Invoke-WebRequest` från Windows PowerShell.

> [!div class="checklist"]
> * Hämta nycklar
> * Skapa ett projekt
> * Skapa taggar
> * Lägg till bilder
> * Utbilda och testa appen
> * Exportera modellen

## <a name="prerequisites"></a>Nödvändiga komponenter

* Grundläggande kunskaper om Representational State Transfer (REST). Det här dokumentet går inte in på detaljnivå på saker som HTTP-verb, JSON eller andra saker som ofta används med REST.

* Antingen en bash (Bourne Again Shell) med [curl](https://curl.haxx.se)-verktyg eller Windows PowerShell 3.0 (eller senare).

* Ett Custom Vision-konto. Mer information finns i dokumentet [Skapa en klassificerare](getting-started-build-a-classifier.md).

## <a name="get-keys"></a>Hämta nycklar

När du använder REST API måste du autentisera med hjälp av en nyckel. När du utför hantering eller utbildningsåtgärder kan du använda __utbildningsnyckeln__. När du använder modellen för att göra förutsägelser kan du använda __förutsägelsenyckeln__.

När du skapar en begäran skickas nyckeln som en rubrik för begäran.

För att hämta nycklarna som används i det här exemplet för ditt konto går du till [Custom Vision-webbsidan](https://customvision.ai) och väljer __kugghjulsikonen__ i det övre högra hörnet. Kopiera värdena från fälten __Utbildningsnyckel__ och __Förutsägelsenyckel__ i avsnittet __Konton__.

![Bild på gränssnittet för nycklarna](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Eftersom nycklarna används för att autentisera varje begäran förutsätter exemplen i detta dokument att nyckelvärdena ingår i miljövariablerna. Använd följande kommandon för att lagra nycklar i miljövariabler innan du använder andra kodfragment i det här dokumentet:
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

Följande exempel skapar ett nytt projekt med namnet `myproject` i din Custom Vision service-instans. Tjänsten använder domänen `General` som standard:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Svaret på begäran liknar följande JSON-dokument:

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> Posten `id` i svaret fungerar som ID för det nya projektet. Detta kommer att användas i andra exempel senare i det här dokumentet.

Mer information om denna begäran finns i [CreateProject](https://go.microsoft.com/fwlink/?linkid=865446).

### <a name="specific-domains"></a>Specifika domäner

Om du vill skapa ett projekt för en specifik domän kan du ange __domän-ID__ som valfri parameter. Följande exempel visar hur du hämtar en lista över tillgängliga domäner:

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Svaret på begäran liknar följande JSON-dokument:

```json
[
    {
        "id": "ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
        "name": "General",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "c151d5b5-dd07-472a-acc8-15d29dea8518",
        "name": "Food",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "ca455789-012d-4b50-9fec-5bb63841c793",
        "name": "Landmarks",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    ...
]
```

Mer information om denna begäran finns i [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

Följande exempel visar hur du skapar ett nytt projekt som använder domänen __Landmärken__:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>Skapa taggar

Du måste använda ett tagg-ID för att tagga bilder. Följande exempel visar hur du skapar en ny tagg med namnet `cat` och hämtar ett tagg-ID. Ersätt `{projectId}` med ditt projekts ID. Använd parametern `name=` för att ange taggens namn:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

Svaret på begäran liknar JSON-dokumentet: 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Spara värdet `id` eftersom det används för att tagga bilder.

Mer information om denna begäran finns i [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Lägg till bilder

Följande exempel visar hur du lägger till en fil från en URL. Ersätt `{projectId}` med ditt projekts ID. Ersätt `{tagId}` med ditt tagg-ID för bilden:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

Svaret på begäran liknar följande JSON-dokument:

```json
{
    "isBatchSuccessful": true,
    "images": [
        {
            "sourceUrl": "http://myimages/cat.jpg",
            "status": "OK",
            "image": {
                "id": "081adaee-a76b-4d94-a70e-e4fd0935a28f",
                "created": "2018-08-13T13:24:22.0815638",
                "width": 640,
                "height": 480,
                "imageUri": "https://linktoimage",
                "thumbnailUri": "https://linktothumbnail",
                "tags": [
                    {
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936"
                    }
                ],
                "regions": [
                    {
                        "regionId": "40f206a1-3f8a-4de7-a6c3-c7b4643117df",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936",
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "left": 119,
                        "top": 94,
                        "width": 240,
                        "height": 140
                    }
                ]
            }
        }
    ]
}
```

Mer information om denna begäran finns i [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>Träna modellen

Följande exempel visar hur du utbildar modellen. Ersätt `{projectId}` med ditt projekts ID:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

Svaret på begäran liknar följande JSON-dokument:

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

Spara värdet `id` eftersom det används för att testa och exportera modellen.

Mer information finns i [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>Testa modellen

Följande exempel visar hur du testar modellen. Ersätt `{projectId}` med ditt projekts ID. Ersätt `{iterationId}` med det ID som returneras när du utbildar modellen. Ersätt `https://linktotestimage` med sökvägen till testbilden.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

Svaret på begäran liknar följande JSON-dokument:

```json
{
    "id": "369b010b-2a92-4f48-a918-4c1a0af91888",
    "project": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "iteration": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "created": "2018-08-16T17:39:20.7944508Z",
    "predictions": [
        {
            "probability": 0.8390652,
            "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName": "cat"
        }
    ]
}
```

Mer information finns i [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>Exportera modellen

Export av en modell är en tvåstegsprocess. Du måste först ange modellformatet och sedan begära URL:en för den exporterade modellen.

### <a name="request-a-model-export"></a>Begär en modellexport

Följande exempel visar hur du exporterar en `coreml`-modell. Ersätt `{projectId}` med ditt projekts ID. Ersätt `{iterationId}` med det ID som returneras när du utbildar modellen.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

Svaret på begäran liknar följande JSON-dokument:

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

Mer information finns i [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Ladda ned den exporterade modellen

Följande exempel visar hur du hämtar en URL för den exporterade modellen. Ersätt `{projectId}` med ditt projekts ID. Ersätt `{iterationId}` med det ID som returneras när du utbildar modellen.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

Svaret på begäran liknar följande JSON-dokument:

```json
[
    {
        "platform": "CoreML",
        "status": "Done",
        "downloadUri": "https://linktoexportedmodel",
        "flavor": null
    }
]
```

Mer information finns i [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).

---
title: 'Använda API: er för Video Indexer för att anpassa en språkmodell - Azure'
titlesuffix: Azure Media Services
description: 'Den här artikeln visar hur du anpassar en språkmodell med API: er för Video Indexer.'
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 95334ac326b346da23f17d3a9d494120235abace
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285370"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Anpassa en språkmodell med API: er för Video Indexer

Video Indexer kan du skapa anpassade språkmodeller om du vill anpassa taligenkänning genom att ladda upp anpassning text, nämligen text från domänen vars ordförråd som motorn efter behov. När du tränar modellen, identifieras nya ord visas i texten anpassning. 

En detaljerad översikt och bästa praxis för anpassade språkmodeller finns i [anpassa en språkmodell med Video Indexer](customize-language-model-overview.md).

Du kan använda API: er för Video Indexer för att skapa och redigera anpassade språkmodeller i ditt konto, enligt beskrivningen i det här avsnittet. Du kan också använda webbplatsen, enligt beskrivningen i [anpassa språkmodellen med Video Indexer webbplats](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Skapa en språkmodell

Följande kommando skapar en ny anpassad språkmodell i det angivna kontot. Du kan ladda upp filer för språkmodellen i det här anropet. Du kan också skapa språkmodellen här och ladda upp filer för modellen senare genom att uppdatera språkmodellen.

> [!NOTE]
> Du måste fortfarande träna modellen med dess aktiverade filer för modellen Läs innehållet i filerna. Information om hur du tränar en språk finns i nästa avsnitt.

### <a name="request-url"></a>URL för begäran

Det här är en POST-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Nedan visas begäran i Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-PersonModel?).

### <a name="request-parameters"></a>Begäranparametrar

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|
|%{ModelName/|sträng|Ja|Namn för språkmodellen|
|language|sträng|Ja|Språket i språkmodellen. <br/>Den **språk** parametern måste anges språket i BCP-47 formatet ”språk tagg-regioner” (t.ex.: ”en-US”). Språk som stöds är engelska (en-US), tyska (de-DE), spanska (es-SP), arabiska (ar-t.ex.), franska (fr-FR), Hindi (Hej-HI), italienska (it-IT), japanska (ja-JP), portugisiska (pt-BR), ryska (ru-RU) och kinesiska (zh-CN).  |

### <a name="request-body"></a>Begärandetext

Om du vill ladda upp filer som ska läggas till språkmodellen, måste du överföra filer i brödtexten med formulärdata förutom att tillhandahålla värden för de obligatoriska parametrarna som ovan. Det finns två sätt att göra detta: 

1. Nyckeln kommer att filnamnet och värdet kommer att vara txt-fil
2. Nyckeln kommer att filnamnet och värdet kommer att vara en URL till txt-fil

### <a name="response"></a>Svar

Svaret innehåller metadata om den nyligen skapade språkmodellen tillsammans med metadata på var och en av de modellfiler som följer formatet på de exempel på JSON-utdata.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Träna en språkmodell

Följande kommando träna en anpassad språkmodell i det angivna kontot med innehållet i filer som har överförts till och aktiverat i språkmodellen. 

> [!NOTE]
> Du måste först skapa språkmodellen och överföra dess filer. Du kan överföra filer när du skapar språkmodellen eller genom att uppdatera språkmodellen. 

### <a name="request-url"></a>URL för begäran

Det här är en PUT-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Nedan visas begäran i Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5bac3cf761779a6c2ab27?).

### <a name="request-parameters"></a>Begäranparametrar

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountID|sträng|Ja|Globalt unik identifierare för kontot|
|modelId|sträng|Ja|Språk modell-id (genereras när språkmodellen skapas)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller metadata om nyligen tränade språkmodellen tillsammans med metadata på var och en av de modellfiler som följer formatet på de exempel på JSON-utdata.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Du bör använda den **id** värdet för språkmodellen för den **linguisticModelId** parametern när [ladda upp en video till indexet](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) och för den  **languageModelId** parametern när [omindexering en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-language-model"></a>Ta bort en språkmodell

Följande kommando tar bort en anpassad språkmodell från det angivna kontot. En video som använde har tagits bort språkmodellen behåller samma index tills du indexera om videon. Om du indexera om videon, kan du tilldela en ny språkmodell till videon. I annat fall använder Video Indexer dess standardmodell för att indexera om videon.

### <a name="request-url"></a>URL för begäran

Det här är en DELETE-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Nedan visas begäran i Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5ba53782606e91f65be9d?).

### <a name="request-parameters"></a>Begäranparametrar 

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountID|sträng|Ja|Globalt unik identifierare för kontot|
|modelId|sträng|Ja|Språk modell-id (genereras när språkmodellen skapas)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Det finns inget returnerade innehåll när språkmodellen har tagits bort.

## <a name="update-a-language-model"></a>Uppdatera en språkmodell

Följande kommando uppdaterar en anpassad person för språkmodell i det angivna kontot.

> [!NOTE]
> Du måste redan ha skapat språkmodellen. Du kan använda det här anropet för att aktivera eller inaktivera alla filer under modellen, uppdatera namnet på språkmodellen och ladda upp filer som ska läggas till språkmodellen.

### <a name="request-url"></a>URL för begäran

Det här är en PUT-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Nedan visas begäran i Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b3ce85f4684240bdb78f?).

### <a name="request-parameters"></a>Begäranparametrar 

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountID|sträng|Ja|Globalt unik identifierare för kontot|
|modelId|sträng|Ja|Språk modell-id (genereras när språkmodellen skapas)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|
|%{ModelName/|sträng|Nej|Nytt namn som du kan ge modellen|
|aktivera|boolesk|Nej|Välj om alla filer under den här modellen är aktiverad (SANT) eller inaktiverad (FALSKT)|

### <a name="request-body"></a>Begärandetext

Om du vill ladda upp filer som ska läggas till språkmodellen, måste du överföra filer i brödtexten med formulärdata förutom att tillhandahålla värden för de obligatoriska parametrarna som ovan. Det finns två sätt att göra detta: 

1. Nyckeln kommer att filnamnet och värdet kommer att vara txt-fil
2. Nyckeln kommer att filnamnet och värdet kommer att vara en URL till txt-fil

### <a name="response"></a>Svar

Svaret innehåller metadata om nyligen tränade språkmodellen tillsammans med metadata på var och en av de modellfiler som följer formatet på de exempel på JSON-utdata.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
Du kan använda den **id** filernas returnerade här att ladda ned innehållet i filen.

## <a name="update-a-file-from-a-language-model"></a>Uppdatera en fil från en språkmodell

Följande kommando gör att du kan uppdatera namn och **aktivera** läget för en fil i en anpassad språkmodell i det angivna kontot.

### <a name="request-url"></a>URL för begäran

Det här är en PUT-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Nedan visas begäran i Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b60547f33c1c2b2d1375?).

### <a name="request-parameters"></a>Begäranparametrar 

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|modelId|sträng|Ja|ID för språkmodellen som innehåller filen (genereras när språkmodellen skapas)|
|fileId|sträng|Ja|ID för den fil som ska uppdateras (genereras när filen har laddats upp när skapandet eller uppdatering av språkmodellen)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|
|fileName|sträng|Nej|Namn för att uppdatera filnamnet för att|
|aktivera|boolesk|Nej|Uppdatera om den här filen är aktiverad (SANT) eller inaktiverad (FALSKT) i språkmodellen||

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller metadata om filen som du har uppdaterat följande formatet för exempel JSON-utdata nedan.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Du kan använda den **id** filens returnerade här att ladda ned innehållet i filen.

## <a name="get-a-specific-language-model"></a>Hämta en specifik språkmodell

Följande kommando returnerar information på den angivna språkmodellen i det angivna kontot som språk och filer som finns i språkmodellen. 

### <a name="request-url"></a>URL för begäran

Det här är en GET-begäran.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Nedan visas begäran i Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4fcbd9b437d27d53f16).

### <a name="request-parameters-and-request-body"></a>Parametrar för begäran och begärandetexten

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountID|sträng|Ja|Globalt unik identifierare för kontot|
|modelId|sträng|Ja|Språk modell-id (genereras när språkmodellen skapas)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller metadata om den angivna språkmodellen tillsammans med metadata på var och en av de modellfiler som följer formatet på exempel JSON-utdata nedan.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Du kan använda den **id** filens returnerade här att ladda ned innehållet i filen.

## <a name="get-all-the-language-models"></a>Hämta alla språkmodeller

Följande kommando returnerar alla anpassade språkmodeller i det angivna kontot i en lista.

### <a name="request-url"></a>URL för begäran

Det här är en hämta-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Nedan visas begäran i Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b4979e6ecbd30faa6f75?).

### <a name="request-parameters"></a>Begäranparametrar

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountID|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller en lista över alla språkmodeller i ditt konto och var och en av deras metadata och filer som följer formatet på exempel JSON-utdata nedan.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Ta bort en fil från en språkmodell

Följande kommando tar bort den angivna filen från den angivna språkmodellen i det angivna kontot. 

### <a name="request-url"></a>URL för begäran

Det här är en DELETE-begäran.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Nedan visas begäran i Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b57b5de51e64ee52242e).

### <a name="request-parameters"></a>Begäranparametrar 

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountID|sträng|Ja|Globalt unik identifierare för kontot|
|modelId|sträng|Ja|ID för språkmodellen som innehåller filen (genereras när språkmodellen skapas)|
|fileId|sträng|Ja|ID för den fil som ska uppdateras (genereras när filen har laddats upp när skapandet eller uppdatering av språkmodellen)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Det finns inget returnerade innehåll när filen tas bort från språkmodellen.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Hämta metadata för en fil från en språkmodell

Detta returnerar innehållet i och metadata på den angivna filen från den valda språkmodellen i på ditt konto.

### <a name="request-url"></a>URL för begäran

Det här är en GET-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Nedan visas begäran i Curl.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b73f9e6416d7a9965b42).

### <a name="request-parameters"></a>Begäranparametrar 

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountID|sträng|Ja|Globalt unik identifierare för kontot|
|modelId|sträng|Ja|ID för språkmodellen som innehåller filen (genereras när språkmodellen skapas)|
|fileId|sträng|Ja|ID för den fil som ska uppdateras (genereras när filen har laddats upp när skapandet eller uppdatering av språkmodellen)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller innehåll och metadata för filen i JSON-format, ungefär så här:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Innehållet i det här exemplet-filen är ord som ”hello” och world ”i två separata rader.

## <a name="download-a-file-from-a-language-model"></a>Hämta en fil från en språkmodell

Följande kommando laddar ned en textfil som innehåller innehållet i den angivna filen från den angivna språkmodellen i det angivna kontot. Den här filen ska matcha innehållet i textfilen som ursprungligen har överförts.

### <a name="request-url"></a>URL för begäran
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Nedan visas begäran i Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/5ae5b99e522cb47bd9679122).

### <a name="request-parameters"></a>Begäranparametrar 

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountID|sträng|Ja|Globalt unik identifierare för kontot|
|modelId|sträng|Ja|ID för språkmodellen som innehåller filen (genereras när språkmodellen skapas)|
|fileId|sträng|Ja|ID för den fil som ska uppdateras (genereras när filen har laddats upp när skapandet eller uppdatering av språkmodellen)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext 

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret ska ha hämtningen av en textfil med innehållet i filen i JSON-format. 

## <a name="next-steps"></a>Nästa steg

[Anpassa språkmodellen med hjälp av webbplats](customize-language-model-with-website.md)

---
title: Använd Azure Video Indexer för att anpassa varumärken modell
titlesuffix: Azure Media Services
description: Den här artikeln visar hur du använder Azure Video Indexer för att anpassa varumärken modell.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 5591c36a13291432f2d790b4c83e98270b4bc035
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284873"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Anpassa en varumärken modell med Video Indexer API

Video Indexer stöder varumärke identifiering från tal- och visual text under indexering och omindexering av video-och ljudinnehåll. Funktionen för identifiering av varumärke identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bings varumärken databas. Till exempel identifierar om Microsoft nämns i en video- eller ljudinnehåll innehåll eller om den visas i visual text i en video, Video Indexer den som ett varumärke i innehållet. En anpassad varumärken modell kan du undanta vissa varumärken från att identifieras och är varumärken som ska ingå i din modell som inte kanske är i Bings varumärken databasen.

En detaljerad översikt finns i [översikt](customize-brands-model-overview.md).

Du kan använda API: er för Video Indexer för att skapa, använda och redigera anpassade varumärken modeller har identifierats i en video, enligt beskrivningen i det här avsnittet. Du kan också använda Video Indexer-webbplats, enligt beskrivningen i [anpassa varumärken modellen med Video Indexer-webbplatsen](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Skapa ett varumärke

Detta skapar en ny anpassad varumärke och lägger till den anpassade varumärken modellen för det angivna kontot. 

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Begäranparametrar 

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Förutom dessa parametrar måste du ange en begäran brödtext JSON-objekt som innehåller information om det nya varumärket följa formatet för exemplet nedan.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Ange **aktiverat** till true placerar varumärket i den *inkludera* för Video Indexer att identifiera. Ange **aktiverat** till false placerar du varumärket i den *undanta* lista, så Video Indexer inte identifierar den.

Den **referenceUrl** värdet kan vara valfri referens webbplatser för varumärke, till exempel en länk till dess Wikipedia-sida.

Den **taggar** värdet är en lista över taggar för varumärket. Detta visas i varumärken *kategori* i Video Indexer-webbplatsen. Varumärket ”Azure” kan till exempel taggade eller kategoriserade som ”moln”.

### <a name="response"></a>Svar

Svaret innehåller information om varumärket som du nyss skapade följande formatet för exemplet nedan.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Ta bort ett varumärke

Tar bort ett varumärke från varumärken anpassade modellen för det angivna kontot. Konton som har angetts i den **accountId** parametern. När namnet har varumärket inte längre i den *inkludera* eller *undanta* anpassar listor.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Begäranparametrar

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|id|heltal|Ja|Märkes-id (genereras när varumärket skapades)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Det finns inget returnerade innehåll när varumärket har tagits bort.

## <a name="get-a-specific-brand"></a>Hämta ett visst Kaffemärke

På så sätt kan du söka efter information om ett varumärke i anpassade varumärken modellen för det angivna kontot med hjälp av märkes-id. 

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Begäranparametrar 

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|id|heltal|Ja|Märkes-ID (genereras när varumärket skapades)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller information om varumärket du sökte (med märkes-ID) följa formatet för exemplet nedan.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> **aktiverad** anges till **SANT** anger att varumärket i den *inkludera* för Video Indexer att identifiera, och **aktiverat** som FALSKT innebär att den varumärke som tillhör den *undanta* lista, så Video Indexer inte identifierar den.

## <a name="update-a-specific-brand"></a>Uppdatera ett visst kaffemärke

På så sätt kan du söka efter information om ett varumärke i anpassade varumärken modellen för det angivna kontot med varumärke-ID. 

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Begäranparametrar

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|id|heltal|Ja|Märkes-ID (genereras när varumärket skapades)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Förutom dessa parametrar måste du ange en begäran brödtext JSON-objekt som innehåller uppdaterad information om varumärket som du vill uppdatera följande formatet för exemplet nedan.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> I det här exemplet varumärket som skapades i begärandetexten exemplet i den **skapa ett varumärke** avsnittet här uppdateras med en ny tagg och nya beskrivning. Den **aktiverat** värdet har också ändrats till false om du vill placera den i den *undanta* lista.

### <a name="response"></a>Svar

Svaret innehåller den uppdaterade informationen om varumärket som du har uppdaterat följande formatet för exemplet nedan.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampeAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Hämta alla varumärkena

Det här returnerar alla de varumärken i anpassade varumärken modellen för det angivna kontot, oavsett om varumärket är avsedd att vara i den *inkludera* eller *undanta* varumärken lista.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Begäranparametrar

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller en lista över alla varumärken i ditt konto och var och en av deras information som följer formatet på exemplet nedan.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
        {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> Varumärket med namnet *exempel* finns i den *inkludera* för Video Indexer för att identifiera och varumärket med namnet *exempel2* finns i den *undanta* lista , så Video Indexer inte identifierar den.

## <a name="get-brands-model-settings"></a>Hämta inställningarna för varumärken

Det här returnerar modellinställningarna varumärken i det angivna kontot. Varumärken modellinställningarna representerar om identifiering från Bing varumärken databasen har aktiverats eller inte. Om Bing varumärken inte är aktiverad, identifierar Video Indexer endast varumärken från varumärken anpassade modellen för det angivna kontot.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Begäranparametrar

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret visar om Bing varumärken är aktiverade följa formatet för exemplet nedan.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** är inställd på Sant representerar den Bing varumärken är aktiverade. Om *useBuiltin* är FALSKT, Bing varumärken är inaktiverade. Den **tillstånd** värde kan ignoreras eftersom den har ersatts.

## <a name="update-brands-model-settings"></a>Uppdatera inställningarna för varumärken

Detta uppdaterar modellinställningarna varumärken i det angivna kontot. Varumärken modellinställningarna representerar om identifiering från Bing varumärken databasen har aktiverats eller inte. Om Bing varumärken inte är aktiverad, identifierar Video Indexer endast varumärken från varumärken anpassade modellen för det angivna kontot.

### <a name="request-url"></a>URL för begäran: 
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```
 
[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Begäranparametrar 

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Förutom dessa parametrar måste du ange en begäran brödtext JSON-objekt som innehåller information om det nya varumärket följa formatet för exemplet nedan.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** är inställd på Sant representerar den Bing varumärken är aktiverade. Om *useBuiltin* är FALSKT, Bing varumärken är inaktiverade.

### <a name="response"></a>Svar

Det finns inget returnerade innehåll när inställningen varumärken modellen har uppdaterats.

## <a name="next-steps"></a>Nästa steg

[Anpassa varumärken modell med hjälp av webbplats](customize-brands-model-with-website.md)

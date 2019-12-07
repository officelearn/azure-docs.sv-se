---
title: Använd Azure Video Indexer för att anpassa varumärkes-modellen
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du använder Azure Video Indexer för att anpassa varumärkes-modellen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 804cc7b2b0b51312ed756723fff5150b02f324cc
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892812"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Anpassa en modell med ett varumärke med Video Indexer-API

Video Indexer stöder varumärkes identifiering från tal-och visuell text vid indexering och Omindexering av video-och ljud innehåll. Funktionen varumärkes identifiering identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bing-databasens varumärken. Om Microsoft till exempel nämns i ett video-eller ljud innehåll eller om det visas i visuell text i en video identifierar Video Indexer det som ett varumärke i innehållet. Med en modell med anpassade varumärken kan du undanta vissa varumärken från att identifieras och innehålla varumärken som ska ingå i din modell som kanske inte finns i Bing-databasen.

En detaljerad översikt finns i [Översikt](customize-brands-model-overview.md).

Du kan använda Video Indexer API: er för att skapa, använda och redigera anpassade varumärke modeller som identifieras i en video, enligt beskrivningen i det här avsnittet. Du kan också använda Video Indexer webbplats som beskrivs i [Anpassa varumärkes-modellen med hjälp av video Indexer webbplats](customize-brands-model-with-api.md).

## <a name="create-a-brand"></a>Skapa ett varumärke

Detta skapar ett nytt anpassat varumärke och lägger till det i anpassade varumärkes-modellen för det angivna kontot.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Parametrar för begäran

|**Namn**|**Typ**|**Obligatoriskt**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|

### <a name="request-body"></a>Begärandetext

Förutom dessa parametrar måste du ange ett JSON-objekt för begär ande texten som innehåller information om det nya varumärket som följer efter formatet i exemplet nedan.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Inställningen **aktive rad** till True placerar varumärket i listan *ta med* för video Indexer att identifiera. Inställningen **Enabled** to false placerar varumärket i *uteslutnings* listan, så video Indexer inte att identifiera det.

**ReferenceUrl** -värdet kan vara alla referens webbplatser för varumärket, till exempel en länk till en wikipedia-sida.

**Tags** -värdet är en lista med taggar för varumärket. Detta visas i fältet för märkes *kategori* på video Indexer webbplats. Till exempel kan märket "Azure" märkas eller kategoriseras som "moln".

### <a name="response"></a>Svar

Svaret innehåller information om det varumärke som du nyss skapade enligt formatet i exemplet nedan.

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

Tar bort ett varumärke från anpassade varumärkes-modellen för det angivna kontot. Kontot anges i **accountId** -parametern. När det har anropats kommer varumärket inte längre att finnas i listorna *Inkludera* eller *exkludera* varumärken.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Parametrar för begäran

|**Namn**|**Typ**|**Obligatoriskt**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|id|heltal|Ja|Varumärkes-ID: t (som genereras när varumärket skapades)|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|

### <a name="request-body"></a>Begärandetext

Ingen ytterligare begär ande text krävs för det här anropet.

### <a name="response"></a>Svar

Det finns inget returnerat innehåll när varumärket har tagits bort.

## <a name="get-a-specific-brand"></a>Skaffa ett speciellt varumärke

På så sätt kan du söka efter information om ett varumärke i anpassade varumärkes-modellen för det angivna kontot med hjälp av märkes-ID.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Parametrar för begäran

|**Namn**|**Typ**|**Obligatoriskt**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|id|heltal|Ja|Varumärkes-ID: t (som genereras när varumärket skapades)|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|

### <a name="request-body"></a>Begärandetext

Ingen ytterligare begär ande text krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller information om det märke som du sökte efter (med varumärkes-ID) enligt exemplet nedan.

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
> **aktive rad** anges till **True** anger att varumärket är i listan *inkludering* för video Indexer att identifiera, och **aktiverat** som false betyder att varumärket är i *uteslutnings* listan, så video Indexer inte identifierar det.

## <a name="update-a-specific-brand"></a>Uppdatera ett speciellt varumärke

På så sätt kan du söka efter information om ett varumärke i anpassade varumärkes-modellen för det angivna kontot med hjälp av märkes-ID.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Parametrar för begäran

|**Namn**|**Typ**|**Obligatoriskt**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|id|heltal|Ja|Varumärkes-ID: t (som genereras när varumärket skapades)|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|

### <a name="request-body"></a>Begärandetext

Förutom dessa parametrar måste du ange ett JSON-objekt för begär ande texten som innehåller uppdaterad information om det varumärke som du vill uppdatera enligt exemplet nedan.

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
> I det här exemplet kommer varumärket som skapades i exempel begär ande texten i avsnittet **skapa ett märke** att uppdateras här med en ny tagg och ny beskrivning. Det **aktiverade** värdet har också ändrats till false för att kunna läggas till i *uteslutnings* listan.

### <a name="response"></a>Svar

Svaret innehåller den uppdaterade informationen om det varumärke som du uppdaterade efter formatet i exemplet nedan.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
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

## <a name="get-all-of-the-brands"></a>Hämta alla varumärken

Detta returnerar alla varumärken i den anpassade varumärkes-modellen för det angivna kontot, oavsett om varumärket är avsett att finnas i listan *med inkluderade* eller *exkluderade* märken.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Parametrar för begäran

|**Namn**|**Typ**|**Obligatoriskt**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|

### <a name="request-body"></a>Begärandetext

Ingen ytterligare begär ande text krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller en lista över alla varumärken i ditt konto och var och en av sina uppgifter efter formatet i exemplet nedan.

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
> Varumärket *varumärke är i* listan *Inkludera* för video Indexer för att identifiera och varumärket med namnet *Example2* finns i *uteslutnings* listan så video Indexer inte att identifiera det.

## <a name="get-brands-model-settings"></a>Hämta inställningar för varumärkes modell

Detta returnerar modell inställningarna för varumärkena i det angivna kontot. Modell inställningarna för varumärken representerar om identifiering från Bing-databasen är aktive rad eller inte. Om Bing-varumärken inte är aktiverade, identifierar Video Indexer bara varumärken från den anpassade varumärke modellen för det angivna kontot.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Parametrar för begäran

|**Namn**|**Typ**|**Obligatoriskt**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|

### <a name="request-body"></a>Begärandetext

Ingen ytterligare begär ande text krävs för det här anropet.

### <a name="response"></a>Svar

Svaret visar om Bing-varumärken är aktiverade efter formatet i exemplet nedan.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** anges till true anger att Bing-varumärken är aktiverade. Om *useBuiltin* är falskt inaktive ras Bing-varumärken. Värdet för **tillstånd** kan ignoreras eftersom det är inaktuellt.

## <a name="update-brands-model-settings"></a>Uppdatera modell inställningar för varumärke

Detta uppdaterar modell inställningarna för varumärke i det angivna kontot. Modell inställningarna för varumärken representerar om identifiering från Bing-databasen är aktive rad eller inte. Om Bing-varumärken inte är aktiverade, identifierar Video Indexer bara varumärken från den anpassade varumärke modellen för det angivna kontot.

### <a name="request-url"></a>URL för begäran:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Parametrar för begäran

|**Namn**|**Typ**|**Obligatoriskt**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|

### <a name="request-body"></a>Begärandetext

Förutom dessa parametrar måste du ange ett JSON-objekt för begär ande texten som innehåller information om det nya varumärket som följer efter formatet i exemplet nedan.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** anges till true anger att Bing-varumärken är aktiverade. Om *useBuiltin* är falskt inaktive ras Bing-varumärken.

### <a name="response"></a>Svar

Det finns inget returnerat innehåll när modell inställningen för varumärkena har uppdaterats.

## <a name="next-steps"></a>Nästa steg

[Anpassa varumärkes-modellen med hjälp av webbplats](customize-brands-model-with-website.md)

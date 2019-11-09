---
title: 'Använd Video Indexer-API: et för att anpassa en person modell – Azure'
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du anpassar en person modell med Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 44f97e3d9af9daac8d62ae42be76bd73dedbd453
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838272"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Anpassa en person modell med Video Indexer API

Video Indexer stöder ansikts igenkänning och kändis igenkänning för video innehåll. Kändis igenkännings funktionen täcker cirka 1 000 000 ansikten baserat på den begärda data källan, till exempel IMDB-, Wikipedia-och Top LinkedIn-påverkan. Ansikten som inte känns igen av kändis igenkännings funktion identifieras. de får dock inget namn. När du har laddat upp videon för att Video Indexer och få tillbaka resultatet kan du gå tillbaka och namnge de ansikten som inte kändes igen. När du har namngett ett ansikte med ett namn läggs ansikte och namn till i kontots person modell. Video Indexer kommer sedan att identifiera den här ytan i dina kommande videor och tidigare videor.

Du kan använda Video Indexer API för att redigera ansikten som upptäcktes i en video, enligt beskrivningen i det här avsnittet. Du kan också använda Video Indexer webbplats, som beskrivs i [Anpassa person modellen med hjälp av video Indexer webbplats](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Hantera flera person modeller 

Video Indexer stöder flera person modeller per konto. Den här funktionen är för närvarande endast tillgänglig via Video Indexer API: er.

Om ditt konto tillgodoser olika användnings fall kan du vilja skapa flera person modeller per konto. Om ditt innehåll till exempel är relaterat till idrotts kan du skapa en separat person modell för varje sport (fotboll, basket boll, fotboll osv.). 

När en modell har skapats kan du använda den genom att ange modell-ID: t för en speciell person modell vid överföring/indexering eller Omindexering av en video. Träna en ny ansikte för en video uppdaterar den speciella anpassade modell som videon var kopplad till.

Varje konto har en gräns på 50 person modeller. Om du inte behöver stöd för flera person modeller tilldelar du inte ett ID för person modeller till videon när du laddar upp/indexerar eller Omindexerar. I det här fallet använder Video Indexer standard modellen för anpassade personer i ditt konto.

## <a name="create-a-new-person-model"></a>Skapa en ny person modell

Skapa en ny person modell i det angivna kontot. 

### <a name="request-url"></a>URL för begäran

Detta är en POST-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Nedan visas en förfrågan i spiral.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Parametrar för begäran 

|**Namn**|**Typ**|**Kunna**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|namn|sträng|Ja|Namnet på person modellen|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|

### <a name="request-body"></a>Begärandetext

Ingen ytterligare begär ande text krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller namnet och det genererade modell-ID: t för den person modell som du nyss skapade enligt formatet i exemplet nedan.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Du bör sedan använda **ID-** värdet för parametern **personModelId** när du [laddar upp en video för att indexera](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller [Indexera om en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Ta bort en person modell

Ta bort en anpassad person modell från det angivna kontot. 

När person modellen har tagits bort förblir indexet för dina aktuella videor som använde den borttagna modellen oförändrade tills du indexerar om dem. Vid Omindexering kommer de ansikten som beskrevs i den borttagna modellen inte att kännas igen av Video Indexer i dina aktuella videor som har indexerats med modellen. dessa ansikten kommer dock fortfarande att identifieras. Dina aktuella videor som indexeras med den borttagna modellen använder nu ditt kontos standard person modell. Om ansikten från den borttagna modellen också är namngivna i ditt kontos standard modell, kommer dessa ansikten att fortsätta att kännas igen i videor.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Nedan visas en förfrågan i spiral.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>Parametrar för begäran

|**Namn**|**Typ**|**Kunna**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|id|sträng|Ja|ID för person modell (skapas när person modellen skapas)|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|

### <a name="request-body"></a>Begärandetext

Ingen ytterligare begär ande text krävs för det här anropet.

### <a name="response"></a>Svar

Det finns inget returnerat innehåll när person modellen har tagits bort.

## <a name="get-all-person-models"></a>Hämta alla person modeller

Hämta alla person modeller på det angivna kontot. 

### <a name="request-call"></a>Begär anrop

Detta är en GET-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Nedan visas en förfrågan i spiral.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>Parametrar för begäran

|**Namn**|**Typ**|**Kunna**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|

### <a name="request-body"></a>Begärandetext

Ingen ytterligare begär ande text krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller en lista över alla person modeller i ditt konto (inklusive standard person modellen i det angivna kontot) och var och en av deras namn och ID: n efter formatet i exemplet nedan.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Du kan välja vilken modell du vill använda för en video genom att använda **ID-** värdet för person modellen för **personModelId** -parametern när du [laddar upp en video för att indexera](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller indexera om [en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Uppdatera ett ansikte

Med det här kommandot kan du uppdatera en ansikte i videon med ett namn med ID: t för videon och ID: t för FACET. Då uppdateras den person modell som videon var kopplad till vid uppladdning/indexering eller Omindexering. Om ingen person modell har tilldelats, uppdaterar den kontots standard person modell. 

När detta händer känner den igen förekomster av samma ansikte i dina andra aktuella videor som delar samma person modell. Det kan ta lite tid att identifiera ansikte i dina andra aktuella videor eftersom det är en batch-process.

Du kan uppdatera en ansikte som Video Indexer identifieras som en kändis med ett nytt namn. Det nya namnet som du ger företräde framför den inbyggda kändis-igenkänningen.

### <a name="request-call"></a>Begär anrop

Detta är en POST-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Nedan visas en förfrågan i spiral.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Se nödvändiga parametrar och testa att använda video Indexer Developer-portalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>Parametrar för begäran

|**Namn**|**Typ**|**Kunna**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Den Azure-region som anropet ska dirigeras till. Mer information finns i [Azure-regioner och video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|videoId|sträng|Ja|ID för videon där ytan som du vill uppdatera visas. Detta skapas när videon laddas upp och indexeras.|
|faceId|heltal|Ja|ID för det ansikte som ska uppdateras. Du kan hämta faceId från video indexet|
|accessToken|sträng|Ja|Åtkomsttoken (måste vara av åtkomst- [token](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)för scope-kontot) för att autentisera mot anropet. Åtkomsttoken upphör att gälla inom en timme.|
|namn|sträng|Ja|Nytt namn att uppdatera ansikte med.|

Namnen är unika för person modeller, så om du ger två olika ansikten i samma person modell samma **namn** parameter värde video Indexer Visa ansikten som samma person och konvergerar dem när du har indexerat om videon. 

### <a name="request-body"></a>Begärandetext

Ingen ytterligare begär ande text krävs för det här anropet.

### <a name="response"></a>Svar

Det finns inget returnerat innehåll när FACET har uppdaterats.

## <a name="next-steps"></a>Nästa steg

[Anpassa person modellen med hjälp av Video Indexer webbplats](customize-person-model-with-website.md)

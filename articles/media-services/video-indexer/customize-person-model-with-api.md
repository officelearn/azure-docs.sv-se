---
title: Använda Video Indexer API för att anpassa en Person modell – Azure
titlesuffix: Azure Media Services
description: Den här artikeln visar hur du anpassar en Person modell med Video Indexer API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: e5a34a75c73401c567a0e898a1ce9f85cde96586
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360518"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Anpassa en Person modell med Video Indexer API

Video Indexer stöder ansiktsigenkänning och kändisigenkänning för videoinnehåll. Funktionen för igenkänning av kändisar täcker ungefär en miljon ansikten baserat på vanliga datakälla, till exempel IMDB och Wikipedia övre LinkedIn Påverkare. Ansikten som inte känns igen av funktionen för igenkänning av kändisar identifieras. men de är kvar Namnlös. När du överför du video till Video Indexer och få tillbaka resultat kan du gå tillbaka och namnge ansikten som inte tolkats. När du sätter en etikett ett ansikte med ett namn, läggs ansikte och namn till i ditt konto Person modellen. Video Indexer ser sedan den här ansikte i framtida videor och senaste videor.

Du kan använda Video Indexer API: et för att redigera ansikten som upptäcktes på en video, enligt beskrivningen i det här avsnittet. Du kan också använda Video Indexer-webbplats, enligt beskrivningen i [anpassa Person modellen med Video Indexer-webbplatsen](customize-person-model-with-api.md).

## <a name="managing-multiple-person-models"></a>Hantera flera Person modeller 

Video Indexer har stöd för flera Person modeller per konto. Den här funktionen är för närvarande bara tillgänglig via API: er för Video Indexer.

Om ditt konto caters till olika användningsfall, kanske du vill skapa flera Person modeller per konto. Om ditt innehåll är relaterat till sport, kan du sedan skapa en separat Person-modell för varje sport (football, basket, soccer osv.). 

När en modell har skapats kan använda du den genom att ange modell-ID för en specifik Person modell när du överför/indexera eller omindexering en video. Utbildning för ett nytt ansikte för en video uppdaterar specifik anpassade modellen som videon var associerad med.

Varje konto har en gräns på 50 Person modeller. Om du inte behöver stöd för flera Person-modellen kan inte tilldela någon modell-ID till videon vid uppladdning/indexera eller omindexering. I det här fallet använder Video Indexer anpassade Person standardmodellen i ditt konto.

## <a name="create-a-new-person-model"></a>Skapa en ny Person-modell

Skapa en ny Person-modell i det angivna kontot. 

### <a name="request-url"></a>URL för begäran

Det här är en POST-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Nedan visas begäran i Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Begäranparametrar 

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|namn|sträng|Ja|Namnet på den Person-modellen|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller namn och genererad modell-ID för personen modell som du nyss skapade följa formatet för exemplet nedan.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Du bör använda den **id** värde för den **personModelId** parametern när [ladda upp en video till indexet](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller [omindexering en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-person-model"></a>Ta bort en Person-modell

Ta bort en anpassad Person-modell från det angivna kontot. 

När den Person-modellen har tagits bort, påverkas index för dina aktuella videor som har använt den borttagna modellen inte förrän du indexera om dem. Vid omindexering känns ansikten med namnen i borttagna modellen inte igen av Video Indexer i din aktuella videor som har indexerats med hjälp av den modellen. Dessa ansikten kommer dock fortfarande identifieras. Aktuella videor som har indexerats med hjälp av modellen har tagits bort kommer nu att använda ditt konto standardmodell Person. Om ansikten från borttagna modellen namnges också i ditt konto standardmodellen, fortsätter de ansiktena ska identifieras i videor.

### <a name="request-url"></a>URL för begäran

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Nedan visas begäran i Curl.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>Begäranparametrar

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|id|sträng|Ja|Person modell-id (genereras när modellen Person skapas)|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Det finns inget returnerade innehåll när den Person-modellen har tagits bort.

## <a name="get-all-person-models"></a>Få alla Person modeller

Hämta alla Person modeller i det angivna kontot. 

### <a name="request-call"></a>Begärandeanrop

Det här är en GET-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Nedan visas begäran i Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>Begäranparametrar

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Svaret innehåller en lista över alla Person-modeller i ditt konto (inklusive Person standardmodellen i det angivna kontot) och var och en av deras namn och ID: n som följer formatet på exemplet nedan.

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

Du kan välja vilken modell som du vill använda ett videoklipp med hjälp av den **id** värdet för den Person-modellen för den **personModelId** parametern när [ladda upp en video till indexet](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) eller [omindexering en video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Uppdatera ett ansikte

Det här kommandot kan du uppdatera ett ansikte i videon med ett namn med ID: T för videon och ID: T för ansiktet. Den här sedan uppdaterar Person-modell som videon var associerad med vid uppladdning/indexering eller omindexering. Om ingen Person modell tilldelades uppdateras kontots standardmodell Person. 

När detta inträffar identifierar förekomster av samma ansikte i dina aktuella videor som delar samma Person modell. Igenkänning av ansikte i dina aktuella videor kan ta lite tid att börja gälla eftersom det här är en batchprocess.

Du kan uppdatera ett ansikte som Video Indexer kan identifieras som en kändisar med ett nytt namn. Det nya namnet som du ger företräde framför inbyggda kändisigenkänning.

### <a name="request-call"></a>Begärandeanrop

Det här är en POST-begäran.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Nedan visas begäran i Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Visa obligatoriska parametrar och testa med hjälp av Video Indexer Developer Portal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>Begäranparametrar

|**Namn**|**Typ**|**Krävs**|**Beskrivning**|
|---|---|---|---|
|location|sträng|Ja|Azure-regionen som anropet ska dirigeras. Mer information finns i [Azure-regioner och Video Indexer](regions.md).|
|accountId|sträng|Ja|Globalt unik identifierare för kontot|
|videoId|sträng|Ja|ID för videon där de står inför som du vill uppdatera visas. Detta skapas när videon har laddats upp och indexeras.|
|faceId|heltal|Ja|ID för ansiktet som ska uppdateras. Du kan hämta faceId från video indexet|
|accessToken|sträng|Ja|Åtkomst-token (måste vara av omfång [konto åtkomsttoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)) att autentisera mot anropet. Åtkomsttoken upphör att gälla inom 1 timme.|
|namn|sträng|Ja|Nytt namn för att uppdatera ansiktet med.|

Namnen är unika för Person modeller, så om du ger två olika ansikten i samma Person modellera samma **namn** parametervärde, Video Indexer visar ansiktena som samma person och konvergerar dem när du indexera om videon. 

### <a name="request-body"></a>Begärandetext

Det finns ingen ytterligare begärandetexten som krävs för det här anropet.

### <a name="response"></a>Svar

Det finns inget returnerade innehåll när de står inför har uppdaterats.

## <a name="next-steps"></a>Nästa steg

[Anpassa Person modell med Video Indexer-webbplats](customize-person-model-with-website.md)

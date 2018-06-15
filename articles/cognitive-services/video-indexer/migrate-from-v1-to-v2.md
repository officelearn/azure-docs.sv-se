---
title: Migrera från Azure Video indexeraren API v1 till v2 | Microsoft Docs
description: Det här avsnittet beskriver hur du migrerar från Azure Video indexeraren API v1 till v2.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/13/2018
ms.author: juliako
ms.openlocfilehash: 00f5a0d7c9a37c24866cd5e1259800d5e431ccc3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35356473"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Migrera från Video indexeraren API v1 till v2

> [!Note]
> API: er för Video indexeraren V1 är nu föråldrade och kommer att tas bort på 1 augusti 2018. Du bör börja använda API: er för Video indexeraren v2 för att undvika avbrott.
>
> För att utveckla med Video indexeraren v2 API: er finns i anvisningarna [här](https://api-portal.videoindexer.ai/). 

Den här artikeln beskriver ändringar som introducerades i v2.  

## <a name="api-changes"></a>API-ändringar

### <a name="authorization-and-operations"></a>Auktorisering och åtgärder

I version 2 ändras Video indexeraren modell för autentisering och auktorisering för API: et. Det finns två uppsättningar av API: er: 

* Auktorisering 
* Åtgärder

Den **auktorisering** API används för att erhålla åtkomsttoken för anropar den **Operations** API. Den **Operations** API innehåller alla de Video indexeraren API: er, till exempel överför video, få insikter och andra åtgärder.

När du [prenumerera](video-indexer-get-started.md) till den **auktorisering** API, kommer du att kunna erhålla åtkomsttoken genom att skicka din prenumeration nyckel (precis som du gjorde i v1.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Hämtar och använder åtkomsttoken för åtgärder API: er

När du anropar den **Operations** API: er, nyckeln prenumeration används inte längre. I stället åtkomsttoken som erhålls genom släpper den **auktorisering** API. 

Varje begäran ska ha en giltig token matchar åtkomstnivån för du anropar API: et. Till exempel kräva åtgärder för dina användare, till exempel komma dina konton, ett åtkomsttoken. Åtgärder på kontot nivå, till exempel lista alla videoklipp, kräver en konto åtkomst-token. Åtgärder på videor, som till exempel omindexera video kräver en konto åtkomst-token eller en video åtkomsttoken.

Om du vill göra det lättare kan du använda **auktorisering** API > **GetAccounts** att hämta token dina konton utan att hämta en användare först. Du kan också begära att få konton med ogiltig token så att du kan hoppa över en ytterligare anropa för att få en token för kontot.

Läs mer om olika åtkomsttoken [Använd Azure Video indexeraren API](video-indexer-use-apis.md).

### <a name="locations"></a>Platser

Varje anrop till API: N bör inkludera platsen för ditt konto Video indexeraren. API-anrop utan platsen eller med ett fel plats misslyckas.

De värden som beskrivs i följande tabell gäller. Den **Param värde** värdet du skickar när du använder API: et.

|**Namn**|**Param värde**|**Beskrivning**|
|---|---|---|
|Utvärdering|utvärderingsversion|Används för utvärderingskonton. Till exempel: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Västra USA|westus2|Används för Azure West US 2-region.  Till exempel: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Norra Europa |europanorra|Används för Azure Norra Europa region. Till exempel: https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Östasien|asienöstra|Används för Östasien för Azure-region. Till exempel: https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Datamodellen

Video indexeraren har nu en förenklad datamodell för att leverera mycket bättre insikter. Mer information om utdata som genereras av v2-API finns [granska Video indexeraren utdata som genereras av v2 API](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

Video indexeraren API-definitioner har uppdaterats i enlighet med detta och kan hämtas via den [API portal](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>V1 eller V2-exempel

Nya V2 API: er omfattar 3 huvudsakliga parametrar:

1. [Plats] - enligt beskrivningen ovan. Antingen utvärderingsversionen westus2, northeurope eller eastasia.
2. [YOUR_ACCOUNT_ID] - en Guid-id för ditt konto. Hämta när du hämtar alla konton (beskrivs nedan).
3. [YOUR_VIDEO_ID] - ID: t för videon (t.ex. ”d4fa369abc”). Returneras när du laddar upp en video eller vid sökning efter videor.

#### <a name="uploading-a-video-in-v1"></a>Ladda upp en video i V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>Ladda upp en video i V2:

1. Hämta ett åtkomsttoken för överför begäran:

  Du kan antingen få alla konton och deras åtkomst-token:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Eller hämta specifika kontoåtkomst-token:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Ladda upp en video:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Hämtar insikter i V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Hämtar insikter i V2:

1. Använd konto åtkomst-token eller skaffa en video åtkomst-token:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Få insikter:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Hämtning av video bearbetning status i V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Hämtning av video bearbetning status i V2:

I API-v2 returneras bearbetningstillstånd som en del av hämta Video Index API.

1. Använd konto åtkomst-token eller skaffa en video åtkomst-token:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Få insikter:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>Nästa steg

[Använd Azure Video indexeraren API](video-indexer-use-apis.md)


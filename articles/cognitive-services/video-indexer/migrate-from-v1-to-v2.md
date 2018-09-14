---
title: Migrera från Azure Video Indexer API v1 till v2 | Microsoft Docs
description: Det här avsnittet beskrivs hur du migrerar från Azure Video Indexer API v1 till v2.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 09/09/2018
ms.author: juliako
ms.openlocfilehash: 2237bde2fa69cf9b701e1c093f97516613cef0c6
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45544153"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Migrera från API för Videoindexering v1 till v2

> [!Note]
> Video Indexer V1 API upphörde den 1 augusti 2018. Nu bör du använda Video Indexer v2 API: et. <br/>Om du vill utveckla med API: er för Video Indexer-v2, se instruktionerna [här](https://api-portal.videoindexer.ai/). 

Den här artikeln redogörs för ändringar som introducerades i v2.  

## <a name="api-changes"></a>API-ändringar

### <a name="authorization-and-operations"></a>Auktorisering och åtgärder

Video Indexer ändra modellen för autentisering och auktorisering för API: et i version 2. Det finns två uppsättningar med API: er: 

* Auktorisering 
* Åtgärder

Den **auktorisering** API används för att erhålla åtkomsttoken att anropa den **Operations** API. Den **Operations** API innehåller alla Video Indexer API: erna, till exempel ladda upp video, ger information och andra åtgärder.

När du [prenumerera](video-indexer-get-started.md) till den **auktorisering** API, kommer du att kunna hämta åtkomsttoken genom att skicka din prenumerationsnyckel (precis som i v1.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Hämta, med åtkomsttoken för API: er

När du anropar den **Operations** API: er, prenumerationsnyckeln kommer inte längre användas. I stället du skickar de åtkomsttoken som erhålls genom att den **auktorisering** API. 

Varje begäran måste ha en giltig token som matchar åtkomstnivån som du anropar API. Exempelvis kan kräva åtgärder på dina användare, till exempel hämta dina konton, en åtkomsttoken för användaren. Åtgärder på kontot nivå, till exempel lista alla videor, kräver en åtkomsttoken för kontot. Åtgärder för videor såsom reindex video, kräver en åtkomsttoken för kontot eller en åtkomsttoken för video.

Om du vill göra det enklare, kan du använda **auktorisering** API > **GetAccounts** att hämta säkerhetstoken för dina konton utan att hämta en användare först. Du kan också begära att få konton med giltig token, så att du kan hoppa över en ytterligare anropet för att hämta en token för konto.

Läs mer om de olika åtkomst-token, [Använd Azure Video Indexer API](video-indexer-use-apis.md).

### <a name="locations"></a>Platser

Varje anrop till API: et bör inkludera platsen för din Video Indexer-kontot. API-anrop utan platsen eller med fel plats misslyckas.

De värden som beskrivs i tabellen nedan gäller. Den **Param värde** värdet som du skickar när du använder API: et.

|**Namn**|**Param värde**|**Beskrivning**|
|---|---|---|
|Utvärdering|utvärderingsversion|Används för utvärderingskonton. Till exempel: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Västra USA|västra USA 2|Används för Azure regionen västra USA 2.  Till exempel: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Norra Europa |europanorra|Används för regionen Azure Norra Europa. Till exempel: https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Östasien|asienöstra|Används för Östasien för Azure-region. Till exempel: https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Datamodell

Video Indexer har nu en förenklad datamodell för att leverera mycket tydligare insikter. Mer information om utdata som genereras av v2 API: et finns i [granska Video Indexer-utdata som genereras av v2 API: et](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

Video Indexer API-definitioner har uppdateras och kan hämtas via [Utvecklarportalen för Video Indexer](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>V1 eller V2-exempel

Den nya V2-API: er omfattar 3 huvudsakliga parametrar:

1. [Plats] - enligt beskrivningen ovan. Utvärdering, västra USA 2, europanorra eller asienöstra.
2. [YOUR_ACCOUNT_ID] – en Guid ID: t för ditt konto. Hämta vid hämtning av alla konton (beskrivs nedan).
3. [YOUR_VIDEO_ID] - ID: t för videon (t.ex. ”d4fa369abc”). Returneras när du laddar upp en video eller när du söker efter videor.

#### <a name="uploading-a-video-in-v1"></a>Ladda upp en video i V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>Ladda upp en video i V2:

1. Hämta en åtkomsttoken för överföringsförfrågan:

  Du kan antingen hämta alla konton och deras åtkomst-token:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Eller hämta det specifika konto åtkomsttoken:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Ladda upp en video:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Få insikter i V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Få insikter i V2:

1. Använd åtkomsttoken konto eller skaffa en video åtkomst-token:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Få insikter:

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Hämtning av statusen för bearbetning av livevideo i V1:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Hämtning av statusen för bearbetning av livevideo i V2:

I API v2 returneras bearbetning tillståndet som en del av den hämta Video-API-Index.

1. Använd åtkomsttoken konto eller skaffa en video åtkomst-token:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Få insikter:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>Nästa steg

[Använda Azure API för Videoindexering](video-indexer-use-apis.md)


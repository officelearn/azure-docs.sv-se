---
title: Uppgradera från API v5 till v7 - API för Bing-webbsökning
titleSuffix: Azure Cognitive Services
description: 'Bestämma vilka delar av ditt program kräver uppdateringar som använder API: er för webbsökning i Bing v7.'
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: reference
ms.date: 01/15/2017
ms.author: scottwhi
ms.openlocfilehash: 7c3e19fd809e442d58f7cb0e6922d4e565673fe2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188940"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Uppgradera från Bing Web Search API v5 till v7

Den här uppgraderingshandboken identifierar ändringar mellan version 5 och version 7 för Bing Web Search API. Använd den här guiden hjälper dig att identifiera delarna av programmet som du behöver uppdatera om du vill använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Versionsnumret för den slutpunkt som har ändrats från v5 till v7. Till exempel https:\/\/api.cognitive.microsoft.com/bing/**v7.0**  /Sök.

### <a name="error-response-objects-and-error-codes"></a>Fel svarsobjekt och felkoder

- Alla misslyckade begäranden ska nu innehålla en `ErrorResponse` objekt i svarstexten.

- Lagt till följande fält i den `Error` objekt.  
  - `subCode`&mdash;Partitionerar felkoden i diskreta buckets, om möjligt
  - `moreDetails`&mdash;Mer information om felet som beskrivs i den `message` fält


- Ersatt v5-felkoder med följande möjliga `code` och `subCode` värden.

|Kod|SubCode|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing returnerar ServerError när något av de underordnade kod inträffar. Svaret innehåller de här felen om HTTP-statuskoden är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran inte är giltig. Till exempel en obligatorisk parameter saknas eller ett parametervärde är inte giltig.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue, är HTTP-statuskod 400.<br/><br/>Om felet är HttpNotAllowed, HTTP-statuskod 410.
|RateLimitExceeded||Bing returnerar RateLimitExceeded varje gång du överskrider din frågor per sekund (QPS) eller frågor per månad (QPM) kvot.<br/><br/>Bing returnerar HTTP-statuskod 429 om du har överskridit Indexlagring och 403 om du har överskridit QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. Till exempel den `Ocp-Apim-Subscription-Key` huvud saknas eller prenumerationsnyckeln är inte giltig.<br/><br/>Redundans inträffar om du anger mer än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization, är HTTP-statuskod 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Det här felet kan inträffa om prenumerationsnyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization, är HTTP-statuskod 403.

- Följande mappar tidigare felkoder till de nya koderna. Om du har gått ett beroende på v5 felkoder, måste din kod uppdateras också.

|Versionskod 5|Version 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blockerad|InvalidRequest.Blocked


## <a name="non-breaking-changes"></a>Icke-ändringar  

### <a name="headers"></a>Rubriker

- Har lagts till det valfria [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#pragma) huvudet i begäran. Som standard returnerar Bing cachelagrat innehåll om det finns. Om du vill förhindra att Bing returnerar cachelagrat innehåll ska du ställa in huvudet Pragma på no-cache (till exempel Pragma: no-cache).

### <a name="query-parameters"></a>Frågeparametrar

- Har lagts till i [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) frågeparameter. Använd den här parametern om du vill ange antal svar som du vill att svaret ska ingå. Svaren är valt baserat på rangordning. Exempel: Om du ställer in den här parametern till tre (3), svaret innehåller de tre översta rankad svar.  

- Har lagts till i [marknadsföra](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) frågeparameter. Använd den här parametern tillsammans med `answerCount` att uttryckligen innehåller en eller flera svar typer, oavsett deras rangordning. Till exempel för att flytta upp videor och bilder i svaret, ange du uppgradera till *videor, bilder*. Listan över svar som du vill flytta upp räknas inte mot den `answerCount` gränsen. Till exempel om `answerCount` är 2 och `promote` är inställd på *videor, bilder*, svaret kan innehålla webbsidor, nyheter, videor och bilder.

### <a name="object-changes"></a>Objekt ändras

- Har lagts till i `someResultsRemoved` automatiskt till den [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) objekt. Fältet innehåller ett booleskt värde som anger om svaret exkluderad vissa resultat från web-svaret.  

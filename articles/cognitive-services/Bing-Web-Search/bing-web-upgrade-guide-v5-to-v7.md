---
title: Uppgradera från Bing Web Sök API v5 till v7 | Microsoft Docs
description: Identifierar delarna av programmet som du behöver uppdatera om du vill använda version 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/15/2017
ms.author: scottwhi
ms.openlocfilehash: 155297f230c0ee02d6fa49d6d35eb24d9941f29b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354531"
---
# <a name="web-search-api-upgrade-guide"></a>Uppgraderingshandboken för Web API-sökning

Den här uppgraderingshandboken identifierar ändringar mellan version 5 och version 7 för Bing webb-API för sökning. Använd den här guiden för att identifiera delarna av programmet som du behöver uppdatera om du vill använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Versionsnummer för slutpunkten har ändrats från v5 till v7. Till exempel https:\/\/api.cognitive.microsoft.com/bing/**v7.0**  /Sök.

### <a name="error-response-objects-and-error-codes"></a>Fel svar objekt och felkoder

- Alla misslyckade begäranden innehåller nu ett `ErrorResponse` -objekt på brödtext för svar.

- Lägga till följande fält till den `Error` objekt.  
  - `subCode`&mdash;Partitionerar felkoden till diskreta buckets, om möjligt
  - `moreDetails`&mdash;Mer information om fel som beskrivs i den `message` fält
   

- Ersatt felkoder v5 med följande möjliga `code` och `subCode` värden.

|Kod|Delkoden|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing returnerar ServerError när något av de underordnade kod inträffar. Svaret innehåller dessa fel om HTTP-statuskoden är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran inte är giltig. Till exempel en obligatorisk parameter saknas eller ett parametervärde är inte giltig.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue, är HTTP-statuskoden 400.<br/><br/>Om felet är HttpNotAllowed, felkod HTTP-status 410.
|RateLimitExceeded||Bing returnerar RateLimitExceeded när du överskrider din frågor per sekund (QPS) eller frågor per månad (QPM) kvoten.<br/><br/>Bing returnerar HTTP-statuskod 429 om du har överskridit QPS och 403 om du har överskridit QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan verifiera anroparen. Till exempel den `Ocp-Apim-Subscription-Key` huvud saknas eller nyckeln prenumerationen är inte giltigt.<br/><br/>Redundans inträffar om du anger fler än en autentiseringsmetod.<br/><br/>Om felet InvalidAuthorization är 401 HTTP-statuskoden.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Det här felet kan inträffa om nyckeln för prenumerationen har inaktiverats eller har upphört att gälla. <br/><br/>Om felet InsufficientAuthorization är 403 HTTP-statuskoden.

- Följande mappar tidigare felkoder till de nya koderna. Om du har gjort ett beroende på v5 felkoder, måste din kod uppdateras också.

|Version 5-kod|Version 7 code.subCode
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


## <a name="non-breaking-changes"></a>Hårt ändringar  

### <a name="headers"></a>Sidhuvuden

- Lägga till valfria [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#pragma) huvudet i begäran. Som standard returnerar Bing cachelagrat innehåll om det är tillgängligt. Ange rubriken Pragma till no cache för att förhindra att Bing returnerar cachelagrat innehåll (till exempel Pragma: no-cache).

### <a name="query-parameters"></a>Frågeparametrar

- Lägga till den [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount) Frågeparametern. Använd den här parametern om du vill ange antal svar som du vill att svaret ska inkludera. Svaren är valt baserat på rangordning. Om du anger den här parametern exempelvis tre (3), svaret innehåller översta tre ranked svar.  
  
- Lägga till den [befordra](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote) Frågeparametern. Använd den här parametern tillsammans med `answerCount` att inkludera en eller flera svar typer, oavsett deras rangordning explicit. Till exempel om du vill höja videor och bilder i svaret, anger du befordra *videor, bilder*. Lista över svar som du vill flytta upp räknas inte mot den `answerCount` gränsen. Till exempel om `answerCount` är 2 och `promote` är inställd på *videor, bilder*, svaret kan innehålla webbsidor, nyheter, videor och bilder.

### <a name="object-changes"></a>Objekt ändras

- Lägga till den `someResultsRemoved` automatiskt till den [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) objekt. Fältet innehåller ett booleskt värde som anger om svaret exkluderad några resultat från webben svaret.  


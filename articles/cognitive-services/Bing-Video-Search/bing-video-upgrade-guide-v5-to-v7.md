---
title: Uppgradera Bing Video API v5 till v7 | Microsoft Docs
description: Identifierar delarna av programmet som du behöver uppdatera om du vill använda version 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: FA7DDF07-97AC-4EBE-8C50-A9737D43B38E
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 62646d026e141d0549c68e18f9318fa32d3e00df
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351639"
---
# <a name="video-search-api-upgrade-guide"></a>Video uppgraderingshandboken för Sök-API

Den här uppgraderingshandboken identifierar ändringar mellan version 5 och version 7 för Bing Video Sök-API. Använd den här guiden för att identifiera delarna av programmet som du behöver uppdatera om du vill använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Versionsnummer för slutpunkten har ändrats från v5 till v7. Till exempel https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/videos/search.

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
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om nyckeln för prenumerationen har inaktiverats eller har upphört att gälla. <br/><br/>Om felet InsufficientAuthorization är 403 HTTP-statuskoden.

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

### <a name="query-parameters"></a>Frågeparametrar

- Byta namn på den `modulesRequested` Frågeparametern till [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Objekt ändras

- Byta namn på den `nextOffsetAddCount` i [videor](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) till `nextOffset`. Hur du använder förskjutningen har också ändrats. Tidigare ska ställas in på [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset) Frågeparametern till den `nextOffset` värde plus värdet för föregående förskjutningen plus antal videor i resultatet. Nu kan du bara ange den `offset` Frågeparametern till den `nextOffset` värde.  
  
- Ändra datatypen för den `relatedVideos` från `Video[]` till [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videosmodule) (se [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videodetails)).


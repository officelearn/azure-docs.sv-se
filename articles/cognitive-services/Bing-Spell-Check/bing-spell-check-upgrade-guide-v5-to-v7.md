---
title: Uppgradera Bing Spell Check API v5 till v7
titlesuffix: Azure Cognitive Services
description: Identifierar delarna av programmet som du behöver uppdatera om du vill använda version 7.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 5e9ae0f7affe563d69fd2a55daeaf03a230c03c7
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55202951"
---
# <a name="spell-check-api-upgrade-guide"></a>Stavningskontrolls-API: er för Uppgraderingsguide

Den här uppgraderingshandboken identifierar ändringar mellan version 5 och version 7 av den stavningskontroll i Bing. Använd den här guiden hjälper dig att identifiera delarna av programmet som du behöver uppdatera om du vill använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Versionsnumret för den slutpunkt som har ändrats från v5 till v7. Till exempel `https://api.cognitive.microsoft.com/bing/v7.0/spellcheck`.

### <a name="error-response-objects-and-error-codes"></a>Fel svarsobjekt och felkoder

- Alla misslyckade begäranden ska nu innehålla en `ErrorResponse` objekt i svarstexten.

- Lagt till följande fält i den `Error` objekt.  
  - `subCode`&mdash;Partitionerar felkoden i diskreta buckets, om möjligt
  - `moreDetails`&mdash;Mer information om felet som beskrivs i den `message` fält
   

- Ersatt v5-felkoder med följande möjliga `code` och `subCode` värden.  
  
|Kod|SubCode|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing returnerar ServerError när något av de obligatorisk inträffar. Svaret innehåller de här felen om HTTP-statuskoden är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran är inte giltig. Till exempel en obligatorisk parameter saknas eller ett parametervärde är inte giltig.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue, är HTTP-statuskod 400.<br/><br/>Om felet är HttpNotAllowed, HTTP-statuskod 410.
|RateLimitExceeded||Bing returnerar RateLimitExceeded varje gång du överskrider din frågor per sekund (QPS) eller frågor per månad (QPM) kvot.<br/><br/>Bing returnerar HTTP-statuskod 429 om du har överskridit Indexlagring och 403 om du har överskridit QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. Till exempel den `Ocp-Apim-Subscription-Key` huvud saknas eller prenumerationsnyckeln är inte giltig.<br/><br/>Redundans inträffar om du anger mer än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization, är HTTP-statuskod 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om prenumerationsnyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization, är HTTP-statuskod 403.

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

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Användnings- och visningskrav](./UseAndDisplayRequirements.md)

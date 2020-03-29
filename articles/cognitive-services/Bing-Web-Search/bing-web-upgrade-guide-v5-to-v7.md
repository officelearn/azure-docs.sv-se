---
title: Uppgradera från API v5 till v7 - API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Ta reda på vilka delar av programmet som kräver uppdateringar för att använda API:erna för Bing Web Search v7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 2133cd59c524112ae8a77c0a20cbce1d1336a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881304"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Uppgradera från Bing Web Search API v5 till v7

Den här uppgraderingsguiden identifierar ändringarna mellan version 5 och version 7 av API:et för webbsökning på Bing. Använd den här guiden för att identifiera de delar av programmet som du behöver uppdatera för att använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Slutpunktens versionsnummer har ändrats från v5 till v7. Till exempel https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Felsvarsobjekt och felkoder

- Alla misslyckade begäranden `ErrorResponse` ska nu innehålla ett objekt i svarstexten.

- Lade till följande `Error` fält i objektet.  
  - `subCode`&mdash;Partitionerar felkoden i diskreta buckets, om möjligt
  - `moreDetails`&mdash;Ytterligare information om felet `message` som beskrivs i fältet


- Ersatte v5-felkoderna med `code` `subCode` följande möjliga och värden.

|Kod|Underkod|Beskrivning
|-|-|-
|ServerError (ServerError)|Oväntatare<br/>ResourceError (ResourceError)<br/>Har inte implementerats|Bing returnerar ServerError när något av underkodsvillkoren inträffar. Svaret innehåller dessa fel om HTTP-statuskoden är 500.
|Ogiltigt begärs|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran inte är giltig. En obligatorisk parameter saknas till exempel eller så är ett parametervärde ogiltigt.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue är HTTP-statuskoden 400.<br/><br/>Om felet är HttpNotAllowed, HTTP-statuskoden 410.
|RateLimitExceed||Bing returnerar RateLimitExceed när du överskrider dina frågor per sekund (QPS) eller frågor per månad (QPM) kvot.<br/><br/>Bing returnerar HTTP-statuskod 429 om du överskridit QPS och 403 om du överskridit QPM.
|Ogiltiga myndigheter|Tillståndstagande<br/>TillståndSbrott|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. `Ocp-Apim-Subscription-Key` Huvudet saknas till exempel eller så är prenumerationsnyckeln ogiltig.<br/><br/>Redundans inträffar om du anger mer än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization är HTTP-statuskoden 401.
|Otillräcklig auktorisering|AuktoriseringDisabled<br/>Auktoriseringexpirerad|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Det här felet kan uppstå om prenumerationsnyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization är HTTP-statuskoden 403.

- Följande mappar de tidigare felkoderna till de nya koderna. Om du har varit beroende av v5-felkoder uppdaterar du koden därefter.

|Version 5-kod|Version 7 code.subCode
|-|-
|BegäranParameterMissing|OgiltigtQuest.parametermissing
BegäranParameterInvalidValue|OgiltigtQuest.parameterInvalidvärde
ResurserAccessDenied|Otillräcklig auktorisering
Överskredvolym|RateLimitExceed
Överskridit QpsLimit|RateLimitExceed
Disabled|Otillräcklig auktorisering.AuthorizationDisabled
Oväntatare|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
Tillståndstagande|Ogiltigauktorisering.AuthorizationMissing
HttpNotAllowed|InvalidRequest.httpNotAllowed
UserAgentMissing|OgiltigtQuest.parametermissing
Har inte implementerats|ServerError.Inte implementerad
Ogiltiga myndigheter|Ogiltiga myndigheter
InvalidAuthorizationMethod|Ogiltiga myndigheter
MultiAuthorizationMethod|Ogiltiga myndigheter.AuthorizationRedundancy
ExpiredAuthorizationToken|Otillräcklig auktorisering.AuthorizationExpirerad
Otillräckligtscope|Otillräcklig auktorisering
Blockerad|Ogiltigrequest.Blockerad


## <a name="non-breaking-changes"></a>Icke-brytande ändringar  

### <a name="headers"></a>Rubriker

- Lade till [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) det valfria pragma-begäranden. Som standard returnerar Bing cachelagrat innehåll om det finns. Om du vill förhindra att Bing returnerar cachelagrat innehåll ska du ställa in huvudet Pragma på no-cache (till exempel Pragma: no-cache).

### <a name="query-parameters"></a>Frågeparametrar

- Lade till frågeparametern [answerCount.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) Använd den här parametern för att ange hur många svar som du vill att svaret ska inkludera. Svaren väljs baserat på rangordning. Om du till exempel anger den här parametern till tre (3) innehåller svaret de tre högst rankade svaren.  

- Lade till parametern [befordra](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) fråga. Använd den här `answerCount` parametern tillsammans med för att uttryckligen inkludera en eller flera svarstyper, oavsett rangordning. Om du till exempel vill marknadsföra videor och bilder till svaret ställer du in marknadsföringen på *videor,bilder*. Listan över svar som du vill marknadsföra `answerCount` räknas inte mot gränsen. Om det `answerCount` till exempel `promote` är 2 och är inställt på *videor,bilder*kan svaret innehålla webbsidor, nyheter, videor och bilder.

### <a name="object-changes"></a>Objektändringar

- Lade `someResultsRemoved` till fältet i [WebAnswer-objektet.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) Fältet innehåller ett booleskt värde som anger om svaret uteslöt vissa resultat från webbsvaret.  

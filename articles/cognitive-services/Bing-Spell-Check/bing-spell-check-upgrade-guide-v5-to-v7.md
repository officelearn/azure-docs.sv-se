---
title: Uppgradera Bing Spell Check API v5 till v7
titleSuffix: Azure Cognitive Services
description: Identifierar de delar av programmet som du behöver uppdatera för att använda version 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 545772a28a67310b12eb55cd1fb14e8d12a95a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68500824"
---
# <a name="spell-check-api-upgrade-guide"></a>Uppgraderingsguide för Stavningskontroll av API

Den här uppgraderingsguiden identifierar ändringarna mellan version 5 och version 7 av API:et för stavningskontroll av Bing. Använd den här guiden för att identifiera de delar av programmet som du behöver uppdatera för att använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Slutpunktens versionsnummer ändrades från v5 till v7. Till exempel `https://api.cognitive.microsoft.com/bing/v7.0/spellcheck`.

### <a name="error-response-objects-and-error-codes"></a>Felsvarsobjekt och felkoder

- Alla misslyckade begäranden `ErrorResponse` ska nu innehålla ett objekt i svarstexten.

- Lade till följande `Error` fält i objektet.  
  - `subCode`&mdash;Partitionerar felkoden i diskreta buckets, om möjligt
  - `moreDetails`&mdash;Ytterligare information om felet `message` som beskrivs i fältet
   

- Ersatte v5-felkoderna med `code` `subCode` följande möjliga och värden.  
  
|Kod|Underkod|Beskrivning
|-|-|-
|ServerError (ServerError)|Oväntatare<br/>ResourceError (ResourceError)<br/>Har inte implementerats|Bing returnerar ServerError när något av underkodsvillkoren inträffar. Svaret innehåller dessa fel om HTTP-statuskoden är 500.
|Ogiltigt begärs|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran inte är giltig. En obligatorisk parameter saknas till exempel eller så är ett parametervärde inte giltigt.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue är HTTP-statuskoden 400.<br/><br/>Om felet är HttpNotAllowed, HTTP-statuskoden 410.
|RateLimitExceed||Bing returnerar RateLimitExceed när du överskrider dina frågor per sekund (QPS) eller frågor per månad (QPM) kvot.<br/><br/>Bing returnerar HTTP-statuskod 429 om du överskridit QPS och 403 om du överskridit QPM.
|Ogiltiga myndigheter|Tillståndstagande<br/>TillståndSbrott|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. `Ocp-Apim-Subscription-Key` Huvudet saknas till exempel eller så är prenumerationsnyckeln ogiltig.<br/><br/>Redundans inträffar om du anger mer än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization är HTTP-statuskoden 401.
|Otillräcklig auktorisering|AuktoriseringDisabled<br/>Auktoriseringexpirerad|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om prenumerationsnyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization är HTTP-statuskoden 403.

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

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Användnings- och visningskrav](./UseAndDisplayRequirements.md)

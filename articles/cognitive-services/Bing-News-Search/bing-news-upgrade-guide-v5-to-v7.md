---
title: Uppgradera Bing News Search API v5 till v7
titleSuffix: Azure Cognitive Services
description: Identifierar de delar av programmet som du behöver uppdatera för att använda version 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: bad0ef849af7c94e63f1dfbebda7f47caef9947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294369"
---
# <a name="news-search-api-upgrade-guide"></a>Uppgraderingsguide för API-uppgradering för nyheter

Den här uppgraderingsguiden identifierar ändringarna mellan version 5 och version 7 av API:et för sökning i Bing-nyheter. Använd den här guiden för att identifiera de delar av programmet som du behöver uppdatera för att använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Slutpunktens versionsnummer ändrades från v5 till v7. Till exempel `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

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

### <a name="object-changes"></a>Objektändringar

- Lade `contractualRules` till fältet i [NewsArticle-objektet.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) Fältet `contractualRules` innehåller en lista med regler som du måste följa (till exempel artikelattribution). Du måste använda attributionen `contractualRules` i `provider`stället för att använda . Artikeln innehåller `contractualRules` endast när [API-svaret](../bing-web-search/search-the-web.md) för webbsökning innehåller ett nyhetssvar.

## <a name="non-breaking-changes"></a>Icke-brytande ändringar

### <a name="query-parameters"></a>Frågeparametrar

- Lade till produkter som ett möjligt [category](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) värde som du kan ange kategorifrågeparametern till. Se [kategorier efter marknader](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- Lade till parametern [SortBy,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) som returnerar trendämnen sorterade efter datum med den senaste först.

- Lade [Since](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) till sedan-frågeparametern, som returnerar populära ämnen som upptäcktes av Bing på eller efter den angivna Tidsstämpeln Unix epoch.

### <a name="object-changes"></a>Objektändringar

- Lade `mentions` till fältet i [NewsArticle-objektet.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) Fältet `mentions` innehåller en lista över entiteter (personer eller platser) som hittades i artikeln.

- Lade `video` till fältet i [NewsArticle-objektet.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) Fältet `video` innehåller en video som är relaterad till nyhetsartikeln. Videon är antingen \<en\> iframe som du kan bädda in eller en rörelseminiatyr.

- Lade `sort` till fältet i [nyhetsobjektet.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) Fältet `sort` visar artiklarnas sorteringsordning. Artiklarna sorteras till exempel efter relevans (standard) eller datum.

- Lade till [sortvärdeobjektet,](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) som definierar en sorteringsordning. Fältet `isSelected` anger om svaret använde sorteringsordningen. Om **värdet är true**användes sorteringsordningen. Om `isSelected` det är **falskt**kan du `url` använda URL:en i fältet för att begära en annan sorteringsordning.

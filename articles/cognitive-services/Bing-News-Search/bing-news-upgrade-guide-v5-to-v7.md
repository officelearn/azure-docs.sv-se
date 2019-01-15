---
title: Uppgradera för nyhetssökning i Bing v5 till v7
titlesuffix: Azure Cognitive Services
description: Identifierar delarna av programmet som du behöver uppdatera om du vill använda version 7.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 46551680937eddef898739fb57b671a0918f2338
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259299"
---
# <a name="news-search-api-upgrade-guide"></a>Uppgraderingsguide för Nyheter Sök-API

Den här uppgraderingshandboken identifierar ändringar mellan version 5 och version 7 av den nyhetssökning i Bing. Använd den här guiden hjälper dig att identifiera delarna av programmet som du behöver uppdatera om du vill använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Versionsnumret för den slutpunkt som har ändrats från v5 till v7. Till exempel https://api.cognitive.microsoft.com/bing/ **v7.0**  /news/search.

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

### <a name="object-changes"></a>Objekt ändras

- Har lagts till i `contractualRules` automatiskt till den [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objekt. Den `contractualRules` fältet innehåller en lista över regler som du måste följa (exempelvis artikeln attribution). Måste du använda den information som anges i `contractualRules` istället för att använda `provider`. Artikeln innehåller `contractualRules` endast när den [API för webbsökning](../bing-web-search/search-the-web.md) svaret innehåller ett nytt svar.

## <a name="non-breaking-changes"></a>Bakåtkompatibla ändringar

### <a name="query-parameters"></a>Frågeparametrar

- Lagt till produkter som ett möjligt värde som du kan ställa in den [kategori](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) frågeparameter till. Se [kategorier av marknader](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#categories-by-market).

- Har lagts till i [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortby) frågeparameter som returnerar populära ämnen sorterade efter datum med det senaste först.

- Har lagts till i [eftersom](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#since) frågeparameter som returnerar populära ämnen som har identifierats av Bing på eller efter den angivna Unix-tidsstämpeln för epoch.

### <a name="object-changes"></a>Objekt ändras

- Har lagts till i `mentions` automatiskt till den [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objekt. Den `mentions` fältet innehåller en lista över entiteter (personer eller platser) som hittades i artikeln.

- Har lagts till i `video` automatiskt till den [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objekt. Den `video` fältet innehåller en video som är relaterad till nyhetsartikeln. Videon är antingen en \<iframe\> som du kan bädda in eller en miniatyr i rörelse.

- Har lagts till i `sort` automatiskt till den [nyheter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) objekt. Den `sort` fältet visas sorteringsordningen av artiklarna. Till exempel sorteras artiklar efter relevans (standard) eller datum.

- Har lagts till i [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortvalue) objekt som definierar en sorteringsordning. Den `isSelected` fältet som anger om svaret användas sorteringsordning. Om **SANT**, svaret används sorteringsordning. Om `isSelected` är **FALSKT**, du kan använda URL: en i den `url` fält som du vill begära en annan sorteringsordning.

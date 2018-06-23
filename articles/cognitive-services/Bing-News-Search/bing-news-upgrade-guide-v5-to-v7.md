---
title: Uppgradera Bing News Search API v5 till v7 | Microsoft Docs
description: Identifierar delarna av programmet som du behöver uppdatera om du vill använda version 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 5334C475-4841-4736-A66E-DC1E07CBCEC9
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: baed6f0091ddad40b4802c0fb52dc2ca1818cd03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351762"
---
# <a name="news-search-api-upgrade-guide"></a>Nyheter Sök API Uppgraderingsguide

Den här uppgraderingshandboken identifierar ändringar mellan version 5 och version 7 för Bing News Sök-API. Använd den här guiden för att identifiera delarna av programmet som du behöver uppdatera om du vill använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Versionsnummer för slutpunkten har ändrats från v5 till v7. Till exempel https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/news/search.

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

### <a name="object-changes"></a>Objekt ändras

- Lägga till den `contractualRules` automatiskt till den [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objekt. Den `contractualRules` fältet innehåller en lista över regler som du måste följa (till exempel artikel information). Du måste använda den information som anges i `contractualRules` istället för att använda `provider`. Artikeln innehåller `contractualRules` endast när den [Web Sök API](../bing-web-search/search-the-web.md) svaret innehåller svar en andra gång.

## <a name="non-breaking-changes"></a>Hårt ändringar

### <a name="query-parameters"></a>Frågeparametrar

- Lägga till produkter som ett möjligt värde som du kan ange den [kategori](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#category) Frågeparametern till. Se [kategorier av marknader](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#categories-by-market).  
    
- Lägga till den [SortBy](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortby) frågeparameter som returnerar trender avsnitt sorteras efter datum med den senaste först.  
  
- Lägga till den [eftersom](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#since) frågeparameter som returnerar trender ämnen som har identifierats av Bing på eller efter den angivna Unix epok tidsstämpeln.

### <a name="object-changes"></a>Objekt ändras

- Lägga till den `mentions` automatiskt till den [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objekt. Den `mentions` fältet innehåller en lista över enheter (personer eller platser) som hittades i artikeln.  
  
- Lägga till den `video` automatiskt till den [NewsArticle](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) objekt. Den `video` fältet innehåller en video som är relaterade till nyhetsartikeln. Videon är antingen en \<iframe\> som du kan bädda in eller en rörelse miniatyr.   
  
- Lägga till den `sort` automatiskt till den [nyheter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) objekt. Den `sort` fältet visas sorteringsordningen för artiklar. Till exempel sorteras artiklar efter relevans (standard) eller datum.  
  
- Lägga till den [SortValue](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#sortvalue) -objekt som definierar en sorteringsordning. Den `isSelected` fältet som anger om svaret användas sorteringsordning. Om **SANT**, svaret används sorteringsordning. Om `isSelected` är **FALSKT**, du kan använda URL-Adressen i det `url` fält som du vill begära en annan sorteringsordning.

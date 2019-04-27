---
title: Uppgradera från Bing bild Search API v5 till v7
titleSuffix: Azure Cognitive Services
description: Den här uppgraderingshandboken redogörs för ändringar mellan version 5 och version 7 av den bildsökning i Bing. Använd den här guiden hjälper dig att identifiera delarna av programmet som du behöver uppdatera om du vill använda version 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 123c5556dc76b35cf4a6b4b34e0c3e2fe437cebe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60635575"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Uppgraderingsguide för bildsökning i Bing v7

Den här uppgraderingshandboken identifierar ändringar mellan version 5 och version 7 av den bildsökning i Bing. Använd den här guiden hjälper dig att identifiera delarna av programmet som du behöver uppdatera om du vill använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Versionsnumret för den slutpunkt som har ändrats från v5 till v7. Till exempel https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

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



### <a name="query-parameters"></a>Frågeparametrar

- Byta namn på den `modulesRequested` frågeparameter till [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).  

- Namn på anteckningar till taggar. Se [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) frågeparameter för taggar.  

- Ändra listan över marknader som stöds av filtervärdet ShoppingSources till endast en-US. Se [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Bild insikter ändringar

- Byta namn på den `annotations` i [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) till `imageTags`.  

- Byta namn på den `AnnotationModule` objektet till [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  

- Byta namn på den `Annotation` objektet till [taggen](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag), och tas bort den `confidence` fält.  

- Byta namn på den `insightsSourcesSummary` i den [bild](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objekt till `insightsMetadata`.  

- Byta namn på den `InsightsSourcesSummary` objektet till [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  

- Har lagts till i `https://api.cognitive.microsoft.com/bing/v7.0/images/details` slutpunkt. Använd denna slutpunkt för information om begäran bilder i stället för search-slutpunkt/bilder /. Se [bild insikter](./image-insights.md).

- Följande frågeparametrar är nu giltiga endast med den `/images/details` slutpunkt.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  

- Byta namn på den `ImageInsightsResponse` objektet till [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  

- Ändra datatyperna för följande fält i den [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) objekt.  

    -   Ändrat typ av den `relatedCollections` från `ImageGallery[]` till [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Ändrat typ av den `pagesIncluding` från `Image[]` till [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Ändrat typ av den `relatedSearches` från `Query[]` till [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Ändrat typ av den `recipes` från `Recipe[]` till [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  

    -   Ändrat typ av den `visuallySimilarImages` från `Image[]` till [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Ändrat typ av den `visuallySimilarProducts` från `ProductSummaryImage[]` till [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  

    -   Tas bort den `ProductSummaryImage` objekt och flyttat produktrelaterad fälten i den [bild](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objekt. Den `Image` objekt innehåller fälten produktrelaterad endast när avbildningen ingår som en del av snarlika produkter i en bild insight-svar.  

    -   Ändrat typ av den `recognizedEntityGroups` från `RecognizedEntityGroup[]` till [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Byta namn på den `categoryClassification` i [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) till `annotations`, och ändras dess typ till `AnnotationsModule`.  

### <a name="images-answer"></a>Svara på avbildningar

-   Ta bort fälten displayShoppingSourcesBadges och displayRecipeSourcesBadges från [avbildningar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  

-   Byta namn på den `nextOffsetAddCount` i [avbildningar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) till `nextOffset`. Hur du använder förskjutningen har också ändrats. Tidigare var du anger den [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) frågeparameter till den `nextOffsetAddCount` värdet plus den föregående intervallförskjutning plus antalet bilder i resultatet. Nu kan du ställa in `offset` till den `nextOffset` värde.  


## <a name="non-breaking-changes"></a>Icke-ändringar

### <a name="query-parameters"></a>Frågeparametrar

- Lagt till Transparent som ett möjligt [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) filtervärde. Transparent filtret returnerar endast bilder med genomskinlig bakgrund.

- Lagt till den som ett möjligt [licens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) filtervärde. Alla filtret returnerar endast de avbildningar som är under licens.

- Har lagts till i [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) och [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) Frågeparametrar. Använd dessa filter om du vill returnera bilder i en mängd filstorlekar.  

- Har lagts till i [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth), [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) Frågeparametrar. Använd dessa filter om du vill returnera bilder inom ett intervall med höjd och bredd.  

### <a name="object-changes"></a>Objekt ändras

- Har lagts till i `description` och `lastUpdated` fält till den [erbjuder](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) objekt.  

- Har lagts till i `name` automatiskt till den [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) objekt.  

- Lagt till `similarTerms` till den [avbildningar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objekt. Det här fältet innehåller en lista med termer som liknar varandra i vilket innebär att användarens frågesträngen.  

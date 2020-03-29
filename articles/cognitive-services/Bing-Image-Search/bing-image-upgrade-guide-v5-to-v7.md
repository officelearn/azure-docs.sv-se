---
title: Uppgradera från API för bildsökning i Bing v5 till v7
titleSuffix: Azure Cognitive Services
description: I den här uppgraderingsguiden beskrivs ändringar mellan version 5 och version 7 av API:et för bildsökning av Bing. Använd den här guiden för att identifiera de delar av programmet som du behöver uppdatera för att använda version 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: c4c6b95996206cfb38ea3f77b89c3ebe3c2c0026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883490"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Bing Image Search API v7 uppgraderingsguide

Den här uppgraderingsguiden identifierar ändringarna mellan version 5 och version 7 av API:et för bildsökning av Bing. Använd den här guiden för att identifiera de delar av programmet som du behöver uppdatera för att använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Slutpunktens versionsnummer ändrades från v5 till v7. Till exempel https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

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



### <a name="query-parameters"></a>Frågeparametrar

- Omdöpt `modulesRequested` frågeparametern till [moduler](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Bytte namn på anteckningarna till taggar. Se frågeparametern [moduler](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) till taggar.  

- Ändrade listan över marknader som stöds av filtret ShoppingSources endast till en-US. Se [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Bildinsikter ändras

- Bytt namn `annotations` på fältet för `imageTags` [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) till .  

- Bytte namn `AnnotationModule` till [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- Bytte namn `Annotation` på objektet till `confidence` [Tagg](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag)och tog bort fältet.  

- Bytte namn `insightsSourcesSummary` på fältet för `insightsMetadata` [bildobjektet](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) till .  

- Bytte namn `InsightsSourcesSummary` till [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Lade `https://api.cognitive.microsoft.com/bing/v7.0/images/details` till slutpunkten. Använd den här slutpunkten för att begära bildinsikter i stället för slutpunkten /images/search. Se [Bildinsikter](./image-insights.md).

- Följande frågeparametrar är nu `/images/details` endast giltiga med slutpunkten.  

    -   [insikterToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [Modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl (på)](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [Cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [Cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [Katt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [Ct](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Bytte namn `ImageInsightsResponse` på objektet till [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Ändrade datatyperna för följande fält i [ImageInsights-objektet.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights)  

    -   Ändrade typen av `relatedCollections` fält `ImageGallery[]` från till [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Ändrade typen av `pagesIncluding` fält `Image[]` från till [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Ändrade typen av `relatedSearches` fält `Query[]` från till [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Ändrade typen av `recipes` fält `Recipe[]` från till [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Ändrade typen av `visuallySimilarImages` fält `Image[]` från till [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Ändrade typen av `visuallySimilarProducts` fält `ProductSummaryImage[]` från till [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Tog `ProductSummaryImage` bort objektet och flyttade de produktrelaterade fälten till [bildobjektet.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) Objektet `Image` innehåller endast produktrelaterade fält när bilden ingår som en del av visuellt liknande produkter i ett bildinsiktssvar.  

    -   Ändrade typen av `recognizedEntityGroups` fält `RecognizedEntityGroup[]` från till [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Bytte namn `categoryClassification` på fältet [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) till `annotations`och `AnnotationsModule`ändrade dess typ till .  

### <a name="images-answer"></a>Bilder svar

-   Tog bort fälten displayShoppingSourcesBadges och displayRecipeSourcesBadges från [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Bytte namn `nextOffsetAddCount` på fältet `nextOffset` [Bilder](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) till . Hur du använder förskjutningen har också ändrats. Tidigare har du angett [parametern förskjutningsfråga](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) till `nextOffsetAddCount` värdet plus föregående förskjutningsvärde plus antalet bilder i resultatet. Nu ställer `offset` du `nextOffset` in värdet.  


## <a name="non-breaking-changes"></a>Icke-brytande ändringar

### <a name="query-parameters"></a>Frågeparametrar

- Lade till Transparent [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) som ett möjligt imageType-filtervärde. Filtret Genomskinlig returnerar bara bilder med en genomskinlig bakgrund.

- Lade till värdet Alla som ett möjligt [licensfilter.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) Alla filter returnerar endast bilder som är under licens.

- Lade till frågeparametrarna [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) och [minFileSize.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) Använd dessa filter för att returnera bilder inom en rad filstorlekar.  

- Lade till frågeparametrarna [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [minWidth.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) Använd dessa filter för att returnera bilder inom en rad höjder och bredder.  

### <a name="object-changes"></a>Objektändringar

- Lade `description` till `lastUpdated` fälten och i [offerobjektet.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer)  

- Lade `name` till fältet i [ImageGallery-objektet.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery)  

- Har `similarTerms` lagts till i objektet [Bilder.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) Det här fältet innehåller en lista med termer som liknar användarens frågesträng.  

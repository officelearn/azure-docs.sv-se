---
title: Uppgradera Bing bild Sök API v5 till v7 | Microsoft Docs
description: Identifierar delarna av programmet som du behöver uppdatera om du vill använda version 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b4d785eafe9ced6fb12e2dac3282dfd286849eb6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351828"
---
# <a name="image-search-api-upgrade-guide"></a>Uppgraderingshandboken för avbildningen Sök-API

Den här uppgraderingshandboken identifierar ändringar mellan version 5 och version 7 för Bing avbildningen Sök-API. Använd den här guiden för att identifiera delarna av programmet som du behöver uppdatera om du vill använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Versionsnummer för slutpunkten har ändrats från v5 till v7. Till exempel https://api.cognitive.microsoft.com/bing/\ * \*v7.0**/images/search.

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

- Byta namn på den `modulesRequested` Frågeparametern till [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules).  
  
- Byta namn på anteckningarna till taggar. Se [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules) Frågeparametern till taggar.  

- Ändra listan över stöds marknader filtervärdet ShoppingSources till en-US endast. Se [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  
 

### <a name="image-insights-changes"></a>Bilden insikter ändras
  
- Byta namn på den `annotations` i [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) till `imageTags`.  
  
- Byta namn på den `AnnotationModule` objekt [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  
  
- Byta namn på den `Annotation` objekt [taggen](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag), och ta bort den `confidence` fältet.  
  
- Byta namn på den `insightsSourcesSummary` i den [bild](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objekt- `insightsMetadata`.  
  
- Byta namn på den `InsightsSourcesSummary` objekt [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  
  
- Lägga till den `https://api.cognitive.microsoft.com/bing/v7.0/images/details` slutpunkt. Använd den här slutpunkten till begäran avbildningen insights istället för/bilder eller Sök slutpunktens. Se [bild insikter](./image-insights.md). 
  
- Följande fråga parametrar är nu giltiga endast med den `/images/details` slutpunkt.  
  
    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [CAB-fil](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [CAL](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [bil](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [datafält](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  
  
- Byta namn på den `ImageInsightsResponse` objekt [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  
  
- Ändra typen för följande fält i den [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights) objekt.  
  
    -   Ändra typen av den `relatedCollections` från `ImageGallery[]` till [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  
  
    -   Ändra typen av den `pagesIncluding` från `Image[]` till [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Ändra typen av den `relatedSearches` från `Query[]` till [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  
  
    -   Ändra typen av den `recipes` från `Recipe[]` till [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  
  
    -   Ändra typen av den `visuallySimilarImages` från `Image[]` till [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Ändra typen av den `visuallySimilarProducts` från `ProductSummaryImage[]` till [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Tas bort i `ProductSummaryImage` objekt och flyttas produktrelaterad fälten i den [bild](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objekt. Den `Image` objektet innehåller fälten produktrelaterad endast när avbildningen ingår som en del av likartade produkter i ett avbildningen insight svar.  
  
    -   Ändra typen av den `recognizedEntityGroups` från `RecognizedEntityGroup[]` till [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  
  
-   Byta namn på den `categoryClassification` i [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) till `annotations`, och ändras dess typ till `AnnotationsModule`.  

### <a name="images-answer"></a>Svara på bilder

-   Ta bort fälten displayShoppingSourcesBadges och displayRecipeSourcesBadges från [bilder](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images).  
  
-   Byta namn på den `nextOffsetAddCount` i [bilder](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) till `nextOffset`. Hur du använder förskjutningen har också ändrats. Tidigare var du ställer in den [förskjutning](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) Frågeparametern till den `nextOffsetAddCount` värde plus värdet för föregående förskjutningen plus antalet avbildningar i resultatet. Nu kan du ange `offset` till den `nextOffset` värde.  
    
  
## <a name="non-breaking-changes"></a>Hårt ändringar

### <a name="query-parameters"></a>Frågeparametrar
  
- Lägga till Transparent som ett möjligt [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) filtervärde. Transparent filtret returnerar endast bilder med en genomskinlig bakgrund.

- Lagt till några som ett möjligt [licens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) filtervärde. Alla filtret returnerar bara de avbildningar som finns under licens.
  
- Lägga till den [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) och [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize) fråga parametrar. Du kan använda filtren för att returnera bilder inom ett intervall av filstorlekar.  
  
- Lägga till den [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth), [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth) fråga parametrar. Du kan använda filtren för att returnera bilder inom ett intervall med höjd och bredd.  

### <a name="object-changes"></a>Objekt ändras
  
- Lägga till den `description` och `lastUpdated` fält till den [erbjuder](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) objekt.  
  
- Lägga till den `name` automatiskt till den [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) objekt.  
  
- Lägga till `similarTerms` till den [bilder](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objekt. Det här fältet innehåller en lista över de uttryck med liknande innebörd för användarens frågesträngen.  

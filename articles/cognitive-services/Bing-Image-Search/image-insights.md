---
title: Hämta information om bilder - bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder den bildsökning i Bing för att få mer information om en bild.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 8798ecd15c673c26c5b1be0920887ff493cc6a85
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818693"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Hämta information om bilder med den bildsökning i Bing

> [!IMPORTANT]
> I stället för att använda/bilder/information om slutpunkten för att få information om bilder, bör du använda [Visual Search](../bing-visual-search/overview.md) eftersom det ger mer omfattande insikter.


Varje avbildning innehåller en insights-token som du kan använda för att få information om avbildningen. Till exempel får du en samling relaterade bilder, webbsidor med avbildningen eller en lista över handlare där du kan köpa produkter som på bilden.  

För att få insikter om en bild kan du avbilda avbildningens [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken) token i svaret.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

Sedan anropa Avbildningsdetaljerna som slutpunkten och ange den [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) frågeparameter till token i `imageInsightsToken`.  

Om du vill ange de insikter som du vill hämta, ange den `modules` frågeparameter. Om du vill hämta alla insikter, ange `modules` till `All`. För att få endast textning och samling insikter kan ange `modules` till `Caption%2CCollection`. En fullständig lista över möjliga insikter, se [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested). Inte alla insikter är tillgängliga för alla avbildningar. Svaret innehåller alla insikter som du har begärt, om det är tillgängligt.

I följande exempel begär alla tillgängliga insikter för föregående bild.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>Få insikter från en känd avbildning

Om du har rätt Webbadress till en avbildning som du vill använda för att visa insikter om den [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) frågeparameter i stället för den [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) parametern för att ange avbildningen. Eller, om du har bildfilen kan du skicka den binära filen bildens i brödtexten i en POST-begäran. Om du använder en POST-begäran den `Content-Type` huvudet måste anges till `multipart/data-form`. Med något av alternativen, får storleken på avbildningen inte överstiga 1 MB.  

Om du har en URL till avbildningen visar i följande exempel hur du begär insikter från en avbildning.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>Hämta information om alla bilder  

Om du vill begära alla insikter från en avbildning, ange den [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) frågeparameter till `All`. Begäran måste innehålla användarens frågesträngen för att hämta relaterade sökningar. Det här exemplet visar med hjälp av den [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) att välja avbildningen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Det översta objektet är en [ImageInsightsResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) objekt i stället för en [avbildningar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objekt.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>Identifiera entiteter i en bild  

Funktionen för igenkänning av entiteten identifierar entiteter i en bild, för närvarande endast personer. För att identifiera enheter i en bild, ange den [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) frågeparameter till `RecognizedEntities`.  

> [!NOTE]
> Du kan inte ange den här modulen med en annan modul. Om du anger den här modulen med andra moduler omfattar inte känt entiteter i svaret.  

Följande visar hur du anger avbildningen med hjälp av den [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl) parametern. Kom ihåg att URL: en koda Frågeparametrar.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Nedan visas svaret på den tidigare begäran. Eftersom bilden innehåller två personer, identifierar en region för varje person i svaret. I det här fallet har personerna som identifierats i grupperna CelebrityAnnotations och CelebRecognitionAnnotations. Bing visar en lista över personer i varje grupp baserat på sannolikheten att de matchar personen i den ursprungliga avbildningen. Listan är i fallande ordning tillförlitlighet. Gruppen CelebRecognitionAnnotations ger högsta möjliga säkerhet att matchningen är korrekt.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

Den `region` fältet identifierar bilden där Bing identifieras entiteten. Personer representerar regionen personens ansikte.  

Värdena för rektangeln är i förhållande till bredden och höjden på den ursprungliga bilden och är i intervallet 0,0 till och med 1.0. Till exempel om avbildningen är 300 x 200 och dess övre, vänstra hörnet är tidpunkt (10, 20) och längst ned till höger är tidpunkt (290, 150) och sedan normaliserade rektangeln är:  

-   Vänster: 10 / 300 = 0.03333...  
-   Överkant:  20 / 200 = 0.1  
-   Höger: 290 / 300 = 0.9667...  
-   Längst ned: 150 / 200 = 0.75  

Du kan använda den region som Bing returnerar i efterföljande insights-anrop. Till exempel för att hämta visuellt likartade bilder av identifierade entiteten. Mer information finns i beskära avbildning som ska användas med visuellt likartade och entiteten igenkänning av moduler. Nedan visas mappningen mellan fälten region och frågeparametrar som du skulle göra att beskära bilder.  

-   Vänster mappar till [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
-   De främsta mappar till [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
-   Höger mappar till [bil](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
-   Nedre mappar till [CAB-fil](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  

## <a name="finding-visually-similar-images"></a>Hitta snarlika bilder  

För att hitta avbildningar som är visuellt liknar den ursprungliga bilden, ange den [moduler](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) frågeparameter till SimilarImages.  

Följande begäran visar hur du hämtar snarlika bilder. Denna begäran använder den [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) frågeparameter för att identifiera den ursprungliga avbildningen. För att förbättra relevans, bör du inkludera användarens frågesträngen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


Nedan visas svaret på den tidigare begäran.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>Beskära avbildning som ska användas med visuellt likartade och entiteten igenkänning av moduler  

Om du vill ange regionen som på bilden med Bing att avgöra om avbildningar är snarlika eller utföra entitetsidentifiering, använda den [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal), [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat), [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab), och [bil](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car) Frågeparametrar. Som standard använder Bing hela bilden.  

Parametrarna ange övre, vänstra hörnet och längst ned, till höger på den region som Bing använder för jämförelse. Ange värden som bråkdelar av den ursprungliga bilden Breddoch höjd. Utgå från Bråkvärden (0.0, 0.0) i övre, vänstra hörnet och i slutet med (1.0, 1.0) i nederkant högra hörnet. Till exempel vill ange att det övre vänstra hörnet startar en fjärdedel av hur nedåt från överst och en fjärdedel av vägen i från vänster sida, ange `cal` till 0,25 och `cat` 0,25.  

Följande sekvens med anrop visar effekten av att ange beskärning region. Det första anropet omfattar inte beskära och Bing identifierar två personer Ständiga sida vid sida mitt i avbildningen.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

Svaret visar två erkända entiteter.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

Det andra anropet Beskär bilden lodrätt ned mitten och Bing identifieras en enskild person på höger sida av avbildningen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Svaret visar en känd entitet.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>Hitta likartade produkter  

För att hitta avbildningar som innehåller produkter som är visuellt liknar de produkter som finns i den ursprungliga bilden, ange den [modueles](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) frågeparameter till SimilarProducts.  

Följande begäran visar hur du hämtar bilder på produkter med visuellt likartade produkter. Denna begäran använder den [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken) fråga parametern för att identifiera ursprungligt bild som returnerades i en tidigare begäran. För att förbättra relevans, bör du inkludera användarens frågesträngen.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Nedan visas svaret på den tidigare begäran. Svaret innehåller en bild av en liknande produkt och anger hur många handlare erbjuder produkten online, om det finns produkten klassificeringar och det lägsta priset hittades (se den `aggregateOffer` fältet).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

Hämta en lista över handlare som erbjuder produkt online (se den [offerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) fältet), anropa API: et igen och ange `modules` till ShoppingSources. Slutligen anger den `insightsToken` frågeparameter som token hittades i den sammanfattande bilden av produkten.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

Följande är svaret på den tidigare begäran.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```

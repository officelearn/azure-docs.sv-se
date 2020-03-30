---
title: Produktmallar i Azure API Management | Microsoft-dokument
description: Lär dig hur du anpassar innehållet på produktsidorna i utvecklarportalen för Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 393563427e936e07315cd44b78cb793d4292b352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243931"
---
# <a name="product-templates-in-azure-api-management"></a>Produktmallar i Azure API Management

Azure API Management ger dig möjlighet att anpassa innehållet på utvecklarportalsidor med hjälp av en uppsättning mallar som konfigurerar deras innehåll. Med hjälp av [DotLiquid-syntax](http://dotliquidmarkup.org/) och valfri redigerare, till exempel [DotLiquid för designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)och en uppsättning lokaliserade [strängresurser,](api-management-template-resources.md#strings) [Glyph-resurser](api-management-template-resources.md#glyphs)och [sidkontroller,](api-management-page-controls.md)har du stor flexibilitet att konfigurera innehållet på sidorna som du tycker passar med hjälp av dessa mallar.  
  
 Med mallarna i det här avsnittet kan du anpassa innehållet på produktsidorna i utvecklarportalen.  
  
-   [Produktlista](#ProductList)  
  
-   [Produkt](#Product)  
  
> [!NOTE]
>  Exempelmallar för exempel ingår i följande dokumentation, men kan komma att ändras på grund av kontinuerliga förbättringar. Du kan visa standardmallarna i utvecklarportalen genom att navigera till önskade enskilda mallar. Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)för API Management med hjälp av mallar .  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a>Produktlista  
 Med **mallen Produktlista** kan du anpassa brödtexten på produktlistesidan i utvecklarportalen.  
  
 ![Lista över produkter](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Standardmall  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Kontroller  
 Mallen `Product list` kan använda följande [sidkontroller](api-management-page-controls.md).  
  
-   [personkontroll](api-management-page-controls.md#paging-control)  
  
-   [sökkontroll](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Sidindelning|[Personsökningsenhet.](api-management-template-data-model-reference.md#Paging)|Växlingsinformationen för produktsamlingen.|  
|Filtrering|[Filtreringsenhet.](api-management-template-data-model-reference.md#Filtering)|Filtreringsinformationen för produktlistesidan.|  
|Produkter|Insamling [Product](api-management-template-data-model-reference.md#Product) av produktentiteter.|De produkter som är synliga för den aktuella användaren.|  
  
### <a name="sample-template-data"></a>Exempel på malldata  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="product"></a><a name="Product"></a>Produkt  
 Med **produktmallen** kan du anpassa produktsidans brödtext i utvecklarportalen.  
  
 ![Produktsida för utvecklarportal](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Standardmall  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Kontroller  
 Mallen `Product list` kan använda följande [sidkontroller](api-management-page-controls.md).  
  
-   [knappen prenumerera](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Produkt|[Produkt](api-management-template-data-model-reference.md#Product)|Den angivna produkten.|  
|ÄrDeveloperPrenumererad|boolean|Om den aktuella användaren prenumererar på den här produkten.|  
|SubscriptionState (PrenumerationState)|nummer|Tillståndet för prenumerationen. Möjliga tillstånd är:<br /><br /> -   `0 - suspended`– prenumerationen är blockerad och abonnenten kan inte anropa några API:er för produkten.<br />-   `1 - active`– prenumerationen är aktiv.<br />-   `2 - expired`– prenumerationen nådde sitt utgångsdatum och inaktiverades.<br />-   `3 - submitted`– prenumerationsbegäran har gjorts av utvecklaren, men har ännu inte godkänts eller avvisats.<br />-   `4 - rejected`– prenumerationsbegäran har nekats av en administratör.<br />-   `5 - cancelled`– prenumerationen har avbrutits av utvecklaren eller administratören.|  
|Begränsningar|matris|Den här egenskapen är inaktuell och bör inte användas.|  
|Delegerad PrenumerationEn kan nås|boolean|Om [delegering](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) är aktiverat för den här prenumerationen.|  
|DelegeradAbonnemangUrl|sträng|Om delegering är aktiverat är den delegerade prenumerations-URL:en.|  
|Isagreed|boolean|Om produkten har villkor, om den aktuella användaren har godkänt villkoren.|  
|Prenumerationer|Samling av [sammanfattningsenheter för](api-management-template-data-model-reference.md#SubscriptionSummary) prenumeration.|Prenumerationerna på produkten.|  
|Api|Insamling [API](api-management-template-data-model-reference.md#API) av API-entiteter.|API:erna i den här produkten.|  
|Det går inte Att läggas till för att användaAbonnemangNumberLimitReached|boolean|Om den aktuella användaren är berättigad att prenumerera på den här produkten med avseende på prenumerationsgränsen.|  
|Det går inte Lägga till förmultimultimulantiaBeteckningar Inte Tillåts|boolean|Om den aktuella användaren är berättigad att prenumerera på den här produkten när det gäller att flera prenumerationer tillåts eller inte.|  
  
### <a name="sample-template-data"></a>Exempel på malldata  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](api-management-developer-portal-templates.md)för API Management med hjälp av mallar .

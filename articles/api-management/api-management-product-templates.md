---
title: Produktmallar i Azure API Management | Microsoft Docs
description: Lär dig hur du anpassar innehållet i produktsidorna i Azure API Management developer-portalen.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: dae757231d8f2ff7fcd8e032d941c0fa9f192796
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23835169"
---
# <a name="product-templates-in-azure-api-management"></a>Produktmallar i Azure API Management
Azure API Management ger dig möjlighet att anpassa innehållet i developer portalens sidor med hjälp av en uppsättning mallar som konfigurerar deras innehåll. Med hjälp av [DotLiquid](http://dotliquidmarkup.org/) syntax och redigeringsprogram, t.ex [DotLiquid för Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), och en angiven uppsättning lokaliserade [String resurser](api-management-template-resources.md#strings), [glyf resurser](api-management-template-resources.md#glyphs), och [sidan kontroller](api-management-page-controls.md), du har stor flexibilitet för att konfigurera innehåll för sidorna som du vill använda dessa mallar.  
  
 Mallarna i det här avsnittet kan du anpassa innehållet i produktsidorna i developer-portalen.  
  
-   [Produktlista](#ProductList)  
  
-   [Produkten](#Product)  
  
> [!NOTE]
>  Standard exempelmallarna ingår i följande dokumentation, men kan komma att ändras på grund av kontinuerliga förbättringar. Du kan visa live standardmallarna i developer-portalen genom att navigera till önskade enskilda mallar. Mer information om hur du arbetar med mallar finns [hur du anpassar API Management developer-portalen med hjälp av mallar](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
##  <a name="ProductList"></a>Produktlista  
 Den **produktlista** mall kan du anpassa innehållet i listan produktsidan i developer-portalen.  
  
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
 Den `Product list` mall kan du använda följande [sidan kontroller](api-management-page-controls.md).  
  
-   [växlingsfil-kontroll](api-management-page-controls.md#paging-control)  
  
-   [Sök-kontroll](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Sidindelning|[Växling](api-management-template-data-model-reference.md#Paging) entitet.|Växlingsfil-information för samlingen produkter.|  
|Filtrering|[Filtrering](api-management-template-data-model-reference.md#Filtering) entitet.|Filtrera information för sidan produkter.|  
|Produkter|Samling av [produkten](api-management-template-data-model-reference.md#Product) entiteter.|Produkterna som är synliga för den aktuella användaren.|  
  
### <a name="sample-template-data"></a>Mallen exempeldata  
  
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
  
##  <a name="Product"></a>Produkten  
 Den **produkten** mall kan du anpassa brödtexten i sidan i developer-portalen.  
  
 ![Developer portal produktsidan](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
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
 Den `Product list` mall kan du använda följande [sidan kontroller](api-management-page-controls.md).  
  
-   [prenumerera på knappen](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|Produkt|[Produkten](api-management-template-data-model-reference.md#Product)|Den angivna produkten.|  
|IsDeveloperSubscribed|Booleskt värde|Om den aktuella användaren som prenumererar på den här produkten.|  
|SubscriptionState|Antal|Tillståndet för prenumerationen. Möjliga tillstånd är:<br /><br /> -   `0 - suspended`– prenumerationen blockeras och prenumeranten kan inte anropa alla API: er av produkten.<br />-   `1 - active`– prenumerationen är aktiv.<br />-   `2 - expired`– prenumerationen uppnåtts dess förfallodatum och har inaktiverats.<br />-   `3 - submitted`– prenumerationsbegäran har gjorts av utvecklaren, men har ännu inte godkänts eller avvisats.<br />-   `4 - rejected`– prenumerationsbegäran har nekats av en administratör.<br />-   `5 - cancelled`– prenumerationen har avbrutits av utvecklare eller administratör.|  
|Begränsningar|matris|Den här egenskapen är inaktuell och bör inte användas.|  
|DelegatedSubscriptionEnabled|Booleskt värde|Om [delegering](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) har aktiverats för den här prenumerationen.|  
|DelegatedSubscriptionUrl|Sträng|Om delegering är aktiverat delegerad prenumeration URL: en.|  
|IsAgreed|Booleskt värde|Om produkten har villkor, oavsett om den aktuella användaren har godkänt villkoren.|  
|Prenumerationer|Samling av [prenumeration sammanfattning](api-management-template-data-model-reference.md#SubscriptionSummary) entiteter.|Prenumerationer till produkten.|  
|API: er|Samling av [API](api-management-template-data-model-reference.md#API) entiteter.|API: er i den här produkten.|  
|CannotAddBecauseSubscriptionNumberLimitReached|Booleskt värde|Om den aktuella användaren är berättigad att prenumerera på den här produkten avseende gränsen.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|Booleskt värde|Om den aktuella användaren är berättigad att prenumerera på den här produkten avseende flera prenumerationer som tillåten eller inte.|  
  
### <a name="sample-template-data"></a>Mallen exempeldata  
  
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
Mer information om hur du arbetar med mallar finns [hur du anpassar API Management developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).
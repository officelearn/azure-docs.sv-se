---
title: Programmallar i Azure API Management | Microsoft-dokument
description: Lär dig hur du anpassar innehållet på programsidorna i utvecklarportalen i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: f3122c4d-e10e-4cdf-977b-36e8f4133fc8
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: d635950c8b34986cd5824660166017317948cbd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176906"
---
# <a name="application-templates-in-azure-api-management"></a>Programmallar i Azure API Management
Azure API Management ger dig möjlighet att anpassa innehållet på utvecklarportalsidor med hjälp av en uppsättning mallar som konfigurerar deras innehåll. Med hjälp av [DotLiquid-syntax](http://dotliquidmarkup.org/) och valfri redigerare, till exempel [DotLiquid för designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)och en uppsättning lokaliserade [strängresurser,](api-management-template-resources.md#strings) [Glyph-resurser](api-management-template-resources.md#glyphs)och [sidkontroller,](api-management-page-controls.md)har du stor flexibilitet att konfigurera innehållet på sidorna som du tycker passar med hjälp av dessa mallar.  
  
 Med mallarna i det här avsnittet kan du anpassa innehållet på programsidorna i utvecklarportalen.  
  
-   [Programlista](#ProductList)  
  
-   [Program](#Application)  
  
> [!NOTE]
>  Exempelmallar för exempel ingår i följande dokumentation, men kan komma att ändras på grund av kontinuerliga förbättringar. Du kan visa standardmallarna i utvecklarportalen genom att navigera till önskade enskilda mallar. Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)för API Management med hjälp av mallar .  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="application-list"></a><a name="ProductList"></a>Programlista  
 **Med programlistemallen** kan du anpassa brödtexten på programlistesidan i utvecklarportalen.  
  
 ![Utvecklarportalmallar för programlista](./media/api-management-application-templates/APIM-Application-List-Page-Developer-Portal-Templates.png "Portalmallar för programlista för programlista")  
  
### <a name="default-template"></a>Standardmall  
  
```xml  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "AppStrings|WebApplicationsHeader" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if applications.size > 0 %}  
        <ul class="list-unstyled">  
        {% for app in applications %}  
            <li>  
            {% if app.application.icon.url != "" %}  
                <aside>  
                    <a href="/applications/details/{{app.application.id}}"><img src="{{app.application.icon.url}}" alt="App Icon" /></a>  
                </aside>  
            {% endif %}  
                <h3><a href="/applications/details/{{app.application.id}}">{{app.application.title}}</a></h3>  
                {{app.application.description}}  
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
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Paging`|[Personsökningsenhet.](api-management-template-data-model-reference.md#Paging)|Växlingsinformationen för programinsamlingen.|  
|`Applications`|Insamling [Application](api-management-template-data-model-reference.md#Application) av application-entiteter.|De program som är synliga för den aktuella användaren.|  
|`CategoryName`|sträng|Kategorin ansökan.|  
  
### <a name="sample-template-data"></a>Exempel på malldata  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Applications": [  
        {  
            "Application": {  
                "Id": "5702b96fb16653124c8f9ba8",  
                "Title": "Contoso Calculator",  
                "Description": "A simple online calculator.",  
                "Url": null,  
                "Version": null,  
                "Requirements": "Free application with no requirements.",  
                "State": 2,  
                "RegistrationDate": "2016-04-04T18:59:00",  
                "CategoryId": 5,  
                "DeveloperId": "5702b5b0b16653124c8f9ba4",  
                "Attachments": [  
                    {  
                        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                        "Type": "Icon",  
                        "ContentType": "image/png"  
                    },  
                    {  
                        "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
                        "Type": "Screenshot",  
                        "ContentType": "image/png"  
                    }  
                ],  
                "Icon": {  
                    "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                    "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                    "Type": "Icon",  
                    "ContentType": "image/png"  
                }  
            },  
            "CategoryName": "Finance"  
        }  
    ]  
}  
```  
  
##  <a name="application"></a><a name="Application"></a>Program  
 **Med programmallen** kan du anpassa programsidans brödtext i utvecklarportalen.  
  
 ![Portalmallar för programsidas utvecklarportal](./media/api-management-application-templates/APIM-Application-Page-Developer-Portal-Templates.png "Utvecklarportalmallar för APIM-programsida")  
  
### <a name="default-template"></a>Standardmall  
  
```xml  
<h2>{{title}}</h2>  
{% if icon.url != "" %}  
<aside class="applications_aside">  
  <div class="image-placeholder">  
    <img src="{{icon.url}}" alt="Application Icon" />  
  </div>  
</aside>  
{% endif %}  
  
<article>  
  {% if url != "" %}  
    <a target="_blank" href="{{url}}">{{url}}</a>  
  {% endif %}  
  
  <p>{{description}}</p>  
  
  {% if requirements != null %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsRequirementsHeader" %}</h3>  
  <p>{{requirements}}</p>  
  {% endif %}  
  
  {% if attachments.size > 0 %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsScreenshotsHeader" %}</h3>  
    {% for screenshot in attachments %}  
      {% if screenshot.type != "Icon" %}  
      <a href="{{screenshot.url}}" data-lightbox="example-set">  
          <img src="/Developer/Applications/Thumbnail?url={{screenshot.url}}" alt='{% localized "AppDetailsStrings|WebApplicationsScreenshotAlt" %}' />  
      </a>  
      {% endif %}  
    {% endfor %}  
  {% endif %}  
</article>  
  
```  
  
### <a name="controls"></a>Kontroller  
 Mallen `Application` tillåter inte användning av [några sidkontroller](api-management-page-controls.md).  
  
### <a name="data-model"></a>Datamodell  
 [Application](api-management-template-data-model-reference.md#Application) entitet.  
  
### <a name="sample-template-data"></a>Exempel på malldata  
  
```json  
{  
    "Id": "5702b96fb16653124c8f9ba8",  
    "Title": "Contoso Calculator",  
    "Description": "A simple online calculator.",  
    "Url": null,  
    "Version": null,  
    "Requirements": "Free application with no requirements.",  
    "State": 2,  
    "RegistrationDate": "2016-04-04T18:59:00",  
    "CategoryId": 5,  
    "DeveloperId": "5702b5b0b16653124c8f9ba4",  
    "Attachments": [  
        {  
            "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
            "Type": "Icon",  
            "ContentType": "image/png"  
        },  
        {  
            "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
            "Type": "Screenshot",  
            "ContentType": "image/png"  
        }  
    ],  
    "Icon": {  
        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
        "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
        "Type": "Icon",  
        "ContentType": "image/png"  
    }  
}  
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](api-management-developer-portal-templates.md)för API Management med hjälp av mallar .

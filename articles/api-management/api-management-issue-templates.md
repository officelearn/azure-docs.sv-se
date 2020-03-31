---
title: Problemmallar i Azure API Management | Microsoft-dokument
description: Lär dig hur du anpassar innehållet på problemsidorna i utvecklarportalen i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1dac90053797caf66af79e458b9dbb95b682cd17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249586"
---
# <a name="issue-templates-in-azure-api-management"></a>Utfärda mallar i Azure API Management
Azure API Management ger dig möjlighet att anpassa innehållet på utvecklarportalsidor med hjälp av en uppsättning mallar som konfigurerar deras innehåll. Med hjälp av [DotLiquid-syntax](http://dotliquidmarkup.org/) och valfri redigerare, till exempel [DotLiquid för designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)och en uppsättning lokaliserade [strängresurser,](api-management-template-resources.md#strings) [Glyph-resurser](api-management-template-resources.md#glyphs)och [sidkontroller,](api-management-page-controls.md)har du stor flexibilitet att konfigurera innehållet på sidorna som du tycker passar med hjälp av dessa mallar.  
  
 Med mallarna i det här avsnittet kan du anpassa innehållet på problemsidorna i utvecklarportalen.  
  
-   [Lista över problem](#IssueList)  
  
> [!NOTE]
>  Exempelmallar för exempel ingår i följande dokumentation, men kan komma att ändras på grund av kontinuerliga förbättringar. Du kan visa standardmallarna i utvecklarportalen genom att navigera till önskade enskilda mallar. Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](api-management-developer-portal-templates.md)för API Management med hjälp av mallar .  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="issue-list"></a><a name="IssueList"></a>Lista över problem  
 Med **ärendelistemallen** kan du anpassa brödtexten på ärendelistesidan i utvecklarportalen.  
  
 ![Utvecklarportal för problemlista](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "Utvecklarportal för APIM-problemlista")  
  
### <a name="default-template"></a>Standardmall  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Kontroller  
 Mallen `Issue list` kan använda följande [sidkontroller](api-management-page-controls.md).  
  
-   [personkontroll](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap|Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Issues`|Insamling [Issue](api-management-template-data-model-reference.md#Issue) av utleveransentiteter.|De problem som är synliga för den aktuella användaren.|  
|`Paging`|[Personsökningsenhet.](api-management-template-data-model-reference.md#Paging)|Växlingsinformationen för programinsamlingen.|  
|`IsAuthenticated`|boolean|Om den aktuella användaren är inloggad på utvecklarportalen.|  
|`CanReportIssues`|boolean|Om den aktuella användaren har behörighet att lämna in ett problem.|  
|`Search`|sträng|Den här egenskapen är inaktuell och bör inte användas.|  
  
### <a name="sample-template-data"></a>Exempel på malldata  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](api-management-developer-portal-templates.md)för API Management med hjälp av mallar .

---
title: Utfärda mallar i Azure API Management | Microsoft Docs
description: Lär dig hur du anpassar innehållet i problemet sidorna i utvecklarportalen i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: c70e88a572bc261ad9d25f9a742b1987d0c19b44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656713"
---
# <a name="issue-templates-in-azure-api-management"></a>Utfärda mallar i Azure API Management
Azure API Management ger dig möjlighet att anpassa innehållet på utvecklarportalens sidor med en uppsättning mallar som konfigurerar sitt innehåll. Med hjälp av [DotLiquid](http://dotliquidmarkup.org/) syntax och redigeringsprogram, till exempel [DotLiquid för Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), och en angiven uppsättning lokaliserade [sträng resurser](api-management-template-resources.md#strings), [tecken resurser](api-management-template-resources.md#glyphs), och [sidan kontroller](api-management-page-controls.md), har bra möjlighet att konfigurera innehållet i sidorna som du vill med hjälp av dessa mallar.  
  
 Mallar i det här avsnittet kan du anpassa innehållet på sidorna problemet i developer-portalen.  
  
-   [Problemet lista](#IssueList)  
  
> [!NOTE]
>  Standardmallarna för exemplet ingår i följande dokumentation, men kan komma att ändras på grund av fortlöpande med förbättringar. Du kan visa live standardmallarna i developer-portalen genom att navigera till önskad enskilda mallarna. Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](api-management-developer-portal-templates.md).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="IssueList"></a> Problemet lista  
 Den **problemet lista** mall kan du anpassa brödtexten i sidan problemet i developer-portalen.  
  
 ![Issue List Developer Portal](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "APIM Issue List Developer Portal")  
  
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
 Den `Issue list` mall kan använda följande [sidan kontroller](api-management-page-controls.md).  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Datamodell  
  
|Egenskap |Typ|Beskrivning|  
|--------------|----------|-----------------|  
|`Issues`|Insamling av [problemet](api-management-template-data-model-reference.md#Issue) entiteter.|Problem som är synliga för den aktuella användaren.|  
|`Paging`|[Växling](api-management-template-data-model-reference.md#Paging) entitet.|Växling information för programmets samling.|  
|`IsAuthenticated`|boolesk|Om den aktuella användaren är inloggad på developer-portalen.|  
|`CanReportIssues`|boolesk|Om den aktuella användaren har behörighet att skicka in ett ärende.|  
|`Search`|string|Den här egenskapen är inaktuell och ska inte användas.|  
  
### <a name="sample-template-data"></a>Mall för exempeldata  
  
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
Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](api-management-developer-portal-templates.md).

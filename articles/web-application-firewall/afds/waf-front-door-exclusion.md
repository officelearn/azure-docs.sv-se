---
title: Undantags listor för brand vägg för webbaserade program i Azures front dörr – Azure Portal
description: Den här artikeln innehåller information om konfiguration av undantags listor i Azure fram till Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925935"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Brand vägg för webbaserade program (WAF) med undantags listor för front dörr tjänsten 

Ibland kan brand vägg för webbaserade program (WAF) blockera en begäran som du vill tillåta för ditt program. Active Directory infogar till exempel tokens som används för autentisering. Dessa tokens kan innehålla specialtecken som kan utlösa ett falskt positivt värde från WAF-reglerna. Med undantags listor för WAF kan du utelämna vissa begär ande attribut från en WAF-utvärdering.  En undantags lista kan konfigureras med hjälp av [PowserShell](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0), [Azure CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [REST API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)eller Azure Portal. I följande exempel visas Azure Portal-konfigurationen. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Konfigurera undantags listor med hjälp av Azure Portal
**Hantera undantag** är tillgängligt från WAF-portalen under **hanterade regler**

![hantera uteslutnings](../media/waf-front-door-exclusion/exclusion1.png)
![hantera exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 En exempel lista för undantag: ![hantera exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

I det här exemplet exkluderas värdet i fältet *användar* huvud. En giltig begäran kan innehålla det *användar* fält som innehåller en sträng som utlöser en SQL-inmatnings regel. Du kan undanta *användar* parametern i det här fallet så att WAF-regeln inte utvärderar något i fältet.

Följande attribut kan läggas till i undantags listor efter namn. Värdena för de fält som du använder utvärderas inte mot WAF-regler, men deras namn utvärderas. Undantags listorna tar bort kontrollen av fältets värde.

* Namn på begär ande huvud
* Cookie-namn för begäran
* Argument namn för frågesträng
* Begär ande brödtext post argument namn

Du kan ange en exakt matchning av rubrik, brödtext, cookie eller frågesträng för begäran.  Du kan också ange partiella matchningar om du vill. Följande operatorer är de matchnings villkor som stöds:

- **Är lika med**: den här operatorn används för en exakt matchning. Om du till exempel vill välja ett sidhuvud med namnet **bearerToken**använder du operatorn Equals med Selector Set as **bearerToken**.
- **Börjar med**: den här operatorn matchar alla fält som börjar med det angivna värdet för väljaren.
- **Slutar med**: den här operatorn matchar alla begär ande fält som slutar med det angivna väljar värdet.
- **Contains**: den här operatorn matchar alla begär ande fält som innehåller det angivna värdet för väljaren.
- **Lika med alla**: den här operatorn matchar alla begär ande fält. * är väljar värde.

Rubrik-och cookie-namn är Skift läges känsliga.

Du kan använda undantags listan för alla regler i den hanterade regel uppsättningen, för regler för en viss regel grupp eller till en enskild regel som visas i föregående exempel. 

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat dina WAF-inställningar kan du läsa om hur du visar dina WAF-loggar. Mer information finns i [front dörr Diagnostics](../afds/waf-front-door-monitor.md).

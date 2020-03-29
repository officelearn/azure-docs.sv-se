---
title: Undantagslistor för brandväggsprogram för webbprogram i Azure Front Door - Azure-portal
description: Den här artikeln innehåller information om uteslutningslistor konfiguration i Azure Front med Azure-portalen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/25/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 6ed382e88700e4ecd7f8de20a2c8da7ed3c13566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925935"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Brandvägg för webbprogram (WAF) med undantagslistor för frontdörrstjänsten 

Ibland kan WAF (Web Application Firewall) blockera en begäran som du vill tillåta för ditt program. Active Directory infogar till exempel token som används för autentisering. Dessa token kan innehålla specialtecken som kan utlösa ett falskt positivt från WAF-reglerna. MED WAF-undantagslistor kan du utelämna vissa begäranattribut från en WAF-utvärdering.  En undantagslista kan konfigureras med [PowserShell,](https://docs.microsoft.com/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-3.5.0) [Azure CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-managed-rules-exclusion-add), [Rest API](https://docs.microsoft.com/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)eller Azure-portalen. I följande exempel visas Azure-portalkonfigurationen. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Konfigurera undantagslistor med Azure-portalen
**Hantera undantag** är tillgängligt från WAF-portalen under **Hanterade regler**

![Hantera](../media/waf-front-door-exclusion/exclusion1.png)
![uteslutning Hantera exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 En undantagslista ![för exempel: Hantera exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

I det här exemplet utesluts värdet i *fältet användarhuvud.* En giltig begäran kan innehålla *användarfältet* som innehåller en sträng som utlöser en SQL-insprutningsregel. Du kan *user* utesluta användarparametern i det här fallet så att WAF-regeln inte utvärderar något i fältet.

Följande attribut kan läggas till i undantagslistor efter namn. Värdena för de fält du använder utvärderas inte mot WAF-regler, men deras namn utvärderas. Undantagslistorna tar bort granskning av fältets värde.

* Namn på begäranhuvud
* Begär cookie-namn
* Namn på frågesträng args
* Begär namn på brödtextpost args

Du kan ange en exakt begäranhetsrubrik, brödtext, cookie eller frågesträngattributmatchning.  Du kan också ange partiella matchningar. Följande operatorer är de matchningsvillkor som stöds:

- **Lika med**: Den här operatorn används för en exakt matchning. Om du till exempel vill välja ett huvud med namnet **bearerToken**använder du operatorn likhet med väljaren som **bärareToken**.
- **Börjar med**: Den här operatorn matchar alla fält som börjar med det angivna väljarvärdet.
- **Slutar med**: Den här operatorn matchar alla begärandefält som slutar med det angivna väljarvärdet.
- **Innehåller**: Den här operatorn matchar alla begärandefält som innehåller det angivna väljarvärdet.
- **Lika med alla**: Den här operatorn matchar alla begärandefält. * är väljarens värde.

Rubrik- och cookienamn är skiftlägesokänsliga.

Du kan använda undantagslistan på alla regler inom den hanterade regeluppsättningen, på regler för en viss regelgrupp eller på en enda regel som visas i föregående exempel. 

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat WAF-inställningarna kan du läsa om hur du visar dina WAF-loggar. Mer information finns i [Diagnostik för ytterdörren](../afds/waf-front-door-monitor.md).

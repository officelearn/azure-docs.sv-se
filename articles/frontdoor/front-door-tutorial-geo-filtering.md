---
title: Självstudie – konfigurera geo-filtrering WAF-princip – Azure-front dörr
description: I den här självstudien får du lära dig hur du skapar en princip för geo-filtrering och associerar principen med din befintliga klient dels värd för klient delen
services: frontdoor
documentationcenter: ''
author: teresayao
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: tyao
ms.openlocfilehash: e3119745e35140d0344d25f34f54b63939d2542d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79471463"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Så konfigurerar du en WAF-princip för geofiltrering för Front Door
Den här självstudiekursen visar hur du använder Azure PowerShell till att skapa ett exempel på en princip för geofiltrering och associerar principen med din befintliga Front Door-klientdelsvärd. Den här exempel principen för geo-filtrering blockerar förfrågningar från alla andra länder/regioner utom USA.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

## <a name="prerequisites"></a>Krav
Innan du börjar konfigurera en geo-filter-princip konfigurerar du din PowerShell-miljö och skapar en profil för en front dörr.
### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan för att logga in med dina Azure-autentiseringsuppgifter och installera AZ PowerShell-modulen.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ansluta till Azure med en interaktiv dialog ruta för inloggning
```
Install-Module -Name Az
Connect-AzAccount
```
Kontrol lera att du har den aktuella versionen av PowerShellGet installerad. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Installera AZ. ytterdörr-modulen 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Skapa en profil för en front dörr
Skapa en profil för en frontend-dörr genom att följa anvisningarna i [snabb start: skapa en profil för front dörren](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definiera matchnings villkor för geo-filtrering

Skapa ett exempel på matchnings villkor som väljer begär Anden som inte kommer från "US" med [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) på parametrar när ett matchnings villkor skapas. Du [kan ange lands](front-door-geo-filtering.md)koderna i två bokstäver till land mappning.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Lägga till matchningsvillkor för geofiltrering i en regel med Action (Åtgärd) och Priority (Prioritet)

Skapa ett CustomRule- `nonUSBlockRule` objekt baserat på matchnings villkoret, en åtgärd och en prioritet med [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  En CustomRule (Anpassad regel) kan ha flera MatchCondition (Matchningsvillkor).  I det här exemplet anges Block (Blockera) för Action (Åtgärd) och 1 som Priority (Prioritet), högsta prioritetsnivån.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Lägga till regler i en princip
Hitta namnet på den resurs grupp som innehåller profilen för front dörren med hjälp `Get-AzResourceGroup`av. Skapa sedan ett `geoPolicy` princip objekt som innehåller `nonUSBlockRule` kommandot [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) i den angivna resurs gruppen som innehåller profilen för den främre dörren. Du måste ange ett unikt namn för geo-filtrerings principen. 

I exemplet nedan används resurs grupps namnet *myResourceGroupFD1* med antagandet att du har skapat en profil för front dörren med hjälp av anvisningarna i [snabb start: skapa en artikel i front dörren](quickstart-create-front-door.md) . I exemplet nedan ersätter du princip namnet *geoPolicyAllowUSOnly* med ett unikt princip namn.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Länka WAF-princip till en klients frontend-värd
Länka WAF-principobjektet till den befintliga värd för frontend-frontend och uppdatera front dörrs egenskaper. 

Det gör du genom att först hämta ditt frontend-objekt med [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Ange sedan egenskapen frontend-WebApplicationFirewallPolicyLink till resourceId för `geoPolicy`funktionen [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Du behöver bara ange egenskapen WebApplicationFirewallPolicyLink en gång för att länka en WAF-princip till en klient dels klient del värd. Efterföljande princip uppdateringar tillämpas automatiskt på klient dels värden.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Azure WebApplication-brandväggen](waf-overview.md).
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).

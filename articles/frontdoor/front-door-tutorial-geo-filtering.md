---
title: Självstudiekurs - Konfigurera geofiltrering AV WAF-principen - Azure Front Door
description: I den här självstudien får du lära dig hur du skapar en geofiltreringsprincip och associerar principen med din befintliga frontsida för ytterdörren
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79471463"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Så konfigurerar du en WAF-princip för geofiltrering för Front Door
Den här självstudiekursen visar hur du använder Azure PowerShell till att skapa ett exempel på en princip för geofiltrering och associerar principen med din befintliga Front Door-klientdelsvärd. Den här exempelprincipen för geofiltrering blockerar begäranden från alla andra länder/regioner utom USA.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

## <a name="prerequisites"></a>Krav
Innan du börjar ställa in en geofilterprincip ställer du in PowerShell-miljön och skapar en frontdörrprofil.
### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ instruktionerna på sidan för att logga in med dina Azure-autentiseringsuppgifter och installera Az PowerShell-modulen.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ansluta till Azure med en interaktiv dialogruta för inloggning
```
Install-Module -Name Az
Connect-AzAccount
```
Kontrollera att du har den aktuella versionen av PowerShellGet installerad. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Installera Az.FrontDoor-modul 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Skapa en profil för ytterdörren
Skapa en profil för ytterdörren genom att följa instruktionerna som beskrivs i [Snabbstart: Skapa en profil för ytterdörren](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definiera villkor för geofiltrering av matchning

Skapa ett exempelmatchningsvillkor som väljer begäranden som inte kommer från "USA" med [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) på parametrar när du skapar ett matchningsvillkor. Två bokstavsland koder till land kartläggning finns [här](front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Lägga till matchningsvillkor för geofiltrering i en regel med Action (Åtgärd) och Priority (Prioritet)

Skapa ett CustomRule-objekt `nonUSBlockRule` baserat på matchningsvillkoret, en åtgärd och en prioritet med [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  En CustomRule (Anpassad regel) kan ha flera MatchCondition (Matchningsvillkor).  I det här exemplet anges Block (Blockera) för Action (Åtgärd) och 1 som Priority (Prioritet), högsta prioritetsnivån.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Lägga till regler i en princip
Leta reda på namnet på resursgruppen som `Get-AzResourceGroup`innehåller profilen Ytterdörr med . Skapa sedan `geoPolicy` ett principobjekt som innehåller `nonUSBlockRule` [new-azfrontdoorwafpolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) i den angivna resursgruppen som innehåller frontdörren-profilen. Du måste ange ett unikt namn för geofiltreringsprincipen. 

I exemplet nedan används namnet Resursgrupp *myResourceGroupFD1* med antagandet att du har skapat profilen Ytterdörr med hjälp av instruktionerna i [snabbstart: Skapa en ytterdörr.](quickstart-create-front-door.md) I exemplet nedan ersätter du principnamnet *geoPolicyAllowUSOnly* med ett unikt principnamn.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Länk WAF politik till en ytterdörr frontend värd
Länka WAF-principobjektet till den befintliga frontdörrens frontend-värd och uppdatera egenskaperna för ytterdörren. 

För att göra det, först hämta din Ytterdörr objekt med [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Ange sedan egenskapen frontend WebApplicationFirewallPolicyLink till `geoPolicy`resursPå grund för den med hjälp av [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Du behöver bara ange webapplicationFirewallPolicyLink egendom en gång för att länka en WAF-princip till en Front Door frontend värd. Efterföljande principuppdateringar tillämpas automatiskt på frontend-värden.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Brandvägg för Azure-webbprogram](waf-overview.md).
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).

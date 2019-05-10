---
title: Självstudie – konfigurera geo-filtrering web application brandväggsprincipen för åtkomsten för Azure-tjänsten
description: I den här självstudiekursen lär du dig hur du skapar en enkel princip för geofiltrering och associerar principen med din befintliga Front Door-klientdelsvärd
services: frontdoor
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: af1846f66996ded553a95188df958e9592ec68a2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523782"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Hur du ställer in en princip för geo-filtrering WAF för ytterdörren
Den här självstudiekursen visar hur du använder Azure PowerShell till att skapa ett exempel på en princip för geofiltrering och associerar principen med din befintliga Front Door-klientdelsvärd. Det här exemplet geo-filtrering principen blockerar begäranden från alla andra länder/regioner utom USA.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar ställa in en princip för geo-filter ställer in din PowerShell-miljö och skapa en profil för åtkomsten.
### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan, för att logga in med dina autentiseringsuppgifter för Azure, och installera Az PowerShell-modulen.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Anslut till Azure med en interaktiv dialogruta för inloggning
```
Connect-AzAccount
Install-Module -Name Az
```
Kontrollera att du har den aktuella versionen av installerat PowerShellGet. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Installera Az.FrontDoor-modulen 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Skapa en ytterdörren-profil
Skapa en profil för åtkomsten genom att följa anvisningarna som beskrivs i [snabbstarten: Skapa en profil för ytterdörren](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Definiera geo-filtrering matchningsvillkor

Skapa en exempel-matchningsvillkor som väljer begäranden som inte kommer från ”US” med [New AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) på parametrar när du skapar ett matchningsvillkor. Landskoder för två bokstäver för land mappning tillhandahålls [här](front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Lägga till matchningsvillkor för geofiltrering i en regel med Action (Åtgärd) och Priority (Prioritet)

Skapa ett objekt för CustomRule `nonUSBlockRule` baserat på matchningsvillkor, en åtgärd och en prioritet med hjälp av [New AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  En CustomRule (Anpassad regel) kan ha flera MatchCondition (Matchningsvillkor).  I det här exemplet anges Block (Blockera) för Action (Åtgärd) och 1 som Priority (Prioritet), högsta prioritetsnivån.

```
$nonUSBlockRule = New-AzFrontDoorCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Lägga till regler i en princip
Hitta namnet på resursgruppen som innehåller ytterdörren profil med `Get-AzResourceGroup`. Skapa sedan en `geoPolicy` princip objekt som innehåller `nonUSBlockRule` med [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) i den angivna resursgruppen som innehåller ytterdörren profilen. Du måste ange ett unikt namn för geo-principen. 

I exemplet nedan använder Resursgruppnamnet *myResourceGroupFD1* profilen med antagandet att du har skapat åtkomsten med hjälp av instruktionerna i den [snabbstarten: Skapa en ytterdörren](quickstart-create-front-door.md) artikeln. I det exemplet nedan ersätter du namnet på principen *geoPolicyAllowUSOnly* med ett unikt principnamn.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Länken WAF-princip till en ytterdörren frontend-värd
Länka WAF-policyobjekt på befintliga ytterdörren klientdel värden och uppdatera ytterdörren egenskaper. 

Om du vill göra det först hämta dina ytterdörren objekt genom att använda [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Ange sedan egenskapen klientdel WebApplicationFirewallPolicyLink som resourceId av den `geoPolicy`med [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Du behöver bara ange WebApplicationFirewallPolicyLink egenskap en gång för att länka en WAF-princip till en ytterdörren frontend-värd. Efterföljande uppdateringar tillämpas automatiskt på frontend-värden.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [säkerhet på programnivå med Front Door](front-door-application-security.md).
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).

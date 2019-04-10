---
title: Konfigurera en princip för web application firewall (WAF) med anpassade regler och standard Ruse för ytterdörren – Azure PowerShell
description: Lär dig hur du konfigurerar en WAF princip består av både anpassade och hanterade regler för en befintlig ytterdörren slutpunkt.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud;tyao
ms.openlocfilehash: 7d024dd958e6b29b52f095a9a55a67154bf6cde6
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59363075"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Konfigurera en web application-brandväggsprincip med Azure PowerShell
Princip för Azure web application firewall (WAF) definierar inspektioner krävs när en begäran kommer till ytterdörren.
Den här artikeln visar hur du konfigurerar en WAF-princip som består av vissa anpassade regler och med Azure-hanterade standard Ruse ange aktiverat.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar ställa in en princip för hastighetsbegränsning ställer in din PowerShell-miljö och skapa en profil för åtkomsten.
### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan, för att logga in med dina autentiseringsuppgifter för Azure, och installerar Az PowerShell-modulen.

#### <a name="sign-in-to-azure"></a>Logga in på Azure
```
Connect-AzAccount

```
Se till att du har installerat den senaste versionen av PowerShellGet, innan du installerar Front Door-modulen. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installera Az.FrontDoor-modulen 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Skapa en ytterdörren-profil
Skapa en profil för åtkomsten genom att följa anvisningarna som beskrivs i [snabbstarten: Skapa en ytterdörren-profil](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Anpassad regel baserat på http-parametrar

I följande exempel visas hur du konfigurerar en anpassad regel med två matchningsvillkor med [New AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject). Begäranden som kommer från en angiven plats som definieras av referent och frågesträngen innehåller inte ”lösenord”. 

```powershell-interactive
$referer = New-AzFrontDoorMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Anpassad regel baserat på http-frågemetoden
Skapa en regel som blockerar ”PLACERA” metod använda [New AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) på följande sätt:

```powershell-interactive
$put = New-AzFrontDoorMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Skapa en anpassad regel som baseras på storleken begränsning

I följande exempel skapas en regel som blockerar begäranden med URL: en som är längre än 100 tecken med Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Lägg till hanterade standard regeluppsättning

I följande exempel skapas en hanterad standard regeluppsättning med Azure PowerShell:
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>Konfigurera en säkerhetsprincip

Hitta namnet på resursgruppen som innehåller ytterdörren profil med `Get-AzResourceGroup`. Konfigurera en säkerhetsprincip med regler som skapats i föregående steg med [New AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) i den angivna resursgruppen som innehåller ytterdörren profilen.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorFireWallPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Länk-princip till en ytterdörren frontend-värd
Länka säkerhetsobjekt för principen till en befintlig klient ytterdörren-värd och uppdatera ytterdörren egenskaper. Först hämta det ytterdörren objekt genom att använda [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Nu ska vi Konfigurera klient *WebApplicationFirewallPolicyLink* egenskap enligt den *resourceId* för ”$myWAFPolicy$” skapade i föregående steg med [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

I exemplet nedan använder Resursgruppnamnet *myResourceGroupFD1* profilen med antagandet att du har skapat åtkomsten med hjälp av instruktionerna i den [snabbstarten: Skapa en ytterdörren](quickstart-create-front-door.md) artikeln. I det exemplet nedan ersätter $frontDoorName med namnet på din ytterdörren-profil. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Du behöver bara ange *WebApplicationFirewallPolicyLink* egenskap en gång för att länka en säkerhetsprincip för till en frontend ytterdörren. Efterföljande uppdateringar tillämpas automatiskt på klientdelen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [ytterdörren](front-door-overview.md) 
- Läs mer om [WAF för ytterdörren](waf-overview.md)

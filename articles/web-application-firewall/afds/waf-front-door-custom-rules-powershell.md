---
title: Konfigurera WAF-anpassade regler & standardregeluppsättning för Azure Front Door
description: Lär dig hur du konfigurerar en WAF-princip består av både anpassade och hanterade regler för en befintlig slutpunkt för ytterdörren.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186638"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Konfigurera en brandvägg för webbprogram med Azure PowerShell

WAF-principen (Azure Web Application Firewall) definierar inspektioner som krävs när en begäran anländer till ytterdörren.
Den här artikeln visar hur du konfigurerar en WAF-princip som består av vissa anpassade regler och med Azure-hanterad standardregeluppsättning aktiverad.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Innan du börjar ställa in en prisgränsprincip ställer du in PowerShell-miljön och skapar en front door-profil.

### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön

Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ instruktionerna på sidan för att logga in med dina Azure-autentiseringsuppgifter och installera Az PowerShell-modulen.

#### <a name="sign-in-to-azure"></a>Logga in på Azure

```
Connect-AzAccount

```
Se till att du har installerat den senaste versionen av PowerShellGet, innan du installerar Front Door-modulen. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installera Az.FrontDoor-modul 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Skapa en profil för ytterdörren

Skapa en profil för ytterdörren genom att följa instruktionerna som beskrivs i [Snabbstart: Skapa en profil för ytterdörren](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Anpassad regel baserad på http-parametrar

I följande exempel visas hur du konfigurerar en anpassad regel med två matchningsvillkor med [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Begäranden kommer från en angiven plats som definieras av referen, och frågesträngen innehåller inte "lösenord". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Anpassad regel baserad på http-begäransmetod

Skapa en regelblockeringsmetod "PUT" med [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) enligt följande:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Skapa en anpassad regel baserat på storleksbegränsning

I följande exempel skapas en regelblockeringsbegäranden med url som är längre än 100 tecken med Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Lägg till hanterad standardregeluppsättning

I följande exempel skapas en hanterad standardregeluppsättning med Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Konfigurera en säkerhetsprincip

Leta reda på namnet på resursgruppen som `Get-AzResourceGroup`innehåller profilen Ytterdörr med . Konfigurera sedan en säkerhetsprincip med skapade regler i föregående steg med [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) i den angivna resursgruppen som innehåller front door-profilen.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Länka policy till en front-end-värd för ytterdörren

Länka säkerhetsprincipobjektet till en befintlig front-end-värd för ytterdörren och uppdatera egenskaper för ytterdörren. Hämta först frontdörren-objektet med [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Ange sedan egenskapen front-end *WebApplicationFirewallPolicyLink* till *resourceId* för den "$myWAFPolicy$" som skapades i föregående steg med [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

I exemplet nedan används namnet Resursgrupp *myResourceGroupFD1* med antagandet att du har skapat profilen Ytterdörr med hjälp av instruktionerna i [snabbstart: Skapa en ytterdörr.](../../frontdoor/quickstart-create-front-door.md) I exemplet nedan ersätter du också $frontDoorName med namnet på din frontdörrsprofil. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Du behöver bara ange *egenskapen WebApplicationFirewallPolicyLink* en gång för att länka en säkerhetsprincip till en frontend-frontsida för ytterdörren. Efterföljande principuppdateringar tillämpas automatiskt på frontend.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [ytterdörren](../../frontdoor/front-door-overview.md) 
- Läs mer om [WAF med ytterdörr](afds-overview.md)

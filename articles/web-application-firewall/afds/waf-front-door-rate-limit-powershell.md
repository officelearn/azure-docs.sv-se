---
title: Konfigurera WAF-hastighetsgränsregel för ytterdörr - Azure PowerShell
description: Lär dig hur du konfigurerar en hastighetsgränsregel för en befintlig slutpunkt för ytterdörren.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649372"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurera en hastighetsgränsgränsregel för brandväggsprogram för webbprogram med Azure PowerShell
Kostnadsgränsregeln för Azure Web Application Firewall (WAF) för Azure Front Door styr antalet begäranden som tillåts från klienter under en minuts varaktighet.
Den här artikeln visar hur du konfigurerar en WAF-hastighetsgränsregel som styr antalet begäranden som tillåts från klienter till ett webbprogram som innehåller */promo* i URL:en med Azure PowerShell.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav
Innan du börjar ställa in en prisgränsprincip ställer du in PowerShell-miljön och skapar en front door-profil.
### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ instruktionerna på sidan för att logga in med dina Azure-autentiseringsuppgifter och installera Az PowerShell-modulen.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ansluta till Azure med en interaktiv dialogruta för inloggning
```
Connect-AzAccount

```
Innan du installerar frontdörrmodulen kontrollerar du att den aktuella versionen av PowerShellGet är installerad. Kör följande kommando och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installera Az.FrontDoor-modul 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Skapa en profil för ytterdörren
Skapa en profil för ytterdörren genom att följa instruktionerna som beskrivs i [Snabbstart: Skapa en profil för ytterdörren](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definiera url-matchningsvillkor
Definiera ett URL-matchningsvillkor (URL innehåller /promo) med [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
I följande exempel matchar */promo* som värdet för *variabeln RequestUri:*

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Skapa en anpassad hastighetsbegränsningsregel
Ange en hastighetsgräns med [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). I följande exempel anges gränsen till 1000. Begäranden från en klient till kampanjsidan som överstiger 1000 under en minut blockeras tills nästa minut startar.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurera en säkerhetsprincip

Leta reda på namnet på resursgruppen som `Get-AzureRmResourceGroup`innehåller profilen Ytterdörr med . Konfigurera sedan en säkerhetsprincip med en anpassad hastighetsbegränsningsregel med [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) i den angivna resursgruppen som innehåller front door-profilen.

I exemplet nedan används namnet Resursgrupp *myResourceGroupFD1* med antagandet att du har skapat profilen Ytterdörr med hjälp av instruktionerna i [snabbstart: Skapa en ytterdörr.](../../frontdoor/quickstart-create-front-door.md)

 använda [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Länka policy till en front-end-värd för ytterdörren
Länka säkerhetsprincipobjektet till en befintlig front-end-värd för ytterdörren och uppdatera egenskaper för ytterdörren. Hämta först objektet Ytterdörren med kommandot [Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)
Ange sedan egenskapen front-end *WebApplicationFirewallPolicyLink* till *resourceId* för den "$ratePolicy" som skapades i föregående steg med kommandot [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 

I exemplet nedan används namnet Resursgrupp *myResourceGroupFD1* med antagandet att du har skapat profilen Ytterdörr med hjälp av instruktionerna i [snabbstart: Skapa en ytterdörr.](../../frontdoor/quickstart-create-front-door.md) I exemplet nedan ersätter du också $frontDoorName med namnet på din frontdörrsprofil. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Du behöver bara ange *egenskapen WebApplicationFirewallPolicyLink* en gång för att länka en säkerhetsprincip till en frontend-frontsida för ytterdörren. Efterföljande principuppdateringar tillämpas automatiskt på frontend.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [ytterdörren.](../../frontdoor/front-door-overview.md) 



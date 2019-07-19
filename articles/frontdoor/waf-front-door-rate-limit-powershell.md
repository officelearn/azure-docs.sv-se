---
title: Konfigurera en regel för brand Väggs begränsning för webb program för frontend-Azure PowerShell
description: Lär dig hur du konfigurerar en hastighets begränsnings regel för en befintlig front dörrs slut punkt.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 99af39e996aaadd572603f63d019ff929b679550
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846251"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurera en regel för brand Väggs begränsning för webb program med hjälp av Azure PowerShell
Hastighets begränsnings regeln för Azure Web Application Firewall (WAF) för Azure-frontend styr antalet begär Anden som tillåts från en enda klient-IP under en minuts varaktighet.
Den här artikeln visar hur du konfigurerar en WAF Rate Limit-regel som styr antalet begär Anden som tillåts från en enskild klient till ett webb program som innehåller */promo* i URL: en med hjälp av Azure PowerShell.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar konfigurera en princip för hastighets begränsning ställer du in din PowerShell-miljö och skapar en profil för en front dörr.
### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan för att logga in med dina Azure-autentiseringsuppgifter och installera AZ PowerShell-modulen.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ansluta till Azure med en interaktiv dialog ruta för inloggning
```
Connect-AzAccount

```
Se till att du har installerat den senaste versionen av PowerShellGet, innan du installerar Front Door-modulen. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Installera AZ. ytterdörr-modulen 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Skapa en profil för en front dörr
Skapa en profil för en frontend-dörr genom att följa [anvisningarna i snabb start: Skapa en profil för en front dörr](quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definiera villkor för URL-matchning
Definiera ett URL-matchnings villkor (URL innehåller/promo) med [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
I följande exempel matchas */promo* som värdet för *RequestUri* -variabeln:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Skapa en regel för anpassad hastighets begränsning
Ange en hastighets gräns med [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). I följande exempel är gränsen inställd på 1000. Begär Anden från en klient till kampanj sidan överstiger 1000 under en minut blockeras tills nästa minut börjar.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurera en säkerhets princip

Hitta namnet på den resurs grupp som innehåller profilen för front dörren med hjälp `Get-AzureRmResourceGroup`av. Konfigurera sedan en säkerhets princip med en anpassad hastighets begränsnings regel med [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) i den angivna resurs gruppen som innehåller profilen för den främre dörren.

I exemplet nedan används resurs grupps namnet *myResourceGroupFD1* med antagandet att du har skapat en profil för front dörren med instruktioner som finns i [snabb starten: Skapa en artikel i](quickstart-create-front-door.md) front dörren.

 använder [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Länka princip till en frontend-slutpunkt
Länka objektet säkerhets princip till en befintlig frontend-slutpunkt på klient sidan och uppdatera egenskaperna för front dörren. Hämta först objektet front dörr med kommandot [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) .
Sedan ställer du in egenskapen frontend- *WebApplicationFirewallPolicyLink* till *resourceId* för "$ratePolicy" som skapades i föregående steg med kommandot [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) . 

I exemplet nedan används resurs grupps namnet *myResourceGroupFD1* med antagandet att du har skapat en profil för front dörren med instruktioner som finns i [snabb starten: Skapa en artikel i](quickstart-create-front-door.md) front dörren. I exemplet nedan ersätter du $frontDoorName med namnet på din profil för din front dörr. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Du behöver bara ange egenskapen *WebApplicationFirewallPolicyLink* en gång för att länka en säkerhets princip till en front dörrs klient del. Efterföljande princip uppdateringar tillämpas automatiskt på klient delen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [front dörren](front-door-overview.md) 



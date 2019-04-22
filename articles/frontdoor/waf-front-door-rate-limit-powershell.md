---
title: Konfigurera en web rate gränsen brandväggsregeln för application för ytterdörren – Azure PowerShell
description: Lär dig hur du konfigurerar en regel för gränsen av priset för en befintlig ytterdörren slutpunkt.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2019
ms.author: kumud;tyao
ms.openlocfilehash: e0ad1e85a4cd47de823bc4f224b5a8834b1068b9
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685946"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Konfigurera en web application rate gränsen brandväggsregel med hjälp av Azure PowerShell
Azure web application firewall (WAF) hastighet gränsen regel för Azure ytterdörren styr antalet begäranden som tillåts från en enskild klient-IP-adress under en tid för en minut.
Den här artikeln visar hur du konfigurerar en regel för WAF hastighet gränsen som styr antalet begäranden som tillåts från en enda klient till ett webbprogram som innehåller */promo* i URL-Adressen med hjälp av Azure PowerShell.

> [!IMPORTANT]
> WAF hastighet gränsen regeln funktionen för Azure ytterdörren är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar ställa in en princip för hastighetsbegränsning ställer in din PowerShell-miljö och skapa en profil för åtkomsten.
### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan, för att logga in med dina autentiseringsuppgifter för Azure, och installerar Az PowerShell-modulen.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Anslut till Azure med en interaktiv dialogruta för inloggning
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

## <a name="define-url-match-conditions"></a>Definiera url matchningsvillkor
Definiera ett villkor för matchning av Webbadress (URL: en innehåller /promo) med hjälp av [New AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject).
I följande exempel matchar */promo* som värde för den *RequestUri* variabeln:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Skapa en regel för anpassade rate-gräns
Ange en frekvens gränsen med [New AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject). I följande exempel anges gränsen på 1000. Begäranden från klienter till sidan kampanj som överstiger 1000 under en minut blockeras tills nästa minut startar.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Konfigurera en säkerhetsprincip

Hitta namnet på resursgruppen som innehåller ytterdörren profil med `Get-AzureRmResourceGroup`. Konfigurera en säkerhetsprincip med en anpassad rate gränsen med [New AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) i den angivna resursgruppen som innehåller ytterdörren profilen.

I exemplet nedan använder Resursgruppnamnet *myResourceGroupFD1* profilen med antagandet att du har skapat åtkomsten med hjälp av instruktionerna i den [snabbstarten: Skapa en ytterdörren](quickstart-create-front-door.md) artikeln.

 using [New-AzFrontDoorFireWallPolicy](/powershell/module/Az.FrontDoor/New-AzFrontDoorFireWallPolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorFireWallPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Länk-princip till en ytterdörren frontend-värd
Länka säkerhetsobjekt för principen till en befintlig klient ytterdörren-värd och uppdatera ytterdörren egenskaper. Först hämta det ytterdörren objekt genom att använda [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) kommando.
Nu ska vi Konfigurera klient *WebApplicationFirewallPolicyLink* egenskap enligt den *resourceId* av den ”$ratePolicy” skapade i föregående steg med [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) kommandot. 

I exemplet nedan använder Resursgruppnamnet *myResourceGroupFD1* profilen med antagandet att du har skapat åtkomsten med hjälp av instruktionerna i den [snabbstarten: Skapa en ytterdörren](quickstart-create-front-door.md) artikeln. I det exemplet nedan ersätter $frontDoorName med namnet på din ytterdörren-profil. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Du behöver bara ange *WebApplicationFirewallPolicyLink* egenskap en gång för att länka en säkerhetsprincip för till en frontend ytterdörren. Efterföljande uppdateringar tillämpas automatiskt på klientdelen.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [ytterdörren](front-door-overview.md) 



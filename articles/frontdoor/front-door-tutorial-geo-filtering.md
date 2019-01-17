---
title: Självstudie – Konfigurera geofiltrering på en domän för Azure Front Door Service | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du skapar en enkel princip för geofiltrering och associerar principen med din befintliga Front Door-klientdelsvärd
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: 68da9a0255cde6cbad5c675901c80193888bf255
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214886"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>Så konfigurerar du en princip för geofiltrering för Front Door
Den här självstudiekursen visar hur du använder Azure PowerShell till att skapa ett exempel på en princip för geofiltrering och associerar principen med din befintliga Front Door-klientdelsvärd. Exempelprincipen för geofiltrering blockerar begäranden från alla andra länder utom USA.

## <a name="1-set-up-your-powershell-environment"></a>1. Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ instruktionerna på sidan, för att logga in med dina Azure-autentiseringsuppgifter och installera AzureRM.
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  Stöd för [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) kommer snart.

Se till att du har installerat den senaste versionen av PowerShellGet, innan du installerar Front Door-modulen. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

Installera AzureRM.FrontDoor-modulen. 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2. Definiera matchningsvillkor för geofiltrering
Skapa först ett exempel på matchningsvillkor som väljer begäranden som inte kommer från ”US” (USA). Se [PowerShell-guiden](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject) för parametrar när du skapar ett matchningsvillkor. [Här](front-door-geo-filtering.md) anges mappning till länder med landskoder på två bokstäver.

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3. Lägga till matchningsvillkor för geofiltrering i en regel med Action (Åtgärd) och Priority (Prioritet)

Skapa sedan ett CustomRule-objekt, `nonUSBlockRule`, utifrån matchningsvillkoret, en åtgärd och en prioritet.  En CustomRule (Anpassad regel) kan ha flera MatchCondition (Matchningsvillkor).  I det här exemplet anges Block (Blockera) för Action (Åtgärd) och 1 som Priority (Prioritet), högsta prioritetsnivån.

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

Se [PowerShell-guiden](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject) för parametrar när du skapar ett CustomRuleObject.

## <a name="4-add-rules-to-a-policy"></a>4. Lägga till regler i en princip
Det här steget skapar ett `geoPolicy`-principobjekt som innehåller `nonUSBlockRule` från tidigare steg i den angivna resursgruppen. Använd `Get-AzureRmResourceGroup` till att hitta ResourceGroupName $resourceGroup.

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

Se [PowerShell-guiden](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy) för parametrar när du skapar en princip.

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5. Länka principen till en Front Door-klientdelsvärd
De sista stegen är att länka skyddsprincipobjektet till en befintlig Front Door-klientdelsvärd och uppdatera Front Door-egenskaper. Du hämtar först ditt Front Door-objekt med hjälp av [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor), följt av att ange ställa in dess klientdelsegenskap WebApplicationFirewallPolicyLink på resourceId för `geoPolicy`.

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Använd följande [kommando](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor) till att uppdatera Front Door-objektet.

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Du behöver bara ange egenskapen WebApplicationFirewallPolicyLink en gång för att länka en skyddsprincip till en Front Door-klientdelsvärd. Efterföljande principuppdateringar tillämpas automatiskt på klientdelsvärden.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [säkerhet på programnivå med Front Door](front-door-application-security.md).
- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).

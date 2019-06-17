---
title: Konfigurera ett eget svar för Brandvägg för webbaserade program på Azure ytterdörren
description: Lär dig hur du konfigurerar en anpassad svarskod och ett meddelande när en begäran blockeras av Brandvägg för webbaserade program (WAF).
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: tyao;kumud
ms.openlocfilehash: d6d73055abe972cd3b6fee253b6bdb2b082ceca8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242977"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Konfigurera ett eget svar för Azure web application firewall

Som standard när Azure Brandvägg för webbaserade program (WAF) med Azure-åtkomsten blockerar en begäran på grund av en matchande regel returnerar statuskoden 403 med **begäran blockeras** meddelande. Den här artikeln beskriver hur du konfigurerar en anpassad Svarets statuskod och svarsmeddelandet när en begäran har blockerats av WAF.

## <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan, för att logga in med dina autentiseringsuppgifter för Azure, och installera Az PowerShell-modulen.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Anslut till Azure med en interaktiv dialogruta för inloggning
```
Connect-AzAccount
Install-Module -Name Az
```
Kontrollera att du har den aktuella versionen av installerat PowerShellGet. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Installera Az.FrontDoor-modulen 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. I det här exemplet skapar du en resursgrupp med hjälp av [New AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Skapa en ny WAF-princip med anpassade svar 

Nedan visas ett exempel för att skapa en ny WAF-princip med anpassade Svarets statuskod 405 och meddelande till **du blockeras.** med hjälp av [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Ändra anpassade svarskod eller svaret brödtext inställningarna för en befintlig WAF-princip med hjälp av [uppdatering AzFrontDoorFireWallPolicy ](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [ytterdörren](front-door-overview.md)
---
title: Konfigurera ett anpassat svar för WAF med Azure Front Door
description: Lär dig hur du konfigurerar en anpassad svarskod och ett meddelande när WAF (Web Application Firewall) blockerar en begäran.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185346"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Konfigurera ett anpassat svar för Azure Web Application-brandväggen

Som standard, när Azure Web Application Firewall (WAF) med Azure Front Door blockerar en begäran på grund av en matchad regel, returnerar den en 403-statuskod med **Begäran blockeras** meddelande. I den här artikeln beskrivs hur du konfigurerar en anpassad svarsstatuskod och svarsmeddelande när en begäran blockeras av WAF.

## <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ instruktionerna på sidan för att logga in med dina Azure-autentiseringsuppgifter och installera Az PowerShell-modulen.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ansluta till Azure med en interaktiv dialogruta för inloggning
```
Connect-AzAccount
Install-Module -Name Az
```
Kontrollera att du har den aktuella versionen av PowerShellGet installerad. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Installera Az.FrontDoor-modul 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. I det här exemplet skapar du en resursgrupp med hjälp av [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Skapa en ny WAF-princip med anpassat svar 

Nedan är ett exempel på att skapa en ny WAF-princip med anpassad svarsstatuskod inställd på 405 och meddelande till **Du är blockerad.** använda [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

Ändra anpassade svarskod- eller svarstextinställningar för en befintlig WAF-princip med hjälp av [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

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
- Läs mer om [brandvägg för webbprogram med Azure Front Door](../afds/afds-overview.md)
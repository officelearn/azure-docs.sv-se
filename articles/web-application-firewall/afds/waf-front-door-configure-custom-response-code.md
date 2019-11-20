---
title: Konfigurera ett anpassat svar för WAF med Azures frontend-dörr
description: Lär dig hur du konfigurerar en anpassad svarskod och ett meddelande när en begäran blockeras av brand vägg för webbaserade program (WAF).
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185346"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Konfigurera ett anpassat svar för Azure Web Application-brandvägg

Som standard när Azure WebApplication-brandväggen (WAF) med Azures frontend-enhet blockerar en begäran på grund av en matchad regel returneras en 403-status kod med **begäran blockerat** meddelande. Den här artikeln beskriver hur du konfigurerar en anpassad svars status kod och ett svarsmeddelande när en begäran blockeras av WAF.

## <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan för att logga in med dina Azure-autentiseringsuppgifter och installera AZ PowerShell-modulen.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Ansluta till Azure med en interaktiv dialog ruta för inloggning
```
Connect-AzAccount
Install-Module -Name Az
```
Kontrol lera att du har den aktuella versionen av PowerShellGet installerad. Kör kommandot nedan och öppna PowerShell igen.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Installera AZ. ytterdörr-modulen 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. I det här exemplet skapar du en resurs grupp med hjälp av [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Skapa en ny WAF-princip med anpassat svar 

Nedan visas ett exempel på hur du skapar en ny WAF-princip med anpassad svars status kod inställd på 405 och meddelandet till **du är blockerad.** använder [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

Ändra anpassade svars koder eller svars text inställningar för en befintlig WAF-princip med hjälp av [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

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
- Lär dig mer om [brand vägg för webbaserade program med Azures front dörr](../afds/afds-overview.md)
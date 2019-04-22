---
title: Distribuera Azure-brandväggen med hjälp av en mall
description: Distribuera Azure-brandväggen med hjälp av en mall
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 12/01/2018
ms.author: victorh
ms.openlocfilehash: e0fbec8b22993345114d8d6642e42095191d0b37
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046282"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Distribuera Azure-brandväggen med hjälp av en mall

Den [skapa AzureFirewall sandbox installationsprogrammet mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox) skapar en testmiljö för nätverk med en brandvägg. Nätverket har ett virtuellt nätverk (VNet) med tre undernät: *AzureFirewallSubnet*, *ServersSubnet*, och *JumpboxSubnet*. Den *ServersSubnet* och *JumpboxSubnet* undernätet varje har en enda, två kärnor Windows Server-datorn.

Brandväggen finns i den *AzureFirewallSubnet* undernät, och har en regelsamling för program med en enda regel som tillåter åtkomst till *www.microsoft.com*.

En användardefinierad väg pekar nätverkstrafik från den *ServersSubnet* undernätet via brandväggen, där brandväggsreglerna tillämpas.

Mer information om Azure-brandväggen finns i [distribuera och konfigurera Azure-brandväggen med hjälp av Azure-portalen](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Använda mallen för att distribuera Azure-brandvägg

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

**Installera och distribuera Azure-brandvägg med hjälp av mallen:**

1. Komma åt mallen [ https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox).
   
1. Läs introduktionen och när du är klar att distribuera Välj **distribuera till Azure**.
   
1. Logga in på Azure-portalen om det behövs. 

1. I portalen på den **skapa en sandbox-inställningar för AzureFirewall** sidan, Skriv eller Välj följande värden:
   
   - **Resursgrupp**: Välj **Skapa nytt**, Skriv ett namn för resursgruppen och välj **OK**. 
   - **Namn på virtuellt nätverk**: Skriv ett namn för det nya VNet. 
   - **Administratörens användarnamn**: Ange ett användarnamn för administratörskontot.
   - **Adminlösenord**: Ange ett administratörslösenord. 
   
1. Läs villkoren och välj sedan **jag godkänner villkoren som anges ovan**.
   
1. Välj **Köp**.
   
   Det tar några minuter att skapa resurser. 
   
1. Utforska de resurser som har skapats med brandväggen. 

Mer information om JSON-syntax och egenskaper för en brandvägg i en mall, se [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem. Du kan ta bort den resursgrupp, brandvägg och alla relaterade resurser genom att köra den [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell-kommando. Ta bort en resursgrupp med namnet *MyResourceGroup*, kör: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Ta inte bort resursgruppen och brandväggen ännu, om du planerar att fortsätta till brandväggen övervakning av självstudien. 

## <a name="next-steps"></a>Nästa steg

Därefter kan du övervaka Azure brandväggsloggar:

> [!div class="nextstepaction"]
> [Självstudier: Monitor Azure Firewall-loggar](./tutorial-diagnostics.md)

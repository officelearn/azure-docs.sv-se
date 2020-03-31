---
title: Distribuera Azure Firewall med hjälp av en mall
description: Distribuera Azure-brandväggen med hjälp av en mall. Det skapade nätverket har ett virtuella nätverk med tre undernät. Virtuella datorer med två kärnor distribueras.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169205"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Distribuera Azure Firewall med hjälp av en mall

[Installationsmallen Skapa AzureFirewall-sandlåda](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) skapar en testnätverksmiljö med en brandvägg. Nätverket har ett virtuellt nätverk (VNet) med tre undernät: *AzureFirewallSubnet*, *ServersSubnet*och *JumpboxSubnet*. Undernätet *ServersSubnet* och *JumpboxSubnet* har var och en en virtuell dator med två kärnor.

Brandväggen finns i *undernätet AzureFirewallSubnet* och har en programregelsamling med `www.microsoft.com`en enda regel som ger åtkomst till .

En användardefinierad väg pekar nätverkstrafik från *ServersSubnet-undernätet* genom brandväggen, där brandväggsreglerna tillämpas.

Mer information om Azure-brandväggen finns i [Distribuera och konfigurera Azure-brandväggen med Azure-portalen](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Använda mallen för att distribuera Azure-brandväggen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

**Så här installerar och distribuerar du Azure-brandväggen med hjälp av mallen:**

1. Öppna mallen [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)på .
   
1. Läs introduktionen och välj Distribuera **till Azure**när du är redo att distribuera .
   
1. Om det behövs loggar du in på Azure-portalen. 

1. Skriv eller välj följande värden på sidan Skapa en sandbox på sidan Skapa en sandbox på sidan **AzureFirewall:**
   
   - **Resursgrupp**: Välj **Skapa nytt**, skriv ett namn för resursgruppen och välj **OK**. 
   - **Namn på virtuellt nätverk:** Skriv ett namn på det nya virtuella nätverket. 
   - **Administratörsanvändarnamn**: Skriv ett användarnamn för administratörsanvändarkontot.
   - **Admin Lösenord:** Skriv ett administratörslösenord. 
   
1. Läs villkoren och välj sedan **jag godkänner de villkor som anges ovan**.
   
1. Välj **Köp**.
   
   Det tar några minuter att skapa resurserna. 
   
1. Utforska de resurser som har skapats med brandväggen. 

Mer information om JSON-syntaxen och egenskaperna för en brandvägg i en mall finns i [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem kan du ta bort resursgruppen, brandväggen och alla relaterade resurser genom att köra kommandot [Ta bort AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell. Så här tar du bort en resursgrupp med namnet *MyResourceGroup:* 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Ta inte bort resursgruppen och brandväggen ännu, om du planerar att fortsätta till brandväggen övervakning handledning. 

## <a name="next-steps"></a>Nästa steg

Därefter kan du övervaka Azure-brandväggsloggarna:

> [!div class="nextstepaction"]
> [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)

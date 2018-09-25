---
title: Distribuera Azure-brandväggen med hjälp av en mall
description: Distribuera Azure-brandväggen med hjälp av en mall
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: d32e6e29c287d140c28206743e36dc025b26158b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991342"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Distribuera Azure-brandväggen med hjälp av en mall

Den här mallen skapar en brandvägg och en testmiljö för nätverket. Nätverket har ett virtuellt nätverk med tre undernät: *AzureFirewallSubnet*, *ServersSubnet*, och en *JumpboxSubnet*. ServersSubnet och JumpboxSubnet har var sin Windows Server med två kärnor.

Brandväggen ligger i AzureFirewallSubnet och är konfigurerad med en programregelsamling som har en enda regel för att tillåta åtkomst till www.microsoft.com.

Dessutom skapas en användardefinierad väg som skickar nätverkstrafik från ServersSubnet via brandväggen där brandväggsreglerna tillämpas.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="template-location"></a>Mallplats

Mallen finns på:

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)

Läs introduktionen och när du är klar att distribuera på **distribuera till Azure**.

## <a name="clean-up-resources"></a>Rensa resurser

Utforska de resurser som har skapats med brandvägg och sedan vid först inte längre behövs kan du använda den [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) kommando för att ta bort resursgruppen, brandvägg och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>Nästa steg

Därefter kan du övervaka Azure brandväggsloggar:

- [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)


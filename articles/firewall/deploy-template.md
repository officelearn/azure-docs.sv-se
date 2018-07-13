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
ms.openlocfilehash: 1a732e22d72c36afe11030e42bae529baa35df1a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991551"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Distribuera Azure-brandväggen med hjälp av en mall

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Exemplen i brandväggen för Azure-artiklar förutsätter att du redan har aktiverat den offentliga förhandsversionen av Azure-brandvägg. Mer information finns i [aktivera offentliga förhandsversionen av Azure-brandvägg](public-preview.md).

Den här mallen skapar en brandvägg och en testmiljö för nätverket. Nätverket har ett virtuellt nätverk med tre undernät: *AzureFirewallSubnet*, *ServersSubnet*, och en *JumpboxSubnet*. ServersSubnet och JumpboxSubnet har du en kärnor 2 Windows Server i dem.

Brandväggen är i AzureFirewallSubnet och är konfigurerad med en regelsamling för program med en enda regel som tillåter åtkomst till www.microsoft.com.

En användardefinierad väg skapas som pekar nätverkstrafiken från ServersSubnet genom brandväggen, där brandväggsreglerna tillämpas.

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

- [Självstudie: Övervaka Azure brandväggsloggar](./tutorial-diagnostics.md)


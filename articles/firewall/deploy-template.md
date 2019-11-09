---
title: Distribuera Azure-brandväggen med en mall
description: Distribuera Azure-brandväggen med en mall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/9/2018
ms.author: victorh
ms.openlocfilehash: c0a6cda54a58e3cc03ba31e221fb57fc725dd779
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839394"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Distribuera Azure-brandväggen med en mall

[Installations mal len skapa AzureFirewall sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) skapar en test nätverks miljö med en brand vägg. Nätverket har ett virtuellt nätverk (VNet) med tre undernät: *AzureFirewallSubnet*, *ServersSubnet*och *JumpboxSubnet*. *ServersSubnet* -och *JumpboxSubnet* -undernätet har en enda virtuell Windows Server-dator med två kärnor.

Brand väggen finns i *AzureFirewallSubnet* -undernätet och har en samlad program regel med en regel som ger åtkomst till `www.microsoft.com`.

En användardefinierad väg pekar nätverks trafik från *ServersSubnet* -undernätet via brand väggen, där brand Väggs reglerna tillämpas.

Mer information om Azure-brandväggen finns i [distribuera och konfigurera Azure-brandväggen med hjälp av Azure Portal](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Använd mallen för att distribuera Azure-brandväggen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

**Installera och Distribuera Azure-brandväggen med hjälp av mallen:**

1. Öppna mallen på [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox).
   
1. Läs introduktionen och välj **distribuera till Azure**när du är redo att distribuera.
   
1. Logga in på Azure Portal vid behov. 

1. I portalen, på sidan **skapa en sandbox-installation av AzureFirewall** , skriver eller väljer du följande värden:
   
   - **Resurs grupp**: Välj **Skapa ny**, Skriv ett namn för resurs gruppen och välj **OK**. 
   - **Virtual Network namn**: Ange ett namn för det nya virtuella nätverket. 
   - **Admin-användar namn**: Ange ett användar namn för administratörs användar kontot.
   - **Administratörs lösen ord**: Ange ett administratörs lösen ord. 
   
1. Läs de allmänna villkoren och välj sedan **Jag accepterar villkoren som anges ovan**.
   
1. Välj **Köp**.
   
   Det tar några minuter att skapa resurserna. 
   
1. Utforska de resurser som har skapats med brand väggen. 

Information om JSON-syntaxen och egenskaperna för en brand vägg i en mall finns i [Microsoft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem kan du ta bort resurs gruppen, brand väggen och alla relaterade resurser genom att köra PowerShell [-kommandot Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Om du vill ta bort en resurs grupp med namnet *MyResourceGroup*kör du: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Ta inte bort resurs gruppen och brand väggen ännu, om du planerar att fortsätta till själv studie kursen om brand Väggs övervakning. 

## <a name="next-steps"></a>Nästa steg

Sedan kan du övervaka Azure Firewall-loggarna:

> [!div class="nextstepaction"]
> [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)

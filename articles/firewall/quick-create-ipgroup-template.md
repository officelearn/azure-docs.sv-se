---
title: 'Snabbstart: Skapa en Azure-brandvägg och IP-grupper – Resource Manager-mall'
description: Lär dig hur du använder en Resource Manager-mall för att skapa en Azure-brandvägg och IP-grupper.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 767e91af7fb5ff3de517a79ac4df55af5057c76f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679837"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>Snabbstart: Skapa en Azure-brandvägg och IP-grupper – Resource Manager-mall

I den här snabbstarten använder du en Resource Manager-mall för att distribuera en Azure-brandvägg med exempel-IP-grupper som används i en nätverksregel och programregel.

En IP-grupp är en resurs på den högsta nivån som gör att du kan definiera och gruppera IP-adresser, intervall och undernät till ett enda objekt. Detta är användbart för att hantera IP-adresser i Azure Firewall-regler. Du kan antingen ange IP-adresser manuellt eller importera dem från en fil.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-and-ip-groups"></a>Skapa en Azure-brandvägg och IP-grupper

Den här mallen skapar en Azure-brandvägg och IP-grupper, tillsammans med nödvändiga resurser för att stödja Azure-brandväggen.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/publicIPAdresser**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera Resource Manager-mall till Azure:

1. Välj **Distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en Azure-brandvägg, nätverksinfrastrukturen och två virtuella datorer.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json"><img src="./media/quick-create-ipgroup-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Skriv eller välj följande värden på sidan **Skapa en Azure-brandvägg med IpGroups** i portalen:
   - Prenumeration: Välj bland befintliga prenumerationer 
   - Resursgrupp: Välj från befintliga resursgrupper eller välj **Skapa ny**och välj **OK**.
   - Plats: Välj en plats
   - Namn på virtuellt nätverk: Skriv ett namn på det nya virtuella nätverket (VNet) 
   - IP-gruppnamn 1: Typnamn för IP-grupp 1 
   - IP-gruppnamn 2: Typnamn för IP-grupp 2 
   - Administratörsanvändarnamn: Skriv användarnamn för administratörsanvändarkontot 
   - Autentisering: Välj sshPublicKey eller lösenord 
   - Administratörslösenord: Ange ett administratörslösenord eller en nyckel

3. Välj **Jag godkänner de villkor som anges ovan** och välj sedan **Köp**. Distributionen kan ta 10 minuter eller längre att slutföra.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

I Azure-portalen granskar du de distribuerade resurserna, särskilt brandväggsreglerna som använder IP-grupper.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP-grupper.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Nätverksregler.":::

Mer information om JSON-syntaxen och egenskaperna för en brandvägg i en mall finns i [Mallreferens för Microsoft.Network azureFirewalls](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du har skapat med brandväggen tar du bort resursgruppen. Detta tar bort brandväggen och alla relaterade resurser.

Om du vill ta `Remove-AzResourceGroup` bort resursgruppen anropar du cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Distribuera och konfigurera Azure-brandväggen i ett hybridnätverk med Azure-portalen](tutorial-hybrid-portal.md)
---
title: 'Snabbstart: Skapa en Azure-brandvägg med flera offentliga IP-adresser - Resource Manager-mall'
description: Lär dig hur du använder en Resource Manager-mall för att skapa en Azure-brandvägg med flera offentliga IP-adresser.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 76211014f96aee864412fd19686d58d0338ec5c2
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310240"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Snabbstart: Skapa en Azure-brandvägg med flera offentliga IP-adresser - Resource Manager-mall

I den här snabbstarten använder du en Resource Manager-mall för att distribuera en Azure-brandvägg med flera offentliga IP-adresser.

Den distribuerade brandväggen har REGLER för insamling av NAT-regler som tillåter RDP-anslutningar till två virtuella Windows Server 2019-datorer.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Mer information om Azure-brandväggen med flera offentliga IP-adresser finns i [Distribuera en Azure-brandvägg med flera offentliga IP-adresser med Azure PowerShell](deploy-multi-public-ip-powershell.md).

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall"></a>Skapa en Azure-brandvägg

Den här mallen skapar en Azure-brandvägg med två offentliga IP-adresser, tillsammans med nödvändiga resurser för att stödja Azure-brandväggen.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/fw-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

Flera Azure-resurser definieras i mallen:

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

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json"><img src="./media/quick-create-multiple-ip-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Skriv eller välj följande värden på sidan **Skapa en Azure-brandvägg med flera IP-adresser** i portalen:
   - Prenumeration: Välj bland befintliga prenumerationer 
   - Resursgrupp: Välj från befintliga resursgrupper eller välj **Skapa ny**och välj **OK**.
   - Plats: Välj en plats
   - Administratörsanvändarnamn: Skriv användarnamn för administratörsanvändarkontot 
   - Administratörslösenord: Ange ett administratörslösenord eller en nyckel

3. Välj **Jag godkänner de villkor som anges ovan** och välj sedan **Köp**. Distributionen kan ta 10 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Granska de distribuerade resurserna i Azure-portalen. Observera brandväggens offentliga IP-adresser.  

Använd Anslutning till fjärrskrivbord för att ansluta till brandväggens offentliga IP-adresser. Lyckade anslutningar demonstrerar NAT-regler för brandvägg som tillåter anslutningen till serverdaservrarna.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du har skapat med brandväggen tar du bort resursgruppen. Detta tar bort brandväggen och alla relaterade resurser.

Om du vill ta `Remove-AzResourceGroup` bort resursgruppen anropar du cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Distribuera och konfigurera Azure-brandväggen i ett hybridnätverk med Azure-portalen](tutorial-hybrid-portal.md)
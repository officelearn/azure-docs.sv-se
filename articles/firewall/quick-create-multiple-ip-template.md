---
title: 'Snabb start: skapa en Azure-brandvägg med flera offentliga IP-adresser – Resource Manager-mall'
description: Lär dig hur du använder en Resource Manager-mall för att skapa en Azure-brandvägg med flera offentliga IP-adresser.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: b81362f191e6317aa7ffa974a6d432b0c7514d8f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680550"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Snabb start: skapa en Azure-brandvägg med flera offentliga IP-adresser – Resource Manager-mall

I den här snabb starten använder du en Resource Manager-mall för att distribuera en Azure-brandvägg med flera offentliga IP-adresser. Den distribuerade brand väggen har regel insamlings regler i NAT som tillåter RDP-anslutningar till två virtuella Windows Server 2019-datorer.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Mer information om Azure-brandväggen med flera offentliga IP-adresser finns i [distribuera en Azure-brandvägg med flera offentliga IP-adresser med hjälp av Azure PowerShell](deploy-multi-public-ip-powershell.md).

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall"></a>Skapa en Azure-brandvägg

Den här mallen skapar en Azure-brandvägg med två offentliga IP-adresser, tillsammans med nödvändiga resurser för att stödja Azure-brandväggen.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/fw-docs-qs).

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera Resource Manager-mall till Azure:

1. Välj **distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en Azure-brandvägg, nätverks infrastrukturen och två virtuella datorer.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. På sidan **skapa en Azure-brandvägg med flera offentliga IP-adresser** i portalen skriver eller väljer du följande värden:
   - Prenumeration: Välj från befintliga prenumerationer 
   - Resurs grupp: Välj från befintliga resurs grupper eller Välj **Skapa ny**och välj **OK**.
   - Plats: Välj en plats
   - Admin-användar namn: Ange användar namn för administratörs användar kontot 
   - Administratörs lösen ord: Ange ett administratörs lösen ord eller en nyckel

3. Välj **Jag accepterar de villkor som anges ovan** och välj sedan **köp**. Distributionen kan ta 10 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Granska de distribuerade resurserna i Azure Portal. Observera brand väggens offentliga IP-adresser.  

Använd Anslutning till fjärrskrivbord för att ansluta till brand väggens offentliga IP-adresser. Lyckade anslutningar visar NAT-regler för brand vägg som tillåter anslutning till backend-servrar.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med brand väggen tar du bort resurs gruppen. Detta tar bort brand väggen och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Distribuera och konfigurera Azure-brandväggen i ett hybrid nätverk med hjälp av Azure Portal](tutorial-hybrid-portal.md)
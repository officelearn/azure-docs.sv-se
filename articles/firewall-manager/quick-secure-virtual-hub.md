---
title: 'Snabb start: säker virtuell hubb med Azure Firewall Manager Preview – Resource Manager-mall'
description: Lär dig hur du skyddar din virtuella hubb med Azure Firewall Manager Preview.
services: firewall-manager
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 05/19/2020
ms.author: victorh
ms.openlocfilehash: b9839e51fcea1e8fe4adc4760e16ae2d73b163ee
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83694237"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---resource-manager-template"></a>Snabb start: skydda din virtuella hubb med Azure Firewall Manager – Resource Manager-mall

I den här snabb starten använder du en Resource Manager-mall för att skydda den virtuella hubben med hjälp av Azure Firewall Manager Preview.

Den distribuerade brand väggen har en program regel som tillåter anslutningar till `www.microsoft.com` . Två virtuella Windows Server 2019-datorer distribueras för att testa brand väggen. En hopp Server används för att ansluta till arbets belastnings servern. Från arbets belastnings servern kan du bara ansluta till `www.microsoft.com` .

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Mer information om för hands versionen av Azure Firewall Manager finns i [Vad är för hands versionen av Azure Firewall Manager?](overview.md).

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-secured-virtual-hub"></a>Skapa en säker virtuell hubb

Den här mallen skapar en säker virtuell hubb med Azure Firewall Manager Preview, tillsammans med nödvändiga resurser för att stödja scenariot.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://github.com/Azure/azure-quickstart-templates/blob/master/fwm-docs-qs/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json" range="001-477" highlight="47-76":::

Flera Azure-resurser definieras i mallen:

- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft. Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft. Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)

### <a name="deploy-the-template"></a>Distribuera mallen

Distribuera Resource Manager-mall till Azure:

1. Välj **distribuera till Azure** för att logga in på Azure och öppna mallen. Mallen skapar en Azure-brandvägg, ett virtuellt WAN-nätverk och en virtuell hubb, nätverks infrastrukturen och två virtuella datorer.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. I portalen på sidan **skyddade virtuella hubbar** skriver eller väljer du följande värden:
   - Prenumeration: Välj från befintliga prenumerationer 
   - Resurs grupp: Välj från befintliga resurs grupper eller Välj **Skapa ny**och välj **OK**.
   - Plats: Välj en plats
   - Admin-användar namn: Ange användar namn för administratörs användar kontot 
   - Administratörs lösen ord: Ange ett administratörs lösen ord eller en nyckel

3. Välj **Granska + skapa** och välj sedan **Skapa**. Distributionen kan ta 10 minuter eller längre att slutföra.

## <a name="validate-the-deployment"></a>Verifiera distributionen

Testa nu brand Väggs reglerna för att bekräfta att den fungerar som förväntat.

1. Från Azure Portal granskar du nätverks inställningarna för den virtuella **arbets belastnings-SRV-** datorn och noterar den privata IP-adressen.
2. Anslut ett fjärr skrivbord till den virtuella datorn för att **hoppa över SRV** och logga in. Därifrån öppnar du en fjärr skrivbords anslutning till den privata IP-adressen för **arbets belastningen** .

3. Öppna Internet Explorer och navigera till `www.microsoft.com`.
4. Välj **OK**  >  **Stäng** i Internet Explorers säkerhets aviseringar.

   Du bör se Microsofts hem sida.

5. Bläddra till `www.google.com`.

   Du bör blockeras av brandväggen.

Nu har du verifierat att brand Väggs reglerna fungerar:

* Du kan bläddra till en tillåten FQDN, men inte till andra.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som du skapade med brand väggen tar du bort resurs gruppen. Detta tar bort brand väggen och alla relaterade resurser.

Anropa cmdleten om du vill ta bort resurs gruppen `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs om betrodda säkerhets partner](trusted-security-partners.md)

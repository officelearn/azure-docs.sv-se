---
title: Snabb start – skapa en skalnings uppsättning för en virtuell Windows-dator med en Azure-mall
description: Lär dig hur du snabbt skapar en skalningsuppsättning för virtuella Windows-datorer med en Azure Resource Manager-mall som distribuerar en exempelapp och konfigurerar regler för automatisk skalning
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 8bf75dc08a033c254152cb6e5b6f3af6389d8380
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198210"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Snabbstart: Skapa en VM-skalningsuppsättning med en Azure-mall i Windows

Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt eller definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. En Azure-lastbalanserare distribuerar sedan trafiken till de virtuella datorinstanserna i skalningsuppsättningen. I den här snabbstarten skapar du en VM-skalningsuppsättning och distribuerar ett exempelprogram med en Azure Resource Manager-mall.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Inga.

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Du kan distribuera grupper av relaterade resurser med hjälp av Azure Resource Manager-mallar. Med en enda mall kan du skapa VM-skalningsuppsättningen, installera program och ange regler för automatisk skalning. Du kan återanvända mallen och använda variabler och parametrar till att uppdatera befintliga, eller skapa ytterligare, skalningsuppsättningar. Du kan distribuera mallar via Azure Portal, Azure CLI, Azure PowerShell eller från pipeliner för kontinuerlig integrering/kontinuerlig leverans (CI/CD).

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/201-vmss-windows-webapp-dsc-autoscale/).

:::code language="json" source="~/quickstart-templates/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json" range="1-397" highlight="236-325":::

Dessa resurser definieras i följande mallar:

- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/belastningsutjämnare**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft. Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

#### <a name="define-a-scale-set"></a>Definiera en skalningsuppsättning

Den markerade delen är resurs definitionen för skalnings uppsättningen. När du skapar en skalningsuppsättning med en mall definierar du lämpliga resurser. Huvuddelarna i resurstypen för VM-skalningsuppsättning:

| Egenskap                     | Egenskapsbeskrivning                                  | Exempelmallvärde                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| typ                         | Azure-resurstypen som ska skapas                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | Namnet på skalningsuppsättningen                                       | myScaleSet                                |
| location                     | Platsen där skalningsuppsättningen skapas                     | USA, östra                                   |
| sku.name                     | VM-storleken för varje skalningsuppsättningsinstans                  | Standard_A1                               |
| sku.capacity                 | Antal VM-instanser som skapas inledningsvis           | 2                                         |
| upgradePolicy.mode           | Uppgraderingsläge för VM-instanser när ändringar sker              | Automatiskt                                 |
| imageReference               | Plattform eller anpassad avbildning som ska användas för VM-instanserna | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Namnprefix för varje VM-instans                     | myvmss                                    |
| osProfile.adminUsername      | Användarnamn för varje VM-instans                        | azureuser                                 |
| osProfile.adminPassword      | Lösenord för varje VM-instans                        | P@ssw0rd!                                 |

Om du vill anpassa en mall för skalnings uppsättningar kan du ändra storleken på den virtuella datorn eller den ursprungliga kapaciteten. Ett annat alternativ är att använda en annan plattform eller en anpassad avbildning.

#### <a name="add-a-sample-application"></a>Lägg till ett exempelprogram

Testa din skalningsuppsättning genom att installera ett grundläggande webbprogram. När du distribuerar en skalningsuppsättning kan du använda VM-tillägg för att utföra konfigurations- och automatiseringsåtgärder efter distributionen, till exempel installera en app. Skript kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Om du vill använda ett tillägg för skalningsuppsättningen lägger du till avsnittet *extensionProfile* i föregående resursexempel. Tilläggsprofilen definierar vanligtvis följande egenskaper:

- Tilläggstyp
- Tilläggsutgivare
- Tilläggsversion
- Plats för konfiguration eller installation av skript
- Kommandon som körs för VM-instanser

Mallen använder PowerShell DSC-tillägget för att installera en ASP.NET MVC-app som körs i IIS.

Ett installationsskript hämtas från GitHub enligt definitionen i *url*. Tillägget kör sedan *InstallIIS* från skriptet *IISInstall.ps1* enligt definitionen i *funktion* och *Skript*. Själva ASP.NET-appen tillhandahålls som ett webbdistributionspaket, vilket också hämtas från GitHub enligt definitionen i *WebDeployPackagePath*:

## <a name="deploy-the-template"></a>Distribuera mallen

Du kan distribuera mallen genom att välja knappen **distribuera till Azure** . Knappen öppnar Azure Portal, läser in hela mallen och frågar efter ett par parametrar, till exempel namn på skalningsuppsättningen, antal instanser och administratörsautentiseringsuppgifter.

[![Distribuera mall till Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Du kan också distribuera en Resource Manager-mall genom att använda Azure PowerShell:

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

Svara på frågorna och ange namn på skalningsuppsättningen och administratörsautentiseringsuppgifter för VM-instanserna. Det kan ta 10–15 minuter för skalningsuppsättningen att skapas och tillämpa tillägget för att konfigurera appen.

## <a name="test-the-deployment"></a>Testa distributionen

Om du vill se din skalningsuppsättning i praktiken så gå till exempelwebbprogrammet i en webbläsare. Hämta den offentliga IP-adressen för lastbalanseraren med [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) enligt följande:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Ange den offentliga IP-adressen för belastningsutjämnaren i en webbläsare i formatet *http: \/ /publicIpAddress/MyApp*. Lastbalanseraren distribuerar trafik till en av dina VM-instanser enligt följande exempel:

![Köra IIS-webbplats](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen och skalningsuppsättningen. Parametern `-Force` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång. Parametern `-AsJob` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Windows-skalningsuppsättning med en Azure-mall och använde PowerShell DSC-tillägget för att installera en grundläggande ASP.NET-app på VM-instanser. Om du vill veta mer kan du gå till självstudiekursen om hur man skapar och hanterar VM-skalningsuppsättningar för Azure.

> [!div class="nextstepaction"]
> [Skapa och hantera VM-skalningsuppsättningar för Azure](tutorial-create-and-manage-powershell.md)

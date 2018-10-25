---
title: Snabbstart – Skapa en VM-skalningsuppsättning med Azure PowerShell | Microsoft Docs
description: Lär dig hur du snabbt skapar en VM-skalningsuppsättning med Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/18
ms.author: zarhoads
ms.openlocfilehash: 6f37a9cb486f7d40506928e751e189843af69528
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467488"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-azure-powershell"></a>Snabbstart: Skapa en VM-skalningsuppsättning med Azure PowerShell
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt eller definiera regler för automatisk skalning baserat på resursanvändning som CPU, minnesefterfrågan eller nätverkstrafik. En Azure-lastbalanserare distribuerar sedan trafiken till de virtuella datorinstanserna i skalningsuppsättningen. I den här snabbstarten skapar du en VM-skalningsuppsättning och distribuerar ett exempelprogram med Azure PowerShell.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt krävs version 6.0.0 eller senare av Azure PowerShell-modulen i den här självstudiekursen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Skapa en VM-skalningsuppsättning med [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som använder *Windows Server 2016 Datacenter*-plattformsavbildningen. Azure-nätverksresurser för virtuellt nätverk, offentlig IP-adress och lastbalanserare skapas automatiskt. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


## <a name="deploy-sample-application"></a>Distribuera exempelprogram
Testa din skalningsuppsättning genom att installera ett grundläggande webbprogram. Det anpassade skripttillägget i Azure används för att hämta och köra ett skript som installerar IIS på de virtuella datorinstanserna. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Mer information finns i [översikten över tillägget för anpassat skript](../virtual-machines/windows/extensions-customscript.md).

Använd det anpassade skripttillägget för att installera en grundläggande IIS-webbserver. Tillämpa tillägget för anpassat skript som installerar IIS på följande sätt:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroup" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Om du vill se din skalningsuppsättning i praktiken så gå till exempelwebbprogrammet i en webbläsare. Hämta den offentliga IP-adressen för din lastbalanserare med [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). I följande exempel hämtas den IP-adress som skapats i resursgruppen *myResourceGroup*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Skriv den offentliga IP-adressen för lastbalanseraren i en webbläsare. Lastbalanseraren distribuerar trafik till en av dina VM-instanser enligt följande exempel:

![Köra IIS-webbplats](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Rensa resurser
När resursgruppen inte längre behövs kan du använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort den, skalningsuppsättningen och alla relaterade resurser på följande sätt. Parametern `-Force` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång. Parametern `-AsJob` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nästa steg
I den här snabbstarten skapade du en grundläggande skalningsuppsättning och använde det anpassade skripttillägget för att installera en grundläggande IIS-webbserver på de virtuella datorinstanserna. Om du vill veta mer kan du gå till självstudiekursen om hur man skapar och hanterar VM-skalningsuppsättningar för Azure.

> [!div class="nextstepaction"]
> [Skapa och hantera VM-skalningsuppsättningar för Azure](tutorial-create-and-manage-powershell.md)

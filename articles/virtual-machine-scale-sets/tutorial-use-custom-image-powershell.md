---
title: Självstudie – Använd en anpassad virtuell datoravbildning i en skalningsuppsättning med Azure PowerShell | Microsoft Docs
description: Läs hur du använder Azure PowerShell för att skapa en anpassad virtuell datoravbildning som du kan använda för att distribuera en VM-skalningsuppsättning
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a44df847ebc7793cf231c542d2267b267fe59432
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365907"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Självstudie: Skapa och använd en anpassad avbildning för VM-skalningsuppsättningar med Azure PowerShell
När du skapar en skalningsuppsättning, kan du ange en avbildning som ska användas när de virtuella datorinstanserna distribueras. Om du vill minska antalet uppgifter när de virtuella datorinstanserna distribueras, kan du använda en anpassad virtuell datoravbildning. Den här anpassade virtuella datoravbildningen inkluderar alla nödvändiga programinstallationer eller konfigurationer. Alla virtuella datorinstanser som skapats i skalningsuppsättningen använder den anpassade virtuella datoravbildningen och är redo att hantera din programtrafik. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa och anpassa en virtuell dator
> * Avetablera och generalisera den virtuella datorn
> * Skapa en anpassad virtuell datoravbildning från den virtuella källdatorn
> * Distribuera en skalningsuppsättning som använder den anpassade virtuella datoravbildningen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt krävs version 6.0.0 eller senare av Azure PowerShell-modulen i den här självstudiekursen. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure. 


## <a name="create-and-configure-a-source-vm"></a>Skapa och konfigurera en virtuell källdator

>[!NOTE]
> Den här självstudien visar processen att skapa och använda en generaliserad VM-avbildning. Det går inte att skapa en skalningsuppsättning från en särskild VM-avbildning.

Först skapar du en resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) därefter skapar du en virtuell dator med [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Den här virtuella datorn används sedan som källan för en anpassad virtuell datoravbildning. Följande exempel skapar en virtuell dator som heter *myCustomVM* i resursgruppen med namnet *myResourceGroup*. När du uppmanas, anger du ett användarnamn och lösenord som ska användas som autentiseringsuppgifter för den virtuella datorn:

```azurepowershell-interactive
# Create a resource a group
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"

# Create a Windows Server 2016 Datacenter VM
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Name "myCustomVM" `
  -ImageName "Win2016Datacenter" `
  -OpenPorts 3389
```

Om du vill ansluta till din virtuella dator, listar du den offentliga IP-adressen med [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) enligt följande:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Skapa en fjärranslutning med den virtuella datorn. Om du använder Azure Cloud Shell, utför du den här åtgärden från en lokal PowerShell-kommandotolk eller klienten för fjärrskrivbord. Ange din egen IP-adress från det föregående kommandot. När du uppmanas, anger du de autentiseringsuppgifter som användes när du skapade den virtuella datorn i det första steget:

```powershell
mstsc /v:<IpAddress>
```

Nu ska vi installera en grundläggande webbserver för att anpassa din virtuella dator. När den virtuella datorinstansen i skalningsuppsättningen sen distribueras så har den alla nödvändiga paket för att köra ett webbprogram. Öppna en lokal PowerShell-kommandotolk på den virtuella datorn och installera IIS-webbservern med [Install-WindowsFeature](/powershell/module/servermanager/install-windowsfeature) på följande sätt:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Det sista steget för att förbereda din virtuella dator för användning som en anpassad avbildning är att generalisera den virtuella datorn. Sysprep tar bort all personlig kontoinformation och konfigurationer och återställer den virtuella datorn till ett rent tillstånd för framtida distributioner. Mer information finns i [Så här använder du Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx).

Om du vill generalisera den virtuella datorn, kör du Sysprep och konfigurerar den virtuella datorn för en helt ny upplevelse. När du är klar, instruerar du Sysprep att stänga av den virtuella datorn:

```powershell
C:\Windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown
```

Fjärranslutningen till den virtuella datorn stängs automatiskt när Sysprep slutför processen och den virtuella datorn stängs av.


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Skapa en anpassad virtuell datoravbildning från den virtuella källdatorn
Den virtuella källdatorn är nu anpassad med IIS-webbservern som är installerad. Nu ska vi skapa den anpassade virtuella datoravbildningen för att använda med en skalningsuppsättning.

Om du vill skapa en avbildning måste den virtuella datorn frigöras. Frigör den virtuella datorn med [Stop-AzureRmVm](/powershell/module/azurerm.compute/stop-azurermvm). Slutligen anger du tillståndet för den virtuella datorn som generaliserad med [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm) så att Azure-plattformen vet att den virtuella datorn är redo för användning som en anpassad avbildning. Du kan bara skapa en avbildning från en generaliserad virtuell dator:

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Force
Set-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myCustomVM" -Generalized
```

Det kan ta några minuter att frigöra och generalisera den virtuella datorn.

Nu skapar du en avbildning av den virtuella datorn med [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) och [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). Följande exempel skapar en avbildning med namnet *myImage* från din virtuella dator:

```azurepowershell-interactive
# Get VM object
$vm = Get-AzureRmVM -Name "myCustomVM" -ResourceGroupName "myResourceGroup"

# Create the VM image configuration based on the source VM
$image = New-AzureRmImageConfig -Location "EastUS" -SourceVirtualMachineId $vm.ID 

# Create the custom VM image
New-AzureRmImage -Image $image -ImageName "myImage" -ResourceGroupName "myResourceGroup"
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Skapa en skalningsuppsättning från den anpassad virtuella datoravbildningen
Nu skapar du en skalningsuppsättning med [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) som använder sig av `-ImageName`-parametern för att definiera den anpassade virtuella datoravbildningen som skapades i föregående steg. För att distribuera trafik till flera virtuella datorinstanser så skapas även en belastningsutjämnare. Belastningsutjämnaren innehåller regler för att distribuera trafik på TCP-port 80 och för att tillåta trafik för fjärrskrivbordet på TCP-port 3389 och PowerShell-fjärrkommunikation på TCP-port 5985. När du uppmanas, anger du dina egna önskade administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättning:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "myImage"
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Om du vill testa din skalningsuppsättning, hämtar du den offentliga IP-adressen för din belastningsutjämnare med [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) enligt följande:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Name "myPublicIPAddress" | Select IpAddress
```

Ange den offentliga IP-adressen i din webbläsare. Standardwebbsidan för IIS visas som det visas i följande exempel:

![IIS körs från en anpassad virtuell datoravbildning](media/tutorial-use-custom-image-powershell/default-iis-website.png)


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och ytterligare resurser så tar du bort resursgruppen och alla dess resurser med [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Parametern `-Force` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång. Parametern `-AsJob` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du läsa om hur du skapar och använder en anpassad virtuell datoravbildning för dina skalningsuppsättningar med Azure PowerShell:

> [!div class="checklist"]
> * Skapa och anpassa en virtuell dator
> * Avetablera och generalisera den virtuella datorn
> * Skapa en anpassad virtuell datoravbildning
> * Distribuera en skalningsuppsättning som använder den anpassade virtuella datoravbildningen

Gå vidare till nästa självstudie för att lära dig hur du distribuerar program till din skalningsuppsättning.

> [!div class="nextstepaction"]
> [Distribuera program till dina skalningsuppsättningar](tutorial-install-apps-powershell.md)

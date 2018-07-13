---
title: Distribuera ett program till en Azure VM-skalningsuppsättning | Microsoft Docs
description: Lär dig hur du distribuerar program till Linux och Windows VM-instanser i en skalningsuppsättning
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 8817facc21d2a7ac86bdaf198aab3179a93c4914
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38718989"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Distribuera ditt program i VM-skalningsuppsättningar
Om du vill köra program på virtuella datorinstanser i en skalningsuppsättning, måste du först installera programkomponenter och nödvändiga filer. Den här artikeln beskrivs olika sätt att skapa en anpassad virtuell datoravbildning för instanser i en skalningsuppsättning eller automatiskt köra installationsskripten på befintliga VM-instanser. Du också lära dig hur du hanterar program eller uppdateringar av Operativsystemet i en skalningsuppsättning.


## <a name="build-a-custom-vm-image"></a>Skapa en anpassad virtuell datoravbildning
När du använder en Azure-plattformen avbildning skapar instanser i din skalningsuppsättning kan är inga ytterligare programvara installerad eller konfigurerad. Du kan automatisera installationen av dessa komponenter, men som lägger till den tid det tar för att etablera VM-instanser till dina skalningsuppsättningar. Om du använder många konfigurationsändringar för VM-instanser finns det av overhead med dessa konfigurationsskript och uppgifter.

För att minska konfigurationshantering och tid för att etablera en virtuell dator kan skapa du en anpassad virtuell datoravbildning som är redo att köra programmet när en instans etableras i skalningsuppsättningen. Mer information om hur du skapar och använder en anpassad virtuell datoravbildning med en skala, se följande självstudiekurser:

- [Azure CLI 2.0](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Installera en app med det anpassade Skripttillägget
Det anpassade skripttillägget laddar ner och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skript kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Mer information om hur du skapar och använder en anpassad virtuell datoravbildning med en skala, se följande självstudiekurser:

- [Azure CLI 2.0](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-mall](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installera en app till en virtuell dator med PowerShell DSC för Windows
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) är en hanteringsplattform för att definiera konfigurationen av måldatorer. DSC-konfigurationer definierar vad som ska installeras på en dator och hur du konfigurerar värden. En motor för lokala Configuration Manager (LCM) körs på varje målnoden som bearbetar begärda åtgärder baserat på pushade konfigurationer.

PowerShell DSC-tillägget kan du anpassa VM-instanser i en skalningsuppsättning med PowerShell. I följande exempel:

- Instruerar VM-instanser för att ladda ned ett DSC-paket från GitHub- *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Anger att köra ett installationsskript - tillägget `configure-http.ps1`
- Hämtar information om en skalningsuppsättning med [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Gäller tillägget för Virtuella datorer med [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

DSC-tillägget tillämpas på den *myScaleSet* VM-instanser i resursgruppen med namnet *myResourceGroup*. Ange egna namn enligt följande:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Om uppgraderingen principen på din skalningsuppsättning är *manuell*, uppdatera dina VM-instanser med [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Denna cmdlet gäller konfigurationen för uppdaterade skalningsuppsättningen för VM-instanser och installerar programmet.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Installera en app till en Linux VM med cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init körs under hela den ursprungliga startprocessen finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Mer information, inklusive ett exempel *cloud-init.txt* fil, se [använda cloud-init för att anpassa virtuella Azure-datorer](../virtual-machines/linux/using-cloud-init.md).

Om du vill skapa en skalningsuppsättning och använder en cloud-init-fil, lägger du till den `--custom-data` parametern till den [az vmss skapa](/cli/azure/vmss#az_vmss_create) kommandot och ange namnet på en cloud-init-fil. I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* i *myResourceGroup* och konfigurerar VM-instanser med en fil med namnet *cloud-init.txt*. Ange egna namn enligt följande:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Installera program med OS-uppdateringar
När nya OS-versioner är tillgängliga kan du kan använda eller skapa en ny anpassad avbildning och [distribuera OS-uppgraderingar](virtual-machine-scale-sets-upgrade-scale-set.md) att en skalningsuppsättning. Varje virtuell datorinstans uppgraderas till den senaste avbildningen som du anger. Du kan använda en anpassad avbildning med programmet redan installerat tillägget för anpassat skript eller PowerShell DSC för att ha ditt program som är automatiskt tillgängligt när du utför uppgraderingen. Du kan behöva planera för programunderhåll när du utför den här processen för att säkerställa att det finns ingen version kompatibilitetsproblem.

Om du använder en anpassad virtuell datoravbildning med programmet redan är installerat, kan du integrera programuppdateringar med en pipeline för distribution och skapa nya avbildningar som du kan distribuera OS-uppgraderingar i skalningsuppsättningen. På så sätt kan pipelinen att hämta de senaste versioner av programmet, skapa och validera en VM-avbildning och sedan uppgradera de Virtuella datorinstanserna i skalningsuppsättningen. Om du vill köra en pipeline för distribution som skapar och distribuerar programuppdateringar över anpassade VM-avbildningar kan du [skapa en Packer-avbildning och distribuera med Visual Studio Team Services](/vsts/pipelines/apps/cd/azure/deploy-azure-scaleset), eller använda en annan plattform som [Spinnaker ](https://www.spinnaker.io/) eller [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Nästa steg
När du skapar och distribuerar program till dina skalningsuppsättningar kan du granska den [skala ange Design översikt](virtual-machine-scale-sets-design-overview.md). Läs mer om hur du hanterar din skalningsuppsättning [Använd PowerShell för att hantera din skalningsuppsättning](virtual-machine-scale-sets-windows-manage.md).

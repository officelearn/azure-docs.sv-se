---
title: Distribuera program till en skaluppsättning för virtuell dator i Azure | Microsoft Docs
description: Lär dig hur du distribuerar program till Linux och Windows virtuell datorinstans i en skaluppsättning
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
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
ms.author: iainfou
ms.openlocfilehash: ed43dc21c8b7c585abc0a2734a541f760ab3c487
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111571"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Distribuera programmet på virtuella datorer
Om du vill köra program på virtuella datorinstanser i en skalningsuppsättning, måste du först installera programkomponenter och nödvändiga filer. Den här artikeln introducerar sätt att skapa en anpassad VM-avbildning för instanser i en skala eller köra automatiskt installera skript på befintliga VM-instanser. Du också lära dig hur du hanterar programmet eller OS-uppdateringar i en skaluppsättning.


## <a name="build-a-custom-vm-image"></a>Skapa en anpassad VM-avbildning
När du använder en av avbildningarna Azure-plattformen för att skapa instanser i din skaluppsättning är ingen ytterligare programvara installeras eller konfigureras. Du kan automatisera installationen av dessa komponenter, men som lägger till den tid det tar för att etablera VM-instanser i skalningsuppsättningar. Om du använder många konfigurationsändringar på VM-instanser finns management omkostnader med dessa konfigurationsskript och uppgifter.

För att minska konfigurationshantering och tid för att etablera en virtuell dator kan skapa du en anpassad VM-avbildning som är redo att köra programmet när en instans har etablerats i uppsättningen av skalan. Mer information om hur du skapar och använder en anpassad VM-avbildning med en skala finns i avsnittet följande kurser:

- [Azure CLI 2.0](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>Installera en app med tillägget för anpassat skript
Det anpassade skripttillägget laddar ner och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skript kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Mer information om hur du skapar och använder en anpassad VM-avbildning med en skala finns i avsnittet följande kurser:

- [Azure CLI 2.0](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-mall](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installera en app på Windows virtuell dator med PowerShell DSC
[PowerShell önskad tillstånd Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) är en plattform för att definiera konfigurationen av måldatorerna. DSC-konfigurationer definierar vad som ska installeras på en dator och hur du konfigurerar värden. Motorn för lokala Configuration Manager (MGM) körs på varje målnoden som bearbetar begärda åtgärder baserat på intryckt konfigurationer.

PowerShell DSC-tillägg kan du anpassa VM-instanser i en skala som anges med PowerShell. I följande exempel:

- Instruerar VM-instanser för att ladda ned ett DSC-paket från GitHub- *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Anger att köra en installationsskriptet - tillägget `configure-http.ps1`
- Hämtar information om en skala med [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Gäller tillägget för VM-instanser med [Update AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

DSC-tillägg tillämpas på den *myScaleSet* VM-instanser i resursgruppen med namnet *myResourceGroup*. Ange egna namn på följande sätt:

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

Om uppgraderingen principen på din skaluppsättning är *manuell*, uppdatera VM-instanser med [uppdatering AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Denna cmdlet gäller uppdaterade skala ange konfiguration för VM-instanser och installerar programmet.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Installera en app till en Linux-VM med molnet initiering
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init körs under hela den ursprungliga startprocessen finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Mer information, inklusive exempel *moln init.txt* fil, se [använda molnet init för att anpassa virtuella Azure-datorer](../virtual-machines/linux/using-cloud-init.md).

Om du vill skapa en skalningsuppsättning och använder en moln-init-fil, lägger du till den `--custom-data` parametern till den [az vmss skapa](/cli/azure/vmss#az_vmss_create) kommando och ange namnet på en moln-init-fil. I följande exempel skapas en uppsättning med namnet skala *myScaleSet* i *myResourceGroup* och konfigurerar VM-instanser med en fil med namnet *moln init.txt*. Ange egna namn på följande sätt:

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
När nya versioner är tillgängliga kan du använda eller skapa en ny anpassad avbildning och [distribuera OS uppgraderingar](virtual-machine-scale-sets-upgrade-scale-set.md) till en skala. Varje VM-instans har uppgraderats till den senaste bilden som du anger. Du kan använda en anpassad avbildning med programmet förinstallerad tillägget för anpassat skript eller PowerShell DSC har programmet automatiskt tillgängliga när du utför uppgraderingen. Du kan behöva planera för programunderhåll när du utför den här processen för att säkerställa att det inte finns någon version kompatibilitetsproblem.

Om du använder en anpassad VM-avbildning med programmet förinstallerat, kan du integrera programuppdateringar med en rörledning distributionen för att skapa nya bilder och distribuera OS uppgraderingar i skaluppsättning. Den här metoden möjliggör rörledning för att hämta de senaste versionerna av programmet, skapa och validera en VM-avbildning och sedan uppgradera VM-instanser i skaluppsättning. Om du vill köra en pipeline för distribution som skapar och distribuerar programuppdateringar över anpassade VM-avbildningar, kan du [skapa en förpackaren avbildning och distribuera med Visual Studio Team Services](/vsts/pipelines/apps/cd/azure/deploy-azure-scaleset), eller Använd en annan plattform som [Spinnaker ](https://www.spinnaker.io/) eller [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Nästa steg
När du skapar och distribuerar program till din skaluppsättningar du granska den [skala ange översikt över Design](virtual-machine-scale-sets-design-overview.md). Läs mer om hur du hanterar din skaluppsättning [Använd PowerShell för att hantera din skaluppsättning](virtual-machine-scale-sets-windows-manage.md).

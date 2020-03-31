---
title: Distribuera ett program till en azure-skalningsuppsättning för virtuella datorer
description: Lär dig hur du distribuerar program till linux- och Windows-instanser för virtuella datorer i en skalningsuppsättning
author: cynthn
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 6bc319ea50da4ff6a654b2c9ab09bbe218695533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278105"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Distribuera din app på VM-skalningsuppsättningar

Om du vill köra program på virtuella datorinstanser i en skalningsuppsättning, måste du först installera programkomponenter och nödvändiga filer. Den här artikeln introducerar sätt att skapa en anpassad VM-avbildning för instanser i en skalningsuppsättning eller automatiskt köra installationsskript på befintliga VM-instanser. Du lär dig också hur du hanterar program- eller OS-uppdateringar över en skalningsuppsättning.


## <a name="build-a-custom-vm-image"></a>Skapa en anpassad vm-avbildning
När du använder en av Azure-plattformsavbildningarna för att skapa instanserna i din skalningsuppsättning installeras eller konfigureras ingen ytterligare programvara. Du kan automatisera installationen av dessa komponenter, men det lägger till den tid det tar att etablera VM-instanser till dina skalningsuppsättningar. Om du använder många konfigurationsändringar på VM-instanserna finns det hanteringskostnader med dessa konfigurationsskript och uppgifter.

Om du vill minska konfigurationshanteringen och tiden för att etablera en virtuell dator kan du skapa en anpassad vm-avbildning som är redo att köra ditt program så snart en instans har etablerats i skalningsuppsättningen. Mer information om hur du skapar och använder en anpassad VM-avbildning med en skalningsuppsättning finns i följande självstudier:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Installera en app med tillägget Anpassat skript
Det anpassade skripttillägget laddar ner och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skript kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Mer information om hur du installerar en app med ett anpassat skripttillägg finns i följande självstudier:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-mall](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installera en app på en Virtuell Windows-dator med PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) är en hanteringsplattform för att definiera konfigurationen av måldatorer. DSC-konfigurationer definierar vad som ska installeras på en dator och hur värden ska konfigureras. En LCM-motor (Local Configuration Manager) körs på varje målnod som bearbetar begärda åtgärder baserat på pushade konfigurationer.

Med PowerShell DSC-tillägget kan du anpassa VM-instanser i en skalningsuppsättning med PowerShell. Följande exempel:

- Instruerar VM-instanserna att hämta ett DSC-paket från GitHub -*https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Anger att tillägget ska köra ett installationsskript -`configure-http.ps1`
- Hämtar information om en skalningsuppsättning med [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Tillämpar tillägget på VM-instanser med [Update-AzVmss](/powershell/module/az.compute/update-azvmss)

DSC-tillägget tillämpas på *myScaleSet* VM-instanserna i resursgruppen *myResourceGroup*. Ange dina egna namn på följande sätt:

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
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Om uppgraderingsprincipen i skalningsuppsättningen är *manuell*uppdaterar du dina VM-instanser med [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Den här cmdleten tillämpar den uppdaterade skalningsuppsättningskonfigurationen på VM-instanserna och installerar ditt program.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Installera en app på en virtuell Linux-dator med moln-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init körs under hela den ursprungliga startprocessen finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Mer information, inklusive en *exempelmoln-init.txt-fil,* finns [i Använda cloud-init för att anpassa Virtuella Azure-datorer](../virtual-machines/linux/using-cloud-init.md).

Om du vill skapa en skalningsuppsättning och `--custom-data` använda en cloud-init-fil lägger du till parametern i kommandot [az vmss create](/cli/azure/vmss) och anger namnet på en moln-init-fil. I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* i *myResourceGroup* och vm-instanser konfigureras med en fil med namnet *cloud-init.txt*. Ange dina egna namn på följande sätt:

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
När nya os-versioner är tillgängliga kan du använda eller skapa en ny anpassad avbildning och [distribuera OS-uppgraderingar](virtual-machine-scale-sets-upgrade-scale-set.md) till en skalningsuppsättning. Varje VM-instans uppgraderas till den senaste avbildningen som du anger. Du kan använda en anpassad avbildning med programmet förinstallerat, det anpassade skripttillägget eller PowerShell DSC för att få programmet automatiskt tillgängligt när du utför uppgraderingen. Du kan behöva planera för programunderhåll när du utför den här processen för att säkerställa att det inte finns några problem med versionskompatibilitet.

Om du använder en anpassad VM-avbildning med programmet förinstallerat kan du integrera programuppdateringarna med en distributionspipeline för att skapa de nya avbildningarna och distribuera OS-uppgraderingar över skalningsuppsättningen. Med den här metoden kan pipelinen plocka upp de senaste programversionerna, skapa och validera en VM-avbildning och sedan uppgradera VM-instanserna i skalningsuppsättningen. Om du vill köra en distributionspipeline som skapar och distribuerar programuppdateringar över anpassade VM-avbildningar kan du [skapa en Packer-avbildning och distribuera med Azure DevOps Services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)eller använda en annan plattform som [Spinnaker](https://www.spinnaker.io/) eller [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Nästa steg
När du skapar och distribuerar program till dina skalningsuppsättningar kan du granska [översikten över skalningsuppsättningsdesign](virtual-machine-scale-sets-design-overview.md). Mer information om hur du hanterar skalningsuppsättningen finns i [Använda PowerShell för att hantera skalningsuppsättningen](virtual-machine-scale-sets-windows-manage.md).

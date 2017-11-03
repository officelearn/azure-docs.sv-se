---
title: "Distribuera program till en skaluppsättning för virtuell dator i Azure | Microsoft Docs"
description: "Lär dig hur du distribuerar program till Linux och Windows virtuell datorinstans i en skaluppsättning"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 7e03d5e2bbdb1b3b206fa7fa455f7dce7951f02b
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Distribuera programmet på virtuella datorer
Om du vill köra program på instanser i en skaluppsättning för virtuella datorer (VM), måste du först installera programkomponenterna och filer som krävs. Den här artikeln introducerar sätt att skapa en anpassad VM-avbildning för instanser i en skala eller köra automatiskt installera skript på befintliga VM-instanser. Du också lära dig hur du hanterar programmet eller OS-uppdateringar i en skaluppsättning.


## <a name="build-a-custom-vm-image"></a>Skapa en anpassad VM-avbildning
När du använder en av avbildningarna Azure-plattformen för att skapa instanser i din skaluppsättning är ingen ytterligare programvara installeras eller konfigureras. Du kan automatisera installationen av dessa komponenter, men som lägger till den tid det tar för att etablera VM-instanser i skalningsuppsättningar. Om du använder många konfigurationsändringar på VM-instanser finns management omkostnader med dessa konfigurationsskript och uppgifter.

För att minska konfigurationshantering och tid för att etablera en virtuell dator kan skapa du en anpassad VM-avbildning som är redo att köra programmet när en instans har etablerats i uppsättningen av skalan. Den övergripande processen att skapa en anpassad VM-avbildning för att skala uppsättning instanser är följande:

1. Om du vill skapa en anpassad VM-avbildning för din scale set instanser, skapar du och logga in på en virtuell dator och sedan installera och konfigurera programmet. Du kan använda förpackaren att definiera och skapa en [Linux](../virtual-machines/linux/build-image-with-packer.md) eller [Windows](../virtual-machines/windows/build-image-with-packer.md) VM-avbildning. Eller så kan du manuellt skapa och konfigurera den virtuella datorn:

    - Skapa en Linux VM med det [Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md), [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md), eller [portal](../virtual-machines/linux/quick-create-portal.md).
    - Skapa en virtuell Windows-dator med den [Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md), [Azure CLI 2.0](../virtual-machines/windows/quick-create-cli.md), eller [portal](../virtual-machines/windows/quick-create-portal.md).
    - Logga in på en [Linux](../virtual-machines/linux/mac-create-ssh-keys.md#use-the-ssh-key-pair) eller [Windows](../virtual-machines/windows/connect-logon.md) VM.
    - Installera och konfigurera de program och verktyg som behövs. Om du behöver specifika versioner av ett bibliotek eller runtime, en anpassad VM-avbildning kan du definiera en version och 

2. Avbilda den virtuella datorn med den [Azure CLI 2.0](../virtual-machines/linux/capture-image.md) eller [Azure PowerShell](../virtual-machines/windows/capture-image.md). Det här steget skapar anpassade VM-avbildning som används för att distribuera instanser i en skaluppsättning.

3. [Skapa en skalningsuppsättning](virtual-machine-scale-sets-create.md) och ange den anpassa VM-avbildning som skapade i föregående steg.


## <a name="already-provisioned"></a>Installera en app med tillägget för anpassat skript
Tillägget för anpassat skript hämtar och kör skript på Azure Virtual Machines. Det här tillägget är användbart för post distributionskonfiguration, Programvaruinstallation eller någon annan konfiguration / hanteringsaktivitet. Skript kan hämtas från Azure storage eller GitHub eller tillhandahålls på Azure-portalen vid körning av tillägget.

Tillägget för anpassat skript kan integreras med Azure Resource Manager-mallar och kan också köras med hjälp av Azure CLI, PowerShell, Azure-portalen eller Azure Virtual Machine REST API. 

Mer information finns i [tillägget för anpassat skript översikt](../virtual-machines/windows/extensions-customscript.md).


### <a name="use-azure-powershell"></a>Använda Azure PowerShell
PowerShell använder en hash-tabell för lagring av filen du vill hämta och kommandot att köras. I följande exempel:

- Instruerar VM-instanser för att hämta ett skript från GitHub - *https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1*
- Anger att köra en installationsskriptet - tillägget`powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1`
- Hämtar information om en skala med [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Gäller tillägget för VM-instanser med [Update AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

Tillägget för anpassat skript tillämpas på den *myScaleSet* VM-instanser i resursgruppen med namnet *myResourceGroup*. Ange egna namn på följande sätt:

```powershell
# Define the script for your Custom Script Extension to run
$customConfig = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Om uppgraderingen principen på din skaluppsättning är *manuell*, uppdatera VM-instanser med [uppdatering AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Denna cmdlet gäller uppdaterade skala ange konfiguration för VM-instanser och installerar programmet.


### <a name="use-azure-cli-20"></a>Använda Azure CLI 2.0
För att använda tillägget för anpassat skript med Azure CLI, kan du skapa en JSON-fil som definierar vilka filer som ska få och kommandon som ska köras. Dessa JSON-definitioner kan återanvändas i set-distributioner att tillämpa konsekvent tillämpning installerar.

Skapa en fil med namnet i din aktuella shell *customConfig.json* och klistra in följande konfiguration. Till exempel skapa filen i molnet Shell inte på den lokala datorn. Du kan använda valfri redigerare som du vill. Ange `sensible-editor cloudConfig.json` att skapa filen och se en lista över tillgängliga redigerare.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Tillämpa konfigurationen av tillägget för anpassat skript till VM-instanser i nivå med [az vmss tillägget set](/cli/azure/vmss/extension#set). I följande exempel gäller den *customConfig.json* konfiguration för att den *myScaleSet* VM-instanser i resursgruppen med namnet *myResourceGroup*. Ange egna namn på följande sätt:

```azurecli
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfig.json
```

Om uppgraderingen principen på din skaluppsättning är *manuell*, uppdatera VM-instanser med [az vmss update-instanser](/cli/azure/vmss#update-instances). Denna cmdlet gäller uppdaterade skala ange konfiguration för VM-instanser och installerar programmet.


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installera en app på Windows virtuell dator med PowerShell DSC
[PowerShell önskad tillstånd Configuration (DSC)](https://msdn.microsoft.com/en-us/powershell/dsc/overview) är en plattform för att definiera konfigurationen av måldatorerna. DSC-konfigurationer definierar vad som ska installeras på en dator och hur du konfigurerar värden. Motorn för lokala Configuration Manager (MGM) körs på varje målnoden som bearbetar begärda åtgärder baserat på intryckt konfigurationer.

PowerShell DSC-tillägg kan du anpassa VM-instanser i en skala som anges med PowerShell. I följande exempel:

- Instruerar VM-instanser för att ladda ned ett DSC-paket från GitHub - *https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip*
- Anger att köra en installationsskriptet - tillägget`configure-http.ps1`
- Hämtar information om en skala med [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Gäller tillägget för VM-instanser med [Update AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

DSC-tillägg tillämpas på den *myScaleSet* VM-instanser i resursgruppen med namnet *myResourceGroup*. Ange egna namn på följande sätt:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip";
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
[Molnet init](https://cloudinit.readthedocs.io/latest/) är ett vanligt sätt att anpassa en Linux VM när den startas för första gången. Du kan använda molnet init för att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom molnet init körs under den ursprungliga startprocessen, det inte finns några ytterligare steg krävs agenter att tillämpa konfigurationen.

Molnet init fungerar även över distributioner. Exempelvis kan du inte använda **lgh get installera** eller **yum installera** att installera ett paket. I stället kan du definiera en lista över paket som ska installeras. Molnet init används automatiskt det ursprungliga paket hanteringsverktyget för distro som du väljer.

Mer information, inklusive exempel *moln init.txt* fil, se [använda molnet init för att anpassa virtuella Azure-datorer](../virtual-machines/linux/using-cloud-init.md).

Om du vill skapa en skalningsuppsättning och använder en moln-init-fil, lägger du till den `--custom-data` parametern till den [az vmss skapa](/cli/azure/vmss#create) kommando och ange namnet på en moln-init-fil. I följande exempel skapas en uppsättning med namnet skala *myScaleSet* i *myResourceGroup* och konfigurerar VM-instanser med en fil med namnet *moln init.txt*. Ange egna namn på följande sätt:

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


## <a name="install-applications-as-a-set-scales-out"></a>Installera program som en uppsättning skalas ut
Skaluppsättningar för kan du öka antalet VM-instanser som kör programmet. Den här skala ut process kan startas manuellt eller automatiskt baserat på mått, till exempel CPU eller minne användning.

Om du har använt en tillägget för anpassat skript på skaluppsättning installeras programmet till varje ny VM-instans. Om skaluppsättning baseras på en anpassad avbildning med programmet förinstallerat, distribueras varje ny VM-instans oanvändbar. 

Om VM-instanser scale set behållaren värdar, kan du använda tillägget för anpassat skript hämtar och kör behovet av behållare bilder. Tillägget för anpassat skript kan också registrera nya VM-instans med en orchestrator, till exempel Azure Container Service.


## <a name="deploy-application-updates"></a>Distribuera programuppdateringar
Om du uppdaterar din programkod, bibliotek och paket kan skicka du senaste programtillståndet till VM-instanser i en skaluppsättning. Om du använder tillägget för anpassat skript, uppdateringar till tillämpningsprogrammet och distribueras inte automatiskt. Ändra konfigurationen för anpassat skript, såsom för att peka till en installationsskriptet som har en uppdaterad versionsnamn. I föregående exempel tillägget för anpassat skript använder ett skript som heter *automate_nginx.sh* på följande sätt:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Alla uppdateringar som du gör i ditt program utsätts inte för tillägget för anpassat skript om inte som installerar skript ändringar. En metod är att inkludera ett versionsnummer som släpper steg med ditt program. Tillägget för anpassat skript kan nu referera *automate_nginx_v2.sh* på följande sätt:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Tillägget för anpassat skript körs nu mot VM-instanser för att tillämpa de senaste uppdateringarna för programmet.


### <a name="install-applications-with-os-updates"></a>Installera program med OS-uppdateringar
När nya versioner är tillgängliga kan du använda eller skapa en ny anpassad avbildning och [distribuera OS uppgraderingar](virtual-machine-scale-sets-upgrade-scale-set.md) till en skala. Varje VM-instans har uppgraderats till den senaste bilden som du anger. Du kan använda en anpassad avbildning med programmet förinstallerad tillägget för anpassat skript eller PowerShell DSC har programmet automatiskt tillgängliga när du utför uppgraderingen. Du kan behöva planera för programunderhåll när du utför den här processen för att säkerställa att det inte finns någon version kompatibilitetsproblem.

Om du använder en anpassad VM-avbildning med programmet förinstallerat, kan du integrera programuppdateringar med en rörledning distributionen för att skapa nya bilder och distribuera OS uppgraderingar i skaluppsättning. Den här metoden möjliggör rörledning för att hämta de senaste versionerna av programmet, skapa och validera en VM-avbildning och sedan uppgradera VM-instanser i skaluppsättning. Du kan använda för att köra en pipeline för distribution som skapar och distribuerar programuppdateringar över anpassade VM-avbildningar, [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Spinnaker](https://www.spinnaker.io/), eller [Jenkins](https://jenkins.io/) .


## <a name="next-steps"></a>Nästa steg
När du skapar och distribuerar program till din skaluppsättningar du granska den [skala ange översikt över Design](virtual-machine-scale-sets-design-overview.md). Läs mer om hur du hanterar din skaluppsättning [Använd PowerShell för att hantera din skaluppsättning](virtual-machine-scale-sets-windows-manage.md).

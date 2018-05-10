---
title: Översikt över Azure-dator | Microsoft Docs
description: Översikt över Azure-dator
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: fb29f0f931715b8a6ba5b4528294eb61ef5762c8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="azure-virtual-machine-agent-overview"></a>Översikt över Azure virtuella datorns Agent
Den virtuella datorns Agent för Microsoft Azure (VM-Agent) är en säker, enkel process som hanterar virtuell dator (VM) interaktion med Azure-Infrastrukturkontrollanten. Den Virtuella Datoragenten har en primär roll i att aktivera och köra tillägg för virtuell dator i Azure. VM-tillägg aktivera efter distributionskonfiguration för virtuell dator, till exempel installera och konfigurera programvara. VM-tillägg också aktivera återställningsfunktioner, till exempel när du återställer lösenordet för administratörer av en virtuell dator. VM-tillägg kan inte köras utan Azure VM-agenten.

Den här artikeln beskrivs installation, identifiering och borttagning av virtuella Azure-agenten.

## <a name="install-the-vm-agent"></a>Installera den Virtuella Datoragenten

### <a name="azure-marketplace-image"></a>Azure Marketplace-avbildning

Azure VM-agenten installeras som standard på alla Windows-VM som distribueras från en Azure Marketplace-avbildning. När du distribuerar en Azure Marketplace-avbildning från portalen, PowerShell, kommandoradsgränssnittet eller en Azure Resource Manager-mall, installeras även Azure VM-agenten.

Windows gäst-agenten är uppdelad i två delar:

- Etableringsagenten (PA)
- Windows-Gästagent (WinGA)

Om du vill starta en virtuell dator måste du ha PA som installerats på den virtuella datorn, men WinGA behöver inte installeras. Distribuera tid på VM, du kan välja att inte installera WinGA. I följande exempel visas hur du väljer den *provisionVmAgent* alternativet med en Azure Resource Manager-mall:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Om du inte har de installerade agenter, kan du inte använda några Azure-tjänster, till exempel Azure Backup eller Azure-säkerhet. Dessa tjänster kräver ett tillägg som ska installeras. Om du har distribuerat en virtuell dator utan WinGA, kan du installera den senaste versionen av agenten senare.

### <a name="manual-installation"></a>Manuell installation
Windows VM-agenten kan installeras manuellt med Windows installer-paket. Manuell installation kan vara nödvändigt när du skapar en anpassad VM-avbildning som distribueras till Azure. Så här installerar du Windows VM-agenten manuellt [hämta installationsprogrammet för VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789).

VM-agenten kan installeras genom att dubbelklicka på den Windows installer-fil. För en automatiserad eller obevakad installation av den Virtuella datoragenten, kör du följande kommando:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Identifiera den Virtuella Datoragenten

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell-modulen kan användas för att hämta information om virtuella Azure-datorer. Om du vill se information om en virtuell dator, till exempel etableringsstatusen för Azure VM-agenten använder [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm):

'' 'powershell' Get-AzureRmVM
```

The following condensed example output shows the the *ProvisionVMAgent* property nested inside *OSProfile*. This property can be used to determine if the VM agent has been deployed to the VM:

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Följande skript kan användas för att returnera en kortfattad lista över Virtuella datornamnen och tillståndet för den Virtuella Datoragenten:

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuell identifiering
När inloggad på en Windows Azure VM, kan du använda Aktivitetshanteraren för att granska processer som körs. För att kontrollera Azure VM-agenten, öppna Aktivitetshanteraren, klicka på den *information* fliken och Sök efter ett processnamn **WindowsAzureGuestAgent.exe**. Förekomst av den här processen anger att den Virtuella datoragenten är installerad.


## <a name="upgrade-the-vm-agent"></a>Uppgradera den Virtuella Datoragenten
Azure VM-agenten för Windows uppgraderas automatiskt. Allteftersom nya virtuella datorer distribueras till Azure, får så de senaste VM-agenten VM etablera samtidigt. Anpassade VM-avbildningar ska uppdateras manuellt för att inkludera den nya VM-agenten vid tidpunkten för skapandet av avbildningen.


## <a name="next-steps"></a>Nästa steg
Mer information om VM-tillägg finns [översikt över funktioner och tillägg av Azure virtuella datorer](overview.md).
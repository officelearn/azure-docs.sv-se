---
title: Översikt över Azure-dator | Microsoft Docs
description: Översikt över Azure-dator
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zroiy
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
ms.author: roiyz
ms.openlocfilehash: 9185b786a3eea402b8013eb71a5d7268787900c5
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414506"
---
# <a name="azure-virtual-machine-agent-overview"></a>Översikt över Azure VM-agenten
Microsoft Azure VM-agenten (VM-agenten) är en säker och enkel process som hanterar kommunikation mellan virtuella datorer (VM) med Azure-Infrastrukturkontrollanten. VM-agenten har en primär roll i att aktivera och köra Azure virtual machine-tillägg. VM-tillägg aktivera efter distribution konfiguration av virtuell dator, till exempel installera och konfigurera programvara. VM-tillägg också aktivera funktioner, som att återställa lösenordet för administratörer av en virtuell dator. VM-tillägg kan inte köras utan Azure VM-agenten.

Den här artikeln beskriver installation, identifiering och borttagning av Azure VM-agenten.

## <a name="install-the-vm-agent"></a>Installera VM-agenten

### <a name="azure-marketplace-image"></a>Azure Marketplace-avbildning

Azure VM-agenten installeras som standard på virtuella Windows-datorer som distribueras från Azure Marketplace-avbildning. När du distribuerar en Azure Marketplace-avbildning från portalen, PowerShell, kommandoradsgränssnittet eller en Azure Resource Manager-mall, installeras även Azure VM-agenten.

Windows gäst-agenten är uppdelad i två delar:

- Etableringsagenten (PA)
- Windows-Gästagenten (WinGA)

Om du vill starta en virtuell dator måste du ha Provideradress installerad på den virtuella datorn, men WinGA behöver inte installeras. Distribuera tid på VM, du kan välja att inte installera WinGA. I följande exempel visar hur du väljer den *provisionVmAgent* alternativet med en Azure Resource Manager-mall:

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

Om du inte har de agenter som har installerats kan använda du inte vissa Azure-tjänster, till exempel Azure Backup eller Azure-säkerhet. De här tjänsterna kräver ett tillägg som ska installeras. Om du har distribuerat en virtuell dator utan WinGA, kan du installera den senaste versionen av agenten senare.

### <a name="manual-installation"></a>Manuell installation
Windows VM-agenten kan installeras manuellt med en Windows installer-paketet. Manuell installation kan vara nödvändigt när du skapar en anpassad virtuell datoravbildning som distribueras till Azure. Installera Windows VM-agenten manuellt [ladda ned installationsprogrammet till VM-agenten](http://go.microsoft.com/fwlink/?LinkID=394789).

VM-agenten kan installeras genom att dubbelklicka på den Windows installer-fil. För en automatiserad eller obevakad installation av VM-agenten, kör du följande kommando:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Identifiera VM-agenten

### <a name="powershell"></a>PowerShell

Azure Resource Managers PowerShell-modulen kan användas för att hämta information om virtuella Azure-datorer. Om du vill se information om en virtuell dator, till exempel Etableringsstatus för Azure VM-agenten använder [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm):

'' 'powershell ”Get-AzureRmVM
```

The following condensed example output shows the *ProvisionVMAgent* property nested inside *OSProfile*. This property can be used to determine if the VM agent has been deployed to the VM:

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Följande skript kan användas för att returnera en kortfattad lista över namn på virtuella datorer och tillståndet för VM-agenten:

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuell identifiering
När inloggad på en Windows Azure VM, kan du använda Aktivitetshanteraren för att undersöka processer som körs. Om du vill söka efter Azure VM-agenten, öppna Aktivitetshanteraren, klicka på den *information* fliken och leta efter ett processnamn **WindowsAzureGuestAgent.exe**. Förekomst av den här processen anger att den Virtuella datoragenten är installerad.


## <a name="upgrade-the-vm-agent"></a>Uppgradera VM-agenten
Azure VM-agenten för Windows uppgraderas automatiskt. Som nya virtuella datorer distribueras till Azure, få de senaste VM-agenten på VM-etablera tid. Anpassade avbildningar av Virtuella datorer ska uppdateras manuellt för att inkludera den nya VM-agenten vid tidpunkten för skapandet av avbildningen.


## <a name="next-steps"></a>Nästa steg
Mer information om VM-tillägg finns i [Azure VM-tillägg och funktioner för översikt över](overview.md).
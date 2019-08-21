---
title: Översikt över Azure Virtual Machine-agent | Microsoft Docs
description: Översikt över Azure Virtual Machine agent
services: virtual-machines-windows
documentationcenter: virtual-machines
author: roiyz-msft
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: roiyz
ms.openlocfilehash: c3b7b0588e6c1446203f7bb4a3ec7f168f08988f
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637498"
---
# <a name="azure-virtual-machine-agent-overview"></a>Översikt över Azure Virtual Machine agent
Den Microsoft Azure virtuella dator agenten (VM-agenten) är en säker, lätt process som hanterar interaktionen mellan virtuella datorer (VM) med Azure Fabric-styrenheten. VM-agenten har en primär roll för att aktivera och köra tillägg för virtuella Azure-datorer. Med VM-tillägg kan du konfigurera virtuella datorer efter distributionen, till exempel installera och konfigurera program vara. VM-tillägg möjliggör också återställnings funktioner som att återställa det administrativa lösen ordet för en virtuell dator. Utan Azure VM-agenten kan VM-tillägg inte köras.

Den här artikeln innehåller information om installation, identifiering och borttagning av Azure Virtual Machine-agenten.

## <a name="install-the-vm-agent"></a>Installera VM-agenten

### <a name="azure-marketplace-image"></a>Azure Marketplace-avbildning

Azure VM-agenten installeras som standard på alla virtuella Windows-datorer som distribueras från en Azure Marketplace-avbildning. När du distribuerar en Azure Marketplace-avbildning från portalen, PowerShell, kommando rads gränssnittet eller en Azure Resource Manager mall, installeras även Azure VM-agenten.

Windows gäst agent-paketet är uppdelat i två delar:

- Etablerings agent (PA)
- Windows gästa Gent (Vinga)

Om du vill starta en virtuell dator måste du ha PA installerat på den virtuella datorn, men vingen behöver inte vara installerad. På den virtuella datorns distributions tid kan du välja att inte installera vingen. I följande exempel visas hur du väljer alternativet *provisionVmAgent* med en mall för Azure Resource Manager:

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

Om du inte har agenterna installerade kan du inte använda vissa Azure-tjänster, till exempel Azure Backup eller Azure-säkerhet. Dessa tjänster kräver att ett tillägg installeras. Om du har distribuerat en virtuell dator utan att Vinga kan du installera den senaste versionen av agenten senare.

### <a name="manual-installation"></a>Manuell installation
Windows VM-agenten kan installeras manuellt med ett Windows Installer-paket. Manuell installation kan vara nödvändigt när du skapar en anpassad VM-avbildning som distribueras till Azure. Om du vill installera den virtuella Windows-agenten manuellt [laddar du ned installations programmet för VM](https://go.microsoft.com/fwlink/?LinkID=394789)-agenten. VM-agenten stöds på Windows Server 2008 R2 och senare.

Du kan installera VM-agenten genom att dubbelklicka på Windows Installer-filen. Kör följande kommando för en automatisk eller obevakad installation av VM-agenten:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Identifiera VM-agenten

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell-modulen kan användas för att hämta information om virtuella Azure-datorer. Använd [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)för att se information om en virtuell dator, till exempel etablerings status för den virtuella Azure-agenten:

```powershell
Get-AzVM
```

I följande komprimerade exempel utdata visas egenskapen *ProvisionVMAgent* kapslad inuti *OSProfile*. Den här egenskapen kan användas för att avgöra om VM-agenten har distribuerats till den virtuella datorn:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Följande skript kan användas för att returnera en kortare lista över VM-namn och tillstånd för VM-agenten:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuell identifiering

När du är inloggad på en virtuell Windows-dator kan du använda aktivitets hanteraren för att undersöka aktiva processer. Om du vill söka efter den virtuella Azure-agenten öppnar du aktivitets hanteraren, klickar på fliken *information* och letar efter ett process namn **WindowsAzureGuestAgent. exe**. Förekomsten av den här processen indikerar att VM-agenten är installerad.


## <a name="upgrade-the-vm-agent"></a>Uppgradera VM-agenten
Azure VM-agenten för Windows uppgraderas automatiskt. När nya virtuella datorer distribueras till Azure får de den senaste virtuella dator agenten vid etablerings tiden för den virtuella datorn. Anpassade VM-avbildningar ska uppdateras manuellt för att inkludera den nya VM-agenten när avbildningen skapas.


## <a name="next-steps"></a>Nästa steg
Mer information om tillägg för virtuella datorer finns i [Översikt över virtuella Azure-datorer och funktioner](overview.md).

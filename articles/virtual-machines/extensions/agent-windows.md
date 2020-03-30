---
title: Översikt över Azure Virtual Machine Agent
description: Översikt över Azure Virtual Machine Agent
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/20/2019
ms.author: akjosh
ms.openlocfilehash: 3d9c178201ab0c22ed4eab9cf65f7d48e59e1359
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78246130"
---
# <a name="azure-virtual-machine-agent-overview"></a>Översikt över Azure Virtual Machine Agent
Microsoft Azure Virtual Machine Agent (VM Agent) är en säker, lätt process som hanterar komspelet med virtuella datorer (VM) med Azure Fabric Controller. VM-agenten har en primär roll när det gäller att aktivera och köra Azure-tillägg för virtuella datorer. VM-tillägg möjliggör konfiguration efter distribution av virtuell dator, till exempel installera och konfigurera programvara. VM-tillägg möjliggör också återställningsfunktioner som att återställa det administrativa lösenordet för en virtuell dator. Utan Azure VM-agenten kan vm-tillägg inte köras.

I den här artikeln beskrivs installation och identifiering av Azure Virtual Machine Agent.

## <a name="install-the-vm-agent"></a>Installera VM-agenten

### <a name="azure-marketplace-image"></a>Avbildning av Azure Marketplace

Azure VM-agenten installeras som standard på alla Windows VM som distribueras från en Azure Marketplace-avbildning. När du distribuerar en Azure Marketplace-avbildning från portalen, PowerShell, Command Line Interface eller en Azure Resource Manager-mall är Azure VM-agenten också installerad.

Windows Guest Agent-paketet är uppdelat i två delar:

- Etableringsagent (PA)
- Windows Gästagent (WinGA)

För att starta en virtuell dator måste du ha PA installerat på den virtuella datorn, men WinGA behöver inte installeras. Vid vm-driftsättningstid kan du välja att inte installera WinGA. I följande exempel visas hur du väljer alternativet *provisionVmAgent* med en Azure Resource Manager-mall:

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

Om du inte har installerat agenterna kan du inte använda vissa Azure-tjänster, till exempel Azure Backup eller Azure Security. Dessa tjänster kräver att ett tillägg installeras. Om du har distribuerat en virtuell dator utan WinGA kan du installera den senaste versionen av agenten senare.

### <a name="manual-installation"></a>Manuell installation
Windows VM-agenten kan installeras manuellt med ett Windows-installationspaket. Manuell installation kan vara nödvändig när du skapar en anpassad VM-avbildning som distribueras till Azure. Om du vill installera Windows VM-agenten manuellt hämtar du installationsprogrammet för [VM-agenten](https://go.microsoft.com/fwlink/?LinkID=394789). VM-agenten stöds på Windows Server 2008 R2 och senare.

> [!NOTE]
> Det är viktigt att uppdatera alternativet AllowExtensionOperations efter att du manuellt har installerat VMAgent på en virtuell dator som har distribuerats från avbildningen utan ProvisionVMAgent aktiverat.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Krav
- Windows VM-agenten behöver minst Windows Server 2008 R2 (64-bitar) för att kunna köras med .Net Framework 4.0. Se [Stöd för minsta version för agenter för virtuella datorer i Azure](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

- Se till att den virtuella datorn har åtkomst till IP-adress 168.63.129.16. För mer information se [Vad är IP-adress 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

## <a name="detect-the-vm-agent"></a>Identifiera VM-agenten

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell-modulen kan användas för att hämta information om virtuella Azure-datorer. Om du vill visa information om en virtuell dator, till exempel etableringstillståndet för Azure VM-agenten, använder du [Get-AzVM:](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)

```powershell
Get-AzVM
```

Följande komprimerade exempelutdata visar egenskapen *ProvisionVMAgent* som är kapslad inuti *OSProfile*. Den här egenskapen kan användas för att avgöra om VM-agenten har distribuerats till den virtuella datorn:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Följande skript kan användas för att returnera en kortfattad lista över VM-namn och tillståndet för VM-agenten:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuell identifiering

När aktivitetshanteraren är inloggad på en virtuell dator i Windows kan du använda aktivitetshanteraren för att undersöka processer som körs. Om du vill söka efter Azure VM-agenten öppnar du Aktivitetshanteraren, klickar på fliken *Information* och letar efter ett processnamn **WindowsAzureGuestAgent.exe**. Förekomsten av den här processen indikerar att VM-agenten är installerad.


## <a name="upgrade-the-vm-agent"></a>Uppgradera VM-agenten
Azure VM-agenten för Windows uppgraderas automatiskt. När nya virtuella datorer distribueras till Azure får de den senaste VM-agenten vid vm-etableringstid. Anpassade VM-avbildningar bör uppdateras manuellt för att inkludera den nya VM-agenten vid skapande av avbildningar.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Windows Guest Agent Automatiska loggar Collection
Windows Gästagent har en funktion för att automatiskt samla in vissa loggar. Den här funktionen är controller av CollectGuestLogs.exe-processen. Den finns för både PaaS Cloud Services och IaaS Virtual Machines och dess mål är att snabbt & automatiskt samla in vissa diagnostikloggar från en virtuell dator - så att de kan användas för offline-analys. De insamlade loggarna är händelseloggar, OS-loggar, Azure-loggar och vissa registernycklar. Den producerar en ZIP-fil som överförs till den virtuella datorns värd. Denna ZIP-fil kan sedan granskas av Engineering Teams och supportproffs för att undersöka problem på begäran av kunden som äger den virtuella datorn.

## <a name="next-steps"></a>Nästa steg
Mer information om vm-tillägg finns i [Azures översikt över virtuella datorer och funktioner](overview.md).

---
title: "Översikt över Azure-dator | Microsoft Docs"
description: "Översikt över Azure-dator"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: danis
ms.openlocfilehash: f3e4ab075f7cd75bac2d66f0391227c0de6e11a0
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="azure-virtual-machine-agent-overview"></a>Översikt över Azure virtuella datorns Agent

Den virtuella datorns Agent för Microsoft Azure (AM Agent) är en säker, enkel process som hanterar VM interaktion med Azure-Infrastrukturkontrollanten. Den Virtuella Datoragenten har en primär roll i att aktivera och köra tillägg för virtuell dator i Azure. VM-tillägg som aktiverar efter distributionskonfigurationen av virtuella datorer, till exempel installera och konfigurera programvara. Tillägg för virtuell dator kan du även aktivera återställningsfunktioner, till exempel när du återställer lösenordet för administratörer för en virtuell dator. Tillägg för virtuell dator utan Azure VM-agenten kan inte köras.

Det här dokumentet beskriver installation, identifiering och borttagning av virtuella Azure-agenten.

## <a name="install-the-vm-agent"></a>Installera den Virtuella Datoragenten

### <a name="azure-gallery-image"></a>Bild av Azure-galleriet

Azure VM-agenten installeras som standard på alla Windows-dator som distribueras från en avbildning i Azure-galleriet. När du distribuerar en Azure-galleriet bild från portalen, PowerShell, kommandoradsgränssnittet eller en Azure Resource Manager-mall, installeras även Azure VM-agenten. 

### <a name="manual-installation"></a>Manuell installation

Windows VM-agenten kan installeras manuellt med hjälp av Windows installer-paket. Manuell installation kan vara nödvändigt när du skapar en virtuell datoravbildning som ska distribueras i Azure. Installera Windows VM-agenten manuellt genom att hämta installationsprogrammet för VM-agenten från den här platsen [Windows Azure VM-agenten hämta](http://go.microsoft.com/fwlink/?LinkID=394789). 

VM-agenten kan installeras genom att dubbelklicka på den windows installer-fil. Kör följande kommando för en automatiserad eller obevakad installation av VM-agenten.

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Identifiera den Virtuella Datoragenten

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell-modulen kan användas för att hämta information om Azure virtuella datorer. Kör `Get-AzureRmVM` returnerar lite inklusive etableringsstatusen för Azure VM-agenten.

```PowerShell
Get-AzureRmVM
```

Följande är bara en delmängd av den `Get-AzureRmVM` utdata. Observera den `ProvisionVMAgent` egenskapen inuti `OSProfile`, den här egenskapen kan användas för att avgöra om den Virtuella datoragenten har distribuerats till den virtuella datorn.

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Följande skript kan användas för att returnera en kortfattad lista över namn på virtuella datorer och tillståndet för den Virtuella Datoragenten.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuell identifiering

När inloggad på en Windows Azure VM, kan du använda Aktivitetshanteraren för att granska processer som körs. För att kontrollera Azure VM-agenten, öppna Aktivitetshanteraren > Klicka på informationsfliken och leta efter ett processnamn `WindowsAzureGuestAgent.exe`. Förekomst av den här processen anger att den Virtuella datoragenten är installerad.

## <a name="upgrade-the-vm-agent"></a>Uppgradera den Virtuella Datoragenten

Azure VM-agenten för Windows uppgraderas automatiskt. Allteftersom nya virtuella datorer distribueras till Azure, kan de få den senaste VM-agenten. Anpassade VM-avbildningar ska uppdateras för att inkludera den nya VM-agenten manuellt.
---
title: Aktivera VM-tillägg med Azure PowerShell
description: Den här artikeln beskriver hur du distribuerar tillägg för virtuella datorer till Azure Arc-aktiverade servrar som körs i hybrid moln miljöer med Azure PowerShell.
ms.date: 10/23/2020
ms.topic: conceptual
ms.openlocfilehash: d2408f75c7b6d81ba297de6dcdb85a712cd8908f
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495438"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Aktivera Azure VM-tillägg med Azure PowerShell

Den här artikeln visar hur du distribuerar och avinstallerar virtuella Azure-tillägg, som stöds av Azure Arc-aktiverade servrar, till en Linux-eller Windows hybrid-dator med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

- En dator med Azure PowerShell. Anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).

Innan du använder Azure PowerShell för att hantera VM-tillägg på hybrid servern som hanteras av Arc-aktiverade servrar måste du installera `Az.ConnectedMachine` modulen. Kör följande kommando på din ARC-aktiverade Server:

`Install-Module -Name Az.ConnectedMachine`.

När installationen är klar returneras följande meddelande:

`The installed extension `AZ. ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Aktivera tillägg

Om du vill aktivera ett VM-tillägg på din ARC-aktiverade Server använder du [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) med parametrarna,,,, `-Name` `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` ,- `ExtensionType` och `-Settings` .

I följande exempel aktive ras Log Analytics VM-tillägget på en ARC-aktiverad Linux-server:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType OmsAgentforLinux"
```

I följande exempel aktive ras det anpassade skript tillägget på en ARC-aktiverad server:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

## <a name="list-extensions-installed"></a>List tillägg har installerats

Om du vill hämta en lista över VM-tilläggen på din ARC-aktiverad server använder du [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) med `-MachineName` `-ResourceGroupName` parametrarna och.

Exempel:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Ta bort ett installerat tillägg

Om du vill ta bort ett installerat VM-tillägg på din ARC-aktiverade Server använder du [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) med `-Name` `-MachineName` parametrarna, och `-ResourceGroupName` .

Om du till exempel vill ta bort Log Analytics VM-tillägget för Linux kör du följande kommando:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Nästa steg

- Du kan distribuera, hantera och ta bort VM-tillägg med hjälp av [Azure CLI](manage-vm-extensions-cli.md), från [Azure Portal](manage-vm-extensions-portal.md)eller [Azure Resource Manager mallar](manage-vm-extensions-template.md).

- Felsöknings information finns i [fel söknings guiden för VM-tillägg](troubleshoot-vm-extensions.md).

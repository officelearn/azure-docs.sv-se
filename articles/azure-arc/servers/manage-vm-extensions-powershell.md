---
title: Aktivera VM-tillägg med Azure PowerShell
description: Den här artikeln beskriver hur du distribuerar tillägg för virtuella datorer till Azure Arc-aktiverade servrar som körs i hybrid moln miljöer med Azure PowerShell.
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: 5ed9db23cd19814ff05c2f142f51cea869f2c2d4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359076"
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

### <a name="key-vault-vm-extension-preview"></a>Key Vault VM-tillägg (för hands version)

> [!WARNING]
> PowerShell-klienter lägger ofta `\` till `"` i settings.jspå vilket gör att akvvm_service Miss lyckas med felet: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

I följande exempel aktive ras Key Vault VM-tillägget (för hands version) på en ARC-aktiverad server:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @{
        "observedCert1"
       }
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationLocationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
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

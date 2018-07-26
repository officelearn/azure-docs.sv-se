---
title: Så här konfigurerar du hanterad tjänstidentitet i en Azure-VMSS med hjälp av PowerShell
description: Steg för steg tilldelas instruktioner för att konfigurera en system- och en identiteter på en Azure-VMSS med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 5d4539c05d05053ac2ea6cd1c5fadbd161b41173
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257750"
---
# <a name="configure-a-vmss-managed-service-identity-using-powershell"></a>Konfigurera en VMSS hanterad tjänstidentitet med hjälp av PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför åtgärder för hanterad tjänstidentitet på en Azure VM-skalningsuppsättning med hjälp av PowerShell:
- Aktivera och inaktivera den systemtilldelade identiteten på en VM-skalningsuppsättning
- Lägga till och ta bort en Användartilldelad identitet på en VM-skalningsuppsättning

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en skalningsuppsättning för virtuell dator och aktivera och ta bort systemtilldelad hanteras och/eller användarens identitet från en virtuell datorskalning ange.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad identitet från och till en VM-skalningsuppsättning.
- Installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) om du inte redan har gjort. 

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och ta bort en systemtilldelade identiteter med Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade identiteter under genereringen av en Azure VM-skalningsuppsättning

Skapa en VMSS med systemtilldelad identitet aktiverat:

1. Referera till *exempel 1* i den [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) cmdlet referensartikeln för att skapa en VMSS med en systemtilldelad identitet.  Lägg till parameter `-IdentityType SystemAssigned` till den `New-AzureRmVmssConfig` cmdlet:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Valfritt) Lägg till en Managed Service Identity VMSS tillägget med hjälp av den `-Name` och `-Type` parametern på den [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet. Du kan skicka antingen ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux”, beroende på vilken typ av virtual machine scale ställa in och använda den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv:

    > [!NOTE]
    > Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade identiteter på en befintlig Azure VM-skalningsuppsättning

Om du vill aktivera en systemtilldelade identiteter på en befintlig Azure VM-skalningsuppsättning:

1. Logga in på Azure med `Login-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på virtuella datorns skalningsuppsättning, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Login-AzureRmAccount
   ```

2. Första hämta VM-skalningsuppsättningen egenskaper med hjälp av den [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) cmdlet. Om du vill aktivera en systemtilldelad identitet, Använd den `-IdentityType` växla den [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) cmdlet:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Lägg till en Managed Service Identity VMSS tillägget med hjälp av den `-Name` och `-Type` parametern på den [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet. Du kan skicka antingen ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux”, beroende på vilken typ av virtual machine scale ställa in och använda den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera den systemtilldelade identiteten från en Azure VM-skalningsuppsättning

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver systemtilldelad identitet, men fortfarande ha användartilldelade identiteter, använder du följande cmdlet:

1. Logga in på Azure med `Login-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på virtuella datorns skalningsuppsättning, till exempel ”virtuell Datordeltagare”:

2. Kör följande cmdlet:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver systemtilldelad identitet och har inga användartilldelade identiteter, använder du följande kommandon:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-identity"></a>Användartilldelad identitet

I det här avsnittet ska du lära dig hur du lägger till och ta bort Användartilldelad identitet från en VM-skalningsuppsättning med Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Tilldela en Användartilldelad identitet under skapandet av en Azure VM-skalningsuppsättning

Skapa en ny VM-skalningsuppsättning med en Användartilldelad identitet stöds inte för närvarande via PowerShell. Se nästa avsnitt om hur du lägger till en Användartilldelad identitet i en befintlig VM-skalningsuppsättning. Kom tillbaka om för att få uppdateringar.

### <a name="assign-a-user-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Tilldela en användaridentitet till en befintlig Azure VM-skalningsuppsättning

Att tilldela en Användartilldelad identitet till en befintlig Azure VM-skalningsuppsättning:

1. Logga in på Azure med `Connect-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på virtuella datorns skalningsuppsättning, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Första hämta VM-skalningsuppsättningen egenskaper med hjälp av den `Get-AzureRmVM` cmdlet. Om du vill tilldela en Användartilldelad identitet till virtuella datorns skalningsuppsättning, använder du sedan den `-IdentityType` och `-IdentityID` växla den [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) cmdlet. Ersätt `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` med dina egna värden.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort en användare som tilldelats hanterad identitet från en Azure VM-skalningsuppsättning

Om din skalningsuppsättning för virtuell dator har flera användartilldelade identiteter, kan du ta bort alla utom den sista som använder följande kommandon. Ersätt parametervärdena `<RESOURCE GROUP>` och `<VMSS NAME>` med dina egna värden. Den `<MSI NAME>` är Användartilldelad identitet namnegenskapen som fortfarande på virtuella datorns skalningsuppsättning. Den här informationen finns i identitetsavsnittet i VM-skaluppsättning som anges med `az vmss show`:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
Om din skalningsuppsättning för virtuell dator har inte en systemtilldelad identitet och du vill ta bort alla användartilldelade identiteter från den, använder du följande kommando:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Om din skalningsuppsättning för virtuell dator har både systemtilldelad och användartilldelade identiteter, du kan ta bort alla användartilldelade identiteter genom att växla med endast system som har tilldelats.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Relaterat innehåll

- [Hanterad tjänstidentitet-översikt](overview.md)
- Fullständig Azure skapandet virtuella datorn Snabbstarter, finns här:
  
  - [Skapa en Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 


















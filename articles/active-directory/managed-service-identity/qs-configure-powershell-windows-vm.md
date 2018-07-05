---
title: Hur du konfigurerar MSI på en Azure-dator med hjälp av PowerShell
description: Steg för steg-instruktioner för att konfigurera en hanterad tjänstidentitet (MSI) på en Azure-dator med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 20bf16eeb6aff952423af6754812f9532e55cd5f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444471"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med hjälp av PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>I den här artikeln får du lära dig hur du utför följande åtgärder för hanterad tjänstidentitet på en Azure-dator med hjälp av PowerShell:
- 

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) om du inte redan har gjort.

## <a name="system-assigned-identity"></a>Systemtilldelad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den systemtilldelade identiteter med Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelad identitet under skapandet av en Azure-dator

Skapa en Azure-dator med systemtilldelad identitet som aktiverat:

1. Se något av följande Azure VM Snabbstarter, Slutför bara de nödvändiga avsnitt (”logga in på Azure”, ”skapa resursgruppen”, ”skapa nätverk grupp”, ”skapa den virtuella datorn”).
    
    När du kommer till avsnittet ”Skapa VM” gör en liten ändring i [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) cmdlet-syntax. Se till att lägga till en `-AssignIdentity:$SystemAssigned` parameter för att etablera den virtuella datorn med den systemtilldelade identiteter aktiverat, till exempel:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Skapa en Windows-dator med hjälp av PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Skapa en Linux-dator med hjälp av PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Valfritt) Lägg till en MSI VM tillägget med hjälp av den `-Type` parametern på den [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux” beroende på vilken typ av virtuell dator och namnge den med hjälp av den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelad identitet på en befintlig Azure VM

Om du vill aktivera en systemtilldelade identiteter på en befintlig virtuell dator:

1. Logga in på Azure med `Login-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Login-AzureRmAccount
   ```

2. Hämta först virtuella datorns egenskaper med hjälp av den `Get-AzureRmVM` cmdlet. Om du vill aktivera en systemtilldelad identitet, Använd den `-AssignIdentity` växla den [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Valfritt) Lägg till en MSI VM tillägget med hjälp av den `-Type` parametern på den [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux” beroende på vilken typ av virtuell dator och namnge den med hjälp av den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv. Se till att ange rätt `-Location` parameter, matcha platsen för den befintliga virtuella datorn:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Inaktivera systemtilldelad identitet från en Azure virtuell dator

> [!NOTE]
>  Inaktiverar hanterad tjänstidentitet från en virtuell dator stöds för närvarande inte. Under tiden kan växla du mellan att använda System tilldelas och tilldelade användaridentiteter.

Om du har en virtuell dator som inte längre behöver systemtilldelad identitet, men fortfarande ha användartilldelade identiteter, använder du följande cmdlet:

1. Logga in på Azure med `Login-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Login-AzureRmAccount
   ```

2. Kör följande cmdlet: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Ta bort MSI VM-tillägg, användare-Name-växeln med den [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet, att ange samma namn som du använde när du har lagt till tillägget:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Användartilldelad identitet

Du lär dig hur du lägger till och ta bort Användartilldelad identitet från en virtuell dator med Azure PowerShell i det här avsnittet.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Tilldela en Användartilldelad identitet till en virtuell dator under skapande

Tilldela en Användartilldelad identitet till en Azure virtuell dator när du skapar den virtuella datorn:

1. Se något av följande Azure VM Snabbstarter, Slutför bara de nödvändiga avsnitt (”logga in på Azure”, ”skapa resursgruppen”, ”skapa nätverk grupp”, ”skapa den virtuella datorn”). 
  
    När du kommer till avsnittet ”Skapa VM” gör en liten ändring i [ `New-AzureRmVMConfig` ](/powershell/module/azurerm.compute/new-azurermvm) cmdlet-syntax. Lägg till den `-IdentityType UserAssigned` och `-IdentityID ` parametrar för att etablera den virtuella datorn med en Användartilldelad identitet.  Ersätt `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, och `<MSI NAME>` med dina egna värden.  Exempel:
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Skapa en Windows-dator med hjälp av PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Skapa en Linux-dator med hjälp av PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Valfritt) Lägg till en MSI VM tillägget med hjälp av den `-Type` parametern på den [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux” beroende på vilken typ av virtuell dator och namnge den med hjälp av den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv. Se till att ange rätt `-Location` parameter, matcha platsen för den befintliga virtuella datorn:
      > [!NOTE]
    > Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Tilldela en användaridentitet till en befintlig Azure VM

Tilldela Användartilldelad identitet till en befintlig virtuell Azure-dator:

1. Logga in på Azure med `Connect-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Skapa en Användartilldelad identitet med hjälp av den [New AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) cmdlet.  Obs den `Id` i utdata eftersom du behöver det i nästa steg.

    > [!IMPORTANT]
    > Skapa användartilldelade identiteter stöder endast alfanumeriskt och bindestreck (0-9 eller a-z eller A-Z eller -) tecken. Namnet bör dessutom begränsas till 24 tecken för tilldelning till VM/VMSS ska fungera korrekt. Kolla igen senare. Mer information finns i [vanliga frågor och kända problem](known-issues.md)


  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Hämta VM-egenskaper med hjälp av den `Get-AzureRmVM` cmdlet. Om du vill tilldela en Användartilldelad identitet för Azure-datorn, Använd den `-IdentityType` och `-IdentityID` växla den [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet.  Värdet för den`-IdentityId` parametern är den `Id` du antecknade i föregående steg.  Ersätt `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, och `<USER ASSIGNED IDENTITY NAME>` med dina egna värden.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Lägg till en MSI VM tillägget med hjälp av den `-Type` parametern på den [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux” beroende på vilken typ av virtuell dator och namnge den med hjälp av den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv. Ange rätt `-Location` parameter, matcha platsen för den befintliga virtuella datorn.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användare som tilldelats hanterad identitet från en Azure-dator

> [!NOTE]
>  Ta bort alla användartilldelade identiteter från en virtuell dator stöds för närvarande inte, om du inte har en systemtilldelad identitet. Kolla igen senare.

Om den virtuella datorn har flera användartilldelade identiteter, kan du ta bort alla utom den sista som använder följande kommandon. Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med dina egna värden. Den `<MSI NAME>` är Användartilldelad identitet namnegenskapen som fortfarande på den virtuella datorn. Den här informationen kan hittas genom att i identitetsavsnittet i en virtuell dator med hjälp av `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Om den virtuella datorn har både systemtilldelad och användartilldelade identiteter, du kan ta bort alla användartilldelade identiteter genom att växla mellan för att använda endast system som har tilldelats. Ange följande kommando:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Relaterat innehåll

- [Hanterad tjänstidentitet-översikt](overview.md)
- Fullständig Azure skapandet virtuella datorn Snabbstarter, finns här:
  
  - [Skapa en Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

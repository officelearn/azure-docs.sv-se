---
title: Hur du konfigurerar MSI på en virtuell Azure-dator med hjälp av PowerShell
description: Steg för steg-instruktioner för hur du konfigurerar en hanterad tjänst identitet (MSI) på en Azure VM, med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 0c316a2c73b451e4d8f67ace7b41c38dcfbc52f0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>I den här artikeln får du lära dig hur du utför följande åtgärder för hanterade tjänstidentiteten på en Azure VM, med hjälp av PowerShell:
- 

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan ett system som har tilldelats och användaren som har tilldelats identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) om du inte redan har gjort.

## <a name="system-assigned-identity"></a>Tilldelats identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar tilldelats identitet med hjälp av Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Aktivera system för tilldelade identitet under skapandet av en Azure VM

Skapa en virtuell dator i Azure med tilldelats identitet aktiverad:

1. Se något av följande Azure VM Snabbstart, Slutför bara de nödvändiga avsnitt (”logga in på Azure”, ”skapa resursgrupp”, ”skapa nätverk grupp”, ”skapa den virtuella datorn”).
    
    När du kommer till avsnittet ”Skapa den virtuella datorns”, gör en mindre ändring av [ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) cmdlet syntax. Se till att lägga till en `-AssignIdentity "SystemAssigned"` parametern för att etablera den virtuella datorn med den tilldelade systemidentitet aktiverad, till exempel:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity "SystemAssigned" ...
    ```

   - [Skapa en Windows-dator med hjälp av PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Skapa en Linux-dator med hjälp av PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Valfritt) Lägg till MSI VM-tillägget med den `-Type` parameter på den [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux”, beroende på vilken typ av virtuell dator och använda den `-Name` parameter. Den `-Settings` parametern anger vilken port som används av token OAuth-slutpunkten för token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Det här steget är valfritt eftersom du kan använda Azure instans Metadata Service (IMDS) identitet slutpunkt, för att hämta token samt.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Aktivera system för tilldelade identiteten på en befintlig virtuell Azure-dator

Om du vill aktivera en tilldelad systemidentitet på en befintlig virtuell dator:

1. Logga in på Azure med hjälp av `Login-AzureRmAccount`. Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

   ```powershell
   Login-AzureRmAccount
   ```

2. Först hämta VM-egenskaper med hjälp av den `Get-AzureRmVM` cmdlet. Om du vill aktivera en tilldelad systemidentitet, Använd den `-AssignIdentity` växla den [uppdatering AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity "SystemAssigned"
   ```

3. (Valfritt) Lägg till MSI VM-tillägget med den `-Type` parameter på den [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux”, beroende på vilken typ av virtuell dator och använda den `-Name` parameter. Den `-Settings` parametern anger vilken port som används av token OAuth-slutpunkten för token. Se till att ange rätt `-Location` parametern matchar platsen för den befintliga virtuella datorn:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Det här steget är valfritt eftersom du kan använda Azure instans Metadata Service (IMDS) identitet slutpunkt, för att hämta token samt.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Inaktivera tilldelats identitet från en Azure VM

> [!NOTE]
>  Inaktivera hanterade tjänstidentiteten från en virtuell dator stöds inte för närvarande. Under tiden kan växla du mellan att använda System tilldelade och tilldelade användaridentiteter.

Om du har en virtuell dator som inte längre behöver tilldelats identitet men fortfarande måste användaren som har tilldelats identiteter, använder du följande cmdlet:

1. Logga in på Azure med hjälp av `Login-AzureRmAccount`. Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

   ```powershell
   Login-AzureRmAccount
   ```

2. Kör följande cmdlet: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Att ta bort MSI VM-tillägget användaren - namnet växeln med den [ta bort AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet, att ange samma namn som du använde när du har lagt till tillägget:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Användaren som har tilldelats identitet

I det här avsnittet lär du dig att lägga till och ta bort en användare som tilldelats identitet från en virtuell dator med hjälp av Azure PowerShell.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Tilldela en användare som tilldelats identitet till en virtuell dator under skapande av

Tilldela en identitet för användaren som har tilldelats till en Azure VM när du skapar den virtuella datorn:

1. Se något av följande Azure VM Snabbstart, Slutför bara de nödvändiga avsnitt (”logga in på Azure”, ”skapa resursgrupp”, ”skapa nätverk grupp”, ”skapa den virtuella datorn”). 
  
    När du kommer till avsnittet ”Skapa den virtuella datorns”, gör en mindre ändring av [ `New-AzureRmVMConfig` ](/powershell/module/azurerm.compute/new-azurermvm) cmdlet syntax. Lägg till den `-IdentityType UserAssigned` och `-IdentityID ` parametrar för att etablera den virtuella datorn med en tilldelad användaridentitet.  Ersätt `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, och `<MSI NAME>` med egna värden.  Exempel:
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Skapa en Windows-dator med hjälp av PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Skapa en Linux-dator med hjälp av PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Valfritt) Lägg till MSI VM-tillägget med den `-Type` parameter på den [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux”, beroende på vilken typ av virtuell dator och använda den `-Name` parameter. Den `-Settings` parametern anger vilken port som används av token OAuth-slutpunkten för token. Se till att ange rätt `-Location` parametern matchar platsen för den befintliga virtuella datorn:
      > [!NOTE]
    > Det här steget är valfritt eftersom du kan använda Azure instans Metadata Service (IMDS) identitet slutpunkt, för att hämta token samt.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Tilldela en användaridentitet till en befintlig virtuell Azure-dator

Tilldela en användare som tilldelats identitet till en befintlig virtuell Azure-dator:

1. Logga in på Azure med hjälp av `Connect-AzureRmAccount`. Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Skapa en användare som tilldelats identitet med hjälp av den [ny AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) cmdlet.  Observera den `Id` i utdata eftersom du behöver det i nästa steg.

    > [!IMPORTANT]
    > Skapa användartilldelade identiteter med specialtecken (dvs understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Sök igen efter uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Hämta VM-egenskaper med hjälp av den `Get-AzureRmVM` cmdlet. Om du vill tilldela en tilldelad användaridentitet för Azure VM, Använd den `-IdentityType` och `-IdentityID` växla den [uppdatering AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet.  Värdet för den`-IdentityId` parametern är den `Id` du antecknade i föregående steg.  Ersätt `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, och `<USER ASSIGNED IDENTITY NAME>` med egna värden.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Lägg till MSI VM-tillägget med den `-Type` parameter på den [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux”, beroende på vilken typ av virtuell dator och använda den `-Name` parameter. Den `-Settings` parametern anger vilken port som används av token OAuth-slutpunkten för token. Ange rätt `-Location` parametern matchar platsen för den befintliga virtuella datorn.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användare som tilldelats hanterade identitet från en Azure VM

> [!NOTE]
>  Ta bort alla användartilldelade identiteter från en virtuell dator stöds för närvarande inte, såvida du inte har ett system som tilldelats identitet. Sök igen efter uppdateringar.

Om den virtuella datorn har flera tilldelade användaridentiteter, kan du ta bort alla utom det senaste med följande kommandon. Se till att ersätta den `<RESOURCE GROUP>` och `<VM NAME>` parametervärden med egna värden. Den `<MSI NAME>` är identiteten för användaren som har tilldelats egenskapen name, vilket ska finnas kvar på den virtuella datorn. Den här informationen finns med i identitetsavsnittet i den virtuella datorn med hjälp av `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Om den virtuella datorn har både system som har tilldelats och användaren som har tilldelats identiteter kan du ta bort alla användaren som har tilldelats identiteter genom att använda endast de system som har tilldelats. Ange följande kommando:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Relaterat innehåll

- [Översikt över hanterade tjänstidentiteten](overview.md)
- För fullständig Azure VM skapa Snabbstart, se:
  
  - [Skapa en Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 


















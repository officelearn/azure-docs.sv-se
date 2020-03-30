---
title: Konfigurera hanterade identiteter på en virtuell Azure-dator med PowerShell - Azure AD
description: Steg för steg instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en Azure VM med PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f24c89477d71df3f497590b49841403576343bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547220"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurera hanterade identiteter för Azure-resurser på en Virtuell Azure-dator med PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln, med PowerShell, lär du dig hur du utför följande hanterade identiteter för Azure-resurser åtgärder på en Azure VM.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan har gjort det.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och inaktiverar den systemtilldelade hanterade identiteten med Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivera systemtilldelade hanterade identiteter när en Azure-virtuell dator skapas

Om du vill skapa en virtuell Azure-dator med den systemtilldelade hanterade identiteten aktiverad behöver ditt konto rolltilldelningen för deltagare i [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Referera till en av följande Snabbstarter för Azure VM och slutför endast de nödvändiga avsnitten ("Logga in på Azure", "Skapa resursgrupp", "Skapa nätverksgrupp", "Skapa den virtuella datorn").
    
    När du kommer till avsnittet "Skapa den virtuella datorn" gör du en liten ändring av cmdletsyntaxen [New-AzVMConfig.](/powershell/module/az.compute/new-azvm) Var noga med `-AssignIdentity:$SystemAssigned` att lägga till en parameter för att etablera den virtuella datorn med den systemtilldelade identiteten aktiverad, till exempel:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Skapa en Virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Aktivera systemtilldelade hanterade identitet på en befintlig Azure-virtuell dator

För att aktivera systemtilldelade hanterade identitet på en virtuell dator som ursprungligen etablerades utan den, behöver ditt konto rolltilldelningen för deltagare i [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Logga in på `Connect-AzAccount`Azure med . Använd ett konto som är associerat med Azure-prenumerationen som innehåller den virtuella datorn.

   ```powershell
   Connect-AzAccount
   ```

2. Hämta först vm-egenskaperna `Get-AzVM` med cmdleten. Om du sedan vill aktivera en systemtilldelad hanterad identitet använder du växeln `-AssignIdentity` på cmdleten [Update-AzVM:](/powershell/module/az.compute/update-azvm)

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>Lägga till VM-system tilldelad identitet till en grupp

När du har aktiverat systemtilldelade identitet på en virtuell dator kan du lägga till den i en grupp.  Följande procedur lägger till en virtuell dators systemtilldelade identitet till en grupp.

1. Logga in på `Connect-AzAccount`Azure med . Använd ett konto som är associerat med Azure-prenumerationen som innehåller den virtuella datorn.

   ```powershell
   Connect-AzAccount
   ```

2. Hämta och `ObjectID` notera (som anges `Id` i fältet för de returnerade värdena) för den virtuella datorns huvudnamn för tjänsten:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Hämta och `ObjectID` notera gruppens (som anges i `Id` fältet för de returnerade värdena):

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Lägg till den virtuella datorns tjänsthuvudnamn i gruppen:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Inaktivera systemtilldelade hanterade identitet från en Virtuell Azure-dator

Om du vill inaktivera den systemtilldelade hanterade identiteten på en virtuell dator behöver kontot rolltilldelningen [för virtuella](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) datorer.  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

Om du har en virtuell dator som inte längre behöver den systemtilldelade hanterade identiteten men fortfarande behöver användartilldelade hanterade identiteter använder du följande cmdlet:

1. Logga in på `Connect-AzAccount`Azure med . Använd ett konto som är associerat med Azure-prenumerationen som innehåller den virtuella datorn.

   ```powershell
   Connect-AzAccount
   ```

2. Hämta vm-egenskaperna `Get-AzVM` med cmdlet `-IdentityType` och `UserAssigned`ange parametern till:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Om du har en virtuell dator som inte längre behöver systemtilldelade hanterade identiteter och den inte har några användartilldelade hanterade identiteter använder du följande kommandon:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användartilldelad hanterad identitet från en virtuell dator med Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Tilldela en användartilldelad hanterad identitet till en virtuell dator när den skapas

Om du vill tilldela en användartilldelning till en virtuell dator behöver ditt konto [rolltilldelningarna för deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitetsoperatör.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Referera till en av följande Snabbstarter för Azure VM och slutför endast de nödvändiga avsnitten ("Logga in på Azure", "Skapa resursgrupp", "Skapa nätverksgrupp", "Skapa den virtuella datorn"). 
  
    När du kommer till avsnittet "Skapa den virtuella datorn" gör du en liten ändring av [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) cmdletsyntaxen. Lägg `-IdentityType UserAssigned` till `-IdentityID` och parametrar för att etablera den virtuella datorn med en användartilldelad identitet.  Ersätt `<VM NAME>``<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` och med dina egna värden.  Ett exempel:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Skapa en Virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Tilldela en användartilldelad hanterad identitet till en befintlig Virtuell Azure-dator

Om du vill tilldela en användartilldelning till en virtuell dator behöver ditt konto [rolltilldelningarna för deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitetsoperatör.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Logga in på `Connect-AzAccount`Azure med . Använd ett konto som är associerat med Azure-prenumerationen som innehåller den virtuella datorn.

   ```powershell
   Connect-AzAccount
   ```

2. Skapa en användartilldelad hanterad identitet med cmdleten [New-AzUserAssignedIdentity.](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity)  Observera `Id` i utdata eftersom du behöver detta i nästa steg.

   > [!IMPORTANT]
   > Att skapa användartilldelade hanterade identiteter stöder bara alfanumeriska, understreck och bindestreck \_ (0-9 eller a-z eller A-Ö eller -) tecken. Dessutom bör namnet begränsas från 3 till 128 teckenlängd för tilldelningen till VM/VMSS att fungera korrekt. Mer information finns i [vanliga frågor och ärenden](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Hämta vm-egenskaperna `Get-AzVM` med cmdleten. Om du sedan vill tilldela en användartilldelade hanterad identitet till Den virtuella Azure-datorn använder du `-IdentityType` och `-IdentityID` aktiverar cmdleten [Update-AzVM.](/powershell/module/az.compute/update-azvm)  Värdet för`-IdentityId` parametern `Id` är det du noterade i föregående steg.  Ersätt `<VM NAME>` `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` och med dina egna värden.

   > [!WARNING]
   > Om du vill behålla tidigare användartilldelade hanterade identiteter `Identity` som tilldelats den virtuella `$vm.Identity`datorn frågar du egenskapen för VM-objektet (till exempel ).  Om någon användare som tilldelats hanterade identiteter returneras, inkludera dem i följande kommando tillsammans med den nya användaren tilldelade hanterade identitet som du vill tilldela den virtuella datorn.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användartilldelad hanterad identitet från en virtuell Azure-dator

Om du vill ta bort en användartilldelning av identitet till en virtuell dator behöver ditt konto rolltilldelningen för deltagare för [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

Om den virtuella datorn har flera användartilldelade hanterade identiteter kan du ta bort alla utom den sista med hjälp av följande kommandon. Ersätt parametervärdena `<RESOURCE GROUP>` och `<VM NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY NAME>` användartilldelade hanterade identitetens namnegenskap, som ska finnas kvar på den virtuella datorn. Den här informationen kan hittas genom att fråga egenskapen `Identity` för VM-objektet.  Till exempel: `$vm.Identity`

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Om den virtuella datorn inte har en systemtilldelad hanterad identitet och du vill ta bort alla användartilldelade hanterade identiteter från den använder du följande kommando:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Om den virtuella datorn har både systemtilldelade och användartilldelade hanterade identiteter kan du ta bort alla användartilldelade hanterade identiteter genom att växla till att endast använda systemtilldelade hanterade identiteter.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Fullständiga snabbstarter för Azure VM finns i:
  
  - [Skapa en virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

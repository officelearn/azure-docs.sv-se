---
title: Så här konfigurerar du MSI i en Azure-VMSS med hjälp av PowerShell
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
ms.openlocfilehash: 61fa6c94c0d717fe1e71bf8929f2e3b4a0982562
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903887"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Konfigurera en VMSS hanterad tjänstidentitet (MSI) med hjälp av PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför åtgärder för hanterad tjänstidentitet på en virtuell dator skala ange (VMSS), med hjälp av PowerShell:
- Aktivera och inaktivera systemtilldelad identitet i en Azure VMSS
- Lägga till och ta bort en Användartilldelad identitet i en Azure VMSS

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) om du inte redan har gjort. 

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och ta bort en systemtilldelade identiteter med Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Aktivera systemtilldelad identitet när du skapar en Azure VMSS

Skapa en VMSS med systemtilldelad identitet aktiverat:

1. Referera till *exempel 1* i den [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) cmdlet referensartikeln för att skapa en VMSS med en systemtilldelad identitet.  Lägg till parameter `-IdentityType SystemAssigned` till den `New-AzureRmVmssConfig` cmdlet:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Valfritt) Lägg till en MSI VMSS tillägget med hjälp av den `-Name` och `-Type` parametern på den [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux” beroende på vilken typ av virtuell dator och namnge den med hjälp av den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv:

    > [!NOTE]
    > Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Aktivera systemtilldelad identitet i en befintlig Azure VMSS

Om du vill aktivera en systemtilldelade identiteter på en befintlig Azure-VMSS:

1. Logga in på Azure med `Login-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Login-AzureRmAccount
   ```

2. Först hämta VMSS-egenskaper med hjälp av den [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) cmdlet. Om du vill aktivera en systemtilldelad identitet, Använd den `-IdentityType` växla den [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet:

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Lägg till en MSI VMSS tillägget med hjälp av den `-Name` och `-Type` parametern på den [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux” beroende på vilken typ av virtuell dator och namnge den med hjälp av den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Inaktivera systemtilldelad identitet från en Azure VMSS

> [!NOTE]
> Inaktiverar hanterad tjänstidentitet från en Virtual Machine Scale Sets stöds för närvarande inte. Under tiden kan växla du mellan att använda System tilldelas och tilldelade användaridentiteter.

Om du har en Virtual Machine Scale Sets som inte längre behöver systemtilldelad identitet, men fortfarande ha användartilldelade identiteter, använder du följande cmdlet:

1. Logga in på Azure med `Login-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”:

2. Kör följande cmdlet:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Användartilldelad identitet

Du lär dig hur du lägger till och ta bort Användartilldelad identitet från en Skalningsuppsättningen med hjälp av Azure PowerShell i det här avsnittet.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Tilldela Användartilldelad identitet under skapandet av en Azure VMSS

Skapa en ny VMSS med en Användartilldelad identitet stöds inte för närvarande via PowerShell. Om hur du lägger till en Användartilldelad identitet i en befintlig VMSS finns i nästa avsnitt. Kolla igen senare.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Tilldela en användaridentitet till en befintlig Azure VMSS

Tilldela Användartilldelad identitet till en befintlig Azure-VMSS:

1. Logga in på Azure med `Connect-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Hämta först virtuella datorns egenskaper med hjälp av den `Get-AzureRmVM` cmdlet. Om du vill tilldela en Användartilldelad identitet för Azure VMSS, Använd den `-IdentityType` och `-IdentityID` växla den [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet. Ersätt `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` med dina egna värden.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Ta bort Användartilldelad identitet från en Azure VMSS

> [!NOTE]
> Ta bort alla användartilldelade identiteter från en Virtual Machine Scale Sets stöds för närvarande inte, om du inte har en systemtilldelad identitet. Kolla igen senare.

Om din VMSS har flera användartilldelade identiteter, kan du ta bort alla utom den sista som använder följande kommandon. Se till att ersätta den `<RESOURCE GROUP>` och `<VMSS NAME>` parametervärden med dina egna värden. Den `<MSI NAME>` är Användartilldelad identitet namnegenskapen som fortfarande på VMSS. Den här informationen kan hittas genom att i identitetsavsnittet av VMSS med `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Om din VMSS har både systemtilldelad och användartilldelade identiteter, du kan ta bort alla användartilldelade identiteter genom att växla mellan för att använda endast system som har tilldelats. Ange följande kommando:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Relaterat innehåll

- [Hanterad tjänstidentitet-översikt](overview.md)
- Fullständig Azure skapandet virtuella datorn Snabbstarter, finns här:
  
  - [Skapa en Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 


















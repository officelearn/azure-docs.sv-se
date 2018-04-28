---
title: Hur du konfigurerar MSI på en Azure-VMSS med hjälp av PowerShell
description: Steg för steg tilldelat instruktioner för konfiguration av en system- och en identiteter på en Azure-VMSS med hjälp av PowerShell.
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
ms.openlocfilehash: 2b3651eaf702cfe2f73320fcaf2ab469dd7c478a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Konfigurera en VMSS hanteras Service identitet (MSI) med hjälp av PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

Lär dig hur du utför följande åtgärder på ett Azure Virtual Machine Scale ange (VMSS), med hjälp av PowerShell på hanterade tjänstidentiteten i den här artikeln:
- Aktivera och inaktivera tilldelats identiteten på ett Azure-VMSS
- Lägga till och ta bort en användare som tilldelats identiteten på ett Azure-VMSS

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan ett system som har tilldelats och användaren som har tilldelats identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) om du inte redan har gjort. 

## <a name="system-assigned-managed-identity"></a>Tilldelats hanterade identitet

Lär dig hur du aktiverar och ta bort en tilldelad systemidentitet med hjälp av Azure PowerShell i det här avsnittet.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Aktivera system för tilldelade identitet under genereringen av en Azure-VMSS

Skapa en VMSS med tilldelade systemidentitet aktiverad:

1. Referera till *exempel 1* i den [ny AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) cmdlet referensartikeln för att skapa en VMSS med en tilldelad systemidentitet.  Lägger till parametern `-IdentityType SystemAssigned` till den `New-AzureRmVmssConfig` cmdlet:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Valfritt) Lägg till en MSI VMSS tillägget med hjälp av den `-Name` och `-Type` parameter på den [Lägg till AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux”, beroende på vilken typ av virtuell dator och använda den `-Name` parameter. Den `-Settings` parametern anger vilken port som används av token OAuth-slutpunkten för token:

    > [!NOTE]
    > Det här steget är valfritt eftersom du kan använda Azure instans Metadata Service (IMDS) identitet slutpunkt, för att hämta token samt.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Aktivera system för tilldelade identiteten på en befintlig Azure-VMSS

Om du vill aktivera en tilldelad systemidentitet på en befintlig Azure-VMSS:

1. Logga in på Azure med hjälp av `Login-AzureRmAccount`. Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

   ```powershell
   Login-AzureRmAccount
   ```

2. Först hämta VMSS-egenskaper med hjälp av den [ `Get-AzureRmVmss` ](/powershell/module/azurerm.compute/get-azurermvmss) cmdlet. Om du vill aktivera en tilldelad systemidentitet, Använd den `-IdentityType` växla den [uppdatering AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet:

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Lägg till en MSI VMSS tillägget med hjälp av den `-Name` och `-Type` parameter på den [Lägg till AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux”, beroende på vilken typ av virtuell dator och använda den `-Name` parameter. Den `-Settings` parametern anger vilken port som används av token OAuth-slutpunkten för token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Inaktivera tilldelats identitet från en Azure-VMSS

> [!NOTE]
> Inaktivera hanterade tjänstidentiteten från Skalningsuppsättning en virtuell dator stöds inte för närvarande. Under tiden kan växla du mellan att använda System tilldelade och tilldelade användaridentiteter.

Om du har en Skalningsuppsättning virtuell dator som inte längre behöver tilldelats identitet men fortfarande måste användaren som har tilldelats identiteter, använder du följande cmdlet:

1. Logga in på Azure med hjälp av `Login-AzureRmAccount`. Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

2. Kör följande cmdlet:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Användaren som har tilldelats identitet

I det här avsnittet lär du dig att lägga till och ta bort en användare som tilldelats identitet från en VMSS med hjälp av Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Tilldela en användare som tilldelats identitet under skapandet av en Azure-VMSS

Skapa en ny VMSS med en användare som tilldelats identitet stöds inte för närvarande via PowerShell. Finns i nästa avsnitt om hur du lägger till en tilldelad användaridentitet i en befintlig VMSS. Sök igen efter uppdateringar.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Tilldela en användaridentitet till en befintlig Azure-VMSS

Tilldela en användare som tilldelats en befintlig Azure-VMSS identitet:

1. Logga in på Azure med hjälp av `Connect-AzureRmAccount`. Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Först hämta VM-egenskaper med hjälp av den `Get-AzureRmVM` cmdlet. Om du vill tilldela en tilldelad användaridentitet Azure VMSS, Använd den `-IdentityType` och `-IdentityID` växeln de [uppdatering AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet. Ersätt `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` med egna värden.

   > [!IMPORTANT]
   > Skapa användartilldelade identiteter med specialtecken (dvs understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Sök igen efter uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Ta bort en användare som tilldelats identitet från en Azure-VMSS

> [!NOTE]
> Ta bort alla användartilldelade identiteter från en Virtual Machine Scale Set stöds för närvarande inte, såvida du inte har ett system som tilldelats identitet. Sök igen efter uppdateringar.

Om din VMSS har flera tilldelade användaridentiteter, kan du ta bort alla utom det senaste med följande kommandon. Se till att ersätta den `<RESOURCE GROUP>` och `<VMSS NAME>` parametervärden med egna värden. Den `<MSI NAME>` är identiteten för användaren som har tilldelats egenskapen name, vilket ska finnas kvar på VMSS. Den här informationen finns med i identity-avsnittet av VMSS med `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Om din VMSS har både system som har tilldelats och användaren som har tilldelats identiteter kan du ta bort alla användaren som har tilldelats identiteter genom att använda endast de system som har tilldelats. Ange följande kommando:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Relaterat innehåll

- [Översikt över hanterade tjänstidentiteten](overview.md)
- För fullständig Azure VM skapa Snabbstart, se:
  
  - [Skapa en Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 


















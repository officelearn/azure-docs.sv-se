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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9a466a5c695277a7b5833f997e2ad7281c962f3f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31588259"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

I den här artikeln beskrivs hur du aktiverar och ta bort MSI för en Azure-dator med hjälp av PowerShell.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Dessutom installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) (version 4.3.1 eller senare) om du inte redan har gjort.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Aktivera MSI under skapandet av en Azure VM

Skapa en MSI-aktiverad virtuell dator:

1. Se något av följande Azure VM Snabbstart, Slutför bara de nödvändiga avsnitt (”logga in på Azure”, ”skapa resursgrupp”, ”skapa nätverk grupp”, ”skapa den virtuella datorn”). 

   > [!IMPORTANT] 
   > När du kommer till avsnittet ”Skapa den virtuella datorns”, gör en mindre ändring av [ny AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) cmdlet syntax. Se till att lägga till en `-IdentityType "SystemAssigned"` parametern för att etablera den virtuella datorn med en MSI, till exempel:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Skapa en Windows-dator med hjälp av PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md)
   - [Skapa en Linux-dator med hjälp av PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md)



2. Lägg till MSI VM-tillägget med den `-Type` parameter på den [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux”, beroende på vilken typ av virtuell dator och använda den `-Name` parameter. Den `-Settings` parametern anger vilken port som används av token OAuth-slutpunkten för token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Aktivera MSI på en befintlig virtuell Azure-dator

Om du behöver aktivera MSI på en befintlig virtuell dator:

1. Logga in på Azure med hjälp av `Connect-AzureRmAccount`. Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Först hämta VM-egenskaper med hjälp av den `Get-AzureRmVM` cmdlet. Om du vill aktivera MSI använder den `-IdentityType` växla den [uppdatering AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Lägg till MSI VM-tillägget med den `-Type` parameter på den [Set AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux”, beroende på vilken typ av virtuell dator och använda den `-Name` parameter. Den `-Settings` parametern anger vilken port som används av token OAuth-slutpunkten för token. Se till att ange rätt `-Location` parametern matchar platsen för den befintliga virtuella datorn:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Ta bort MSI från en virtuell dator i Azure

Om du har en virtuell dator som inte längre behöver en MSI, kan du använda den `RemoveAzureRmVMExtension` för att ta bort MSI från den virtuella datorn:

1. Logga in på Azure med hjälp av `Connect-AzureRmAccount`. Använd ett konto som är kopplad till den Azure-prenumeration som innehåller den virtuella datorn. Kontrollera också att ditt konto hör till en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”Virtual Machine-deltagare”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Använd den `-Name` växel med den [ta bort AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet, att ange samma namn som du använde när du har lagt till tillägget:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Relaterat innehåll

- [Översikt över hanterade tjänstidentiteten](msi-overview.md)
- För fullständig Azure VM skapa Snabbstart, se:
  
  - [Skapa en Windows-dator med PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en virtuell Linux-dator med PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md) 

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.

















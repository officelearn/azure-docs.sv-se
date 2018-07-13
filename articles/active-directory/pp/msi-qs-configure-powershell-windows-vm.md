---
title: Hur du konfigurerar MSI på en Azure-dator med hjälp av PowerShell
description: Steg för steg-instruktioner för att konfigurera en hanterad tjänstidentitet (MSI) på en Azure-dator med hjälp av PowerShell.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611007"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Konfigurera en virtuell dator hanterad tjänstidentitet (MSI) med hjälp av PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och ta bort MSI för en Azure-dator med hjälp av PowerShell.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Dessutom installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) (version 4.3.1 eller senare) om du inte redan har gjort.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Aktivera MSI under skapandet av en Azure-dator

Skapa en MSI-aktiverad virtuell dator:

1. Se något av följande Azure VM Snabbstarter, Slutför bara de nödvändiga avsnitt (”logga in på Azure”, ”skapa resursgruppen”, ”skapa nätverk grupp”, ”skapa den virtuella datorn”). 

   > [!IMPORTANT] 
   > När du kommer till avsnittet ”Skapa VM” gör en liten ändring i [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) cmdlet-syntax. Se till att lägga till en `-IdentityType "SystemAssigned"` parameter för att etablera den virtuella datorn med en MSI, till exempel:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Skapa en Windows-dator med hjälp av PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md)
   - [Skapa en Linux-dator med hjälp av PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md)



2. Lägg till en MSI VM tillägget med hjälp av den `-Type` parametern på den [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux” beroende på vilken typ av virtuell dator och namnge den med hjälp av den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Aktivera MSI på en befintlig Azure VM

Om du vill aktivera MSI på en befintlig virtuell dator:

1. Logga in på Azure med `Connect-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Hämta först virtuella datorns egenskaper med hjälp av den `Get-AzureRmVM` cmdlet. Om du vill aktivera MSI, Använd den `-IdentityType` växla den [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) cmdlet:

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Lägg till en MSI VM tillägget med hjälp av den `-Type` parametern på den [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) cmdlet. Du kan skicka ”ManagedIdentityExtensionForWindows” eller ”ManagedIdentityExtensionForLinux” beroende på vilken typ av virtuell dator och namnge den med hjälp av den `-Name` parametern. Den `-Settings` parametern anger den port som används av OAuth-token-slutpunkten för tokenförvärv. Se till att ange rätt `-Location` parameter, matcha platsen för den befintliga virtuella datorn:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Ta bort MSI från en Azure virtuell dator

Om du har en virtuell dator som inte längre behöver en MSI, kan du använda den `RemoveAzureRmVMExtension` cmdlet för att ta bort MSI från den virtuella datorn:

1. Logga in på Azure med `Connect-AzureRmAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Connect-AzureRmAccount
   ```

2. Använd den `-Name` växel med den [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) cmdlet, att ange samma namn som du använde när du har lagt till tillägget:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Relaterat innehåll

- [Hanterad tjänstidentitet-översikt](msi-overview.md)
- Fullständig Azure skapandet virtuella datorn Snabbstarter, finns här:
  
  - [Skapa en Windows-dator med PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en Linux-dator med PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md) 

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.

















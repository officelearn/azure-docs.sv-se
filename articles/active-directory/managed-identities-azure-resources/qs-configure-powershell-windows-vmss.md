---
title: Konfigurera hanterade identiteter på skalningsuppsättningar för virtuella datorer med PowerShell - Azure AD
description: Steg för steg instruktioner för att konfigurera ett system och användartilldelade hanterade identiteter på en virtuell dator skala uppsättning med PowerShell.
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
ms.openlocfilehash: 755aee312fd0492fd57a82cb7a437b04ebf72987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547271"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurera hanterade identiteter för Azure-resurser på skalningsuppsättningar för virtuella datorer med PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden. 

I den här artikeln, med PowerShell, lär du dig hur du utför hanterade identiteter för Azure-resurser åtgärder på en virtuell dator skala uppsättning:
- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en skalningsuppsättning för virtuella datorer
- Lägga till och ta bort en användartilldelad hanterad identitet i en skaluppsättning för virtuella datorer

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användarhanterad tilldelad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att utföra hanteringsåtgärderna i den här artikeln behöver ditt konto följande Azure-rollbaserade åtkomstkontrolltilldelningar:

    > [!NOTE]
    > Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

    - [Deltagare i virtuella](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) datorer för att skapa en skaluppsättning för virtuella datorer och aktivera och ta bort den systemtilldelade hanterade och/eller användartilldelade hanterade identiteten från en skaluppsättning för virtuella datorer.
    - [Hanterad identitetsbidragstagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en användartilldelade hanterad identitet.
    - [Hanterad identitetsoperatorroll](/azure/role-based-access-control/built-in-roles#managed-identity-operator) för att tilldela och ta bort en användartilldelad hanterad identitet från och till en skaluppsättning för virtuella datorer.
- Installera [den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan har gjort det. 

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och tar bort en systemtilldelad hanterad identitet med Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterade identitet under skapandet av en Azure-skalningsuppsättning för virtuella datorer

Så här skapar du en skalningsuppsättning för virtuella datorer med den systemtilldelade hanterade identiteten aktiverad:

1. Se *exempel 1* i cmdlet-referensartikeln [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) för att skapa en skalauppsättning för virtuella datorer med en systemtilldelad hanterad identitet.  Lägg till `-IdentityType SystemAssigned` parametern i cmdlet: `New-AzVmssConfig`

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterade identitet på en befintlig Azure-skalningsuppsättning för virtuella datorer

Om du behöver aktivera en systemtilldelad hanterad identitet på en befintlig Azure-skalauppsättning för virtuella datorer:

1. Logga in på `Connect-AzAccount`Azure med . Använd ett konto som är associerat med Azure-prenumerationen som innehåller skaluppsättningen för den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet för skalningsuppsättningen för den virtuella datorn, till exempel "Deltagare för virtuell dator":

   ```powershell
   Connect-AzAccount
   ```

2. Hämta först egenskaperna för skalningsuppsättningen för den virtuella datorn med hjälp av cmdleten. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) Om du sedan vill aktivera en systemtilldelad hanterad identitet använder du växeln `-IdentityType` på cmdleten [Update-AzVmss:](/powershell/module/az.compute/update-azvmss)

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera den systemtilldelade hanterade identiteten från en Azure-skalningsuppsättning för virtuella datorer

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver den systemtilldelade hanterade identiteten men fortfarande behöver användartilldelade hanterade identiteter använder du följande cmdlet:

1. Logga in på `Connect-AzAccount`Azure med . Använd ett konto som är associerat med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet för skalningsuppsättningen för den virtuella datorn, till exempel "Deltagare för virtuell dator":

2. Kör följande cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver systemtilldelade hanterade identiteter och den inte har några användartilldelade hanterade identiteter använder du följande kommandon:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet får du lära dig hur du lägger till och tar bort en användartilldelad hanterad identitet från en skalningsuppsättning för virtuella datorer med Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Tilldela en användartilldelad hanterad identitet när en Azure-skalningsuppsättning för virtuella datorer skapas

Det går för närvarande inte att skapa en ny skalningsuppsättning för virtuella datorer med en användartilldelad hanterad identitet via PowerShell. Se nästa avsnitt om hur du lägger till en användartilldelad hanterad identitet i en befintlig skaluppsättning för virtuella datorer. Kom tillbaka om för att få uppdateringar.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Tilldela en användartilldelad hanterad identitet till en befintlig Azure-skalningsuppsättning för virtuella datorer

Så här tilldelar du en användartilldelade hanterad identitet till en befintlig Azure-skalningsuppsättning för virtuella datorer:

1. Logga in på `Connect-AzAccount`Azure med . Använd ett konto som är associerat med Azure-prenumerationen som innehåller skaluppsättningen för den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet för skalningsuppsättningen för den virtuella datorn, till exempel "Deltagare för virtuell dator":

   ```powershell
   Connect-AzAccount
   ```

2. Hämta först egenskaperna för skalningsuppsättningen för den virtuella datorn med hjälp av cmdleten. `Get-AzVM` Om du sedan vill tilldela en användartilldelade hanterad `-IdentityType` identitet `-IdentityID` till skaluppsättningen för den virtuella datorn använder du och aktiverar cmdleten [Update-AzVmss.](/powershell/module/az.compute/update-azvmss) Ersätt `<VM NAME>` `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` , , med dina egna värden.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort en användartilldelad hanterad identitet från en Azure-skalningsuppsättning för virtuella datorer

Om skaluppsättningen för virtuella datorer har flera användartilldelade hanterade identiteter kan du ta bort alla utom den sista med hjälp av följande kommandon. Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY NAME>` användartilldelade hanterade identitetens namnegenskap, som ska finnas kvar på skalningsuppsättningen för den virtuella datorn. Den här informationen finns i identitetsavsnittet i `az vmss show`den virtuella datorns skalningsuppsättning med:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Om skaluppsättningen för den virtuella datorn inte har en systemtilldelad hanterad identitet och du vill ta bort alla användartilldelade hanterade identiteter från den använder du följande kommando:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Om skaluppsättningen för virtuella datorer har både systemtilldelade och användartilldelade hanterade identiteter kan du ta bort alla användartilldelade hanterade identiteter genom att växla till att endast använda den systemtilldelade hanterade identiteten.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Fullständiga snabbstarter för Azure VM finns i:
  
  - [Skapa en virtuell Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en virtuell Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 


















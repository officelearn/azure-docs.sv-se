---
title: Hur du konfigurerar hanterade identiteter för Azure-resurser på en virtuell datorskalning Sets med PowerShell
description: Anvisningar för att konfigurera ett system och användartilldelade hanterade identiteter på en virtuell datorskalning ange steg för steg med hjälp av PowerShell.
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
ms.date: 11/27/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4917720af2396b68ccd36cc0410c9acbbba2d9b2
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448587"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurera hanterade identiteter för Azure-resurser på VM-skalningsuppsättningar med hjälp av PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln med hjälp av PowerShell, du lära dig hur du utför de hanterade identiteterna för Azure-resurser på en VM-skalningsuppsättning:
- Aktivera och inaktivera systemtilldelade hanterad identitet på en VM-skalningsuppsättning
- Lägga till och ta bort en Användartilldelad hanterad identitet på en VM-skalningsuppsättning

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelade och hanteras av användare Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande Azure rollbaserad åtkomstkontroll tilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:

    > [!NOTE]
    > Inga ytterligare Azure AD directory rolltilldelningar krävs.

    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en virtuell datorskalning och aktivera och ta bort systemtilldelade hanteras och/eller användartilldelade hanterad identitet från en skalningsuppsättning för virtuell dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad hanterad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad hanterad identitet från och till en VM-skalningsuppsättning.
- Installera [den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan har gjort. 

## <a name="system-assigned-managed-identity"></a>Systemtilldelade hanterad identitet

I det här avsnittet får du lära dig hur du aktiverar och ta bort en automatiskt genererad hanterad identitet med hjälp av Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterad identitet när du skapar en Azure VM-skalningsuppsättning

Skapa en VM-skalningsuppsättning med systemtilldelade hanterade identiteten aktiverat:

1. Referera till *exempel 1* i den [New AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet referensartikeln om du skapar en VM-skalningsuppsättning som anges med en automatiskt genererad hanterad identitet.  Lägg till parameter `-IdentityType SystemAssigned` till den `New-AzVmssConfig` cmdlet:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```
> [!NOTE]
> Du kan etablera hanterade identiteter för Azure-resurser VM-skalningsuppsättningen tillägget, men snart upphör att gälla. Vi rekommenderar att du använder Azure Instance Metadata identitet slutpunkten för autentisering. Mer information finns i [sluta använda VM-tillägget och börja använda Azure IMDS slutpunkten för autentisering](howto-migrate-vm-extension.md).


## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterad identitet på en befintlig Azure VM-skalningsuppsättning

Om du vill aktivera en automatiskt genererad hanterad identitet på en befintlig Azure VM-skalningsuppsättning:

1. Logga in på Azure med `Connect-AzAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på virtuella datorns skalningsuppsättning, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Connect-AzAccount
   ```

2. Första hämta VM-skalningsuppsättningen egenskaper med hjälp av den [ `Get-AzVmss` ](/powershell/module/az.compute/get-azvmss) cmdlet. Om du vill aktivera en automatiskt genererad hanterad identitet, Använd den `-IdentityType` växla den [uppdatering AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

> [!NOTE]
> Du kan etablera hanterade identiteter för Azure-resurser VM-skalningsuppsättningen tillägget, men snart upphör att gälla. Vi rekommenderar att du använder Azure Instance Metadata identitet slutpunkten för autentisering. Mer information finns i [migrera från VM-tillägget till Azure IMDS slutpunkten för autentisering](howto-migrate-vm-extension.md).

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera systemtilldelade hanterad identitet från en Azure VM-skalningsuppsättning

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver systemtilldelade hanterad identitet, men fortfarande ha användartilldelade hanterade identiteter, använder du följande cmdlet:

1. Logga in på Azure med `Connect-AzAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på virtuella datorns skalningsuppsättning, till exempel ”virtuell Datordeltagare”:

2. Kör följande cmdlet:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver systemtilldelade hanterad identitet och har inga hanterade användartilldelade identiteter, använder du följande kommandon:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

Du lär dig hur du lägger till och ta bort en hanterad Användartilldelad identitet från en VM-skalningsuppsättning med Azure PowerShell i det här avsnittet.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Tilldela en hanterad Användartilldelad identitet under skapandet av en Azure VM-skalningsuppsättning

Skapa en ny VM-skalningsuppsättning med en Användartilldelad hanterad identitet stöds inte för närvarande via PowerShell. Se nästa avsnitt om hur du lägger till en hanterad Användartilldelad identitet i en befintlig VM-skalningsuppsättning. Kom tillbaka om för att få uppdateringar.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Tilldela en hanterad Användartilldelad identitet till en befintlig Azure VM-skalningsuppsättning

Så här tilldelar du en hanterad Användartilldelad identitet till en befintlig Azure VM-skalningsuppsättning:

1. Logga in på Azure med `Connect-AzAccount`. Använd ett konto som är associerad med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning. Kontrollera också att ditt konto tillhör en roll som ger dig skrivbehörighet på virtuella datorns skalningsuppsättning, till exempel ”virtuell Datordeltagare”:

   ```powershell
   Connect-AzAccount
   ```

2. Första hämta VM-skalningsuppsättningen egenskaper med hjälp av den `Get-AzVM` cmdlet. Om du vill tilldela en hanterad Användartilldelad identitet till virtuella datorns skalningsuppsättning, använder du sedan den `-IdentityType` och `-IdentityID` växla den [uppdatering AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet. Ersätt `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` med dina egna värden.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort en hanterad Användartilldelad identitet från en Azure VM-skalningsuppsättning

Om din skalningsuppsättning för virtuell dator har flera användartilldelade hanterade identiteter, kan du ta bort alla utom den sista som använder följande kommandon. Ersätt parametervärdena `<RESOURCE GROUP>` och `<VIRTUAL MACHINE SCALE SET NAME>` med dina egna värden. Den `<USER ASSIGNED IDENTITY NAME>` är användartilldelade hanterade identitetens namnegenskapen som fortfarande på virtuella datorns skalningsuppsättning. Den här informationen finns i identitetsavsnittet i VM-skaluppsättning som anges med `az vmss show`:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Om din skalningsuppsättning för virtuell dator inte har en automatiskt genererad hanterad vill identitet och du ta bort alla användartilldelade hanterade identiteter från den, använder du följande kommando:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Om din VM-skalningsuppsättningen har båda systemtilldelade och användartilldelade hanterade identiteter kan du ta bort alla användartilldelade hanterade identiteter genom att växla mellan att använda endast systemtilldelade hanterad identitet.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Nästa steg

- [Hanterade identiteter för översikt över Azure-resurser](overview.md)
- Fullständig Azure skapandet virtuella datorn Snabbstarter, finns här:
  
  - [Skapa en Windows-dator med PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Skapa en Linux-dator med PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 


















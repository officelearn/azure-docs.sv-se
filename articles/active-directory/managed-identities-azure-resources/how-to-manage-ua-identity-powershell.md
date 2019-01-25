---
title: Hur du skapar, lista och ta bort en Användartilldelad hanterad identitet med hjälp av Azure PowerShell
description: Steg för steg-anvisningar om hur du skapar, lista och ta bort Användartilldelad hanterad identitet med hjälp av Azure PowerShell.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: priyamo
ms.openlocfilehash: 74922c93a44ea5fea20f899680cf762a0981a203
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903884"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Skapa, visa eller ta bort en Användartilldelad hanterad identitet med hjälp av Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du skapar, lista och ta bort en Användartilldelad hanterad identitet med hjälp av Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan har gjort.
- Om du kör PowerShell lokalt behöver du även göra följande: 
    - Kör `Connect-AzAccount` för att skapa en anslutning med Azure.
    - Installera [den senaste versionen av PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Kör `Install-Module -Name PowerShellGet -AllowPrerelease` för att hämta förhandsversionen av `PowerShellGet`-modulen (du kan behöva `Exit` ur den aktuella PowerShell-sessionen när du har kört det här kommandot för att installera `Az.ManagedServiceIdentity`-modulen).
    - Kör `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` du installerar förhandsversionen av den `Az.ManagedServiceIdentity` modulen för att utföra den användartilldelade hanterad identitet åtgärder i den här artikeln.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Om du vill skapa en hanterad Användartilldelad identitet, ditt konto måste den [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rolltilldelning.

Du kan skapa en hanterad Användartilldelad identitet med den `New-AzUserAssignedIdentity` kommando. Den `ResourceGroupName` parametern anger resursgruppens namn var du vill skapa en hanterad identitet användartilldelade och `-Name` parametern anger dess namn. Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärden med dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista användartilldelade hanterade identiteter

Om du vill lista/läsa en hanterad Användartilldelad identitet, ditt konto måste den [hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rolltilldelning.

Hanterade identiteter, Använd den [Get-AzUserAssigned] kommandot listan tilldelade användare.  Den `-ResourceGroupName` parametern anger resursgruppen där den hanterade Användartilldelad identitet har skapats. Ersätt den `<RESOURCE GROUP>` med ditt eget värde:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
I svaret, användartilldelade hanterade identiteter har `"Microsoft.ManagedIdentity/userAssignedIdentities"` värdet som returneras för nyckel `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en hanterad Användartilldelad identitet

Om du vill ta bort en hanterad Användartilldelad identitet måste ditt konto måste den [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rolltilldelning.

Ta bort en hanterad Användartilldelad identitet genom att använda den `Remove-AzUserAssignedIdentity` kommando.  Den `-ResourceGroupName` parametern anger resursgruppen där Användartilldelad identitet har skapats och `-Name` parametern anger dess namn. Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärdena med dina egna värden:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Tar bort en hanterad Användartilldelad identitet kommer inte att ta bort referensen, från alla resurser som tilldelades till. Identitet tilldelningar måste tas bort separat.

## <a name="next-steps"></a>Nästa steg

En fullständig lista och mer av Azure PowerShell hanterade identiteter för Azure-resurser kommandon finns i [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).

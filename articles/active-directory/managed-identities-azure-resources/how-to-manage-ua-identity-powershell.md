---
title: Skapa, lista & ta bort användartilldelade hanterade identitet med Azure PowerShell - Azure AD
description: Steg för steg instruktioner om hur du skapar, lista och ta bort användartilldelade hanterade identitet med Azure PowerShell.
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
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c512a867685b4480c7b31ac582e2cee069ee2447
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547404"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Skapa, lista eller ta bort en användartilldelad hanterad identitet med Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du skapar, listar och tar bort en användartilldelad hanterad identitet med Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan har gjort det.
- Om du kör PowerShell lokalt behöver du även göra följande: 
    - Kör `Connect-AzAccount` för att skapa en anslutning med Azure.
    - Installera [den senaste versionen av PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Kör `Install-Module -Name PowerShellGet -AllowPrerelease` för att hämta förhandsversionen av `PowerShellGet`-modulen (du kan behöva `Exit` ur den aktuella PowerShell-sessionen när du har kört det här kommandot för att installera `Az.ManagedServiceIdentity`-modulen).
    - Kör `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` för att installera förhandsversionen `Az.ManagedServiceIdentity` av modulen för att utföra de användartilldelade hanterade identitetsåtgärderna i den här artikeln.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

Om du vill skapa en användartilldelad hanterad identitet behöver ditt konto rolltilldelningen [Managed Identity Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Om du vill skapa en användartilldelad hanterad identitet använder du `New-AzUserAssignedIdentity` kommandot. Parametern `ResourceGroupName` anger resursgruppen var den användartilldelade hanterade identiteten `-Name` ska skapas och parametern anger dess namn. Ersätt `<RESOURCE GROUP>` värdena och parametern med `<USER ASSIGNED IDENTITY NAME>` dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista användartilldelade hanterade identiteter

Om du vill lista/läsa en användartilldelad hanterad identitet behöver ditt konto rolltilldelningen [Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller Managed Identity [Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Om du vill visa användartilldelade hanterade identiteter använder du kommandot [Get-AzUserAssigned].  Parametern `-ResourceGroupName` anger den resursgrupp där den användartilldelade hanterade identiteten skapades. Ersätt `<RESOURCE GROUP>` med ditt eget värde:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
I svaret har `"Microsoft.ManagedIdentity/userAssignedIdentities"` användartilldelade hanterade identiteter returnerat `Type`för nyckel.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användartilldelad hanterad identitet

Om du vill ta bort en användartilldelad hanterad identitet behöver kontot rolltilldelningen [Managed Identity Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Om du vill ta bort en `Remove-AzUserAssignedIdentity` användartilldelad hanterad identitet använder du kommandot.  Parametern `-ResourceGroupName` anger den resursgrupp där den användartilldelade `-Name` identiteten skapades och parametern anger dess namn. Ersätt `<RESOURCE GROUP>` värdena `<USER ASSIGNED IDENTITY NAME>` och parametrarna med dina egna värden:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Om du tar bort en användartilldelad hanterad identitet tas inte referensen bort från någon resurs som den har tilldelats. Identitetstilldelningar måste tas bort separat.

## <a name="next-steps"></a>Nästa steg

En fullständig lista och mer information om Azure PowerShell-hanterade identiteter för Azure-resurser finns i [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).

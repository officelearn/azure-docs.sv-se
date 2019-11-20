---
title: Skapa, Visa & ta bort användardefinierad hanterad identitet med hjälp av Azure PowerShell-Azure AD
description: Steg för steg-anvisningar om hur du skapar, visar och tar bort användarspecifika hanterade identiteter med hjälp av Azure PowerShell.
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
ms.openlocfilehash: 3aa6a929ef6e3d23cab81cfb199bf733ac25927f
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184175"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Skapa, Visa eller ta bort en användardefinierad hanterad identitet med hjälp av Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva ange autentiseringsuppgifter i koden. 

I den här artikeln får du lära dig hur du skapar, visar och tar bort en användardefinierad hanterad identitet med hjälp av Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)** .
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan har gjort.
- Om du kör PowerShell lokalt behöver du även göra följande: 
    - Kör `Connect-AzAccount` för att skapa en anslutning med Azure.
    - Installera [den senaste versionen av PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Kör `Install-Module -Name PowerShellGet -AllowPrerelease` för att hämta förhandsversionen av `PowerShellGet`-modulen (du kan behöva `Exit` ur den aktuella PowerShell-sessionen när du har kört det här kommandot för att installera `Az.ManagedServiceIdentity`-modulen).
    - Kör `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` för att installera för hands versionen av `Az.ManagedServiceIdentity`-modulen för att utföra användar tilldelningen av hanterade identitets åtgärder i den här artikeln.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet

För att skapa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Om du vill skapa en användardefinierad hanterad identitet använder du kommandot `New-AzUserAssignedIdentity`. Parametern `ResourceGroupName` anger den resurs grupp där den tilldelade hanterade identiteten ska skapas och parametern `-Name` anger dess namn. Ersätt `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parameter värden med dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista användare-tilldelade hanterade identiteter

Om du vill visa en lista över/läsa en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitet](/azure/role-based-access-control/built-in-roles#managed-identity-operator) eller [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Använd kommandot [Get-AzUserAssigned] för att visa en lista över användare som tilldelats hanterade identiteter.  Parametern `-ResourceGroupName` anger resurs gruppen där den användare som tilldelats den hanterade identiteten skapades. Ersätt `<RESOURCE GROUP>` med ditt eget värde:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
I svaret har användare tilldelade hanterade identiteter `"Microsoft.ManagedIdentity/userAssignedIdentities"` värde som returneras för nyckel `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en användare som tilldelats en hanterad identitet

För att ta bort en användardefinierad hanterad identitet måste ditt konto ha roll tilldelningen [hanterad identitets deltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Om du vill ta bort en användardefinierad hanterad identitet använder du kommandot `Remove-AzUserAssignedIdentity`.  Parametern `-ResourceGroupName` anger resurs gruppen där den användare tilldelade identiteten skapades och parametern `-Name` anger dess namn. Ersätt `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parameter värden med dina egna värden:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Om du tar bort en tilldelad hanterad identitet tas inte referensen bort från alla resurser som den har tilldelats. Identitets tilldelningar måste tas bort separat.

## <a name="next-steps"></a>Nästa steg

En fullständig lista och mer information om Azure PowerShell hanterade identiteter för Azure-resurser finns i [AZ. ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).

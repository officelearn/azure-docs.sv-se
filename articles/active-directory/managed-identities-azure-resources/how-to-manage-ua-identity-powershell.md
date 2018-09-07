---
title: Hur du skapar, lista och ta bort en Användartilldelad hanterad identitet med hjälp av Azure PowerShell
description: Steg för steg-anvisningar om hur du skapar, lista och ta bort Användartilldelad hanterad identitet med hjälp av Azure PowerShell.
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: b8cd0de938676204bc6fdc7785c49442d6dc8663
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028507"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Skapa, visa eller ta bort en Användartilldelad hanterad identitet med hjälp av Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du skapar, lista och ta bort en Användartilldelad hanterad identitet med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) om du inte redan har gjort.
- Om du väljer att installera och använda PowerShell lokalt måste du ha version 5.7.0 eller senare av Azure PowerShell-modulen. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). 
- Om du kör PowerShell lokalt behöver du även göra följande: 
    - Kör `Login-AzureRmAccount` för att skapa en anslutning med Azure.
    - Installera [den senaste versionen av PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Kör `Install-Module -Name PowerShellGet -AllowPrerelease` för att hämta förhandsversionen av `PowerShellGet`-modulen (du kan behöva `Exit` ur den aktuella PowerShell-sessionen när du har kört det här kommandot för att installera `AzureRM.ManagedServiceIdentity`-modulen).
    - Kör `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` du installerar förhandsversionen av den `AzureRM.ManagedServiceIdentity` modulen för att utföra den användartilldelade hanterad identitet åtgärder i den här artikeln.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rollen kan skapa, läsa (lista), uppdatera och ta bort en hanterad Användartilldelad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att läsa (lista) egenskaperna för en hanterad Användartilldelad identitet.

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en hanterad Användartilldelad identitet

Du kan skapa en hanterad Användartilldelad identitet med den [New AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) kommando. Den `ResourceGroupName` parametern anger resursgruppens namn var du vill skapa en hanterad identitet användartilldelade och `-Name` parametern anger dess namn. Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärden med dina egna värden:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista användartilldelade hanterade identiteter

Använd om du vill visa användartilldelade hanterade identiteter på [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) kommando.  Den `-ResourceGroupName` parametern anger resursgruppen där den hanterade Användartilldelad identitet har skapats. Ersätt den `<RESOURCE GROUP>` med ditt eget värde:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
I svaret, användartilldelade hanterade identiteter har `"Microsoft.ManagedIdentity/userAssignedIdentities"` värdet som returneras för nyckel `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Ta bort en hanterad Användartilldelad identitet

Ta bort en hanterad Användartilldelad identitet genom att använda den [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) kommando.  Den `-ResourceGroupName` parametern anger resursgruppen där Användartilldelad identitet har skapats och `-Name` parametern anger dess namn. Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärdena med dina egna värden:

 ```azurepowershell-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Tar bort en hanterad Användartilldelad identitet kommer inte att ta bort referensen, från alla resurser som tilldelades till. Identitet tilldelningar måste tas bort separat.

## <a name="next-steps"></a>Nästa steg

En fullständig lista och mer av Azure PowerShell hanterade identiteter för Azure-resurser kommandon finns i [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).

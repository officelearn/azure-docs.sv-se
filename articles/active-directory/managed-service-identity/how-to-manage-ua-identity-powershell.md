---
title: Hur du skapar, lista och ta bort en Användartilldelad (MSI) med Azure PowerShell
description: Steg för steg tilldelas instruktioner om hur du skapar, lista och ta bort användaren hanterad tjänstidentitet med hjälp av Azure PowerShell.
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
ms.openlocfilehash: 6fb376cb5924fc283fa405f1a03643d79bac44b0
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159023"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Skapa, visa eller ta bort en Användartilldelad identitet med hjälp av Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig skapa, visa och ta bort en Användartilldelad identitet med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) om du inte redan har gjort.
- Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-Modulversion 5.7.0-installationsprogram eller senare. Kör ` Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

Du kan skapa en Användartilldelad identitet med den [New AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) kommando. Den `ResourceGroupName` parametern anger resursgruppens namn var du vill skapa en Användartilldelad identitet, och `-Name` parametern anger dess namn. Minst ditt konto måste tilldelas den [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad identitet. Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärden med dina egna värden:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Lista användartilldelade identiteter

Använd om du vill visa användartilldelade identiteter i [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) kommando.  Den `-ResourceGroupName` parametern anger resursgruppen där Användartilldelad identitet har skapats. Minst ditt konto måste tilldelas den [hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att visa egenskaperna för en Användartilldelad identitet. Ersätt den `<RESOURCE GROUP>` med ditt eget värde:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
I svaret, användaridentiteter har `"Microsoft.ManagedIdentity/userAssignedIdentities"` värdet som returneras för nyckel `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Ta bort Användartilldelad identitet

Om du vill ta bort en användaridentitet, använda den [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) kommando.  Den `-ResourceGroupName` parametern anger resursgruppen där Användartilldelad identitet har skapats och `-Name` parametern anger dess namn. Minst ditt konto måste tilldelas den [hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) att kunna ta bort en Användartilldelad identitet. Ersätt den `<RESOURCE GROUP>` och `<USER ASSIGNED IDENTITY NAME>` parametervärdena med dina egna värden:

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Tar bort en Användartilldelad identitet kommer inte att ta bort referensen, från alla resurser som tilldelades till. Identitet tilldelningar måste tas bort separat.

## <a name="related-content"></a>Relaterat innehåll

En fullständig lista och mer information om MSI för Azure PowerShell-kommandon finns i [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).


 

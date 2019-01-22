---
title: Så här tilldelar du en hanterad identitet åtkomst till en Azure-resurs med hjälp av PowerShell
description: Steg för steg åtkomst instruktioner för att tilldela en hanterad identitet på en enda resurs, till en annan resurs, med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: daveba
ms.openlocfilehash: 72e05af92e88dc04f470d8be9a65347672777556
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427669"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Tilldela en hanterad identitet åtkomst till en resurs med hjälp av PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet, kan du ge hanterad identitet-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du får en Azure virtuell dator hanterad identitet åtkomst till ett Azure storage-konto med hjälp av PowerShell.

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) om du inte redan har gjort.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använd RBAC för att tilldela en hanterad identitet åtkomst till en annan resurs

När du har aktiverat hanterad identitet på en Azure-resurs [, till exempel en Azure VM](qs-configure-powershell-windows-vm.md):

1. Logga in på Azure med hjälp av den `Connect-AzureRmAccount` cmdlet. Använd ett konto som är associerade med Azure-prenumerationen som du har konfigurerat den hanterade identitet:

   ```powershell
   Connect-AzureRmAccount
   ```
2. I det här exemplet ger vi åtkomst till en Azure virtuell dator till ett lagringskonto. Först använder vi [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) att hämta tjänstens huvudnamn för den virtuella datorn med namnet `myVM`, som skapades när vi aktiverat hanterad identitet. Använd sedan [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) att ge den virtuella datorn **läsare** åtkomst till ett lagringskonto med namnet `myStorageAcct`:

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Nästa steg

- [Hanterad identitet för översikt över Azure-resurser](overview.md)
- För att aktivera hanterad identitet på en Azure VM, se [konfigurera hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av PowerShell](qs-configure-powershell-windows-vm.md).

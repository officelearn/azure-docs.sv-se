---
title: Tilldela en hanterad identitets åtkomst till en resurs med hjälp av PowerShell – Azure AD
description: Steg för steg-instruktioner för att tilldela en hanterad identitet på en resurs, åtkomst till en annan resurs med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2283ac076ef761fd098d75e7120e6557a959574
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547251"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Tilldela en hanterad identitets åtkomst till en resurs med hjälp av PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet kan du ge den hanterade identiteten åtkomst till en annan resurs, precis som alla säkerhets objekt. Det här exemplet visar hur du ger åtkomst till ett Azure Storage-konto med hjälp av PowerShell i en virtuell Azure-dators hanterade identitet.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [avsnittet Översikt](overview.md). **Se till att granska [skillnaden mellan en tilldelad och användardefinierad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)** .
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan gjort det.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använd RBAC för att tilldela en hanterad identitets åtkomst till en annan resurs

När du har aktiverat hanterad identitet på en Azure-resurs, [till exempel en virtuell Azure-dator](qs-configure-powershell-windows-vm.md):

1. Logga in på Azure med hjälp av `Connect-AzAccount`-cmdleten. Använd ett konto som är associerat med den Azure-prenumeration under vilken du har konfigurerat den hanterade identiteten:

   ```powershell
   Connect-AzAccount
   ```
2. I det här exemplet ger vi en Azure VM-åtkomst till ett lagrings konto. Först använder vi [Get-AzVM](/powershell/module/az.compute/get-azvm) för att hämta tjänstens huvud namn för den virtuella datorn med namnet `myVM`, som skapades när vi aktiverade hanterad identitet. Använd sedan [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) för att ge VM- **läsaren** åtkomst till ett lagrings konto med namnet `myStorageAcct`:

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identiteter för Azure-resurser](overview.md)
- Om du vill aktivera hanterad identitet på en virtuell Azure-dator, se [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med PowerShell](qs-configure-powershell-windows-vm.md).

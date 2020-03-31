---
title: Tilldela en hanterad identitetsåtkomst till en resurs med PowerShell - Azure AD
description: Steg för steg instruktioner för att tilldela en hanterad identitet på en resurs, tillgång till en annan resurs, med hjälp av PowerShell.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547251"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Tilldela en hanterad identitetsåtkomst till en resurs med PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en hanterad identitet kan du ge den hanterade identitetsåtkomsten till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du ger en Azure-virtuell dators hanterade identitetsåtkomst till ett Azure-lagringskonto med PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Installera [den senaste versionen av Azure PowerShell](/powershell/azure/install-az-ps) om du inte redan har gjort det.

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Använda RBAC för att tilldela en hanterad identitetsåtkomst till en annan resurs

När du har aktiverat hanterad identitet på en [Azure-resurs, till exempel en Virtuell Azure-dator:](qs-configure-powershell-windows-vm.md)

1. Logga in på `Connect-AzAccount` Azure med cmdlet. Använd ett konto som är associerat med Azure-prenumerationen som du har konfigurerat den hanterade identiteten under:

   ```powershell
   Connect-AzAccount
   ```
2. I det här exemplet ger vi en Azure VM-åtkomst till ett lagringskonto. Först använder vi [Get-AzVM](/powershell/module/az.compute/get-azvm) för att hämta `myVM`tjänstens huvudnamn för den virtuella datorn som heter , som skapades när vi aktiverade hanterad identitet. Använd sedan [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) för att ge **VM-läsaren** åtkomst till ett lagringskonto som heter: `myStorageAcct`

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Nästa steg

- [Översikt över hanterade identitet för Azure-resurser](overview.md)
- Information om hur du aktiverar hanterad identitet på en virtuell Azure-dator finns i [Konfigurera hanterade identiteter för Azure-resurser på en Azure VM med PowerShell](qs-configure-powershell-windows-vm.md).

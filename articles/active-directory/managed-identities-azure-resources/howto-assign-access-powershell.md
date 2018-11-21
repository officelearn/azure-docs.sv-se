---
title: Tilldela MSI-åtkomst till en Azure-resurs, med hjälp av PowerShell
description: Steg för steg åtkomst instruktioner för att tilldela en MSI på en enda resurs, till en annan resurs, med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 058578093a91d38f4eed8827888f79b5cc33ce87
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "47107091"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>Tilldela en hanterad tjänstidentitet (MSI)-åtkomst till en resurs med hjälp av PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

När du har konfigurerat en Azure-resurs med en MSI, kan du ge MSI-åtkomst till en annan resurs, precis som alla säkerhetsobjekt. Det här exemplet visar hur du får en Azure virtuell dator MSI åtkomst till ett Azure storage-konto med hjälp av PowerShell.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Dessutom installera [Azure PowerShell-version 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1) om du inte redan har gjort.

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Använd RBAC för att tilldela MSI-åtkomst till en annan resurs

När du har aktiverat MSI på en Azure-resurs [, till exempel en Azure VM](qs-configure-powershell-windows-vm.md):

1. Logga in på Azure med hjälp av den `Connect-AzureRmAccount` cmdlet. Använd ett konto som är associerade med Azure-prenumerationen som du har konfigurerat MSI:

   ```powershell
   Connect-AzureRmAccount
   ```
2. I det här exemplet ger vi åtkomst till en Azure virtuell dator till ett lagringskonto. Först använder vi [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) att hämta tjänstens huvudnamn för den virtuella datorn med namnet ”myVM” som skapades när vi har aktiverat MSI. Vi använder sedan [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) om du vill ge den virtuella datorn ”läsare” åtkomst till ett lagringskonto med namnet ”myStorageAcct”:

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>Felsökning

Om MSI för resursen inte visas i listan över tillgängliga identiteter, kontrollerar du att MSI har aktiverats. I det här fallet vi gå tillbaka till den virtuella Azure-datorn i den [Azure-portalen](https://portal.azure.com) och:

- Titta på sidan ”Configuration” och se till att MSI aktiverat = ”Yes”.
- Titta på sidan ”tillägg” och se till att MSI-tillägget har distribuerats.

Om något är fel, kan du behöva distribuera om MSI på resursen igen eller felsöka distributionsfel.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI, se [hanterad tjänstidentitet översikt](overview.md).
- För att aktivera MSI på en Azure VM, se [konfigurera en Azure VM hanterad tjänstidentitet (MSI) med hjälp av PowerShell](qs-configure-powershell-windows-vm.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.


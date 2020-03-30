---
title: Använda PowerShell för att skapa en SAS för användardelegering för en behållare eller blob
titleSuffix: Azure Storage
description: Lär dig hur du skapar en SAS för användardelegation med Azure Active Directory-autentiseringsuppgifter med hjälp av PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536181"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Skapa en SAS för användardelegering för en behållare eller blob med PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD) autentiseringsuppgifter för att skapa en användaredelegering SAS för en behållare eller blob med Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Installera PowerShell-modulen

Om du vill skapa en SAS-användardelegation med PowerShell installerar du version 1.10.0 eller senare av modulen Az.Storage. Så här installerar du den senaste versionen av modulen:

1. Avinstallera alla tidigare installationer av Azure PowerShell:

    - Ta bort alla tidigare installationer av Azure PowerShell från Windows med inställningen **För appar & funktioner** under **Inställningar**.
    - Ta bort alla `%Program Files%\WindowsPowerShell\Modules` **Azure-moduler** från .

1. Kontrollera att du har den senaste versionen av PowerShellGet installerad. Öppna ett Windows PowerShell-fönster och kör följande kommando för att installera den senaste versionen:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Stäng och öppna PowerShell-fönstret igen när du har installerat PowerShellGet.

1. Installera den senaste versionen av Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Kontrollera att du har installerat Azure PowerShell version 3.2.0 eller senare. Kör följande kommando för att installera den senaste versionen av Azure Storage PowerShell-modulen:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Stäng och öppna PowerShell-fönstret igen.

Om du vill kontrollera vilken version av Az.Storage-modulen som är installerad kör du följande kommando:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Mer information om hur du installerar Azure PowerShell finns i [Installera Azure PowerShell med PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Logga in på Azure PowerShell med Azure AD

Anropa kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att logga in med ditt Azure AD-konto:

```powershell
Connect-AzAccount
```

Mer information om hur du loggar in med PowerShell finns [i Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-rbac"></a>Tilldela behörigheter med RBAC

Om du vill skapa en SAS för användardelegering från Azure PowerShell måste Azure AD-kontot som används för att logga in på PowerShell tilldelas en roll som innehåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Med den här behörigheten kan Azure AD-kontot begära *användardelegeringsnyckeln*. Användardelegeringsnyckeln används för att signera SAS för användardelegationen. Rollen som tillhandahåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** måste tilldelas på lagringskontonivå, resursgruppen eller prenumerationen. Mer information om RBAC-behörigheter för att skapa en SAS för användardelegering finns i avsnittet **Tilldela behörigheter med RBAC** i [Skapa en SAS för användardelegering](/rest/api/storageservices/create-user-delegation-sas).

Om du inte har tillräcklig behörighet för att tilldela RBAC-roller till ett Azure AD-säkerhetsobjekt kan du behöva be kontoägaren eller administratören att tilldela nödvändiga behörigheter.

I följande exempel tilldelas rollen **Storage Blob Data Contributor,** som innehåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Rollen är begränsad på lagringskontots nivå.

Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Mer information om de inbyggda rollerna som innehåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** finns [i Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Använda Azure AD-autentiseringsuppgifter för att skydda en SAS

När du skapar en SAS för användardelegering med Azure PowerShell skapas användardelegeringsnyckeln som används för att signera SAS implicit. Starttid och utgångstid som du anger för SAS används också som starttid och förfallotid för användardelegeringsnyckeln. 

Eftersom det maximala intervallet som användardelegeringsnyckeln är giltig över är 7 dagar från startdatumet, bör du ange en utgångstid för SAS som är inom 7 dagar från starttiden. SAS är ogiltigt när användardelegeringsnyckeln har upphört att gälla, så en SAS med en förfallotid på mer än 7 dagar är fortfarande bara giltig i 7 dagar.

Om du vill skapa en SAS för användardelegering för en behållare eller blob med Azure PowerShell skapar du först ett nytt Azure Storage-kontextobjekt som anger parametern. `-UseConnectedAccount` Parametern `-UseConnectedAccount` anger att kommandot skapar kontextobjektet under Azure AD-kontot som du loggade in med.

Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Skapa en SAS för användardelegering för en behållare

Om du vill returnera en SAS-token för användardelegering för en behållare anropar du kommandot [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) och skickar i azure storage-kontextobjektet som du skapade tidigare.

I följande exempel returneras en SAS-token för användardelegering för en behållare. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Den SAS-token för användardelegation som returneras påminner om:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Skapa en SAS för användardelegering för en blob

Om du vill returnera en SAS-token för en användardelegering för en blob anropar du kommandot [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) och skickar i azure storage-kontextobjektet som du skapade tidigare.

Följande syntax returnerar en SAS för användardelegering för en blob. Exemplet anger parametern, `-FullUri` som returnerar blob URI med SAS-token bifogad. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Den användardelegation SAS URI som returneras liknar:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> En SAS för användardelegering stöder inte att definiera behörigheter med en lagrad åtkomstprincip.

## <a name="revoke-a-user-delegation-sas"></a>Återkalla en SAS för användardelegering

Om du vill återkalla en SAS-användaredelegering från Azure PowerShell anropar du kommandot **Revoke-AzStorageAccountUserDelegationKeys.** Det här kommandot återkallar alla användardelegeringsnycklar som är associerade med det angivna lagringskontot. Alla signaturer för delad åtkomst som är associerade med dessa nycklar är ogiltiga.

Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Både användardelegeringsnyckeln och RBAC-rolltilldelningarna cachelagras av Azure Storage, så det kan finnas en fördröjning mellan när du initierar återkallningsprocessen och när en befintlig användaredelegering SAS blir ogiltig.

## <a name="next-steps"></a>Nästa steg

- [Skapa en SAS -för användardelegation (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Hämta åtgärden Nyckel för användardelegering](/rest/api/storageservices/get-user-delegation-key)

---
title: Använd PowerShell för att skapa en användar Delegerings-SAS för en behållare eller BLOB
titleSuffix: Azure Storage
description: Lär dig hur du skapar en användar Delegerings-SAS (för hands version) med Azure Active Directory autentiseringsuppgifter med hjälp av PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5f4947921a77f2bc94d1810c9b1d1951431d3d71
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892523"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell-preview"></a>Skapa en användar Delegerings-SAS för en behållare eller BLOB med PowerShell (för hands version)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD)-autentiseringsuppgifter för att skapa en användar Delegerings-SAS för en behållare eller BLOB med Azure PowerShell (för hands version).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-preview-module"></a>Installera Preview-modulen

Om du vill använda PowerShell för att skapa en användar Delegerings-SAS måste du först installera modulen AZ. Storage 1.3.1-Preview. Följ de här stegen för att installera modulen:

1. Avinstallera tidigare installationer av Azure PowerShell:

    - Ta bort alla tidigare installationer av Azure PowerShell från Windows med hjälp av inställningen **appar & funktioner** under **Inställningar**.
    - Ta bort alla **Azure** -moduler från `%Program Files%\WindowsPowerShell\Modules`.

1. Kontrol lera att du har den senaste versionen av PowerShellGet installerad. Öppna ett Windows PowerShell-fönster och kör följande kommando för att installera den senaste versionen:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Stäng PowerShell-fönstret och öppna det igen när du har installerat PowerShellGet.

1. Installera den senaste versionen av Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Installera en Azure Storage Preview-modul som stöder användar Delegerings-SAS:

    ```powershell
    Install-Module Az.Storage `
        –Repository PSGallery `
        -RequiredVersion 1.3.1-preview `
        –AllowPrerelease `
        –AllowClobber `
        –Force
    ```

1. Stäng PowerShell-fönstret och öppna det igen.

Eftersom PowerShell läser in den senaste AZ. Storage-modulen som standard, kan du behöva läsa in modulen 1.3.1-Preview explicit när du startar-konsolen. Om du vill läsa in modulen för förhands granskning kör du kommandot [import-module](/powershell/module/microsoft.powershell.core/import-module) :

```powershell
Import-Module Az.Storage -RequiredVersion 1.3.1
```

Mer information om hur du installerar Azure PowerShell finns i [installera Azure PowerShell med PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Logga in på Azure PowerShell med Azure AD

Anropa kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att logga in med ditt Azure AD-konto:

```powershell
Connect-AzAccount
```

Mer information om hur du loggar in med PowerShell finns i [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-rbac"></a>Tilldela behörigheter med RBAC

Om du vill skapa en användar Delegerings-SAS från Azure PowerShell måste det Azure AD-konto som används för att logga in på PowerShell tilldelas en roll som innehåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Med den här behörigheten kan Azure AD-kontot begära *användar Delegerings nyckeln*. Användar Delegerings nyckeln används för att signera användar Delegerings-SAS. Rollen som tillhandahåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** måste tilldelas på lagrings kontots nivå, resurs gruppen eller prenumerationen. Mer information om RBAC-behörigheter för att skapa en användar Delegerings-SAS finns i avsnittet **tilldela behörigheter med RBAC** i [skapa en användar Delegerings-SAS](/rest/api/storageservices/create-user-delegation-sas).

Om du inte har tillräcklig behörighet för att tilldela RBAC-roller till ett säkerhets objekt i Azure AD kan du behöva be kontots ägare eller administratör att tilldela de nödvändiga behörigheterna.

I följande exempel tilldelas rollen **Storage BLOB data Contributor** , som innehåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Rollen är begränsad på lagrings kontots nivå.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Mer information om de inbyggda roller som innehåller åtgärden **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** finns i [inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Använd Azure AD-autentiseringsuppgifter för att skydda en SAS

När du skapar en användar Delegerings-SAS med Azure PowerShell skapas den användar Delegerings nyckel som används för att signera SAS för implicit. Start tiden och den förfallo tid som du anger för SAS används också som start tid och förfallo tid för användar Delegerings nyckeln. 

Eftersom det maximala intervallet med vilken användar Delegerings nyckeln är giltigt är 7 dagar från start datumet, bör du ange en förfallo tid för SAS som ligger inom 7 dagar från start tiden. SAS är ogiltig när användar Delegerings nyckeln upphör att gälla, så en SAS med en förfallo tid på mer än 7 dagar är fortfarande bara giltig i 7 dagar.

Om du vill skapa en användar Delegerings-SAS för en behållare eller BLOB med Azure PowerShell måste du först skapa ett nytt Azure Storage-kontext objekt och ange `-UseConnectedAccount`-parametern. Parametern `-UseConnectedAccount` anger att objektet skapar objektet context under det Azure AD-konto som du loggade in med.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Skapa en användar Delegerings-SAS för en behållare

Om du vill returnera en användar delegering SAS-token för en behållare anropar du kommandot [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) och skickar det Azure Storage-kontext objekt som du skapade tidigare.

I följande exempel returneras en användar Delegerings-SAS-token för en behållare. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Den användardefinierade SAS-token som returnerades ser ut ungefär så här:

```
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Skapa en användar Delegerings-SAS för en BLOB

Om du vill returnera en användar delegering SAS-token för en BLOB anropar du kommandot [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) och skickar det Azure Storage-kontext objekt som du skapade tidigare.

Följande syntax returnerar en användar Delegerings-SAS för en blob. I exemplet anges `-FullUri` parameter, som returnerar BLOB-URI: n med SAS-token tillagda. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Den användardefinierade SAS-URI: n som returnerades ser ut ungefär så här:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> En användar Delegerings-SAS har inte stöd för att definiera behörigheter med en lagrad åtkomst princip.

## <a name="revoke-a-user-delegation-sas"></a>Återkalla en användar Delegerings-SAS

Om du vill återkalla en användar Delegerings-SAS från Azure PowerShell anropar du kommandot **REVOKE-AzStorageAccountUserDelegationKeys** . Det här kommandot återkallar alla de användar Delegerings nycklar som är kopplade till det angivna lagrings kontot. Alla signaturer för delad åtkomst som är kopplade till nycklarna är ogiltiga.

Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Både användar Delegerings nyckeln och RBAC-roll tilldelningar cachelagras av Azure Storage, så det kan uppstå en fördröjning mellan när du initierar återkallnings processen och när en befintlig användar Delegerings-SÄKERHETSASSOCIATIONER blir ogiltig.

## <a name="next-steps"></a>Nästa steg

- [Skapa en användar Delegerings-SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Hämta åtgärd för användar Delegerings nyckel](/rest/api/storageservices/get-user-delegation-key)

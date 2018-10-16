---
title: Kör Azure CLI eller PowerShell-kommandon under en Azure AD-identitet till Azure Storage (förhandsversion) | Microsoft Docs
description: Azure CLI och PowerShell stöder loggat in med en Azure AD-identitet köra kommandon i Azure Storage-behållare och köer och deras data. En åtkomsttoken för sessionen och används för att auktorisera anropande åtgärder. Behörigheter beror på vilken roll som tilldelats Azure AD-identitet.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/20/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 42422580acec0811b0eb65d6fe1c0bed791eb9bd
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344548"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Använda en Azure AD-identitet för åtkomst till Azure Storage med CLI eller PowerShell (förhandsversion)

Azure Storage tillhandahåller förhandsversion tillägg för Azure CLI och PowerShell som gör det möjligt att logga in och kör skriptkommandon under en Azure Active Directory (Azure AD)-identitet. Azure AD-identitet kan vara en användare, grupp eller tjänstens huvudnamn för programmet eller det kan vara en [hanterad identitet för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Du kan tilldela behörigheter för att få åtkomst till lagringsresurser till Azure AD-identitet via rollbaserad åtkomstkontroll (RBAC). Mer information om RBAC-roller i Azure Storage finns i [hantera åtkomsträttigheter till Azure Storage-data med RBAC (förhandsversion)](storage-auth-aad-rbac.md).

När du loggar in till Azure CLI eller PowerShell med Azure AD-identitet, returneras en åtkomsttoken för att komma åt Azure Storage under den identiteten. Den token används sedan automatiskt av CLI eller PowerShell för att auktorisera åtgärder mot Azure Storage. För åtgärder som stöds behöver du inte längre att skicka en nyckel eller en SAS-token med kommandot.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="supported-operations"></a>Åtgärder som stöds

Tillägg för förhandsversionen har stöd för åtgärder på behållare och köer. Vilka åtgärder som du kan anropa beror på behörigheter till Azure AD-identitet som du loggar in på Azure CLI eller PowerShell. Behörigheter till Azure Storage-behållare eller de köer som har tilldelats via rollbaserad åtkomstkontroll (RBAC). Till exempel om en dataläsare roll tilldelas till identitet som kan du köra skriptkommandon som läser data från en behållare eller en kö. Om en Data deltagarbehörighet roll tilldelas till identiteten, kan du köra skriptkommandon som läsa, skriva eller ta bort en behållare eller kön eller den data de innehåller. 

Mer information om de behörigheter som krävs för varje Azure Storage-åtgärd på en behållare eller kön finns [behörigheter för att anropa REST-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Anropa CLI-kommandon med en Azure AD-identitet

Installera tillägget förhandsversion för Azure CLI:

1. Kontrollera att du har installerat Azure CLI version 2.0.32 eller senare. Kör `az --version` att kontrollera den installerade versionen.
2. Kör följande kommando för att installera tillägget förhandsversion: 

    ```azurecli
    az extension add -n storage-preview
    ```

Tillägget förhandsversion lägger till en ny `--auth-mode` parameter kommandon som stöds:

- Ange den `--auth-mode` parameter `login` att logga in med en Azure AD-identitet.
- Ange den `--auth-mode` parametern till äldre `key` värde att fråga efter en konto-nyckel om inga autentiseringsparametrar för kontot har angetts. 

Till exempel för att ladda ned en blob i Azure CLI med hjälp av en Azure AD-identitet, kör först `az login`, sedan anropa kommandot med `--auth-mode` inställd `login`:

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

Miljövariabeln som är associerade med den `--auth-mode` parametern är `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Anropa PowerShell-kommandon med en Azure AD-identitet

Azure PowerShell har stöd för inloggning med en Azure AD-identitet med en av de följande förhandsversionsmoduler endast: 

- 4.4.0-Preview 
- 4.4.1-Preview 

Använda Azure PowerShell för att logga in med en Azure AD-identitet:

1. Avinstallera alla tidigare installationer av Azure PowerShell:

    - Ta bort alla tidigare installationer av Azure PowerShell från Windows med hjälp av den **appar och funktioner** inställningen **inställningar**.
    - Ta bort alla **Azure*** moduler från `%Program Files%\WindowsPowerShell\Modules`.

1. Se till att du har den senaste versionen av installerat PowerShellGet. Öppna ett Windows PowerShell-fönster och kör följande kommando för att installera den senaste versionen:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. Stäng och öppna PowerShell-fönstret när du har installerat PowerShellGet. 

1. Installera den senaste versionen av Azure PowerShell:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

1. Installera en förhandsversion av Azure Storage-moduler som har stöd för Azure AD:

    ```powershell
    Install-Module Azure.Storage –Repository PSGallery -RequiredVersion 4.4.1-preview  –AllowPrerelease –AllowClobber –Force 
    ```
1. Stäng och öppna PowerShell-fönstret.
1. Anropa den [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) cmdlet för att skapa en kontext och inkludera den `-UseConnectedAccount` parametern. 
1. Skicka nya kontexten till cmdlet för att anropa en cmdlet med ett Azure AD-identitet.

I följande exempel visar hur du listar blobarna i en behållare från Azure PowerShell med hjälp av en Azure AD-identitet. Tänk på att ersätta platshållaren konto och en behållare namnen med dina egna värden: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName storagesamples -UseConnectedAccount 
Get-AzureStorageBlob -Container sample-container -Context $ctx 
```

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för Azure storage finns [hantera åtkomsträttigheter till storage-data med RBAC (förhandsversion)](storage-auth-aad-rbac.md).
- Läs om hur du använder hanterade identiteter för Azure-resurser med Azure Storage i [autentisera åtkomst till blobbar och köer med Azure hanterade identiteter för Azure-resurser (förhandsgranskning)](storage-auth-aad-msi.md).
- Läs hur du tillåter åtkomst till behållare och köer från i ditt storage-program i [använda Azure AD med lagring program](storage-auth-aad-app.md).
- Ytterligare information om Azure AD-integrering för Azure-Blobbar och köer finns i Azure Storage-teamets blogg publicerar, [meddelande om förhandsversionen av Azure AD-autentisering för Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).

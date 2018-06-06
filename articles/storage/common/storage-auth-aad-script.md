---
title: Kör Azure CLI eller PowerShell-kommandon under en Azure AD identity åtkomst till Azure-lagring (förhandsversion) | Microsoft Docs
description: Azure CLI och PowerShell stöder loggat in med en Azure AD-identitet för att köra kommandon på Azure Storage-behållare och köer och deras data. En åtkomst-token för sessionen och används för att auktorisera anropa åtgärder. Behörigheter beror på vilken roll som tilldelats Azure AD-identitet.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.openlocfilehash: 27397d490902625d10e54ea17a9e534fbfbca8cd
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757510"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Använda en Azure AD-identitet för åtkomst till Azure Storage med CLI eller PowerShell (förhandsgranskning)

Azure Storage tillhandahåller preview tillägg för Azure CLI och PowerShell att logga in och kör skriptkommandon under en identitet för Azure Active Directory (AD Azure). Azure AD identity kan vara en användare, grupp eller tjänstens huvudnamn för programmet, eller det kan vara en [hanterade tjänstidentiteten](../../active-directory/managed-service-identity/overview.md). Du kan tilldela behörigheter för att få åtkomst till lagringsresurser till Azure AD identity via rollbaserad åtkomstkontroll (RBAC). Mer information om RBAC-roller i Azure Storage finns [hantera behörigheter till Azure Storage-data med RBAC (förhandsgranskning)](storage-auth-aad-rbac.md).

När du loggar in på Azure CLI eller PowerShell med en Azure AD-identitet returneras en åtkomst-token för åtkomst till Azure Storage under identitet. Säkerhetstoken används sedan automatiskt av CLI eller PowerShell för att auktorisera åtgärder mot Azure Storage. För åtgärder som stöds behöver du inte längre att skicka en nyckel eller en SAS-token med kommandot.

> [!IMPORTANT]
> Den här förhandsgranskningen är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga förrän Azure AD-integrering för Azure Storage har deklarerats allmänt tillgänglig. Om Azure AD-integrering inte stöds ännu för ditt scenario, fortsätta att använda delad nyckel auktorisering eller SAS-token i dina program. Mer information om förhandsversionen finns [autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory (förhandsgranskning)](storage-auth-aad.md).
>
> Under förhandsgranskningen inkluderas kan RBAC rolltilldelningar ta upp till fem minuter att sprida.
>
> Azure AD-integrering med Azure Storage kräver att du använder HTTPS för Azure Storage-åtgärder.

## <a name="supported-operations"></a>De åtgärder som stöds

Förhandsgranska tillägg stöds för åtgärder i behållare och köer. Vilka åtgärder som du kan anropa beror på behörigheter till Azure AD-identitet som du loggar in på Azure CLI eller PowerShell. Behörigheter till Azure Storage-behållare eller de köer som har tilldelats via rollbaserad åtkomstkontroll (RBAC). Till exempel om en dataläsare roll har tilldelats identitet som kan du köra skriptkommandon som läser data från en behållare eller kön. Om en Data deltagarbehörighet roll har tilldelats identitet, kan du köra skriptkommandon som läsa, skriva eller ta bort en behållare eller kön eller data som de innehåller. 

Mer information om de behörigheter som krävs för varje Azure Storage-åtgärd på en behållare eller kön finns [behörigheter för att anropa REST-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Anropa CLI-kommandon med en Azure AD-identitet

Installera tillägget förhandsgranskning för Azure CLI:

1. Se till att du har installerat Azure CLI version 2.0.32 eller senare. Kör `az --version` att kontrollera den installerade versionen.
2. Kör följande kommando för att installera tillägget preview: 

    ```azurecli
    az extension add -n storage-preview
    ```

Förhandsgranska tillägget lägger till en ny `--auth-mode` parameter till kommandon som stöds:

- Ange den `--auth-mode` parameter till `login` att logga in med en Azure AD-identitet.
- Ange den `--auth-mode` parameter till äldre `key` värde och försök att fråga efter en nyckel om kontot inte autentiseringsparametrar för kontot har angetts. 

Till exempel för att hämta en blobb i Azure CLI med hjälp av en Azure AD-identitet, kör först `az login`, anropa kommandot med `--auth-mode` inställd på `login`:

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

Miljövariabeln som är associerade med den `--auth-mode` parametern är `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Anropa PowerShell-kommandon med en Azure AD-identitet

Använda Azure PowerShell för att logga in med en Azure AD-identitet:

1. Kontrollera att du har den senaste versionen av PowerShellGet installerad. Kör följande kommando för att installera senaste:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. Avinstallera alla tidigare installationer av Azure PowerShell.
3. Installera AzureRM:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. Installera modulen preview:

    ```powershell
    Install-Module-Name Azure.Storage-RequiredVersion 4.4.0-AllowPrerelease –AllowClobber -Repository PSGallery -Force 
    ```

5. Anropa den [ny AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) för att skapa en kontext och inkludera den `-UseConnectedAccount` parameter. 
6. För att anropa en cmdlet med en Azure AD-identitet klara kontexten för cmdleten.

I följande exempel visas hur du visa blobbar i en behållare från Azure PowerShell med hjälp av en Azure AD-identitet: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för Azure storage finns [hantera behörigheter till storage-data med RBAC (förhandsgranskning)](storage-auth-aad-rbac.md).
- Läs om hur du använder hanterade tjänstidentiteten med Azure Storage i [autentisera med Azure AD från en Azure hanterade tjänstidentiteten (förhandsgranskning)](storage-auth-aad-msi.md).
- Om du vill lära dig mer om att bevilja åtkomst till behållare och köer från i storage-program, se [använda Azure AD med lagring program](storage-auth-aad-app.md).
- Ytterligare information om Azure AD-integrering för Azure-Blobbar och köer finns i Azure Storage-teamets blogg inlägg, [om förhandsversionen av Azure AD-autentisering för Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).

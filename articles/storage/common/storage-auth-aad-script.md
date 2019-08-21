---
title: Kör Azure CLI-eller PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data | Microsoft Docs
description: Azure CLI och PowerShell stöder inloggning med Azure AD-autentiseringsuppgifter för att köra kommandon på Azure Storage blob-och köer-data. En åtkomsttoken har angetts för sessionen och används för att auktorisera anrops åtgärder. Behörigheter är beroende av den RBAC-roll som tilldelats Azure AD-säkerhetsobjektet.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 32b3f113658a20790e0f149739a882004f12fe21
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640813"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Kör Azure CLI-eller PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data

Azure Storage innehåller tillägg för Azure CLI och PowerShell som gör att du kan logga in och köra skript kommandon med Azure Active Directory (autentiseringsuppgifter för Azure AD). När du loggar in på Azure CLI eller PowerShell med autentiseringsuppgifter för Azure AD returneras en OAuth 2,0-åtkomsttoken. Denna token används automatiskt av CLI eller PowerShell för att auktorisera efterföljande data åtgärder mot BLOB-eller Queue-lagring. För åtgärder som stöds behöver du inte längre skicka en konto nyckel eller SAS-token med kommandot.

Du kan tilldela behörigheter till blob-och Queue-data till ett säkerhets objekt i Azure AD via rollbaserad åtkomst kontroll (RBAC). Mer information om RBAC-roller i Azure Storage finns i [Hantera åtkomst rättigheter för att Azure Storage data med RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Åtgärder som stöds

Tilläggen stöds för åtgärder på behållare och köer. Vilka åtgärder som kan anropas beror på vilka behörigheter som beviljats för det säkerhets objekt i Azure AD som du loggar in på Azure CLI eller PowerShell. Behörigheter för att Azure Storage behållare eller köer tilldelas via rollbaserad åtkomst kontroll (RBAC). Om du till exempel har tilldelats rollen **BLOB data Reader** kan du köra skript kommandon som läser data från en behållare eller kö. Om du har tilldelats rollen **BLOB data Contributor** kan du köra skript kommandon som läser, skriver eller tar bort en behållare eller kö eller de data som de innehåller. 

Mer information om de behörigheter som krävs för varje Azure Storage-åtgärd på en behållare eller kö finns i [anropa lagrings åtgärder med OAuth](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)-token.  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Anropa CLI-kommandon med Azure AD-autentiseringsuppgifter

Azure CLI stöder `--auth-mode` parametern för blob-och Queue data-åtgärder:

- Ange att `login` parametern ska logga in med ett säkerhets objekt i Azure AD. `--auth-mode`
- Ange parametern till det bakåtkompatibla `key` värdet för att försöka fråga efter en konto nyckel om inga autentiseringsmetoder har angetts för kontot. `--auth-mode` 

I följande exempel visas hur du skapar en behållare i ett nytt lagrings konto från Azure CLI med hjälp av dina autentiseringsuppgifter för Azure AD. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden: 

1. Kontrol lera att du har installerat Azure CLI-version 2.0.46 eller senare. Kör `az --version` för att kontrol lera den installerade versionen.

1. Kör `az login` och autentisera i webbläsarfönstret: 

    ```azurecli
    az login
    ```

1. Ange önskad prenumeration. Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Skapa ett lagrings konto i den resurs gruppen med [AZ lagrings konto Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. Innan du skapar behållaren ska du tilldela rollen [Storage BLOB data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) till dig själv. Även om du är kontots ägare behöver du explicita behörigheter för att utföra data åtgärder mot lagrings kontot. Mer information om hur du tilldelar RBAC-roller finns i [bevilja åtkomst till Azure blob och Queue data med RBAC i Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Det kan ta några minuter att sprida RBAC-roll tilldelningar.

1. Anropa kommandot [AZ Storage container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) med `--auth-mode` parametern inställd på `login` för att skapa behållaren med dina autentiseringsuppgifter för Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

Den miljö variabel som är associerad `--auth-mode` med parametern `AZURE_STORAGE_AUTH_MODE`är. Du kan ange lämpligt värde i miljövariabeln för att undvika att inkludera det vid varje anrop till en Azure Storage data åtgärd.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Anropa PowerShell-kommandon med Azure AD-autentiseringsuppgifter

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill använda Azure PowerShell för att logga in och köra efterföljande åtgärder mot Azure Storage med Azure AD-autentiseringsuppgifter skapar du en lagrings kontext för att referera till `-UseConnectedAccount` lagrings kontot och inkluderar parametern.

I följande exempel visas hur du skapar en behållare i ett nytt lagrings konto från Azure PowerShell med dina autentiseringsuppgifter för Azure AD. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

1. Logga in på ditt Azure-konto med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Mer information om hur du loggar in på Azure med PowerShell finns i [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Skapa en Azure-resurs grupp genom att anropa [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Skapa ett lagrings konto genom att anropa [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Hämta lagrings konto kontexten som anger det nya lagrings kontot genom att anropa [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). När du agerar på ett lagrings konto kan du referera till kontexten i stället för att upprepade gånger skicka in autentiseringsuppgifterna. `-UseConnectedAccount` Inkludera parametern för att anropa eventuella efterföljande data åtgärder med dina autentiseringsuppgifter för Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Innan du skapar behållaren ska du tilldela rollen [Storage BLOB data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) till dig själv. Även om du är kontots ägare behöver du explicita behörigheter för att utföra data åtgärder mot lagrings kontot. Mer information om hur du tilldelar RBAC-roller finns i [bevilja åtkomst till Azure blob och Queue data med RBAC i Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Det kan ta några minuter att sprida RBAC-roll tilldelningar.

1. Skapa en behållare genom att anropa [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Eftersom det här anropet använder kontexten som skapades i föregående steg, skapas behållaren med dina autentiseringsuppgifter för Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för Azure Storage finns i [Hantera åtkomst behörigheter till lagrings data med RBAC](storage-auth-aad-rbac.md).
- Information om hur du använder hanterade identiteter för Azure-resurser med Azure Storage finns i [autentisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md).
- Information om hur du auktoriserar åtkomst till behållare och köer i dina lagrings program finns i [använda Azure AD med lagrings program](storage-auth-aad-app.md).

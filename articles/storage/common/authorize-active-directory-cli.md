---
title: Kör Azure CLI-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data
titleSuffix: Azure Storage
description: Azure CLI-stöd för att logga in med Azure AD-autentiseringsuppgifter för att köra kommandon på Azure Storage blob-och köer-data. En åtkomsttoken har angetts för sessionen och används för att auktorisera anrops åtgärder. Behörigheter är beroende av den RBAC-roll som tilldelats Azure AD-säkerhetsobjektet.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfbb466b7679e841c9c0154dbe28de14574d4282
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553467"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Kör Azure CLI-kommandon med Azure AD-autentiseringsuppgifter för att få åtkomst till BLOB-eller Queue-data

Azure Storage innehåller tillägg för Azure CLI som gör att du kan logga in och köra skript kommandon med Azure Active Directory (autentiseringsuppgifter för Azure AD). När du loggar in på Azure CLI med Azure AD-autentiseringsuppgifter returneras en OAuth 2,0-åtkomsttoken. Token används automatiskt av Azure CLI för att auktorisera efterföljande data åtgärder mot BLOB-eller Queue-lagring. För åtgärder som stöds behöver du inte längre skicka en konto nyckel eller SAS-token med kommandot.

Du kan tilldela behörigheter till blob-och Queue-data till ett säkerhets objekt i Azure AD via rollbaserad åtkomst kontroll (RBAC). Mer information om RBAC-roller i Azure Storage finns i [Hantera åtkomst rättigheter för att Azure Storage data med RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Åtgärder som stöds

Azure Storage-tillägg stöds för åtgärder på blob-och Queue-data. Vilka åtgärder som kan anropas beror på vilka behörigheter som beviljats för det säkerhets objekt i Azure AD som du loggar in på Azure CLI. Behörigheter för att Azure Storage behållare eller köer tilldelas via RBAC. Om du till exempel har tilldelats rollen **BLOB data Reader** kan du köra skript kommandon som läser data från en behållare eller kö. Om du har tilldelats rollen **BLOB data Contributor** kan du köra skript kommandon som läser, skriver eller tar bort en behållare eller kö eller de data som de innehåller.

Mer information om de behörigheter som krävs för varje Azure Storage-åtgärd på en behållare eller kö finns i [anropa lagrings åtgärder med OAuth-token](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Anropa Azure CLI-kommandon med Azure AD-autentiseringsuppgifter

Azure CLI stöder `--auth-mode` parameter för blob-och Queue data-åtgärder:

- Ange `--auth-mode` parametern till `login` för att logga in med ett säkerhets objekt för Azure AD.
- Ange `--auth-mode` parametern till det äldre `key`-värdet för att försöka fråga efter en konto nyckel om inga autentiseringsinställningar för kontot har angetts.

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

1. Anropa kommandot [AZ Storage container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) med parametern `--auth-mode` som har angetts till `login` för att skapa behållaren med dina autentiseringsuppgifter för Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

Den miljö variabel som är associerad med `--auth-mode`-parametern är `AZURE_STORAGE_AUTH_MODE`. Du kan ange lämpligt värde i miljövariabeln för att undvika att inkludera det vid varje anrop till en Azure Storage data åtgärd.

## <a name="next-steps"></a>Nästa steg

- [Använd Azure CLI för att tilldela en RBAC-roll för åtkomst till blob-och Queue-data](storage-auth-aad-rbac-cli.md)
- [Ge åtkomst till blob-och Queue-data med hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md)

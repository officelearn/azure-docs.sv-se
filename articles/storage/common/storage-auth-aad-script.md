---
title: Kör Azure CLI eller PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att komma åt data blob eller kön | Microsoft Docs
description: Azure CLI och PowerShell stöd för logga in med autentiseringsuppgifter för Azure AD för att köra kommandon på Azure Storage blob- och köer. En åtkomsttoken för sessionen och används för att auktorisera anropande åtgärder. Behörigheter är beroende av RBAC-roll som tilldelats Azure AD-säkerhetsobjektet.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 96be1e600c8d5895cc0eb5b058ce17f7265fa0a9
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149656"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Kör Azure CLI eller PowerShell-kommandon med Azure AD-autentiseringsuppgifter på dataåtkomst för blob eller en kö

Azure Storage tillhandahåller tillägg för Azure CLI och PowerShell som gör det möjligt att logga in och kör skriptkommandon med autentiseringsuppgifter för Azure Active Directory (AD Azure). När du loggar in på Azure CLI eller PowerShell med Azure AD-autentiseringsuppgifter, returneras en OAuth 2.0-åtkomsttoken. Den token används automatiskt av CLI eller PowerShell för att auktorisera efterföljande åtgärder mot Blob eller Queue storage. För åtgärder som stöds behöver du inte längre att skicka en nyckel eller en SAS-token med kommandot.

Du kan tilldela behörigheter till blob och kö data till en Azure AD-säkerhetsobjekt via rollbaserad åtkomstkontroll (RBAC). Mer information om RBAC-roller i Azure Storage finns i [hantera åtkomsträttigheter till Azure Storage-data med RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Åtgärder som stöds

Tillägg som stöds för åtgärder på behållare och köer. Vilka åtgärder som du kan anropa beror på behörigheter till Azure AD-säkerhetsobjekt som du loggar in på Azure CLI eller PowerShell. Behörigheter till Azure Storage-behållare eller de köer som har tilldelats via rollbaserad åtkomstkontroll (RBAC). Exempel: Om du har tilldelats den **Blob Data-läsare** rollen och du kan köra skriptkommandon som läser data från en behållare eller en kö. Om du har tilldelats den **Blob Data-deltagare** rollen och du kan köra skriptkommandon som läsa, skriva eller ta bort en behållare eller kön eller den data de innehåller. 

Mer information om de behörigheter som krävs för varje Azure Storage-åtgärd på en behållare eller kön finns [behörigheter för att anropa REST-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Anropa CLI-kommandon med hjälp av Azure AD-autentiseringsuppgifter

Azure CLI har stöd för den `--auth-mode` parametern för åtgärder för blob och kö:

- Ange den `--auth-mode` parameter `login` att logga in med en Azure AD-säkerhetsobjekt.
- Ange den `--auth-mode` parametern till äldre `key` värde att fråga efter en konto-nyckel om inga autentiseringsparametrar för kontot har angetts. 

I följande exempel visar hur du skapar en behållare i ett nytt lagringskonto från Azure CLI med hjälp av Azure AD-autentiseringsuppgifter. Kom ihåg att ersätta platshållarvärdena i vinkelparenteser med dina egna värden: 

1. Kontrollera att du har installerat Azure CLI version 2.0.46 eller senare. Kör `az --version` att kontrollera den installerade versionen.

1. Kör `az login` och autentisera i webbläsarfönstret: 

    ```azurecli
    az login
    ```
    
1. Ange den önskade prenumerationen. Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Skapa ett lagringskonto i den resurs med [az storage-konto skapar](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

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
    
1. Innan du skapar behållaren, tilldela den [Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) rollen till dig själv. Även om du är ägare, måste du explicit behörighet att utföra åtgärder mot lagringskontot. Mer information om att tilldela RBAC-roller finns i [bevilja åtkomst till Azure blob och kö data med RBAC i Azure-portalen](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > RBAC-rolltilldelningar kan ta några minuter att sprida.
    
1. Anropa den [az storage container skapa](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) med den `--auth-mode` parameteruppsättning till `login` att skapa behållaren med hjälp av Azure AD-autentiseringsuppgifter:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

Miljövariabeln som är associerade med den `--auth-mode` parametern är `AZURE_STORAGE_AUTH_MODE`. Du kan ange lämpligt värde i miljövariabeln för att undvika att inkludera det vid varje anrop till en åtgärd för Azure Storage-data.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Anropa PowerShell-kommandon med hjälp av Azure AD-autentiseringsuppgifter

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Skapa en storage-kontext för att referera till storage-konto för att använda Azure PowerShell för att logga in och köra efterföljande åtgärder mot Azure Storage med Azure AD-autentiseringsuppgifter, och, inklusive den `-UseConnectedAccount` parametern.

I följande exempel visar hur du skapar en behållare i ett nytt lagringskonto från Azure PowerShell med hjälp av Azure AD-autentiseringsuppgifter. Kom ihåg att ersätta platshållarvärdena i vinkelparenteser med dina egna värden:

1. Logga in på Azure-prenumerationen med den `Connect-AzAccount` och följer den på skärmen anvisningar för att ange dina autentiseringsuppgifter för Azure AD: 

    ```powershell
    Connect-AzAccount
    ```
    
1. Skapa en Azure-resursgrupp genom att anropa [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Skapa ett lagringskonto genom att anropa [New AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Hämta lagringskontokontexten som anger det nya lagringskontot genom att anropa [New AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). När du arbetar med ett storage-konto, refererar du till kontexten i stället för att skicka flera gånger i autentiseringsuppgifterna. Inkludera den `-UseConnectedAccount` parametern för att anropa alla efterföljande åtgärder med hjälp av Azure AD-autentiseringsuppgifter:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Innan du skapar behållaren, tilldela den [Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) rollen till dig själv. Även om du är ägare, måste du explicit behörighet att utföra åtgärder mot lagringskontot. Mer information om att tilldela RBAC-roller finns i [bevilja åtkomst till Azure blob och kö data med RBAC i Azure-portalen](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > RBAC-rolltilldelningar kan ta några minuter att sprida.

1. Skapa en behållare genom att anropa [New AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Eftersom det här anropet använder kontexten som skapades i föregående steg, skapas behållaren med hjälp av Azure AD-autentiseringsuppgifter. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Nästa steg

- Mer information om RBAC-roller för Azure storage finns [hantera åtkomsträttigheter till storage-data med RBAC](storage-auth-aad-rbac.md).
- Läs om hur du använder hanterade identiteter för Azure-resurser med Azure Storage i [autentisera åtkomst till blobbar och köer med Azure Active Directory och hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md).
- Läs hur du tillåter åtkomst till behållare och köer från i ditt storage-program i [använda Azure AD med lagring program](storage-auth-aad-app.md).

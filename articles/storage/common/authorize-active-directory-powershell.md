---
title: Kör PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att komma åt blob- eller ködata
titleSuffix: Azure Storage
description: PowerShell stöder inloggning med Azure AD-autentiseringsuppgifter för att köra kommandon på Azure Storage-blob och ködata. En åtkomsttoken tillhandahålls för sessionen och används för att auktorisera anropande åtgärder. Behörigheter beror på rbac-rollen som tilldelats Azure AD-säkerhetsobjektet.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553454"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Kör PowerShell-kommandon med Azure AD-autentiseringsuppgifter för att komma åt blob- eller ködata

Azure Storage innehåller tillägg för PowerShell som gör att du kan logga in och köra skriptkommandon med Azure Active Directory (Azure AD) autentiseringsuppgifter. När du loggar in på PowerShell med Azure AD-autentiseringsuppgifter returneras en OAuth 2.0-åtkomsttoken. Den token används automatiskt av PowerShell för att auktorisera efterföljande dataåtgärder mot Blob- eller Queue storage. För åtgärder som stöds behöver du inte längre skicka en kontonyckel eller SAS-token med kommandot.

Du kan tilldela behörigheter till blob- och ködata till ett Azure AD-säkerhetsobjekt via rollbaserad åtkomstkontroll (RBAC). Mer information om RBAC-roller i Azure Storage finns i [Hantera åtkomsträttigheter till Azure Storage-data med RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Åtgärder som stöds

Azure Storage-tilläggen stöds för åtgärder på blob- och ködata. Vilka åtgärder du kan anropa beror på vilka behörigheter som beviljas Azure AD-säkerhetsobjektet som du loggar in på PowerShell. Behörigheter till Azure Storage-behållare eller köer tilldelas via RBAC. Om du till exempel har tilldelats rollen **Blob Data Reader** kan du köra skriptkommandon som läser data från en behållare eller kö. Om du har tilldelats rollen **Blob Data Contributor** kan du köra skriptkommandon som läser, skriver eller tar bort en behållare eller kö eller de data de innehåller.

Mer information om de behörigheter som krävs för varje Azure Storage-åtgärd i en behållare eller kö finns i [Anropa lagringsåtgärder med OAuth-token](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Anropa PowerShell-kommandon med Azure AD-autentiseringsuppgifter

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill använda Azure PowerShell för att logga in och köra efterföljande åtgärder mot Azure `-UseConnectedAccount` Storage med Azure AD-autentiseringsuppgifter skapar du en lagringskontext för att referera till lagringskontot och inkludera parametern.

I följande exempel visas hur du skapar en behållare i ett nytt lagringskonto från Azure PowerShell med dina Azure AD-autentiseringsuppgifter. Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

1. Logga in på ditt Azure-konto med kommandot [Connect-AzAccount:](/powershell/module/az.accounts/connect-azaccount)

    ```powershell
    Connect-AzAccount
    ```

    Mer information om hur du loggar in på Azure med PowerShell finns [i Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Skapa en Azure-resursgrupp genom att anropa [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Skapa ett lagringskonto genom att anropa [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Hämta den lagringskontokontext som anger det nya lagringskontot genom att anropa [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). När du agerar på ett lagringskonto kan du referera till kontexten i stället för att upprepade gånger skicka in autentiseringsuppgifterna. Inkludera `-UseConnectedAccount` parametern för att anropa eventuella efterföljande dataåtgärder med dina Azure AD-autentiseringsuppgifter:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Innan du skapar behållaren tilldelar du rollen [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) till dig själv. Även om du är kontoägare behöver du explicit behörighet för att utföra dataåtgärder mot lagringskontot. Mer information om hur du tilldelar RBAC-roller finns i [Bevilja åtkomst till Azure-blob och ködata med RBAC i Azure-portalen](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > RBAC-rolltilldelningar kan ta några minuter att sprida.

1. Skapa en behållare genom att anropa [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Eftersom det här anropet använder kontexten som skapats i föregående steg skapas behållaren med dina Azure AD-autentiseringsuppgifter.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Nästa steg

- [Använda PowerShell för att tilldela en RBAC-roll för åtkomst till blob- och ködata](storage-auth-aad-rbac-powershell.md)
- [Auktorisera åtkomst till blob- och ködata med hanterade identiteter för Azure-resurser](storage-auth-aad-msi.md)

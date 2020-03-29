---
title: Använda Azure CLI för att skapa en SAS för användardelegering för en behållare eller blob
titleSuffix: Azure Storage
description: Lär dig hur du skapar en SAS för användardelegation med Azure Active Directory-autentiseringsuppgifter med hjälp av Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371997"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Skapa en SAS för användardelegering för en behållare eller blob med Azure CLI

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Den här artikeln visar hur du använder Azure Active Directory (Azure AD) autentiseringsuppgifter för att skapa en användaredelegering SAS för en behållare eller blob med Azure CLI.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Installera den senaste versionen av Azure CLI

Om du vill använda Azure CLI för att skydda en SAS med Azure AD-autentiseringsuppgifter kontrollerar du först att du har installerat den senaste versionen av Azure CLI. Mer information om hur du installerar Azure CLI finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du vill skapa en SAS för användardelegering med Azure CLI kontrollerar du att du har installerat version 2.0.78 eller senare. Om du vill kontrollera `az --version` den installerade versionen använder du kommandot.

## <a name="sign-in-with-azure-ad-credentials"></a>Logga in med Azure AD-autentiseringsuppgifter

Logga in på Azure CLI med dina Azure AD-autentiseringsuppgifter. Mer information finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Tilldela behörigheter med RBAC

Om du vill skapa en SAS för användardelegering från Azure PowerShell måste Azure AD-kontot som används för att logga in på Azure CLI tilldelas en roll som innehåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Med den här behörigheten kan Azure AD-kontot begära *användardelegeringsnyckeln*. Användardelegeringsnyckeln används för att signera SAS för användardelegationen. Rollen som tillhandahåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** måste tilldelas på lagringskontonivå, resursgruppen eller prenumerationen.

Om du inte har tillräcklig behörighet för att tilldela RBAC-roller till ett Azure AD-säkerhetsobjekt kan du behöva be kontoägaren eller administratören att tilldela nödvändiga behörigheter.

I följande exempel tilldelas rollen **Storage Blob Data Contributor,** som innehåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Rollen är begränsad på lagringskontots nivå.

Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Mer information om de inbyggda rollerna som innehåller åtgärden **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** finns [i Inbyggda roller för Azure-resurser](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Använda Azure AD-autentiseringsuppgifter för att skydda en SAS

När du skapar en SAS för användardelegering med Azure CLI skapas användardelegeringsnyckeln som används för att signera SAS implicit. Starttid och utgångstid som du anger för SAS används också som starttid och förfallotid för användardelegeringsnyckeln.

Eftersom det maximala intervallet som användardelegeringsnyckeln är giltig över är 7 dagar från startdatumet, bör du ange en utgångstid för SAS som är inom 7 dagar från starttiden. SAS är ogiltigt när användardelegeringsnyckeln har upphört att gälla, så en SAS med en förfallotid på mer än 7 dagar är fortfarande bara giltig i 7 dagar.

När du skapar en sas-för användardelegering krävs `--auth-mode login` och `--as-user parameters` krävs. Ange *inloggning* `--auth-mode` för parametern så att begäranden som görs till Azure Storage auktoriseras med dina Azure AD-autentiseringsuppgifter. Ange `--as-user` parametern för att ange att SAS som returneras ska vara en SAS-användardelegation.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Skapa en SAS för användardelegering för en behållare

Om du vill skapa en SAS för användardelegering för en behållare med Azure CLI anropar du kommandot [az storage container generate-sas.](/cli/azure/storage/container#az-storage-container-generate-sas)

Behörigheter som stöds för en sas för användardelegering i en behållare är Lägg till, Skapa, Ta bort, Lista, Läsa och Skriva. Behörigheter kan anges var för sig eller kombineras. Mer information om dessa behörigheter finns i [Skapa en SAS för användardelegering](/rest/api/storageservices/create-user-delegation-sas).

I följande exempel returneras en SAS-token för användardelegering för en behållare. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

Den SAS-token för användardelegation som returneras påminner om:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Skapa en SAS för användardelegering för en blob

Om du vill skapa en SAS för användardelegering för en blob med Azure CLI anropar du kommandot [az storage blob generate-sas.](/cli/azure/storage/blob#az-storage-blob-generate-sas)

Behörigheter som stöds för en sas för användardelegering i en blob inkluderar Lägg till, Skapa, Ta bort, Läsa och Skriva. Behörigheter kan anges var för sig eller kombineras. Mer information om dessa behörigheter finns i [Skapa en SAS för användardelegering](/rest/api/storageservices/create-user-delegation-sas).

Följande syntax returnerar en SAS för användardelegering för en blob. Exemplet anger parametern, `--full-uri` som returnerar blob URI med SAS-token bifogad. Kom ihåg att ersätta platshållarvärdena inom parentes med dina egna värden:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

Den användardelegation SAS URI som returneras liknar:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> En SAS för användardelegering stöder inte att definiera behörigheter med en lagrad åtkomstprincip.

## <a name="revoke-a-user-delegation-sas"></a>Återkalla en SAS för användardelegering

Om du vill återkalla en SAS för användardelegering från Azure CLI anropar du kommandot [az storage account revoke-delegation-keys.](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys) Det här kommandot återkallar alla användardelegeringsnycklar som är associerade med det angivna lagringskontot. Alla signaturer för delad åtkomst som är associerade med dessa nycklar är ogiltiga.

Kom ihåg att ersätta platshållarvärden i vinkelparenteser med dina egna värden:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Både användardelegeringsnyckeln och RBAC-rolltilldelningarna cachelagras av Azure Storage, så det kan finnas en fördröjning mellan när du initierar återkallningsprocessen och när en befintlig användaredelegering SAS blir ogiltig.

## <a name="next-steps"></a>Nästa steg

- [Skapa en SAS -för användardelegation (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Hämta åtgärden Nyckel för användardelegering](/rest/api/storageservices/get-user-delegation-key)

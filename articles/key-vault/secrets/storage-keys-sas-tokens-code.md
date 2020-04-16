---
title: Hanterat lagringskonto för Azure Key Vault - PowerShell-version
description: Funktionen för hanterade lagringskonto ger en sömlös integrering mellan Azure Key Vault och ett Azure-lagringskonto.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 7307741e56c7fc912f60d0496979243eb4be77a4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431272"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Hämta token för signaturer för delad åtkomst i kod

Du kan hantera ditt [lagringskonto med signaturtoken för delad åtkomst](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) i nyckelvalvet. Den här artikeln innehåller exempel på C#-kod som hämtar en SAS-token och utför åtgärder med den.  Information om hur du skapar och lagrar SAS-token finns i [Hantera lagringskontonycklar med Key Vault och Azure CLI](overview-storage-keys.md) eller Hantera [lagringskontonycklar med Key Vault och Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Kodexempel

I det här exemplet hämtar koden en SAS-token från nyckelvalvet, använder den för att skapa ett nytt lagringskonto och skapar en ny Blob-tjänstklient.  

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<YourKeyVaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Om signaturtoken för delad åtkomst håller på att upphöra att gälla kan du hämta signaturtoken för delad åtkomst från nyckelvalvet och uppdatera koden.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [hanterar lagringskontonycklar med Key Vault och Azure CLI](overview-storage-keys.md) eller Azure [PowerShell](overview-storage-keys-powershell.md).
- Se [Exempel på hanterade lagringskontonyckeln](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [PowerShell-referens för Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

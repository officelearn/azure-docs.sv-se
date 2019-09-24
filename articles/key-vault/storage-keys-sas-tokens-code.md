---
title: Azure Key Vault hanterat lagrings konto – PowerShell-version
description: Funktionen hanterat lagrings konto ger en sömlös integrering mellan Azure Key Vault och ett Azure Storage-konto.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: a20f5b16b90b9bac7e6bc3ff808cf443b252fb44
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204554"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Hämta token för signaturer för delad åtkomst i kod

Du kan hantera ditt lagrings konto med [signatur-token för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md) i ditt nyckel valv. Den här artikeln innehåller exempel C# på kod som hämtar en SAS-token och utför åtgärder med den.  Information om hur du skapar och lagrar SAS-token finns i [Hantera lagrings konto nycklar med Key Vault och Azure CLI](key-vault-ovw-storage-keys.md) eller [Hantera lagrings konto nycklar med Key Vault och Azure PowerShell](key-vault-overview-storage-keys-powershell.md).

# <a name="code-samples"></a>Kodexempel

I det här exemplet hämtar koden en SAS-token från ditt nyckel valv, använder den för att skapa ett nytt lagrings konto och skapar en ny Blob Service-klient.  

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

Om signaturens token för delad åtkomst håller på att gå ut kan du hämta signatur-token för delad åtkomst från ditt nyckel valv och uppdatera koden.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [hanterar lagrings konto nycklar med Key Vault och Azure CLI](key-vault-ovw-storage-keys.md) eller [Azure PowerShell](key-vault-overview-storage-keys-powershell.md).
- Se [nyckel exempel för hanterade lagrings konton](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Om nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md)
- [Key Vault PowerShell-referens](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

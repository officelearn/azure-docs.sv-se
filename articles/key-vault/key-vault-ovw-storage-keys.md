---
ms.assetid: ''
title: Azure Key Vault hanteras lagringskonto – CLI
description: Lagringskontonycklar ge en seemless integrering mellan Azure Key Vault och viktiga baserat åtkomst till Azure Storage-konto.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 0392d84efa3a82a6323d6d09db792df7d6c42256
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210683"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Azure Key Vault hanteras lagringskonto – CLI

> [!NOTE]
> [Azure storage-integrering med Azure Active Directory (Azure AD) finns nu i förhandsversion](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Vi rekommenderar att du använder Azure AD för autentisering och auktorisering, som ger OAuth2-tokenbaserad åtkomst till Azure storage, precis som Azure Key Vault. På så sätt kan du:
> - Autentisera ditt klientprogram som använder en program- eller identitet, i stället för autentiseringsuppgifterna för lagringskontot. 
> - Använd en [Azure AD-hanterad identitet](/azure/active-directory/managed-identities-azure-resources/) när körs på Azure. Hanterade identiteter ta bort behovet av klientautentisering som helhet och lagra autentiseringsuppgifter i eller med ditt program.
> - Använd rollbaserad åtkomstkontroll (RBAC) för att hantera auktorisering, vilket även stöds av Key Vault.

- Azure Key Vault hanterar nycklar för ett Azure Storage konto (ASA).
    - Azure Key Vault kan internt lista nycklar (sync) med Azure Storage-kontot.    
    - Genererar om Azure Key Vault (roterar) nycklarna regelbundet.
    - Nyckelvärden returneras aldrig i svaret till anroparen.
    - Azure Key Vault hanterar nycklarna för både Storage-konton och klassiska Lagringskonton.
    
> [!IMPORTANT]
> En Azure AD-klient ger varje registrerade program med en  **[tjänstens huvudnamn](/azure/active-directory/develop/developer-glossary#service-principal-object)**, som fungerar som programmets identitet. Program-ID för tjänstens huvudnamn används när ger den behörighet att komma åt andra Azure-resurser via rollbaserad åtkomstkontroll (RBAC). Eftersom Key Vault är ett Microsoft-program, är den redan registrerad i alla Azure AD-klienter under samma program-ID, i varje Azure-molnet:
> - Azure AD-klienter i Azure government-molnet använder program-ID `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Azure AD-klienter i offentliga Azure-molnet och alla andra använda program-ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Förutsättningar
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) installera Azure CLI   
2. [Skapa ett Lagringskonto](https://azure.microsoft.com/services/storage/)
    - Följ stegen i den här [dokumentet](https://docs.microsoft.com/azure/storage/) att skapa ett lagringskonto  
    - **Riktlinjer för namngivning:** Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Steg för steg-instruktioner om hur du använder Key Vault för att hantera Lagringskontonycklar
--------------------------------------------------------------------------------
I den nedan information vi tilldela Key Vault som en tjänst har operatorn behörigheter för ditt storage-konto

> [!NOTE]
> . Observera att när du har konfigurerat Azure Key Vault hanteras storage kontonycklar de bör **nr** längre ändras utom via Key Vault. Hanterade Storage-kontonycklar innebär att Key Vault administrerar rotera nyckeln till lagringskontot

1. När du har skapat ett lagringskonto som kör följande kommando för att hämta resurs-ID för lagringskontot som vill du hantera

    ```
    az storage account show -n storageaccountname (Copy ID field out of the result of this command)
    ```
    
2. Hämta program-ID för Azure Key Vault-tjänstens huvudnamn 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Tilldela lagring nyckeln operatörsrollen till Azure Key Vault Identity

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ApplicationIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Skapa Key Vault hanteras Storage-konto.     <br /><br />
   Nedan har anger vi en återskapandeperiod 90 dagar. Efter 90 dagar, Key Vault återskapa ”key1” och växla den aktiva nyckeln från ”key2” till ”key1”.
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    Om användaren inte har skapat lagringskontot och inte har behörighet att storage-konto, ange behörigheter för ditt konto så att du kan hantera alla behörigheter som lagring i Key Vault i stegen nedan.
 > [!NOTE] 
    I det fallet att användaren inte har behörighet att storage-konto kan hämta vi först objekt-Id för användaren

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```
    
## <a name="how-to-access-your-storage-account-with-sas-tokens"></a>Hur du kommer åt ditt storage-konto med SAS-token

I det här avsnittet diskuteras hur du kan göra åtgärder på ditt lagringskonto genom att hämta [SAS-token](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) från Key Vault

I den under avsnittet vi visar hur du kan hämta din lagringskontonyckel som lagras i Key Vault och använda den för att skapa en definition för SAS (Shared Access Signature) för ditt lagringskonto.

> [!NOTE] 
  Det finns 3 sätt att autentisera till Key Vault eftersom du kan läsa i den [grundläggande begrepp](key-vault-whatis.md#basic-concepts)
- Med hjälp av hanterad tjänstidentitet (rekommenderas)
- Med hjälp av tjänstens huvudnamn och certifikat 
- Med tjänstens huvudnamn och lösenord (rekommenderas inte)

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Om din SAS-token snart upphör att gälla, sedan du hämta SAS-token igen från Key Vault och uppdatera koden

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```


### <a name="relavant-azure-cli-cmdlets"></a>Relavant Azure CLI-cmdletar
[Storage-cmdletar för Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>Relevanta Powershell-cmdletar

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>Se också

- [Om nycklar, hemligheter och certifikat](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault-teamets blogg](https://blogs.technet.microsoft.com/kv/)

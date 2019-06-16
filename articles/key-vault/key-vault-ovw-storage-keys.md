---
title: Hantera lagringskontonycklar med Azure Key Vault och Azure CLI
description: Lagringskontonycklar ger sömlös integrering mellan Azure Key Vault och nyckelbaserad åtkomst till ett Azure storage-konto.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 91cc3f96f9cdd231c38232c972c2628d12b9f4b3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476147"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Hantera lagringskontonycklar med Azure Key Vault och Azure CLI 

Azure Key Vault hanterar nycklar för Azure storage-konton och klassiska lagringskonton. Du kan använda funktionen för Key Vault hanterad lagring-konto för att slutföra flera viktiga hanteringsfunktioner åt dig.

En [Azure storage-konto](/azure/storage/storage-create-storage-account) använder en autentiseringsuppgift som består av ett kontonamn och en nyckel. Nyckeln är automatiskt genererade och fungerar som ett lösenord, snarare än en som en kryptografisk nyckel. Key Vault hanterar lagringskontonycklar genom att lagra dem som [Key Vault-hemligheter](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Nycklar listas (synkroniserade) med ett Azure storage-konto och skapas med jämna mellanrum eller _roterad_. 

När du använder funktionen viktiga hanterad lagring kan du överväga följande punkter:

- Nyckelvärden returneras aldrig som svar på en anropare.
- Endast Key Vault ska hantera dina lagringskontonycklar. Inte hantera nycklarna själv och undvika störningar på Key Vault-processer.
- Endast ett enda Key Vault-objekt bör hantera lagringskontonycklar. Tillåt inte nyckelhantering från flera objekt.
- Du kan begära Key Vault för att hantera ditt lagringskonto med en annan användare, men inte med ett huvudnamn för tjänsten.
- Återskapa nycklar genom att endast använda Key Vault. Inte manuellt återskapa dina lagringskontonycklar. 

> [!NOTE]
> Azure Storage-integrering med Azure Active Directory (Azure AD) är Microsofts molnbaserade identitets- och management-tjänsten.
> Azure AD-integrering är tillgängligt för [Azure-blobbar och köer](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
> Använd Azure AD för autentisering och auktorisering.
> Azure AD tillhandahåller OAuth2-tokenbaserad åtkomst till Azure Storage precis som Azure Key Vault.
>
> Azure AD kan du autentisera ditt klientprogram med hjälp av en program- eller identitet, i stället för autentiseringsuppgifterna för lagringskontot.
> Du kan använda en [Azure AD-hanterad identitet](/azure/active-directory/managed-identities-azure-resources/) när du kör på Azure. Hanterade identiteter ta bort behovet av klientautentisering och lagra autentiseringsuppgifter i eller med ditt program.
> Azure AD använder rollbaserad åtkomstkontroll (RBAC) för att hantera auktorisering, som stöds också av Key Vault.

### <a name="service-principal-application-id"></a>Service principal program-ID

En Azure AD-klient ger varje registrerade program med en [tjänstens huvudnamn](/azure/active-directory/develop/developer-glossary#service-principal-object). Tjänstens huvudnamn fungerar som Programidentitet (ID). Program-ID används under installationen av auktorisering för åtkomst till andra Azure-resurser via RBAC.

Key Vault är en Microsoft-program som redan är registrerad i alla Azure AD-klienter. Key Vault har registrerats under samma program-ID och inom varje Azure-molnet.

| Klienter | Molnet | Program-ID:t |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azures offentliga | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Annat  | Alla | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du använder Key Vault för att hantera din lagringskontonyckel kan du granska kraven:

- Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Skapa en [Azure storage-konto](https://azure.microsoft.com/services/storage/). Följ [här](https://docs.microsoft.com/azure/storage/).
- Lagringskontonamnet måste använda endast gemena bokstäver och siffror. Längden på namnet måste vara mellan 3 och 24 tecken.        
      
## <a name="manage-storage-account-keys"></a>Hantera lagringskontonycklar

Det finns fyra grundläggande stegen för att använda Key Vault för att hantera storage-kontonycklar:

1. Få ett befintligt lagringskonto.
1. Hämta ett befintligt nyckelvalv.
1. Lägga till ett lagringskonto för Key Vault hanteras i valvet. Ange `key1` som den aktiva nyckeln med en återskapandeperiod på 180 dagar.
1. Använd `key1` att ange en storage-kontext för det angivna lagringskontot.

> [!NOTE]
> Key Vault som en tjänst är tilldelad behörigheten operatör på ditt lagringskonto.
> 
> När du har konfigurerat Azure Key Vault hanterade lagringskontonycklar du bara ändra nycklarna med Key Vault.
> Hanterade lagringskontonycklar hanteras Key Vault rotation av storage-kontonyckel.

1. När du har skapat ett lagringskonto, kör du följande kommando för att hämta resurs-ID för storage-konto för att hantera:
    ```
    az storage account show -n storageaccountname
    ```

    Kopiera resurs-ID-värde från utdata från kommandot:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Exempel på utdata:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Tilldela rollen RBAC ”Storage-konto nyckeln Tjänstroll som operatör” till Key Vault. Den här rollen begränsar omfattningen för åtkomst till ditt lagringskonto. Använda rollen ”klassiska Storage-konto nyckeln Tjänstroll som operatör” för ett klassiskt lagringskonto.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` är objekt-ID för Key Vault i det offentliga Azure-molnet. För att hämta objekt-ID för Key Vault i Azure Government-molnet, se [principal program-ID för tjänstens](#service-principal-application-id).
    
1. Skapa ett Key Vault hanteras storage-konto:

    Ange en återskapandeperiod 90 dagar. Efter 90 dagar, Key Vault genererar `key1` och byter ut den aktiva nyckeln från `key2` till `key1`. `key1` sedan markeras den aktiva nyckeln. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Skapa och generera token

Du kan även ställa Key Vault för att generera signaturtoken för delad åtkomst. En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt storage-konto. Du kan ge klienterna åtkomst till resurser i ditt lagringskonto utan att dela dina kontonycklar. En signatur för delad åtkomst ger dig ett säkert sätt att dela dina lagringsresurser utan att kompromissa med lagringsnycklar.

Kommandona i det här avsnittet utför följande åtgärder:

- Ange ett konto som delas åtkomst signatur definition `<YourSASDefinitionName>`. Definitionen är inställt på ett lagringskonto för Key Vault hanteras `<YourStorageAccountName>` i ditt nyckelvalv `<VaultName>`.
- Skapa signaturtoken för delad åtkomst ett konto för Blob, fil, tabell och kö-tjänster. Token har skapats för resurstyperna Service, behållare och objekt. Token har skapats med alla behörigheter över https, och med de angivna start- och slutdatumen.
- Ange en definition av Key Vault hanteras storage delad åtkomst signatur i valvet. Definitionen innehåller mallen URI för signatur för delad åtkomst-token som har skapats. Definitionen har typen signatur för delad åtkomst `account` och är giltig i N dagar.
- Hämta den faktiska åtkomst-token från Key Vault-hemlighet som motsvarar definitionen för signatur för delad åtkomst.

När du har slutfört stegen i föregående avsnitt, kör du följande kommandon för att be Key Vault för att generera signaturtoken för delad åtkomst. 

1. Skapa en definition för signatur för delad åtkomst. När definitionen för signatur för delad åtkomst har skapats kan du be Key Vault för att generera mer delade signatur åtkomsttoken. Den här åtgärden kräver den `storage` och `setsas` behörigheter.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Hjälp om åtgärden finns i den [az storage-konto generera sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) referensdokumentation.

    Kopiera utdata när åtgärden har körts.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Använd den `$sasToken` genereras av det föregående kommandot och skapa en definition för signatur för delad åtkomst. Mer information om kommandoparametrarna finns i den [az keyvault storage sas-definitionen skapa](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) referensdokumentation.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    När användaren inte har behörighet att storage-konto, bör du först hämta objekt-ID för användaren:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Hämta token i koden

Utför åtgärder i ditt storage-konto genom att hämta [delad signaturtoken för åtkomst](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) från Key Vault.

Det finns tre sätt att autentisera till Key Vault:

- Använda hanterade tjänstidentiteter. Den här metoden rekommenderas starkt.
- Använd ett huvudnamn för tjänsten och certifikat. 
- Använd ett huvudnamn för tjänsten och lösenord. Den här metoden rekommenderas inte.

Mer information finns i [Azure Key Vault: Grundläggande begrepp](key-vault-whatis.md#basic-concepts).

I följande exempel visar hur du hämtar signaturtoken för delad åtkomst. Du kan hämta token när du har skapat en definition för signatur för delad åtkomst. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Om din signaturtoken för delad åtkomst är upphör snart att gälla, hämta signaturtoken för delad åtkomst igen från Key Vault och uppdatera koden.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure CLI-kommandon

Information om Azure CLI-kommandon som är relevanta för hanterade storage-konton finns i den [az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) referensdokumentation.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [nycklar, hemligheter och certifikat](https://docs.microsoft.com/rest/api/keyvault/).
- Granska artiklar på den [Azure Key Vault-teamets blogg](https://blogs.technet.microsoft.com/kv/).

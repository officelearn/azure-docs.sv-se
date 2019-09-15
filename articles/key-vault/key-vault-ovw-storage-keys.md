---
title: Hantera lagrings konto nycklar med Azure Key Vault och Azure CLI
description: Lagrings konto nycklar ger sömlös integrering mellan Azure Key Vault och nyckelbaserad åtkomst till ett Azure Storage-konto.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/01/2019
ms.openlocfilehash: 68c9279333e9ee92cbca1034f70801c153b6cdb8
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000540"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Hantera lagrings konto nycklar med Azure Key Vault och Azure CLI 

Azure Key Vault hanterar nycklar för Azure Storage-konton och klassiska lagrings konton. Du kan använda funktionen Key Vault hanterat lagrings konto för att slutföra flera nyckel hanterings funktioner åt dig.

Ett [Azure Storage-konto](/azure/storage/storage-create-storage-account) använder en autentiseringsuppgift som består av ett konto namn och en nyckel. Nyckeln genereras automatiskt och fungerar som ett lösen ord i stället för en kryptografisk nyckel. Key Vault hanterar lagrings konto nycklar genom att lagra dem som [Key Vault hemligheter](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Nycklar visas (synkroniseras) med ett Azure Storage-konto och återskapas eller _roteras_regelbundet. 

Tänk på följande när du använder funktionen för hanterad lagrings konto nyckel:

- Nyckel värden returneras aldrig som svar på en anropare.
- Endast Key Vault bör hantera dina lagrings konto nycklar. Hantera inte nycklarna själv och Undvik att störa Key Vault processer.
- Endast ett enda Key Vault-objekt bör hantera lagrings konto nycklar. Tillåt inte nyckel hantering från flera objekt.
- Du kan begära Key Vault att hantera ditt lagrings konto med ett huvud namn för användaren, men inte med ett huvud namn för tjänsten.
- Återskapa nycklar med hjälp av Key Vault. Återskapa inte dina lagrings konto nycklar manuellt. 

> [!NOTE]
> Azure Storage integration med Azure Active Directory (Azure AD) är Microsofts molnbaserade identitets-och åtkomst hanterings tjänst.
> Azure AD-integrering är tillgänglig för [Azure-blobbar och-köer](../storage/common/storage-auth-aad.md).
> Använd Azure AD för autentisering och auktorisering.
> Azure AD tillhandahåller OAuth2-tokenbaserad åtkomst till Azure Storage precis som Azure Key Vault.
>
> Med Azure AD kan du autentisera klient programmet med hjälp av ett program eller en användar identitet, i stället för autentiseringsuppgifterna för lagrings kontot.
> Du kan använda en [hanterad Azure AD-identitet](/azure/active-directory/managed-identities-azure-resources/) när du kör på Azure. Hanterade identiteter tar bort behovet av klientautentisering och lagrar autentiseringsuppgifter i eller med ditt program.
> Azure AD använder rollbaserad åtkomst kontroll (RBAC) för att hantera auktorisering, som också stöds av Key Vault.

### <a name="service-principal-application-id"></a>Program-ID för tjänstens huvud namn

En Azure AD-klient tillhandahåller varje registrerat program med ett [huvud namn för tjänsten](/azure/active-directory/develop/developer-glossary#service-principal-object). Tjänstens huvud namn fungerar som program identitet (ID). Program-ID används vid konfiguration av auktorisering för åtkomst till andra Azure-resurser via RBAC.

Key Vault är ett Microsoft-program som är förregistrerat i alla Azure AD-klienter. Key Vault registreras under samma program-ID och i varje Azure-moln.

| klienter | Molnet | Program-ID:t |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure, offentlig | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Annat  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Förutsättningar

Innan du använder Key Vault för att hantera lagrings konto nyckeln bör du granska kraven:

- Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Skapa ett [Azure Storage-konto](https://azure.microsoft.com/services/storage/). Följ [de här stegen](../storage/index.yml).
- Lagrings konto namnet får bara innehålla gemena bokstäver och siffror. Namnet måste innehålla mellan 3 och 24 tecken.        
      
## <a name="manage-storage-account-keys"></a>Hantera lagrings konto nycklar

Det finns fyra grundläggande steg för att använda Key Vault för att hantera lagrings konto nycklar:

1. Hämta ett befintligt lagrings konto.
1. Hämta ett befintligt nyckel valv.
1. Lägg till ett Key Vault hanterat lagrings konto i valvet. Ange `key1` som den aktiva nyckeln med en återställnings period på 90 dagar.
1. Används `key1` för att ange en lagrings kontext för det angivna lagrings kontot.

> [!NOTE]
> Key Vault som en tjänst tilldelas operatörs behörigheter på ditt lagrings konto.
> 
> När du har konfigurerat Azure Key Vault hanterade lagrings konto nycklar ska du bara ändra nycklarna med Key Vault.
> För hanterade lagrings konto nycklar hanterar Key Vault rotationen av lagrings konto nyckeln.

1. När du har skapat ett lagrings konto kör du följande kommando för att hämta resurs-ID för det lagrings konto som ska hanteras:
    ```
    az storage account show -n storageaccountname
    ```

    Kopiera resurs-ID-värdet från kommandots utdata:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Exempel på utdata:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Tilldela RBAC-rollen "lagrings kontots nyckel operatör" för Key Vault. Den här rollen begränsar åtkomstscope till ditt lagrings konto. För ett klassiskt lagrings konto använder du rollen "klassisk lagrings kontots nyckel operatörs tjänst roll".

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`är objekt-ID: t för Key Vault i det offentliga Azure-molnet. Information om hur du hämtar objekt-ID för Key Vault i Azure Government molnet finns i [program-ID för tjänstens huvud namn](#service-principal-application-id).
    
1. Skapa ett Key Vault hanterat lagrings konto:

    Ange en tids period på 90 dagar. Efter 90 dagar återskapar `key1` Key Vault och byter den aktiva nyckeln från `key2` till `key1`. `key1`markeras sedan som den aktiva nyckeln. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Skapa och generera token

Du kan också be Key Vault att generera token för signaturer för delad åtkomst. En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt storage-konto. Du kan bevilja klienter åtkomst till resurser i ditt lagrings konto utan att dela dina konto nycklar. En signatur för delad åtkomst ger dig ett säkert sätt att dela dina lagrings resurser utan att kompromissa med dina konto nycklar.

Kommandona i det här avsnittet Slutför följande åtgärder:

- Ange en definition `<YourSASDefinitionName>`för signaturen för delad åtkomst till kontot. Definitionen anges för ett Key Vault hanterat lagrings konto `<YourStorageAccountName>` i ditt nyckel valv `<VaultName>`.
- Skapa en signatur-token för delad åtkomst för BLOB-, fil-, tabell-och Queue-tjänster. Token skapas för resurs typ tjänst, behållare och objekt. Token skapas med alla behörigheter, över https, och med de angivna start-och slutdatumen.
- Ange en definition av signaturen för delad åtkomst för en Key Vault hanterad lagring i valvet. Definitionen har mall-URI: n för signaturen för signaturen för delad åtkomst som skapades. Definitionen har signaturen `account` för delad åtkomst och är giltig i N dagar.
- Hämta den faktiska åtkomsttoken från Key Vault hemlighet som motsvarar definitionen för signaturen för delad åtkomst.

När du har slutfört stegen i föregående avsnitt kör du följande kommandon för att be Key Vault att generera signatur-token för delad åtkomst. 

1. Skapa en definition för signatur för delad åtkomst. När definitionen av signaturen för delad åtkomst har skapats ber du Key Vault att generera fler signaturer för delad åtkomst. Den här åtgärden kräver `storage` behörigheterna och `setsas` .
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Mer information om åtgärden finns i [AZ Storage Account generate-SAS](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) Reference Documentation.

    Kopiera utdata när åtgärden har körts.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Använd det `$sasToken` som genererades av föregående kommando och skapa en definition för signaturen för delad åtkomst. Mer information om kommando parametrarna finns i AZ för nyckel [valv lagring SAS-definition Create](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) Reference Documentation.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    När användaren inte har behörighet till lagrings kontot hämtar du först objekt-ID: t för användaren:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Hämta token i kod

Kör åtgärder på ditt lagrings konto genom att hämta [token för signatur för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md) från Key Vault.

Det finns tre sätt att autentisera till Key Vault:

- Använd en hanterad tjänst identitet. Den här metoden rekommenderas starkt.
- Använd tjänstens huvud namn och certifikat. 
- Använd tjänstens huvud namn och lösen ord. Den här metoden rekommenderas inte.

Mer information finns i [Azure Key Vault: Grundläggande begrepp](basic-concepts.md).

Följande exempel visar hur du hämtar token för signaturer för delad åtkomst. Du hämtar tokens när du har skapat en definition av en signatur för delad åtkomst. 

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

Om signaturens token för delad åtkomst håller på att gå ut, hämtar du signaturen för delad åtkomst från Key Vault och uppdaterar koden.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure CLI-kommandon

Information om de Azure CLI-kommandon som är relevanta för hanterade lagrings konton finns i referens dokumentationen för AZ-nyckel [valv](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) .

## <a name="next-steps"></a>Nästa steg

- Läs mer om [nycklar, hemligheter och certifikat](https://docs.microsoft.com/rest/api/keyvault/).
- Läs artiklarna i [Azure Key Vault teamets blogg](https://blogs.technet.microsoft.com/kv/).

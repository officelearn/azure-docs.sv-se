---
title: Azure Key Vault hanteras lagringskonto – CLI
description: Lagringskontonycklar ger en sömlös integrering mellan Azure Key Vault och viktiga baserat åtkomst till Azure Storage-konto.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: mbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 0812d1523c6db077bf93a16a9a14771f5534d712
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60304922"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Azure Key Vault hanteras lagringskonto – CLI

> [!NOTE]
> [Azure storage-integrering med Azure Active Directory (Azure AD) finns nu i förhandsversion](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Vi rekommenderar att du använder Azure AD för autentisering och auktorisering, som ger OAuth2-tokenbaserad åtkomst till Azure storage, precis som Azure Key Vault. På så sätt kan du:
> - Autentisera ditt klientprogram som använder en program- eller identitet, i stället för autentiseringsuppgifterna för lagringskontot. 
> - Använd en [Azure AD-hanterad identitet](/azure/active-directory/managed-identities-azure-resources/) när körs på Azure. Hanterade identiteter ta bort behovet av klientautentisering som helhet och lagra autentiseringsuppgifter i eller med ditt program.
> - Använd rollbaserad åtkomstkontroll (RBAC) för att hantera auktorisering, vilket även stöds av Key Vault.

En [Azure storage-konto](/azure/storage/storage-create-storage-account) använder en autentiseringsuppgift som består av ett kontonamn och en nyckel. Nyckeln är automatiskt genererade och fungerar mer som ”lösenord” till skillnad från en kryptografisk nyckel. Key Vault kan hantera dessa lagringskontonycklar genom att lagra dem som [Key Vault-hemligheter](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Översikt

Key Vault hanteras storage-konto som funktionen utför flera hanteringsfunktioner åt dig:

- Listor (synkronisering) nycklar med ett Azure storage-konto.
- Återskapar (roterar) nycklarna regelbundet.
- Hanterar nycklar för både storage-konton och klassiska lagringskonton.
- Nyckelvärden returneras aldrig i svaret till anroparen.

När du använder funktionen viktiga hanterad lagring:

- **Tillåt endast Key Vault för att hantera dina lagringskontonycklar.** Försök inte hantera dem själv, som du ska påverka Key Vault-processer.
- **Tillåt inte storage-kontonycklar som ska hanteras av fler än ett objekt i Key Vault**.
- **Inte manuellt återskapa dina lagringskontonycklar**. Vi rekommenderar att du återskapar dem via Key Vault.
- Ber Key Vault för att hantera ditt lagringskonto kan göras genom att ett UPN för tillfället och inte ett huvudnamn för tjänsten

I följande exempel visar hur du kan låta Key Vault för att hantera dina lagringskontonycklar.

> [!IMPORTANT]
> En Azure AD-klient ger varje registrerade program med en  **[tjänstens huvudnamn](/azure/active-directory/develop/developer-glossary#service-principal-object)**, som fungerar som programmets identitet. Program-ID för tjänstens huvudnamn används när ger den behörighet att komma åt andra Azure-resurser via rollbaserad åtkomstkontroll (RBAC). Eftersom Key Vault är ett Microsoft-program, är den redan registrerad i alla Azure AD-klienter under samma program-ID, i varje Azure-molnet:
> - Azure AD-klienter i Azure government-molnet använder program-ID `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Azure AD-klienter i offentliga Azure-molnet och alla andra använda program-ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Nödvändiga komponenter
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) installera Azure CLI   
2. [Skapa ett Lagringskonto](https://azure.microsoft.com/services/storage/)
    - Följ stegen i den här [dokumentet](https://docs.microsoft.com/azure/storage/) att skapa ett lagringskonto  
    - **Riktlinjer för namngivning:** Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Steg för steg-instruktioner om hur du använder Key Vault för att hantera Lagringskontonycklar
--------------------------------------------------------------------------------
Begreppsmässigt lista över steg som är
- Vi först hämta ett storage-konto (befintlig)
- Vi sedan enkelt hämta (befintlig) key vault
- Vi sedan lägga till ett KeyVault-hanterade storage-konto till valvet, ställa in Key1 som den aktiva nyckeln och med en återskapandeperiod på 180 dagar
- Till sist ska du ange en storage-kontext för det angivna lagringskontot med Key1

I den nedan information vi tilldela Key Vault som en tjänst har operatorn behörigheter för ditt storage-konto

> [!NOTE]
> . Observera att när du har konfigurerat Azure Key Vault hanteras storage kontonycklar de bör **nr** längre ändras utom via Key Vault. Hanterade Storage-kontonycklar innebär att Key Vault administrerar rotera nyckeln till lagringskontot

> [!IMPORTANT]
> En Azure AD-klient ger varje registrerade program med en  **[tjänstens huvudnamn](/azure/active-directory/develop/developer-glossary#service-principal-object)**, som fungerar som programmets identitet. Program-ID för tjänstens huvudnamn används när ger den behörighet att komma åt andra Azure-resurser via rollbaserad åtkomstkontroll (RBAC). Eftersom Key Vault är ett Microsoft-program, är den redan registrerad i alla Azure AD-klienter under samma program-ID, i varje Azure-molnet:
> - Azure AD-klienter i Azure government-molnet använder program-ID `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Azure AD-klienter i offentliga Azure-molnet och alla andra använda program-ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

> - För närvarande kan du använda UPN be Key Vault för att hantera ett storage-konto och inte ett huvudnamn för tjänsten


1. När du har skapat ett lagringskonto som kör följande kommando för att hämta resurs-ID för lagringskontot som vill du hantera

    ```
    az storage account show -n storageaccountname 
    ```
    Kopiera ID-fält av resultatet av kommandot ovan, som ser ut som nedan
    ```
    /subscriptions/0xxxxxx-4310-48d9-b5ca-0xxxxxxxxxx/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    
2. Tilldela RBAC-roll ”Storage-konto nyckeln Tjänstroll som operatör” till Key Vault, begränsar åtkomstomfånget till ditt lagringskonto. För ett klassiskt lagringskonto använder du ”klassisk lagring konto nyckeln Tjänstroll som operatör”.
    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    ”93c27d83-f79b-4cb2-8dd4-4aa716542e74” är objekt-ID för Key Vault i offentliga molnet. För att hämta objekt-ID för Key Vault i nationella moln finns i avsnittet viktiga ovan
    
3. Skapa Key Vault hanteras Storage-konto.     <br /><br />
   Nedan har anger vi en återskapandeperiod 90 dagar. Efter 90 dagar, Key Vault återskapa ”key1” och växla den aktiva nyckeln från ”key2” till ”key1”. Det blir det Key1 den aktiva nyckeln nu. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<a name="step-by-step-instructions-on-how-to-use-key-vault-to-create-and-generate-sas-tokens"></a>Steg för steg-instruktioner om hur du använder Key Vault för att skapa och generera SAS-token
--------------------------------------------------------------------------------
Du kan även ställa Key Vault för att generera SAS (Shared Access Signature)-token. En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt storage-konto. Med en SAS kan kan du ge klienterna åtkomst till resurser i ditt storage-konto utan att dela dina kontonycklar. Det här är en viktig aspekt av att använda signaturer för delad åtkomst i dina program – en SAS är ett säkert sätt att dela dina lagringsresurser utan att kompromissa med lagringsnycklar.

När du har slutfört köra stegen ovan kan du följande kommandon för att be Key Vault för att generera SAS-token åt dig. 

Lista över saker som kan uppnås på den nedanstående steg är
- Anger ett konto med namnet SAS-definitionen `<YourSASDefinitionName>` ett KeyVault-hanterade lagringskonto `<YourStorageAccountName>` i ditt valv `<VaultName>`. 
- Skapar en SAS-token för kontot för tjänsterna Blob, fil, tabell och kö för resurstyperna Service, behållare och objekt, med alla behörigheter över https och med de angivna start- och slutdatumen
- Anger en hanterad KeyVault-lagring SAS-definitionen i valvet med mall-uri som SAS-token som skapade ovan, för SAS-typ 'account' och giltig för N dagar
- Hämtar den faktiska åtkomst-token från KeyVault-hemligheten som motsvarar den SAS-definitionen

1. I det här steget ska vi skapa en SAS-definitionen. När den här SAS-definitionen har skapats kan be du Key Vault för att generera mer SAS-token åt dig. Den här åtgärden kräver behörigheten lagring/setsas.

```
$sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
```
Du kan se mer hjälp om åtgärden ovan [här](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)

När den här åtgärden har körts kan bör du se utdata som liknar enligt nedan. Kopiera som

```console
   "se=2020-01-01&sp=***"
```

1. I det här steget använder vi utdata ($sasToken) genereras ovan för att skapa en SAS-Definition. Mer dokumentation finns [här](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)   

```
az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
```
                        

 > [!NOTE] 
 > I det fallet att användaren inte har behörighet att storage-konto kan hämta vi först objekt-Id för användaren

 ```
 az ad user show --upn-or-object-id "developer@contoso.com"

 az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
 ```
    
## <a name="fetch-sas-tokens-in-code"></a>Hämta SAS-token i koden

I det här avsnittet diskuteras hur du kan göra åtgärder på ditt lagringskonto genom att hämta [SAS-token](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) från Key Vault

I den under avsnittet vi visar hur du kan hämta SAS-token när en SAS-definitionen har skapats enligt ovan.

> [!NOTE]
>   Det finns 3 sätt att autentisera till Key Vault eftersom du kan läsa i den [grundläggande begrepp](key-vault-whatis.md#basic-concepts)
> - Med hjälp av hanterad tjänstidentitet (rekommenderas)
> - Med hjälp av tjänstens huvudnamn och certifikat 
> - Med tjänstens huvudnamn och lösenord (rekommenderas inte)

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

### <a name="relevant-azure-cli-commands"></a>Relevant Azure CLI-kommandon

[Azure CLI-Storage-kommandon](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

## <a name="see-also"></a>Se också

- [Om nycklar, hemligheter och certifikat](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault-teamets blogg](https://blogs.technet.microsoft.com/kv/)

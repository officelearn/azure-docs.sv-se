---
ms.assetid: ''
title: Azure Key Vault-Lagringskontonycklar
description: Lagringskontonycklar ge en seemless integrering mellan Azure Key Vault och viktiga baserat åtkomst till Azure Storage-konto.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 38717fed9f3877dfd0aa9819571ef0f32befc117
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785519"
---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault-lagringskontonycklar

Innan Azure Key Vault-lagringskontonycklar, utvecklare var tvungen att hantera sina egna nycklar för Azure Storage-konto (ASA) och rotera dem manuellt eller via en extern automation. Nu kan Key Vault-Lagringskontonycklar implementeras som [Key Vault-hemligheter](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) för att autentisera med Azure Storage-kontot.

Funktionen Azure Storage-konto (ASA) hanterar hemliga rotation för dig. Det tar också bort behovet av direkt kontakt med en nyckel som ASA, genom att erbjuda signaturer för delad åtkomst (SAS) som en metod.

Mer allmän information om Azure Storage-konton finns i [om Azure storage-konton](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Stöd för gränssnitt

Du hittar en fullständig lista och länkar till våra programmerings- och skriptspråk gränssnitt i den [Utvecklarguide för Key Vault](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>Key Vault hanterar

Key Vault utför flera interna hanteringsfunktioner åt dig när du använder hanterade Lagringskontonycklar.

- Azure Key Vault hanterar nycklar för ett Azure Storage konto (ASA).
    - Azure Key Vault kan internt lista nycklar (sync) med Azure Storage-kontot.
    - Genererar om Azure Key Vault (roterar) nycklarna regelbundet.
    - Nyckelvärden returneras aldrig i svaret till anroparen.
    - Azure Key Vault hanterar nycklarna för både Storage-konton och klassiska Lagringskonton.
- Azure Key Vault kan du valvobjekt/ägare, att skapa SAS (signatur för delad åtkomst, konto eller tjänst-SAS)-definitioner.
    - SAS-värde, som skapats med hjälp av SAS-definitionen, returneras som en hemlighet via REST-URI-sökväg. Mer information finns i åtgärder för SAS-definitionen i den [Azure Key Vault REST API-referens](/rest/api/keyvault).

## <a name="naming-guidance"></a>Riktlinjer för namngivning

- Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
- En SAS-definitionsnamnet måste vara 1-102 tecken som innehåller bara 0-9, a – z, A-Z.

## <a name="developer-experience"></a>Utvecklarupplevelse

### <a name="before-azure-key-vault-storage-keys"></a>Innan Azure Key Vault Lagringsnycklar

Utvecklare som behöver du följande åtgärder med en lagringskontonyckel att få åtkomst till Azure storage.
1. Store anslutningssträng eller SAS-token i Azure App Service-inställningar för program eller en annan lagring.
1. Vid start av program, hämta anslutningssträng eller SAS-token.
1. Skapa [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) att interagera med lagring.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>När du har Azure Key Vault Lagringsnycklar

Utvecklare kan skapa en [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) och utnyttjar detta för att hämta SAS-token för lagring. Sedan kan de skapa [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) med denna token.

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Vägledning för utvecklare

- Tillåt endast Key Vault för att hantera dina ASA-nycklar. Försök inte hantera dem själv, du stör Key Vault-processer.
- Tillåt inte ASA nycklar som ska hanteras av fler än ett Key Vault-objekt.
- Om du måste manuellt återskapa nycklarna ASA, rekommenderar vi att du återskapar dem via Key Vault.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Auktorisera Key Vault får tillgång till ditt storage-konto

Innan Key Vault kan komma åt och hantera dina lagringskontonycklar, måste du godkänna åtkomsten ditt storage-konto.  Liksom många program, Key Vault kan integreras med Azure AD för identitets- och hanteringstjänster. 

Eftersom Key Vault är ett Microsoft-program, den redan är registrerad i Azure AD-klienter under program-ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`. Och som alla program som är registrerade med Azure AD en [tjänstens huvudnamn](/azure/active-directory/develop/app-objects-and-service-principals) objektet innehåller programmets identitetsegenskaper. Tjänstens huvudnamn kan sedan få åtkomstbehörighet till en annan resurs via rollbaserad åtkomstkontroll (RBAC).  

Azure Key Vault-programmet kräver behörighet att *lista* och *återskapa* nycklar för ditt lagringskonto. Dessa behörigheter aktiveras genom inbyggda [Storage Account Key operatorn Service](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role) RBAC-roll. Du kan tilldela Key Vault-tjänstens huvudnamn i den här rollen med följande steg:

```powershell
# Get the resource ID of the Azure Storage Account you want Key Vault to manage
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ApplicationId “cfa8b339-82a2-471a-a3c9-0fc0be7a4093” -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

> [!NOTE]
> För en klassisk kontotyp inställd rollparametern *”klassisk lagring konto nyckeln Tjänstroll som operatör”.*

Vid lyckad rolltilldelningen, bör du se utdata som liknar följande

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgSandbox/providers/Microsoft.Storage/storageAccounts/sabltest/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgSandbox/providers/Microsoft.Storage/storageAccounts/sabltest
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : Storage Account Key Operator Service Role
RoleDefinitionId   : 81a9blll-bebf-436f-a333-f67b29880f1z
ObjectId           : c730c8da-blll-4032-8ad5-945e9dc8262z
ObjectType         : ServicePrincipal
CanDelegate        : False
```

## <a name="working-example"></a>Exempel

I följande exempel visar hur du skapar ett Key Vault hanteras Azure Storage-konto och associerade SAS-definitioner.

### <a name="prerequisite"></a>Krav

Innan du startar, se till att du [auktorisera Key Vault för att få åtkomst till ditt lagringskonto](#authorize-key-vault-to-access-to-your-storage-account).

### <a name="setup"></a>Konfiguration

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Nu ska vi konfigurera behörigheter för **ditt konto** så att du kan hantera alla behörigheter som lagring i Key Vault. I exemplet nedan våra Azure-konto är _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Skapa Key Vault hanteras Storage-konto

Nu kan skapa ett hanterat Lagringskonto i Azure Key Vault och använda en åtkomstnyckel från ditt lagringskonto för att skapa SAS-token.
- `-ActiveKeyName` använder ”key2” för att generera SAS-token.
- `-AccountName` används för att identifiera ditt hanterade storage-konto. Vi använder namnet på lagringskontot för att förenkla allt nedan, men det kan vara vilket namn som helst.
- `-DisableAutoRegenerateKey` Anger inte återskapa lagringskontonycklarna.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Åtkomstnyckeln återskapades

Om du vill Key Vault för att regelbundet återskapar dina lagringsåtkomstnycklar, kan du ange en återskapandeperiod. Nedan har anger vi en återskapandeperiod på tre dagar. Efter tre dagar, Key Vault återskapa ”key1” och växla den aktiva nyckeln från ”key2” till ”key1”.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Ange SAS-definitioner

Kontots SAS ger åtkomst till blobtjänsten med olika behörigheter.
Ange SAS-definitionerna i Key Vault för ditt hanterade storage-konto.
- `-AccountName` är namnet på det hanterade lagringskontot i Key Vault.
- `-Name` är identifieraren för SAS-token i lagring.
- `-ValidityPeriod` Anger ett sista giltighetsdatum för den genererade SAS-token.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>Hämta SAS-token

Hämta motsvarande SAS-token och göra anrop till lagring. `-SecretName` konstrueras med indata från den `AccountName` och `Name` parametrar när du körde [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Skapa lagring

Observera att komma åt *$readSasToken* misslyckas, men att vi kan komma åt med *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

# Ensure the txt file in command exists in local path mentioned
Set-AzureStorageBlobContent -Container containertest1 -File "./abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "./file.txt" -Context $context2
```

Du kan få åtkomst till lagringsblobens innehåll med den SAS-token som har skrivbehörighet.

### <a name="relevant-powershell-cmdlets"></a>Relevanta Powershell-cmdletar

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Onboarding för Storage-konto

Exempel: som en Key Vault objektets ägare du lägger till ett lagringsobjekt för kontot till Azure Key Vault för att integrera ett lagringskonto.

Under onboarding-processen, Key Vault behöver kontrollera att identiteten för onboarding-kontot har behörighet att *lista* och *återskapa* lagringsnycklar. För att verifiera dessa behörigheter, Key Vault får en OBO (på uppdrag av) token från Autentiseringstjänsten målgruppen som har angetts till Azure Resource Manager och gör en *lista* nyckeln anrop till Azure Storage-tjänsten. Om den *lista* anropet misslyckas, Key Vault objektskapande misslyckas med HTTP-statuskoden för *förbjuden*. Tangenterna som anges i det här sättet cachelagras med nyckelvalv entitet lagringen.

Key Vault måste kontrollera att identiteten har *återskapa* behörigheter innan den kan bli ägare till återskapar dina nycklar. Kontrollera att identiteten via OBO-token, samt Key Vault första part identiteten har dessa behörigheter:

- Key Vault visar RBAC-behörigheter på resursen för lagringskonton.
- Key Vault verifierar svar via matchning med reguljära uttryck för åtgärder och icke-åtgärder.

Hitta exempel stödjande på [Key Vault - hanterad Storage-konto nycklar-exempel](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=).

Om identiteten inte har *återskapa* behörigheter eller om Key Vault första part identitet inte har *lista* eller *återskapa* behörighet och sedan onboarding-processen begäran misslyckas returnerar en felkod och ett meddelande.

OBO-token fungerar bara när du använder från första part, interna klientprogram av PowerShell eller CLI.

## <a name="other-applications"></a>Andra program

- SAS-token, konstrueras med Key Vault-lagringskontonycklar, ge ännu mer kontrollerad åtkomst till ett Azure storage-konto. Mer information finns i [använda signaturer för delad åtkomst](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Se också

- [Om nycklar, hemligheter och certifikat](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault-teamets blogg](https://blogs.technet.microsoft.com/kv/)

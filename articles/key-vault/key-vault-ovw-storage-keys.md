---
ms.assetid: ''
title: Azure Key Vault Lagringskontonycklar
description: Lagringskontonycklar ge en seemless integrering mellan Azure Key Vault och baserat åtkomst till Azure Storage-konto.
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 4f42a47a6d934bf0538efccbcf7f057fd28e2c03
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179596"
---
# <a name="azure-key-vault-storage-account-keys"></a>Azure Key Vault Lagringskontonycklar

Innan Azure Key Vault Lagringskontonycklar hade utvecklare att hantera sina egna nycklar för Azure Storage-konto (ASA) och rotera dem manuellt eller via en extern automation. Nu Key Vault Lagringskontonycklar implementeras som [Key Vault hemligheter](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) för att autentisera med Azure Storage-konto.

Funktionen Azure Storage-konto (ASA) hanterar hemliga rotation för dig. Det tar också bort behovet av din direkt kontakt med en ASA nyckel genom att erbjuda delad åtkomst signaturer (SAS) som en metod.

Mer allmän information om Azure Storage-konton finns [om Azure storage-konton](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Stöder gränssnitt

Du hittar en fullständig lista och länkar till vår programmeringsspråk och scripting gränssnitt i den [Key Vault-utvecklare](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>Key Vault hanterar

Key Vault utför flera interna hanteringsfunktioner för din räkning när du använder hanteras Lagringskontonycklar.

- Azure Key Vault hanterar nycklar för ett Azure Storage konto (ASA).
    - Azure Key Vault kan internt, lista (sync) nycklar med ett Azure Storage-konto.
    - Azure Key Vault återskapar (roterar) nycklar med jämna mellanrum.
    - Nyckelvärden returneras aldrig i svaret till anroparen.
    - Azure Key Vault hanterar nycklarna i både Storage-konton och klassiska Lagringskonton.
- Azure Key Vault kan du valvet/objektets ägare, att skapa definitioner för SAS (konto eller tjänst-SAS).
    - SAS-värdet som skapats med hjälp av SAS-definition, returneras som en hemlighet via REST-URI-sökväg. Mer information finns i [Azure Key Vault lagringsåtgärder konto](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

## <a name="naming-guidance"></a>Riktlinjer för namngivning

- Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
- Namnet på definitionen av en SAS måste vara 1 102 tecken som innehåller bara 0-9, a-z, A-Z.

## <a name="developer-experience"></a>Upplevelse för utvecklare

### <a name="before-azure-key-vault-storage-keys"></a>Innan Azure Key Vault Lagringsnycklar

Utvecklare används för att göra följande med en lagringskontonyckel att få åtkomst till Azure-lagring.
1. Lagra anslutningssträngen eller SAS-token i Azure Apptjänst programinställningar eller en annan lagringsplats.
1. Vid start av program, hämta anslutningssträngen eller SAS-token.
1. Skapa [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) att interagera med lagring.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>När Azure Key Vault Lagringsnycklar

Utvecklare kan skapa en [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) och utnyttja om du vill hämta SAS-token för lagring. Sedan kan de skapa [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) med denna token.

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

 ### <a name="developer-guidance"></a>Guide för utvecklare

- Tillåt endast Key Vault att hantera ASA-nycklar. Försök inte hantera dem själv, du stör Key Vault-processer.
- Tillåt inte ASA nycklar som ska hanteras av fler än ett Key Vault-objekt.
- Om du måste manuellt återskapa nycklarna ASA, rekommenderar vi att återskapa via Nyckelvalvet.

## <a name="getting-started"></a>Komma igång

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Installationsprogrammet för rollbaserade behörigheter för åtkomstkontroll (RBAC)

Azure Key Vault Programidentitet behöver behörighet att *lista* och *återskapa* nycklar för ett lagringskonto. Ställa in behörigheterna med följande steg:

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Fungerande exempel

Exemplet nedan visar hur du skapar ett Nyckelvalv hanterade Azure Storage-konto och de associerade definitionerna av delade signatur åtkomst (SAS).

### <a name="prerequisite"></a>Krav

Se till att du har slutfört [för rollbaserade behörigheter för åtkomstkontroll (RBAC)](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Konfiguration

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Därefter ange behörigheter för **ditt konto** så att du kan hantera alla behörigheter för lagring i Nyckelvalvet. I exemplet nedan våra Azure-konto är _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Skapa en Key Vault hanteras Storage-konto

Nu skapa ett Lagringskonto som hanteras i Azure Key Vault och använda en snabbtangent från ditt lagringskonto för att skapa SAS-token.
- `-ActiveKeyName` använder 'key2' för att generera SAS-token.
- `-AccountName` används för att identifiera hanterade storage-konto. Vi använder lagringskontonamnet för att göra det enkelt nedan, men det kan vara vilket namn som helst.
- `-DisableAutoRegenerateKey` Anger inte återskapa lagringskontonycklarna.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Nycklar

Key Vault återskapa åtkomstnycklar för din lagring med jämna mellanrum kan du ange en period återskapas. Nedan anger vi en återskapandet period på tre dagar. Efter tre dagar Key Vault återskapa 'key1' och växla den aktiva nyckeln från 'key2' till 'key1'.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Ange SAS definitioner

Konto-SAS ger åtkomst till blob-tjänsten med olika behörigheter.
Ange SAS-definitioner i Key Vault för hanterade storage-konto.
- `-AccountName` är namnet på det hanterade lagringskontot i Nyckelvalvet.
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

Hämta motsvarande SAS-token och göra anrop till lagring. `-SecretName` med information från den `AccountName` och `Name` parametrar när du körde [Set AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Skapa lagring

Observera att försök att komma åt med *$readSasToken* misslyckas, men att vi kan nå med *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

Du kan använda blob storage innehåll med den SAS-token som har skrivbehörighet.

### <a name="relevant-powershell-cmdlets"></a>Relevanta Powershell-cmdlets

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Onboarding för Storage-konto

Exempel: som en Key Vault objektägaren som du lägger till ett lagringsobjekt konto till din Azure Key Vault att publicera ett lagringskonto.

Under onboarding, Key Vault behöver kontrollera att identiteten för onboarding-tjänstkontot har behörighet att *lista* och *återskapa* lagringsnycklar. För att kontrollera behörigheterna Key Vault får en OBO (på uppdrag av) token från Autentiseringstjänsten målgruppen som har angetts till Azure Resource Manager och gör en *lista* nyckeln anrop till Azure Storage-tjänsten. Om den *lista* anropet misslyckas Nyckelvalvet objektgenereringen misslyckas med en HTTP-statuskoden *förbjuden*. Nycklarna som anges i det här sättet cachelagras med nyckelvalv entitet lagringen.

Key Vault måste kontrollera att identiteten har *återskapa* behörigheter innan den kan bli ägare till återskapar dina nycklar. Att kontrollera att identiteten via OBO token som Key Vault första part identitet har följande behörigheter:

- Key Vault visar RBAC-behörighet på resursen storage-konto.
- Key Vault verifierar svar via matchning med reguljära uttryck för och icke-åtgärder.

Hitta exempel på [Key Vault - hanterade Storage-konto nycklar exempel](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167).

Om identiteten saknar *återskapa* behörigheter eller om Key Vault första part identitet inte har *lista* eller *återskapa* behörighet och sedan onboarding-processen för begäran misslyckas returnerar en felkod och ett meddelande.

OBO token fungerar endast när du använder från första part, interna klientprogram av PowerShell eller CLI.

## <a name="other-applications"></a>Andra program

- SAS-token som skapats med hjälp av Key Vault lagringskontonycklar ger ännu mer kontrollerad åtkomst till ett Azure storage-konto. Mer information finns i [använder signaturer för delad åtkomst](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Se också

- [Om nycklar, hemligheter och certifikat](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault-teamets blogg](https://blogs.technet.microsoft.com/kv/)

---
ms.assetid: 
title: Azure Key Vault Lagringskontonycklar
description: "Lagringskontonycklar ge en seemless integrering mellan Azure Key Vault och baserat åtkomst till Azure Storage-konto."
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 6ebac5fc90e259b19e0a4103a732754384232a44
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
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
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>När Azure Key Vault Lagringsnycklar 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Guide för utvecklare

- Tillåt endast Key Vault att hantera ASA-nycklar. Försök inte hantera dem själv, du stör Key Vault-processer. 
- Tillåt inte ASA nycklar som ska hanteras av fler än ett Key Vault-objekt. 
- Om du måste manuellt återskapa nycklarna ASA, rekommenderar vi att återskapa via Nyckelvalvet. 

## <a name="getting-started"></a>Komma igång

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Installationsprogrammet för rollbaserade behörigheter för åtkomstkontroll (RBAC)

Azure Key Vault Programidentitet behöver behörighet att *lista* och *återskapa* nycklar för ett lagringskonto. Ställa in behörigheterna med följande steg:

- Hämta ObjectId Azure Key Vault identitet: 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`

- Tilldela Azure Key Vault Identity operatörsrollen lagring nyckel: 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > Ange parametern roll för klassiska kontotyp, *”klassisk lagring konto nyckeln Service Operatörsrollen”.*

## <a name="working-example"></a>Fungerande exempel

Exemplet nedan visar hur du skapar ett Nyckelvalv hanterade Azure Storage-konto och de associerade definitionerna av delade signatur åtkomst (SAS).

### <a name="assumptions"></a>Antaganden

Följande instruktioner är givens för det här exemplet fungerar.

- Storage-resursen finns på: */subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1*

- Namnet på ditt nyckelvalv är: *yourtest1*

### <a name="get-a-service-principal"></a>Hämta ett huvudnamn för tjänsten

```powershell
$yourKeyVaultServicePrincipalId = (Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Utdata från kommandot ovan innehåller din ServicePrincipal som vi ringer upp dig *yourKeyVaultServicePrincipalId*. 

### <a name="set-permissions"></a>Ange behörigheter

Kontrollera att din lagring behörighetsinställningen *alla*. Du kan hämta youruserPrincipalId och ange behörigheter för valvet med följande kommandon.

```powershell
$youruserPrincipalId = (Get-AzureRmADUser -SearchString "your user principal name").Id
```
Nu söka efter ditt namn och hämta de relaterade objekt-ID som du ska använda i Ange behörigheter för valvet.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId $youruserPrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>Tillåt åtkomst

Du måste ge Key Vault-tjänståtkomst till storage-konton innan du kan skapa en hanterad storage-konto och SAS-definitioner.

```powershell
New-AzureRmRoleAssignment -ObjectId $yourKeyVaultServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>Skapa lagringskonto

Nu skapa ett Lagringskonto som hanteras och två SAS-definitioner. Konto-SAS ger åtkomst till blob-tjänsten med olika behörigheter.

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>Återskapandet

Anger återskapas perioden med följande kommandon.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Ange SAS definitioner

Ange SAS-definitioner i Key Vault för hanterade storage-konto.

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>Hämta token

Hämta motsvarande SAS-token och göra anrop till lagring.

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>Skapa lagring

Observera att försök att komma åt med *$sastoken1* misslyckas, men att vi kan nå med *$sastoken2*. 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>Exempel sammanfattning

Du kan använda blob storage innehåll med den SAS-token som har skrivbehörighet.

### <a name="relevant-powershell-cmdlets"></a>Relevanta Powershell-cmdlets

- [Get-AzureKeyVaultManagedStorageAccount ](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

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

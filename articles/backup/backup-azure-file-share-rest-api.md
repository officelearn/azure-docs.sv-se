---
title: Säkerhetskopiera Azure-filresurser med REST API
description: Lär dig hur du använder REST API för att säkerhetskopiera Azure-filresurser i Recovery Services Vault
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 2cf385830ec1be17cb62432e6ef9cba7d82a9db1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248104"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Säkerhetskopiera Azure-filresurs med Azure Backup via Rest API

I den här artikeln beskrivs hur du säkerhetskopierar en Azure-filresurs med Azure Backup via REST API.

Den här artikeln förutsätter att du redan har skapat ett valv för återställningstjänster och en princip för att konfigurera säkerhetskopiering för filresursen. Om du inte har gjort det läser du [skapa valvet](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatevault) och [skapar princip](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy) REST API-självstudier för att skapa nya valv och principer.

I den här artikeln använder vi följande resurser:

- **RecoveryServicesVault**: *azurefilesvault*

- **Policy:** *schema1*

- **Resursgrupp**: *azurefiles*

- **Lagringskonto**: *testvault2*

- **Fildelning:** *testdelning*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Konfigurera säkerhetskopiering för en oskyddad Azure-filresurs med REST API

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Identifiera lagringskonton med oskyddade Azure-filresurser

Valvet måste identifiera alla Azure-lagringskonton i prenumerationen med filresurser som kan säkerhetskopieras till Recovery Services Vault. Detta utlöses med hjälp av [uppdateringsåtgärden](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Det är en asynkron *POST-åtgärd* som säkerställer att valvet får den senaste listan över alla oskyddade Azure File-resurser i den aktuella prenumerationen och "cachelagrar" dem. När filresursen är "cachelagrade" kan återställningstjänster komma åt filresursen och skydda den.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST-uri `{subscriptionId}`har `{vaultName}` `{vaultresourceGroupName}`, `{fabricName}` , och parametrar. I vårt exempel skulle värdet för de olika parametrarna vara följande:

- `{fabricName}`är *Azure*

- `{vaultName}`är *azurefilesvault*

- `{vaultresourceGroupName}`är *azurefiles*

- $filter=backupManagementType eq 'AzureStorage'

Eftersom alla nödvändiga parametrar anges i URI, finns det inget behov av en separat begärandetext.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>Svar

Uppdateringsåtgärden är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Två svar returneras: 202 (Godkänd) när en annan åtgärd skapas och 200 (OK) när åtgärden är klar.

##### <a name="example-responses"></a>Exempel på svar

När *POST-begäran* har skickats tillbaka returneras ett 202-svar (accepterad).

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Spåra den resulterande åtgärden med hjälp av "Plats"-huvudet med ett enkelt *GET-kommando*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

När alla Azure Storage-konton har identifierats returnerar KOMMANDOT GET ett 200 -svar (inget innehåll). Valvet kan nu identifiera alla lagringskonto med filresurser som kan säkerhetskopieras i prenumerationen.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>Hämta lista över lagringskonton som kan skyddas med Recovery Services-valv

För att bekräfta att "cachelagring" är gjort, lista alla skyddsbara lagringskonton under prenumerationen. Leta sedan upp önskat lagringskonto i svaret. Detta görs med hjälp av [GET ProtectableContainers-åtgärden.](https://docs.microsoft.com/rest/api/backup/protectablecontainers/list)

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*GET* URI har alla nödvändiga parametrar. Inget ytterligare begärandeorgan behövs.

Exempel på svarstext:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Eftersom vi kan hitta *testvault2-lagringskontot* i svarstexten med det egna namnet, lyckades uppdateringsåtgärden som utfördes ovan. Valvet för återställningstjänster kan nu identifiera lagringskonton med oskyddade filresurser i samma prenumeration.

### <a name="register-storage-account-with-recovery-services-vault"></a>Registrera lagringskonto med Recovery Services-valv

Det här steget behövs bara om du inte registrerade lagringskontot med valvet tidigare. Du kan registrera valvet via [ProtectionContainers-Register-åtgärden](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register).

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Ange variablerna för URI enligt följande:

- {resourceGroupName} - *azurefiles*
- {fabricName} - *Azure*
- {vaultName} - *azurefilesvault*
- {containerName} - Det här är namnattributet i svarstexten för åtgärden GET ProtectableContainers.
   I vårt exempel är det *StorageContainer; Lagring; AzureFiles;testvault2*

>[!NOTE]
> Ta alltid namnet attributet för svaret och fyll i den här begäran. Inte hårdkoda eller skapa behållarnamnsformatet. Om du skapar eller hårdkodar det misslyckas API-anropet om behållarnamnet ändras i framtiden.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

Det skapa förfråspråkstexten är följande:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

För en fullständig förteckning över definitioner av begärandeorganet och andra detaljer, se [ProtectionContainers-Register](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Detta är en asynkron åtgärd och returnerar två svar: "202 Accepterad" när åtgärden accepteras och "200 OK" när åtgärden är klar.  Om du vill spåra åtgärdsstatus använder du platshuvudet för att få den senaste statusen för åtgärden.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Exempel på svarstext när åtgärden är klar:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

Du kan kontrollera om registreringen lyckades från värdet för *parametern registrationstatus* i svarstexten. I vårt fall visar den status som registrerad för *testvault2*, så registreringen lyckades.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Fråga alla oskyddade filresurser under ett lagringskonto

Du kan fråga om skyddsbara objekt i ett lagringskonto med hjälp av åtgärden [Skyddsbehållare-Fråga.](https://docs.microsoft.com/rest/api/backup/protectioncontainers/inquire) Det är en asynkron åtgärd och resultaten bör spåras med hjälp av platshuvudet.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Ställ in variablerna för ovanstående URI enligt följande:

- {vaultName} - *azurefilesvault*
- {fabricName} - *Azure*
- {containerName}- Se namnattributet i svarstexten för åtgärden GET ProtectableContainers. I vårt exempel är det *StorageContainer; Lagring; AzureFiles;testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

När begäran har lyckades returneras statuskoden "OK"

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Markera den filresurs som du vill säkerhetskopiera

Du kan lista alla skyddsbara objekt under prenumerationen och hitta önskad filresurs som ska säkerhetskopieras med hjälp av åtgärden [GET backupprotectableItems.](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list)

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Konstruera URI enligt följande:

- {vaultName} - *azurefilesvault*
- {$filter} - *säkerhetskopieringManagementType eq 'AzureStorage'*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Exempelsvar:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

Svaret innehåller en lista över alla oskyddade filresurser och innehåller all information som krävs av Azure Recovery Service för att konfigurera säkerhetskopian.

### <a name="enable-backup-for-the-file-share"></a>Aktivera säkerhetskopiering för filresursen

När den relevanta filresursen har "identifierats" med det egna namnet väljer du den princip som ska skyddas. Mer information om befintliga principer i valvet finns i [lista princip-API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Välj sedan relevant [princip](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) genom att referera till principnamnet. Om du vill skapa principer läser du [skapa princips självstudiekurs](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy).

Aktivera skydd är en asynkron *PUT-åtgärd* som skapar ett "skyddat objekt".

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Ange **variabler för behållarnamn** och **protecteditemname** med hjälp av ID-attributet i svarstexten för åtgärden GET backupprotectableitems.

I vårt exempel är id för filresurs som vi vill skydda:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containername} - *storagecontainer;storage;azurefiles;testvault2*
- {protectedItemName} - *azurefileshare;testshare*

Du kan också **name** referera till namnattributet för skyddsbehållaren och skyddsbara objektsvar.

>[!NOTE]
>Ta alltid namnet attributet för svaret och fyll i den här begäran. Inte hårdkoda eller skapa formatet för behållarnamn eller skyddade objektnamn. Om du skapar eller hårdkodar det misslyckas API-anropet om behållarnamnsformatet eller det skyddade objektnamnsformatet ändras i framtiden.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Skapa en förfråmstext:

Följande begärandetext definierar egenskaper som krävs för att skapa ett skyddat objekt.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**SourceResourceId** är **parentcontainerFabricID** som svar på GET backupprotectableItems.

Exempelsvar

Skapandet av ett skyddat objekt är en asynkron åtgärd som skapar en annan åtgärd som måste spåras. Två svar returneras: 202 (Godkänd) när en annan åtgärd skapas och 200 (OK) när åtgärden är klar.

När du har skickat *put-begäran* om att skapa eller uppdatera skyddade objekt är det första svaret 202 (Accepterad) med ett platshuvud.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Spåra sedan den resulterande åtgärden med hjälp av platshuvudet eller Azure-AsyncOperation-huvudet med kommandot *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

När åtgärden är klar returneras 200 (OK) med det skyddade objektets innehåll i svarstexten.

Exempel svarstext:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Detta bekräftar att skyddet är aktiverat för filresursen och den första säkerhetskopian kommer att utlösas enligt principschemat.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Utlösa en säkerhetskopiering på begäran för filresurs

När en Azure-filresurs har konfigurerats för säkerhetskopiering körs säkerhetskopior enligt principschemat. Du kan vänta på den första schemalagda säkerhetskopieringen eller utlösa en säkerhetskopiering på begäran när som helst.

Utlösa en on-demand backup är en POST-åtgärd.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} och {protectedItemName} är de som är konstruerade ovan när säkerhetskopiering aktiveras. Till vårt exempel innebär detta att:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Skapa förfråningstext

För att utlösa en säkerhetskopiering på begäran följer följande komponenter i begärandetexten.

| Namn       | Typ                       | Beskrivning                       |
| ---------- | -------------------------- | --------------------------------- |
| Egenskaper | AzurefilesharebackupReques | Egenskaper för BackupRequestResource |

För en fullständig lista över definitioner av begärandetexten och annan information finns [i utlösarkopior för skyddade objekt REST API-dokument](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

Exempel på begärantext

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses"></a>Svar

Att utlösa en säkerhetskopiering på begäran är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Två svar returneras: 202 (Godkänd) när en annan åtgärd skapas och 200 (OK) när åtgärden är klar.

### <a name="example-responses"></a>Exempel på svar

När du har skickat *POST-begäran* om säkerhetskopiering på begäran är det första svaret 202 (accepterad) med ett platshuvud eller Azure-async-header.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Spåra sedan den resulterande åtgärden med hjälp av platshuvudet eller Azure-AsyncOperation-huvudet med kommandot *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

När åtgärden är klar returneras 200 (OK) med ID:t för det resulterande säkerhetskopieringsjobbet i svarstexten.

#### <a name="sample-response-body"></a>Exempel på svarstext

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Eftersom säkerhetskopieringsjobbet är en tidskrävande åtgärd måste det spåras enligt beskrivningen i [övervakarjobben med REST API-dokument](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [återställer Azure-filresurser med Rest API](restore-azure-file-share-rest-api.md).

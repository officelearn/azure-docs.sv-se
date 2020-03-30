---
title: Säkerhetskopiera virtuella Azure-datorer med REST API
description: I den här artikeln kan du lära dig hur du konfigurerar, initierar och hanterar säkerhetskopiering av Azure VM Backup med REST API.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 4789ef1e0e09df521f8cab539d972e9e669e0a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248169"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Säkerhetskopiera en virtuell Azure-dator med Azure Backup via REST API

I den här artikeln beskrivs hur du hanterar säkerhetskopior för en Azure VM med Azure Backup via REST API. Konfigurera skydd för första gången för en tidigare oskyddad Azure-virtuell dator, utlösa en säkerhetskopiering på begäran för en skyddad Azure-virtuell dator och ändra säkerhetskopieringsegenskaper för en säkerhetskopierad virtuell dator via REST API som förklaras här.

Se skapa [valv](backup-azure-arm-userestapi-createorupdatevault.md) och [skapa princip](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API-självstudier för att skapa nya valv och principer.

Anta att du vill skydda en virtuell dator "testVM" under en resursgrupp "testRG" till ett Recovery Services-valv "testVault", som finns i resursgruppen "testVaultRG", med standardprincipen (med namnet "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Konfigurera säkerhetskopiering för en oskyddad Azure-virtuell dator med REST API

### <a name="discover-unprotected-azure-vms"></a>Upptäck oskyddade virtuella Azure-datorer

Först bör valvet kunna identifiera Den virtuella Azure-datorn. Detta utlöses med hjälp av [uppdateringsåtgärden](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Det är en asynkron *POST-åtgärd* som ser till att valvet får den senaste listan över alla oskyddade virtuella datorer i den aktuella prenumerationen och "cachelagrar" dem. När den virtuella datorn har cachelagrats kan återställningstjänster komma åt den virtuella datorn och skydda den.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

POST-uri `{subscriptionId}`har `{vaultName}` `{vaultresourceGroupName}`parametrar `{fabricName}` för POST., , , parameters. Den `{fabricName}` är "Azure". Enligt vårt exempel `{vaultName}` är "testVault" och `{vaultresourceGroupName}` är "testVaultRG". Eftersom alla nödvändiga parametrar anges i URI finns det inget behov av ett separat begärandeorgan.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Svar

Uppdateringsåtgärden är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Två svar returneras: 202 (Godkänd) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden är klar.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|204 Inget innehåll     |         |  OK utan att något innehåll returneras      |
|202 Accepterad     |         |     Accepterad    |

##### <a name="example-responses"></a>Exempel på svar

När *POST-begäran* har skickats tillbaka returneras ett 202-svar (accepterad).

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Spåra den resulterande åtgärden med hjälp av "Plats"-huvudet med ett enkelt *GET-kommando*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

När alla virtuella Azure-datorer har identifierats returnerar KOMMANDOT GET ett 204 -svar (inget innehåll). Valvet kan nu identifiera alla virtuella datorer i prenumerationen.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>Välja relevant Virtuell Azure

 Du kan bekräfta att "cachelagring" görs genom [att lista alla skyddade objekt](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) under prenumerationen och hitta önskad virtuell dator i svaret. [Svaret från den här åtgärden](#example-responses-1) ger dig också information om hur Återställningstjänster identifierar en virtuell dator.  När du är bekant med mönstret kan du hoppa över det här steget och direkt fortsätta att [aktivera skydd](#enabling-protection-for-the-azure-vm).

Den här åtgärden är en *GET-åtgärd.*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*GET* URI har alla nödvändiga parametrar. Inget ytterligare begärandeorgan behövs.

#### <a name="responses"></a><a name="responses-1"></a>Svar

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses"></a><a name="example-responses-1"></a>Exempel på svar

När *GET-begäran* har skickats returneras ett 200 -svar (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> Antalet värden i ett *GET-svar* är begränsat till 200 för en "sida". Använd fältet nästalänk för att hämta URL:en för nästa uppsättning svar.

Svaret innehåller en lista över alla oskyddade virtuella `{value}` Azure-datorer och var och en innehåller all information som krävs av Azure Recovery Service för att konfigurera säkerhetskopiering. Om du vill `{name}` konfigurera säkerhetskopiering noterar du fältet och `{virtualMachineId}` fältet i `{properties}` avsnittet. Konstruera två variabler från dessa fältvärden som nämns nedan.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "vm;" +`{name}`
- `{virtualMachineId}`används senare i [begäran kroppen](#example-request-body)

I exemplet översätter ovanstående värden till:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Aktivera skydd för den virtuella Azure-datorn

När den relevanta virtuella datorn har "cachelagrats" och "identifierats" väljer du principen för att skydda. Mer information om befintliga principer i valvet finns i [lista princip-API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Välj sedan relevant [princip](/rest/api/backup/protectionpolicies/get) genom att referera till principnamnet. Om du vill skapa principer läser du [skapa princips självstudiekurs](backup-azure-arm-userestapi-createorupdatepolicy.md). "DefaultPolicy" väljs i exemplet nedan.

Aktivera skydd är en asynkron *PUT-åtgärd* som skapar ett "skyddat objekt".

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Den `{containerName}` `{protectedItemName}` och är som konstruerade ovan. Den `{fabricName}` är "Azure". Till vårt exempel innebär detta att:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Skapa förfrådetexten

Om du vill skapa ett skyddat objekt följer följande komponenter i begärandetexten.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | AzureIaaSVMSkyddadItem        |Egenskaper för ProtectedItem Resource         |

För en fullständig lista över definitioner av begärandetexten och annan information finns i skapa [ett skyddat REST API-dokument](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body)för objektet .

##### <a name="example-request-body"></a>Exempel på begäran

Följande begärandetext definierar egenskaper som krävs för att skapa ett skyddat objekt.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

Det `{sourceResourceId}` är `{virtualMachineId}` som nämns ovan från [svaret av listan protectable objekt](#example-responses-1).

#### <a name="responses"></a>Svar

Skapandet av ett skyddat objekt är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Två svar returneras: 202 (Godkänd) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden är klar.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 Accepterad     |         |     Accepterad    |

##### <a name="example-responses"></a>Exempel på svar

När du har skickat *PUT-begäran* om att skapa eller uppdatera skyddade objekt är det första svaret 202 (accepterad) med ett platshuvud eller Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Spåra sedan den resulterande åtgärden med hjälp av platshuvudet eller Azure-AsyncOperation-huvudet med ett enkelt *GET-kommando.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

När åtgärden är klar returneras 200 (OK) med det skyddade objektets innehåll i svarstexten.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Detta bekräftar att skyddet är aktiverat för den virtuella datorn och den första säkerhetskopian kommer att utlösas enligt principschemat.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Utlösa en säkerhetskopiering på begäran för en skyddad Azure VM

När en Azure-dator har konfigurerats för säkerhetskopiering sker säkerhetskopieringar enligt principschemat. Du kan vänta på den första schemalagda säkerhetskopieringen eller utlösa en säkerhetskopiering på begäran när som helst. Kvarhållning för säkerhetskopiering på begäran är separat från bevarandet av säkerhetskopieringsprincipen och kan anges till en viss datumtid. Om det inte anges antas det vara 30 dagar från dagen för utlösaren av säkerhetskopiering på begäran.

Utlösa en on-demand backup *POST* är en POST-åtgärd.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

Den `{containerName}` `{protectedItemName}` och är som konstruerade [ovan](#responses-1). Den `{fabricName}` är "Azure". Till vårt exempel innebär detta att:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>Skapa förfrådetexten

För att utlösa en säkerhetskopiering på begäran följer följande komponenter i begärandetexten.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Egenskaper för BackupRequestResource         |

För en fullständig lista över definitioner av begärandetexten och annan information finns [i utlösarkopior för skyddade objekt REST API-dokument](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Exempel på begäran

Följande begärandetext definierar egenskaper som krävs för att utlösa en säkerhetskopia för ett skyddat objekt. Om kvarhållningen inte har angetts behålls den i 30 dagar från tidpunkten för utlösaren av säkerhetskopieringsjobbet.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Svar

Att utlösa en säkerhetskopiering på begäran är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Två svar returneras: 202 (Godkänd) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden är klar.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|202 Accepterad     |         |     Accepterad    |

#### <a name="example-responses"></a><a name="example-responses-3"></a>Exempel på svar

När du har skickat *POST-begäran* om säkerhetskopiering på begäran är det första svaret 202 (accepterad) med ett platshuvud eller Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Spåra sedan den resulterande åtgärden med hjälp av platshuvudet eller Azure-AsyncOperation-huvudet med ett enkelt *GET-kommando.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

När åtgärden är klar returneras 200 (OK) med ID:t för det resulterande säkerhetskopieringsjobbet i svarstexten.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Eftersom säkerhetskopieringsjobbet är en tidskrävande åtgärd måste det spåras enligt beskrivningen i [övervakarjobben med REST API-dokument](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Ändra konfigurationen för säkerhetskopiering för en skyddad Azure-virtuell dator

### <a name="changing-the-policy-of-protection"></a>Ändra skyddspolitiken

Om du vill ändra principen som den virtuella datorn är skyddad med kan du använda samma format som [att aktivera skydd](#enabling-protection-for-the-azure-vm). Ange bara det nya princip-ID:t i [begäran](#example-request-body) och skicka begäran. Om du till exempel vill ändra principen för testVM från 'DefaultPolicy' till 'ProdPolicy' anger du ProdPolicy-ID:t i begärandetexten.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Svaret kommer att följa samma format som nämnts [för att möjliggöra skydd](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Stoppa skyddet men behålla befintliga data

Om du vill ta bort skyddet för en skyddad virtuell dator men behålla de data som redan säkerhetskopierats tar du bort principen i begärandetexten och skickar begäran. När kopplingen till principen har tagits bort utlöses inte längre säkerhetskopior och inga nya återställningspunkter skapas.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Svaret kommer att följa samma format som nämns [för att utlösa en säkerhetskopiering på begäran](#example-responses-3). Det resulterande jobbet bör spåras enligt beskrivningen i [övervakarjobben med REST API-dokument](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Stoppa skydd och ta bort data

Om du vill ta bort skyddet för en skyddad virtuell dator och även ta bort säkerhetskopierade data utför du en borttagningsåtgärd som beskrivs [här](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

Att stoppa skyddet och ta bort data är en *DELETE-åtgärd.*

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Den `{containerName}` `{protectedItemName}` och är som konstruerade [ovan](#responses-1). `{fabricName}`är "Azure". Till vårt exempel innebär detta att:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses"></a><a name="responses-2"></a>Svar

*DELETE-skydd* är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Två svar returneras: 202 (Accepterad) när en annan åtgärd skapas och sedan 204 (NoContent) när åtgärden är klar.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|204 NoContent     |         |  Ingetinnehåll       |
|202 Accepterad     |         |     Accepterad    |

> [!IMPORTANT]
> För att skydda mot oavsiktliga borttagningsscenarier finns det en [mjuk borttagningsfunktion tillgänglig](use-restapi-update-vault-properties.md#soft-delete-state) för Recovery services vault. Om valvets mjuka borttagningstillstånd är inställt på aktiverat, kommer borttagningsåtgärden INTE omedelbart att ta bort data. Det kommer att hållas i 14 dagar och sedan permanent rensas. Kunden debiteras inte för lagring under denna 14-dagarsperiod. Om du vill ångra borttagningen läser du [avsnittet ångra borttagning](#undo-the-stop-protection-and-delete-data).

### <a name="undo-the-stop-protection-and-delete-data"></a>Ångra stoppskyddet och ta bort data

Ångra oavsiktlig borttagning liknar att skapa säkerhetskopieringsobjektet. När du har ångrat borttagningen behålls objektet men inga framtida säkerhetskopior utlöses.

Ångra borttagning är en *PUT-åtgärd* som är mycket lik [att ändra principen](#changing-the-policy-of-protection) och /eller aktivera [skyddet](#enabling-protection-for-the-azure-vm). Bara ge avsikten att ångra borttagningen med variabeln *isRehydrate* i [begäran kroppen](#example-request-body) och skicka begäran. Till exempel: För att ångra borttagningen för testVM bör följande begärandetext användas.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

Svaret kommer att följa samma format som nämns [för att utlösa en säkerhetskopiering på begäran](#example-responses-3). Det resulterande jobbet bör spåras enligt beskrivningen i [övervakarjobben med REST API-dokument](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Nästa steg

[Återställa data från en azure virtual machine-säkerhetskopiering](backup-azure-arm-userestapi-restoreazurevms.md).

Mer information om AZURE Backup REST API:er finns i följande dokument:

- [REST-API för Azure Recovery Services-provider](/rest/api/recoveryservices/)
- [Komma igång med Azure REST API](/rest/api/azure/)

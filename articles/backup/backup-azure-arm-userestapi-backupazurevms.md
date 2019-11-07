---
title: 'Azure Backup: säkerhetskopiera virtuella Azure-datorer med REST API'
description: Hantera säkerhets kopierings åtgärder för säkerhets kopiering av virtuella Azure-datorer med REST API
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: REST API; Azure VM-säkerhetskopiering; Återställning av Azure VM;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: dacurwin
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 837401256aa264a527e2323b055713f4bd8e8d1c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73671690"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Säkerhetskopiera en virtuell Azure-dator med hjälp av Azure Backup via REST API

Den här artikeln beskriver hur du hanterar säkerhets kopior för en virtuell Azure-dator med hjälp av Azure Backup via REST API. Konfigurera skydd för första gången för en tidigare oskyddad virtuell Azure-dator, Utlös en säkerhets kopiering på begäran för en skyddad virtuell Azure-dator och ändra säkerhets kopierings egenskaperna för en säkerhets kopie rad virtuell dator via REST API enligt beskrivningen här.

Se [skapa valv](backup-azure-arm-userestapi-createorupdatevault.md) och [skapa principer](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API själv studie kurser för att skapa nya valv och principer.

Anta att du vill skydda en virtuell dator "testVM" under en resurs grupp "testRG" till ett Recovery Services valv "testVault", som finns i resurs gruppen "testVaultRG", med standard principen (med namnet "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Konfigurera säkerhets kopiering för en oskyddad virtuell Azure-dator med hjälp av REST API

### <a name="discover-unprotected-azure-vms"></a>Identifiera oskyddade virtuella Azure-datorer

Först ska valvet kunna identifiera den virtuella Azure-datorn. Detta utlöses med hjälp av [uppdaterings åtgärden](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Det är en asynkron *post* -åtgärd som ser till att valvet får den senaste listan över alla oskyddade virtuella datorer i den aktuella prenumerationen och cachelagrar dem. När den virtuella datorn är "cachelagrad" kommer återställnings tjänster att kunna komma åt den virtuella datorn och skydda den.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

POST-URI: n har `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}``{fabricName}` parametrar. `{fabricName}` är "Azure". Som enligt vårt exempel är `{vaultName}` "testVault" och `{vaultresourceGroupName}` "testVaultRG". Eftersom alla parametrar som krävs anges i URI: n behöver du inte ha någon separat brödtext i begäran.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Svar

Åtgärden Uppdatera är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|204 inget innehåll     |         |  OK utan innehåll som returneras      |
|202 accepterad     |         |     Accept    |

##### <a name="example-responses"></a>Exempel svar

När *post* -begäran har skickats returneras ett 202-svar (accepterat).

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
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Spåra den resulterande åtgärden med hjälp av rubriken "location" med ett enkelt *Get* -kommando

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
```

När alla virtuella Azure-datorer har identifierats returnerar GET-kommandot ett 204-svar (inget innehåll). Valvet kan nu identifiera vilken virtuell dator som helst i prenumerationen.

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

### <a name="selecting-the-relevant-azure-vm"></a>Välja relevant virtuell Azure-dator

 Du kan bekräfta att "cachelagring" görs genom att [lista alla objekt](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) som kan skyddas under prenumerationen och hitta önskad virtuell dator i svaret. [Svaret på den här åtgärden](#example-responses-1) ger också information om hur Recovery Services identifierar en virtuell dator.  När du känner till mönstret kan du hoppa över det här steget och fortsätta att [aktivera skyddet](#enabling-protection-for-the-azure-vm)direkt.

Den här åtgärden är en *Get* -åtgärd.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*Hämta* URI har alla nödvändiga parametrar. Ingen ytterligare begär ande text krävs.

##### <a name="responses-1"></a>Registrera

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

##### <a name="example-responses-1"></a>Exempel svar

När *Get* -begäran har skickats returneras ett 200-svar (OK).

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
> Antalet värden i *Get* -svaret är begränsat till 200 för en sida. Använd fältet ' nextLink ' för att hämta URL: en för nästa uppsättning svar.

Svaret innehåller en lista över alla oskyddade virtuella Azure-datorer och varje `{value}` innehåller all information som krävs av Azure Recovery Service för att konfigurera säkerhets kopiering. Om du vill konfigurera säkerhets kopieringen noterar du `{name}` fältet och fältet `{virtualMachineId}` i `{properties}` avsnittet. Konstruera två variabler från dessa fält värden enligt vad som anges nedan.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "VM;" + `{name}`
- `{virtualMachineId}` används senare i [begär ande texten](#example-request-body)

I exemplet översätts ovanstående värden till:

- containerName = "iaasvmcontainer; iaasvmcontainerv2; testRG; testVM"
- protectedItemName = "VM; iaasvmcontainerv2; testRG; testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Aktiverar skydd för den virtuella Azure-datorn

När den aktuella virtuella datorn är "cachelagrad" och "identifierad" väljer du den princip som du vill skydda. Om du vill veta mer om befintliga principer i valvet, se [lista över princip-API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Välj sedan den [aktuella principen](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) genom att referera till princip namnet. Information om hur du skapar principer finns i [själv studie kursen skapa princip](backup-azure-arm-userestapi-createorupdatepolicy.md). "DefaultPolicy" är markerat i exemplet nedan.

Aktivering av skydd är en asynkron *placerings* åtgärd som skapar ett skyddat objekt.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

`{containerName}` och `{protectedItemName}` som skapats ovan. `{fabricName}` är "Azure". I vårt exempel översätts detta till:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Skapa begär ande texten

Om du vill skapa ett skyddat objekt följer du komponenterna i begär ande texten.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Egenskaper för ProtectedItem-resurs         |

En fullständig lista över definitioner av begär ande texten och annan information finns i [skapa skyddat objekt REST API dokument](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Exempel på begär ande text

Följande begär ande text definierar egenskaper som krävs för att skapa ett skyddat objekt.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}` är det `{virtualMachineId}` som anges ovan från [svar på lista objekt](#example-responses-1)som kan skyddas.

#### <a name="responses"></a>Svar

Att skapa ett skyddat objekt är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 accepterad     |         |     Accept    |

##### <a name="example-responses"></a>Exempel svar

När du skickar in *begäran om att skapa* eller uppdatera skyddade objekt är det första svaret 202 (accepteras) med ett plats huvud eller Azure-async-header.

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

Spåra sedan den resulterande åtgärden med hjälp av plats rubriken eller Azure-AsyncOperation-huvudet med ett enkelt *Get* -kommando.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

När åtgärden har slutförts returneras 200 (OK) med det skyddade objekt innehållet i svars texten.

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

Detta bekräftar att skyddet har Aktiver ATS för den virtuella datorn och att den första säkerhets kopian utlöses enligt princip schemat.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Utlös en säkerhets kopiering på begäran för en skyddad virtuell Azure-dator

När en virtuell Azure-dator har kon figurer ATS för säkerhets kopiering sker säkerhets kopieringarna enligt princip schemat. Du kan vänta på den första schemalagda säkerhets kopieringen eller utlösa en säkerhets kopiering på begäran när som helst. Kvarhållning för säkerhets kopiering på begäran är separat från säkerhets kopierings principens kvarhållning och kan anges till en viss datum tid. Om inget anges antas det vara 30 dagar från dagen för utlösaren av säkerhets kopiering på begäran.

Att utlösa en säkerhets kopiering på begäran är en *post* -åtgärd.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}` och `{protectedItemName}` som skapats [ovan](#responses-1). `{fabricName}` är "Azure". I vårt exempel översätts detta till:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2019-05-13
```

### <a name="create-the-request-body"></a>Skapa begär ande texten

Om du vill utlösa en säkerhets kopiering på begäran, följer du komponenterna i begär ande texten.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Egenskaper för BackupRequestResource         |

En fullständig lista över definitioner av begär ande texten och annan information finns i [Utlös säkerhets kopiering för skyddade objekt REST API dokument](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Exempel på begär ande text

Följande begär ande text definierar egenskaper som krävs för att utlösa en säkerhets kopia för ett skyddat objekt. Om kvarhållning inte anges kommer den att behållas i 30 dagar från den tidpunkt då säkerhets kopierings jobbet utlöses.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Svar

Att utlösa en säkerhets kopiering på begäran är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|202 accepterad     |         |     Accept    |

##### <a name="example-responses-3"></a>Exempel svar

När du har skickat in *post* -begäran för en säkerhets kopiering på begäran är det första svaret 202 (accepteras) med ett plats huvud eller Azure-async-header.

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
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Spåra sedan den resulterande åtgärden med hjälp av plats rubriken eller Azure-AsyncOperation-huvudet med ett enkelt *Get* -kommando.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

När åtgärden har slutförts returneras 200 (OK) med ID: t för det resulterande säkerhets kopierings jobbet i svars texten.

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

Eftersom säkerhets kopierings jobbet är en tids krävande åtgärd måste det spåras enligt beskrivningen i [övervaknings jobben med REST API-dokument](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Ändra säkerhets kopierings konfigurationen för en skyddad virtuell Azure-dator

### <a name="changing-the-policy-of-protection"></a>Ändra principen för skydd

Om du vill ändra principen som den virtuella datorn är skyddad med kan du använda samma format som att [Aktivera skydd](#enabling-protection-for-the-azure-vm). Ange bara det nya princip-ID: t i [begär ande texten](#example-request-body) och skicka begäran. Exempel: om du vill ändra principen för testVM från "DefaultPolicy" till "ProdPolicy" anger du "ProdPolicy"-ID: t i begär ande texten.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Svaret följer samma format som det som nämnts [för att aktivera skydd](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Stoppa skyddet men behåll befintliga data

Ta bort skyddet för en skyddad virtuell dator men Behåll de data som redan har säkerhetskopierats genom att ta bort principen i begär ande texten och skicka begäran. När associationen med principen tas bort utlöses inte säkerhets kopieringarna och inga nya återställnings punkter skapas.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Svaret följer samma format som det som nämnts [för att utlösa en säkerhets kopiering på begäran](#example-responses-3). Det resulterande jobbet bör spåras enligt beskrivningen i [övervaknings jobben med REST API-dokument](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Stoppa skyddet och ta bort data

Ta bort skyddet på en skyddad virtuell dator och ta bort säkerhetskopierade data också genom att utföra en borttagnings åtgärd som beskrivs [här](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

Att stoppa skyddet och ta bort data är en *borttagnings* åtgärd.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}` och `{protectedItemName}` som skapats [ovan](#responses-1). `{fabricName}` är "Azure". I vårt exempel översätts detta till:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

### <a name="responses-2"></a>Registrera

*Borttagning* av skydd är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som måste spåras separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd skapas och sedan 204 (nocontent) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|204 noåll     |         |  Inget innehåll       |
|202 accepterad     |         |     Accept    |

## <a name="next-steps"></a>Nästa steg

[Återställa data från en säkerhets kopia av en virtuell Azure-dator](backup-azure-arm-userestapi-restoreazurevms.md).

Mer information om Azure Backup REST API: er finns i följande dokument:

- [Azure Recovery Services-Provider REST API](/rest/api/recoveryservices/)
- [Komma igång med Azure REST API](/rest/api/azure/)

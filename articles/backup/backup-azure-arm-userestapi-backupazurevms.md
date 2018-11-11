---
title: 'Azure Backup: Säkerhetskopiera virtuella Azure-datorer med hjälp av REST API'
description: Hantera säkerhetskopiering åtgärder på Azure VM Backup med hjälp av REST API
services: backup
author: pvrk
manager: shivamg
keywords: 'REST API: ET Azure VM-säkerhetskopiering; Återställning av Azure virtuella datorer;'
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 8a47d3cf346d7961e9f8b1c4fa615a2faa6b1da0
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289787"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Säkerhetskopiera en virtuell Azure-dator med Azure Backup via REST-API

Den här artikeln beskriver hur du hanterar säkerhetskopior för en Azure virtuell dator med Azure Backup via REST-API. Konfigurera skydd för första gången för en tidigare oskyddade Azure-dator, utlösa en säkerhetskopiering på begäran för en skyddad virtuell Azure-dator och ändra egenskaper för säkerhetskopiering av en säkerhetskopierad virtuell dator via REST-API som beskrivs här.

Referera till [skapa valv](backup-azure-arm-userestapi-createorupdatevault.md) och [skapa princip](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API-självstudier för att skapa nya valv och principer.

Anta att du vill skydda en virtuell dator ”testVM” under en resource group ”testRG-namn” till ett Recovery Services-valv ”testVault”, finns i resursgruppen ”testVaultRG”, med standardprincipen (med namnet ”DefaultPolicy”).

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Konfigurera säkerhetskopiering för en oskyddad Azure-dator med hjälp av REST API

### <a name="discover-unprotected-azure-vms"></a>Identifiera oskyddade virtuella Azure-datorer

Först ska valvet kunna identifiera Azure VM. Detta aktiveras med hjälp av den [uppdatera åtgärden](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Det är en asynkron *POST* åtgärd som ser till att valvet hämtar den senaste listan över alla oskyddade virtuella datorer i den aktuella prenumerationen och '' lagrar dem i cacheminnet. När den virtuella datorn 'cachelagras ', kommer Recovery services att kunna komma åt den virtuella datorn och skydda den.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

POST-URI: N har `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}` parametrar. Den `{fabricName}` är ”Azure”. Enligt vårt exempel `{vaultName}` är ”testVault” och `{vaultresourceGroupName}` är ”testVaultRG”. Eftersom de obligatoriska parametrarna anges i URI: N, finns det inget behov av en separat begärandetexten.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Svar

Uppdateringen är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som kräver uppföljning separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd har skapats och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|204 inget innehåll     |         |  OK med inget innehåll returneras      |
|202-accepterad     |         |     Accepterad    |

##### <a name="example-responses"></a>Exempelsvar

När den *POST* begäran har skickats kan ett 202 (accepterad)-svar returneras.

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

Spåra resulterande igen med rubriken ”plats” med en enkel *hämta* kommando

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
```

När alla virtuella Azure-datorer identifieras returnerar kommandot GET svar 204 (inget innehåll). Valvet är nu kunna identifiera virtuella datorer i prenumerationen.

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

### <a name="selecting-the-relevant-azure-vm"></a>Att välja relevanta Azure-VM

 Du kan bekräfta att ”cachelagring” sker via [visa en lista över alla objekt som ska skyddas](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) under prenumerationen och leta upp den virtuella datorn i svaret. [Svaret på den här åtgärden](#example-responses-1) ger dig också information om hur återställningstjänster identifierar en virtuell dator.  När du är bekant med mönster, kan du hoppa över det här steget och fortsätta direkt till [att aktivera skydd](#enabling-protection-for-the-azure-vm).

Den här åtgärden är en *hämta* igen.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

Den *hämta* URI: N har de obligatoriska parametrarna. Inga ytterligare begärandetexten krävs.

#### <a name="responses"></a>Svar

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       Ok |

##### <a name="example-responses"></a>Exempelsvar

När den *hämta* begäran har skickats, returneras ett svar med 200 (OK).

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
> Antalet värden i en *hämta* svar är begränsad till 200 för en sida. Använd ”nextLink”-fältet för att hämta URL för nästa uppsättning svar.

Svaret innehåller en lista över alla oskyddade virtuella Azure-datorer och varje `{value}` innehåller all information som krävs av Azure Recovery-tjänsten för att konfigurera säkerhetskopiering. För att konfigurera säkerhetskopiering, notera den `{name}` fält och `{virtualMachineId}` i `{properties}` avsnitt. Skapa två variabler från värdena i fälten enligt beskrivningen nedan.

- containerName = ”iaasvmcontainer”; +`{name}`
- protectedItemName = ”vm”; + `{name}`
- `{virtualMachineId}` används senare i [begärandetexten](#example-request-body)

I det här exemplet Översätt ovanstående värden till:

- containerName = ”iaasvmcontainer; iaasvmcontainerv2; testRG; testVM”
- protectedItemName = ”vm; iaasvmcontainerv2; testRG; testVM”

### <a name="enabling-protection-for-the-azure-vm"></a>Att aktivera skydd för Azure-dator

När den relevanta virtuella datorn är ”cachelagrade” och ”identifierat”, väljer du principen för att skydda. Om du vill veta mer om befintliga principer i valvet, referera till [lista princip API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Välj sedan den [relevant princip](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) genom att referera till namnet på principen. Om du vill skapa principer som avser [skapa princip självstudien](backup-azure-arm-userestapi-createorupdatepolicy.md). ”DefaultPolicy” är markerad i det exemplet nedan.

Aktiverar skydd är en asynkron *PLACERA* åtgärd som skapar ett skyddat objekt.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

Den `{containerName}` och `{protectedItemName}` som konstruerats ovan. Den `{fabricName}` är ”Azure”. I vårt exempel innebär detta att:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

#### <a name="create-the-request-body"></a>Skapa begärandetexten

Här följer komponenterna i begärandetexten för att skapa ett skyddat objekt.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |ProtectedItem resursegenskaper         |

Den fullständiga listan över definitioner av begärandetexten och annan information finns i [skapa skyddat objekt REST API-dokumentet](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Exempel-begärandetexten

Följande begäran definierar egenskaper som krävs för att skapa ett skyddat objekt.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

Den `{sourceResourceId}` är den `{virtualMachineId}` nämndes ovan från den [svaret på listan över objekt som ska skyddas](#example-responses-1).

#### <a name="responses"></a>Svar

Skapandet av ett skyddat objekt är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som kräver uppföljning separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd har skapats och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  Ok       |
|202-accepterad     |         |     Accepterad    |

##### <a name="example-responses"></a>Exempelsvar

När du skickar in den *PLACERA* begäran för skyddat objekt skapas eller uppdatera, Rapid response är 202 (accepterad) med en platsrubrik eller Azure-async-rubrik.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Spåra resulterande åtgärden med en enkel platsrubrik eller Azure-AsyncOperation rubrik *hämta* kommando.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

När åtgärden har slutförts, returnerar 200 (OK) med innehåll för skyddat objekt i svarstexten.

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

Detta bekräftar att skyddsinställningarna för den virtuella datorn och den första säkerhetskopieringen Utlöses efter ett Principschema.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Utlösa en säkerhetskopiering på begäran för en skyddad virtuell Azure-dator

När en Azure-dator har konfigurerats för säkerhetskopiering, säkerhetskopieringar göras efter ett Principschema. Du kan vänta tills den första schemalagda säkerhetskopieringen eller utlösa en säkerhetskopiering på begäran när som helst. Kvarhållning för säkerhetskopieringar på begäran skiljer sig från backup policy kvarhållning och kan anges för att en viss tid. Om inte anges, antas den vara 30 dagar från dag då utlösaren för säkerhetskopiering på begäran.

Utlösa en säkerhetskopiering på begäran är en *POST* igen.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

Den `{containerName}` och `{protectedItemName}` som konstruerats [ovan](#responses-1). Den `{fabricName}` är ”Azure”. I vårt exempel innebär detta att:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>Skapa begärandetexten

Här följer komponenterna i begärandetexten för att utlösa en säkerhetskopiering på begäran.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource egenskaper         |

Den fullständiga listan över definitioner av begärandetexten och annan information finns i [utlösa säkerhetskopieringar för skyddade objekt REST API-dokumentet](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Exempel-begärandetexten

Följande begäran definierar egenskaper som krävs för att utlösa en säkerhetskopiering för ett skyddat objekt. Om kvarhållningen anges bevaras den i 30 dagar från utlösaren för säkerhetskopieringsjobbet.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Svar

Utlösa en säkerhetskopiering på begäran är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som kräver uppföljning separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd har skapats och sedan 200 (OK) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|202-accepterad     |         |     Accepterad    |

#### <a name="example-responses"></a>Exempelsvar

När du skickar in den *POST* begäran för en säkerhetskopiering på begäran första svar är 202 (accepterad) med en platsrubrik eller Azure-async-rubrik.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
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

Spåra resulterande åtgärden med en enkel platsrubrik eller Azure-AsyncOperation rubrik *hämta* kommando.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

När åtgärden har slutförts, returnerar 200 (OK) med ID: T för det resulterande säkerhetskopieringsjobbet i svarstexten.

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

Eftersom jobbet är en tidskrävande åtgärd, den måste spåras som beskrivs i den [övervaka jobb med hjälp av REST API-dokumentet](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Ändra konfigurationen för säkerhetskopiering för en skyddad virtuell Azure-dator

### <a name="changing-the-policy-of-protection"></a>Ändra policyn för skydd

Om du vill ändra principen som den virtuella datorn är skyddad, kan du använda samma format som [att aktivera skydd](#enabling-protection-for-the-azure-vm). Lämna den nya princip-ID i [begärandetexten](#example-request-body) och skicka begäran. För t.ex.: Om du vill ändra policyn för testVM från 'DefaultPolicy' till 'ProdPolicy', ange 'ProdPolicy' id i begärandetexten.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Svaret följer samma format som vi redan nämnt [för att aktivera skydd](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Avslutar skyddet men behåller befintliga data

Om du vill ta bort skydd på en skyddad virtuell dator men spara dina data som redan har säkerhetskopierats, ta bort principen i begärandetexten och skicka begäran. När kopplingen till principen tas bort, utlöses inte längre säkerhetskopior och inga nya återställningspunkter skapas.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Svaret följer samma format som vi redan nämnt [för att utlösa en säkerhetskopiering på begäran](#example-responses-3). Det resulterande jobbet som ska spåras enligt beskrivningen i den [övervaka jobb med hjälp av REST API-dokumentet](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Stoppa skyddet och ta bort data

Om du vill ta bort skyddet på en skyddad virtuell dator och ta bort dina säkerhetskopierade data, utföra en borttagningsåtgärd enligt anvisningarna [här](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

Stoppa skyddet och ta bort data är en *ta bort* igen.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

Den `{containerName}` och `{protectedItemName}` som konstruerats [ovan](#responses-1). `{fabricName}` är ”Azure”. I vårt exempel innebär detta att:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

### <a name="responses"></a>Svar

*Ta bort* protection är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som kräver uppföljning separat.

Den returnerar två svar: 202 (accepterad) när en annan åtgärd har skapats och sedan 204 (NoContent) när åtgärden har slutförts.

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202-accepterad     |         |     Accepterad    |

## <a name="next-steps"></a>Nästa steg

[Återställa data från en säkerhetskopiering av virtuella Azure-datorer](backup-azure-arm-userestapi-restoreazurevms.md).

Mer information om Azure Backup REST-API: er finns i följande dokument:

- [Azure Recovery Services-provider REST API](/rest/api/recoveryservices/)
- [Kom igång med Azure REST API](/rest/api/azure/)

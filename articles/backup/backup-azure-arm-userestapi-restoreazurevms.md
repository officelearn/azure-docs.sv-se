---
title: 'Azure Backup: Återställa virtuella Azure-datorer med hjälp av REST API'
description: Hantera återställningsåtgärder för säkerhetskopiering av Azure virtuella datorer med hjälp av REST API
services: backup
author: pvrk
manager: shivamg
keywords: 'REST API: ET Azure VM-säkerhetskopiering; Återställning av Azure virtuella datorer;'
ms.service: backup
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: pullabhk
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 4a65e8a855b9be797c1ceeacf4b74fea74697d00
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100223"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Återställa virtuella Azure-datorer med hjälp av REST API

När säkerhetskopian av en Azure virtuell dator med Azure Backup är klar kan en återställa hela virtuella Azure-datorer eller diskar eller filer från samma säkerhetskopian. Den här artikeln beskriver hur du återställer en Azure-dator eller diskar med hjälp av REST API.

För att återställa, en är att identifiera den relevanta återställningspunkten först.

## <a name="select-recovery-point"></a>Välj återställningspunkt

Tillgängliga återställningspunkter för en säkerhetskopieringsobjekt kan anges med hjälp av den [lista återställningspunkt REST API](https://docs.microsoft.com/rest/api/backup/recoverypoints/list). Det är en enkel *hämta* igen med alla relevanta värden.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2016-12-01
```

Den `{containerName}` och `{protectedItemName}` som konstruerats [här](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` är ”Azure”.

Den *hämta* URI: N har de obligatoriska parametrarna. Det finns inget behov av en ytterligare begärandetexten

### <a name="responses"></a>Svar

|Name  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       Ok  |

#### <a name="example-response"></a>Exempelsvar

När den *hämta* URI skickas, returneras ett svar med 200 (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Återställningspunkten har identifierats med den `{name}` i ovanstående svaret.

## <a name="restore-disks"></a>Återställ diskar

Om det finns ett behov av att anpassa skapandet av en virtuell dator från säkerhetskopierade data, en bara återställa diskar till en valda storage-konto och skapa en virtuell dator från dessa diskar enligt deras krav. Lagringskontot måste vara i samma region som recovery services-valvet och får inte vara zonredundant. Diskarna, samt konfiguration av säkerhetskopierade virtuella datorn (”vmconfig.json”) kommer att lagras i det angivna lagringskontot.

Utlöser återställning av diskar är en *POST* begäran. Om du vill veta mer om åtgärden Återställ diskar, referera till den [”aktivera återställning” REST API](https://docs.microsoft.com/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2016-12-01
```

Den `{containerName}` och `{protectedItemName}` som konstruerats [här](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` är ”Azure” och `{recoveryPointId}` är den `{name}` fältet för återställningspunkten som nämns [ovan](#example-response).

### <a name="create-request-body"></a>Skapa begärandetexten

Här följer komponenterna i begärandetexten för att utlösa en diskåterställning från en virtuell Azure-säkerhetskopiering.

|Name  |Typ  |Beskrivning  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Den fullständiga listan över definitioner av begärandetexten och annan information finns i [utlösa återställa REST API-dokumentet](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Exempelbegäran

Följande begäran definierar egenskaper som krävs för att utlösa en diskåterställning av.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="response"></a>Svar

Utlösningen av en återställning disk är en [asynkron åtgärd](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Det innebär att den här åtgärden skapar en annan åtgärd som kräver uppföljning separat.

Två svar returneras: 202 (accepterad) när en annan åtgärd har skapats och sedan 200 (OK) när åtgärden har slutförts.

|Name  |Typ  |Beskrivning  |
|---------|---------|---------|
|202-accepterad     |         |     Accepterad    |

#### <a name="example-responses"></a>Exempelsvar

När du skickar in den *POST* URI för att utlösa Återställ diskar är det första svaret 202 (accepterad) med en platsrubrik eller Azure-async-rubrik.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Spåra resulterande åtgärden med en enkel platsrubrik eller Azure-AsyncOperation rubrik *hämta* kommando.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
```

När åtgärden har slutförts, returnerar 200 (OK) med ID: T för den resulterande återställningsjobbet i svarstexten.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Eftersom jobbet är en tidskrävande åtgärd, den ska spåras enligt beskrivningen i den [övervaka jobb med hjälp av REST API-dokumentet](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

När det körs under lång tid jobbet har slutförts, att diskar och konfigurationen av den säkerhetskopierade virtuella datorn (”VMConfig.json”) finnas kvar i det angivna lagringskontot.

## <a name="restore-as-another-virtual-machine"></a>Återställa som en annan virtuell dator

[Välj återställningspunkten](#select-recovery-point) och skapa begärandetexten som anges nedan för att skapa ett annat virtuella Azure-dator med data från återställningspunkten.

Följande begäran definierar egenskaper som krävs för att utlösa en återställning av virtuell dator.

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2016-12-01",
      "parameters": {
        "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
        }
      }
    }
}
```

Svaret ska hanteras på samma sätt som [förklaras ovan för att återställa diskar](#response).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Backup REST-API: er finns i följande dokument:

- [Azure Recovery Services-provider REST API](/rest/api/recoveryservices/)
- [Kom igång med Azure REST API](/rest/api/azure/)
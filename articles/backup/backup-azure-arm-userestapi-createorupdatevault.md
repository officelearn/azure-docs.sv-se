---
title: 'Azure Backup: Skapa Recovery Services-valv med REST API'
description: hantera säkerhets kopierings-och återställnings åtgärder för Azure VM backup med REST API
author: pvrk
manager: shivamg
keywords: REST API; Azure VM-säkerhetskopiering; Återställning av Azure VM;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 0373098dd344df79be79871227f20c8a995958fa
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466947"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Skapa Azure Recovery Services Vault med REST API

Stegen för att skapa ett Azure Recovery Services-valv med REST API beskrivs i [skapa valv REST API](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) dokumentation. Låt oss använda det här dokumentet som referens för att skapa ett valv som heter "testVault" i "västra USA".

Använd *följande åtgärd* för att skapa eller uppdatera ett Azure Recovery Services-valv.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Skapa en förfrågan

`{subscription-id}` Parametern krävs för att du ska kunna skapa en *begäran* . Om du har flera prenumerationer kan du läsa mer i [arbeta med flera prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Du definierar en `{resourceGroupName}` och `{vaultName}` för dina `api-version` resurser, tillsammans med parametern. I den här `api-version=2016-06-01`artikeln används.

Följande rubriker krävs:

| Begär ande huvud   | Beskrivning |
|------------------|-----------------|
| *Innehålls typ:*  | Obligatoriskt. Ange till `application/json`. |
| *Authorization:* | Obligatoriskt. Ange en giltig `Bearer` [åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Mer information om hur du skapar begäran finns i [komponenter i en REST API begäran/svar](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Skapa begär ande texten

Följande vanliga definitioner används för att bygga en begär ande text:

|Namn  |Obligatorisk  |Typ  |Beskrivning  |
|---------|---------|---------|---------|
|eTag     |         |   Sträng      |  Valfri eTag       |
|location     |  true       |Sträng         |   Resursplats      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Egenskaper för valvet       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifierar det unika system-ID: t för varje Azure-resurs     |
|taggar     |         | Object        |     Resurstaggar    |

Observera att valv namnet och resurs gruppens namn anges i PLACERINGs-URI: n. I begär ande texten definieras platsen.

## <a name="example-request-body"></a>Exempel på begär ande text

Följande exempel text används för att skapa ett valv i "västra USA". Ange platsen. SKU: n är alltid "standard".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Responses

Det finns två lyckade svar för åtgärden att skapa eller uppdatera ett Recovery Services-valv:

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |   [Valv](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | Ok        |
|201 har skapats     | [Valv](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Skapad      |

Mer information om REST API svar finns i [bearbeta svars meddelandet](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Exempelsvar

Ett komprimerat *201* -svar från föregående exempel begär ande brödtext visar att ett *ID* har tilldelats och att *provisioningState* har *slutförts*:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Nästa steg

[Skapa en säkerhets kopierings princip för säkerhets kopiering av en virtuell Azure-dator i det här valvet](backup-azure-arm-userestapi-createorupdatepolicy.md).

Mer information om Azure REST-API: er finns i följande dokument:

- [Azure Recovery Services-Provider REST API](/rest/api/recoveryservices/)
- [Kom igång med Azure REST API](/rest/api/azure/)

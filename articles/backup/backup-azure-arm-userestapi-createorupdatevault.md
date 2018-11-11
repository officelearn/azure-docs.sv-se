---
title: 'Azure Backup: Skapa Recovery Services-valv med hjälp av REST API'
description: Hantera säkerhetskopiering och återställning av Azure VM Backup med hjälp av REST API
services: backup
author: pvrk
manager: shivamg
keywords: 'REST API: ET Azure VM-säkerhetskopiering; Återställning av Azure virtuella datorer;'
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 7d1a4e6b1093344d1217e8577a56f34cd3c1f52c
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289703"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Skapa Azure Recovery Services-valv med hjälp av REST API

Stegen för att skapa ett Azure Recovery Services-valv med hjälp av REST API beskrivs i [skapa valv REST API](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) dokumentation. Använd berätta det här dokumentet som referens för att skapa ett valv som heter ”testVault” i ”USA, västra”.

Använd följande för att skapa eller uppdatera ett Azure Recovery Services-valv, *PLACERA* igen.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Skapa en förfrågan

Att skapa den *PLACERA* begäran, den `{subscription-id}` parametern är obligatorisk. Om du har flera prenumerationer, se [arbeta med flera prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Du definierar en `{resourceGroupName}` och `{vaultName}` för dina resurser, tillsammans med den `api-version` parametern. Den här artikeln använder `api-version=2016-06-01`.

Följande huvuden krävs:

| Begärandehuvud   | Beskrivning |
|------------------|-----------------|
| *Content-Type:*  | Krävs. Ange `application/json`. |
| *Auktorisering:* | Krävs. Ange att ett giltigt `Bearer` [åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Mer information om hur du skapar begäran finns i [komponenterna i en REST API-begäran/svar](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Skapa begärandetexten

Följande vanliga definitioner används för att skapa en brödtext i begäran:

|Namn  |Krävs  |Typ  |Beskrivning  |
|---------|---------|---------|---------|
|eTag     |         |   Sträng      |  Valfritt eTag       |
|location     |  true       |Sträng         |   Resursplats      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Egenskaper för valvet       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifierar unika system-identifieraren för varje Azure-resurs     |
|tags     |         | Objekt        |     Resurstaggar    |

Observera att valvnamnet och resursgruppens namn anges i PUT URI. Begärandetexten definierar platsen.

## <a name="example-request-body"></a>Exempel-begärandetexten

Följande exempel brödtext används för att skapa ett valv i ”USA, västra”. Ange platsen. SKU: N är alltid ”Standard”.

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Svar

Det finns två lyckades för åtgärden att skapa eller uppdatera ett Recovery Services-valv:

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |   [Valv](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | Ok        |
|201 Skapad     | [Valv](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Skapad      |

Mer information om REST API-svar finns i [bearbeta svarsmeddelandet](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Exempelsvar

Ett komprimerat *201 Skapad* svar från föregående exempelbegäran body visar en *id* har tilldelats och *provisioningState* är *lyckades* :

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

[Skapa en princip för säkerhetskopiering för att säkerhetskopiera en virtuell Azure-dator i det här valvet](backup-azure-arm-userestapi-createorupdatepolicy.md).

Mer information om Azure REST API: er finns i följande dokument:

- [Azure Recovery Services-provider REST API](/rest/api/recoveryservices/)
- [Kom igång med Azure REST API](/rest/api/azure/)

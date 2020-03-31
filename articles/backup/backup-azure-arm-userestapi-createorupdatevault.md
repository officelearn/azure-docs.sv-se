---
title: Skapa Recovery Services-valv med REST API
description: I den här artikeln kan du läsa om hur du hanterar säkerhetskopiering och återställning av Azure VM Backup med REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173422"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Skapa Azure Recovery Services Vault med REST API

Stegen för att skapa ett Azure Recovery Services Vault med REST API beskrivs i [skapa REST API-dokumentation för valv.](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) Låt oss använda det här dokumentet som referens för att skapa ett valv som kallas "testVault" i "Västra USA".

Om du vill skapa eller uppdatera ett Azure Recovery Services-valv använder du följande *PUT-åtgärd.*

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Skapa en begäran

För att skapa *PUT-begäran* krävs parametern. `{subscription-id}` Om du har flera prenumerationer läser [du Arbeta med flera prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Du definierar a `{resourceGroupName}` och `{vaultName}` för `api-version` dina resurser, tillsammans med parametern. Den här `api-version=2016-06-01`artikeln använder .

Följande huvuden krävs:

| Begärandehuvud   | Beskrivning |
|------------------|-----------------|
| *Content-Type:*  | Krävs. Ange till `application/json`. |
| *Auktorisering:* | Krävs. Ange till en giltig -`Bearer` [åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Mer information om hur du skapar begäran finns i Komponenter i [en REST API-begäran/svar](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Skapa förfrådetexten

Följande vanliga definitioner används för att skapa en begärantext:

|Namn  |Krävs  |Typ  |Beskrivning  |
|---------|---------|---------|---------|
|Etag     |         |   String      |  Valfri eTag       |
|location     |  true       |String         |   Resursplats      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Egenskaper för valvet       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifierar den unika systemidentifieraren för varje Azure-resurs     |
|tags     |         | Objekt        |     Resurstaggar    |

Observera att valvets namn och resursgruppsnamn finns i PUT URI. Begärandetexten definierar platsen.

## <a name="example-request-body"></a>Exempel på begäran

Följande exempeltext används för att skapa ett valv i "Västra USA". Ange plats. SKU är alltid "Standard".

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

Det finns två lyckade svar för åtgärden för att skapa eller uppdatera ett Recovery Services-valv:

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |   [Valv](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Skapad     | [Valv](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Skapad      |

Mer information om REST API-svar finns [i Bearbeta svarsmeddelandet](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Exempelsvar

Ett komprimerat *201 Skapat* svar från föregående exempelbegäran visar att ett *ID* har tilldelats och *att etableringstaten* *har lyckats:*

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

[Skapa en princip för säkerhetskopiering av en Azure-dator i det här valvet](backup-azure-arm-userestapi-createorupdatepolicy.md).

Mer information om Azure REST API:er finns i följande dokument:

- [REST-API för Azure Recovery Services-provider](/rest/api/recoveryservices/)
- [Komma igång med Azure REST API](/rest/api/azure/)

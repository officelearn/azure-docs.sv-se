---
title: Skapa Recovery Services-valv med REST API
description: I den här artikeln lär du dig att hantera säkerhets kopierings-och återställnings åtgärder för Azure VM backup med REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173422"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Skapa Azure Recovery Services Vault med REST API

Stegen för att skapa ett Azure Recovery Services-valv med REST API beskrivs i [skapa valv REST API](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) dokumentation. Låt oss använda det här dokumentet som referens för att skapa ett valv som heter "testVault" i "västra USA".

Använd *följande åtgärd* för att skapa eller uppdatera ett Azure Recovery Services-valv.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Skapa en begäran

Parametern `{subscription-id}` måste anges *om du vill skapa en begäran* . Om du har flera prenumerationer kan du läsa mer i [arbeta med flera prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Du definierar en `{resourceGroupName}` och `{vaultName}` för dina resurser, tillsammans med `api-version`-parametern. Den här artikeln använder `api-version=2016-06-01`.

Följande huvuden krävs:

| Begärandehuvud   | Beskrivning |
|------------------|-----------------|
| *Content-Type:*  | Krävs. Ange till `application/json`. |
| *Auktorisering:* | Krävs. Ange till en giltig `Bearer`-[åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

Mer information om hur du skapar begäran finns i [komponenter i en REST API begäran/svar](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Skapa begär ande texten

Följande vanliga definitioner används för att bygga en begär ande text:

|Namn  |Krävs  |Typ  |Beskrivning  |
|---------|---------|---------|---------|
|eTag     |         |   Sträng      |  Valfri eTag       |
|location     |  true       |Sträng         |   Resurs plats      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Egenskaper för valvet       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifierar det unika system-ID: t för varje Azure-resurs     |
|taggar     |         | Objekt        |     Resurstaggar    |

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
|200 OK     |   [Valv](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
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
- [Komma igång med Azure REST API](/rest/api/azure/)

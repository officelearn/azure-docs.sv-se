---
title: Uppdatera Recovery Services valv konfiguration med REST API
description: I den här artikeln lär du dig hur du uppdaterar valv konfigurationen med REST API.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252368"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Uppdatera Azure Recovery Services Vault-konfigurationer med REST API

I den här artikeln beskrivs hur du uppdaterar säkerhets kopierings-relaterade konfigurationer i Azure Recovery Services Vault med REST API.

## <a name="soft-delete-state"></a>Läge för mjuk borttagning

Att ta bort säkerhets kopior av ett skyddat objekt är en viktig åtgärd som måste övervakas. För att skydda mot oavsiktlig borttagning har Azure Recovery Services Vault en funktion för mjuk borttagning. Den här funktionen gör det möjligt för kunder att återställa borttagna säkerhets kopior, om det behövs inom en tids period efter borttagningen.

Men det finns scenarier där den här funktionen inte krävs. Det går inte att ta bort ett Azure Recovery Services-valv om det finns säkerhets kopierings objekt i det, även mjuk borttagning av dem. Detta kan innebära ett problem om valvet måste tas bort omedelbart. Till exempel: distributions åtgärder rensar ofta de skapade resurserna i samma arbets flöde. En distribution kan skapa ett valv, konfigurera säkerhets kopior för ett objekt, göra en test återställning och sedan fortsätta att ta bort säkerhets kopierings objekt och valvet. Om valvet inte kan tas bort kan hela distributionen Miss lyckas. Att inaktivera mjuk borttagning är det enda sättet att garantera omedelbar borttagning.

Kunden måste därför noggrant välja om du vill inaktivera mjuk borttagning för ett visst valv beroende på scenariot. Mer information finns i artikeln om [mjuk borttagning](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Hämta läget för mjuk borttagning med REST API

Som standard aktive ras läget för mjuk borttagning för alla nyskapade Recovery Services-valv. Om du vill hämta/uppdatera status för mjuk borttagning för ett valv använder du säkerhets kopierings valvets konfiguration relaterad [REST API dokument](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs)

Om du vill hämta det aktuella läget för mjuk borttagning för ett valv använder du följande *Get* -åtgärd

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

Hämta URI har `{subscriptionId}`, `{vaultName}``{vaultresourceGroupName}` parametrar. I det här exemplet är `{vaultName}` "testVault" och `{vaultresourceGroupName}` är "testVaultRG". Eftersom alla parametrar som krävs anges i URI: n behöver du inte ha någon separat brödtext i begäran.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Svar

Det framgångs bara svaret för GET-åtgärden visas nedan:

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Exempelsvar

När GET-begäran har skickats returneras ett 200-svar (lyckades).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>Uppdatera läget för mjuk borttagning med REST API

Om du vill uppdatera det mjuka borttagnings läget för Recovery Services-valvet med REST API använder du följande *korrigerings* åtgärd

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

KORRIGERINGs-URI: n har `{subscriptionId}``{vaultName}``{vaultresourceGroupName}` parametrar. I det här exemplet är `{vaultName}` "testVault" och `{vaultresourceGroupName}` är "testVaultRG". Om vi ersätter URI: n med värdena ovan kommer URI: n att se ut så här.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Skapa begär ande texten

Följande vanliga definitioner används för att skapa en begär ande text

Mer information finns i REST API- [dokumentationen](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|Namn  |Krävs  |Typ  |Beskrivning  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  Valfri eTag       |
|location     |  true       |String         |   Resurs plats      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Egenskaper för valvet       |
|taggar     |         | Objekt        |     Resurstaggar    |

#### <a name="example-request-body"></a>Exempel på begär ande text

Följande exempel används för att uppdatera läget för mjuk borttagning till disabled.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Svar

Det framgångs bara svaret för åtgärden "PATCH" visas nedan:

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Exempelsvar

När PATCH-begäran har skickats returneras ett 200-svar (lyckades).

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>Nästa steg

[Skapa en säkerhets kopierings princip för säkerhets kopiering av en virtuell Azure-dator i det här valvet](backup-azure-arm-userestapi-createorupdatepolicy.md).

Mer information om Azure REST-API: er finns i följande dokument:

- [Azure Recovery Services-Provider REST API](/rest/api/recoveryservices/)
- [Komma igång med Azure REST API](/rest/api/azure/)

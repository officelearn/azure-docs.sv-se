---
title: Uppdatera valvkonfiguration för Återställningstjänster med REST API
description: I den här artikeln får du lära dig hur du uppdaterar valvens konfiguration med REST API.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 6cecbb18e0cd6f548e1688ef978f10dcee7d9fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252368"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>Uppdatera Azure Recovery Services Vault-konfigurationer med REST API

I den hÃ¤r artikeln beskrivs hur du uppdaterar ã¤ã¤ringar på ã¤6ã¤ringar på ã¶r ã¤ã¶kning av relaterade konfigurationer i Azure Recovery Services-valv med REST API.

## <a name="soft-delete-state"></a>Mjukt borttagningstillstånd

Att ta bort säkerhetskopior av ett skyddat objekt är en betydande åtgärd som måste övervakas. För att skydda mot oavsiktliga borttagningar har Azure Recovery Services-valvet en mjuk borttagningsfunktion. Med den här funktionen kan kunder återställa borttagna säkerhetskopior, om det behövs, inom en tidsperiod efter borttagningen.

Men det finns scenarier där den här funktionen inte krävs. Det går inte att ta bort ett Azure Recovery Services-valv om det finns säkerhetskopieringsobjekt i det, även mjuka bort. Detta kan utgöra ett problem om valvet måste tas bort omedelbart. Till exempel: distributionsåtgärder rensar ofta de skapade resurserna i samma arbetsflöde. En distribution kan skapa ett valv, konfigurera säkerhetskopior för ett objekt, göra en teståterställning och sedan fortsätta att ta bort säkerhetskopior och valvet. Om borttagningen av valvet misslyckas kan hela distributionen misslyckas. Att inaktivera mjuk borttagning är det enda sättet att garantera omedelbar borttagning.

Därför måste kunden noggrant välja om att inaktivera mjuk-borttagning för ett visst valv beroende på scenariot. Mer information finns i [artikeln för mjuk borttagning](backup-azure-security-feature-cloud.md#soft-delete).

### <a name="fetch-soft-delete-state-using-rest-api"></a>Hämta mjukt borttagningstillstånd med REST API

Som standard aktiveras tillståndet för mjuk borttagning för alla nyligen skapade Recovery Services-valv. Om du vill hämta/uppdatera tillståndet för mjuk borttagning för ett valv använder du säkerhetskopieringsvalvets config-relaterade [REST API-dokument](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs)

Om du vill hämta det aktuella tillståndet för mjuk borttagning för ett valv använder du följande *GET-åtgärd*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

GET-uri `{subscriptionId}`har `{vaultName}` `{vaultresourceGroupName}` parametrar för GET. I det `{vaultName}` här exemplet är "testVault" och `{vaultresourceGroupName}` är "testVaultRG". Eftersom alla nödvändiga parametrar anges i URI finns det inget behov av ett separat begärandeorgan.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>Svar

Det lyckade svaret för GET-åtgärden visas nedan:

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Exempelsvar

När GET-begäran har skickats returneras ett 200 -svar (lyckat) svar.

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

### <a name="update-soft-delete-state-using-rest-api"></a>Uppdatera mjukt borttagningstillstånd med REST API

Om du vill uppdatera det mjuka borttagningstillståndet för återställningstjänstvalvet med REST API använder du följande *PATCH-åtgärd*

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

PATCH-urien `{subscriptionId}` `{vaultName}`har `{vaultresourceGroupName}` , , parametrar. I det `{vaultName}` här exemplet är "testVault" och `{vaultresourceGroupName}` är "testVaultRG". Om vi ersätter URI med värdena ovan, kommer URI att se ut så här.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Skapa förfrådetexten

Följande vanliga definitioner används för att skapa en begärandetext

Mer information finns [i REST API-dokumentationen](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/update#request-body)

|Namn  |Krävs  |Typ  |Beskrivning  |
|---------|---------|---------|---------|
|Etag     |         |   String      |  Valfri eTag       |
|location     |  true       |String         |   Resursplats      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Egenskaper för valvet       |
|tags     |         | Objekt        |     Resurstaggar    |

#### <a name="example-request-body"></a>Exempel på begäran

Följande exempel används för att uppdatera läget för mjuk borttagning till "inaktiverat".

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses"></a>Svar

Det lyckade svaret för "PATCH"-åtgärden visas nedan:

|Namn  |Typ  |Beskrivning  |
|---------|---------|---------|
|200 OK     |   [BackupResourceVaultConfig](https://docs.microsoft.com/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | OK        |

##### <a name="example-response"></a>Exempelsvar

När "PATCH"-begäran har skickats returneras ett 200 -svar (lyckat) svar.

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

[Skapa en princip för säkerhetskopiering av en Azure-dator i det här valvet](backup-azure-arm-userestapi-createorupdatepolicy.md).

Mer information om Azure REST API:er finns i följande dokument:

- [REST-API för Azure Recovery Services-provider](/rest/api/recoveryservices/)
- [Komma igång med Azure REST API](/rest/api/azure/)

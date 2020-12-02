---
title: Azure Key Vault som Event Grid källa
description: Beskriver de egenskaper och schema som anges för Azure Key Vault händelser med Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 605502794f2f3aa4f4edd14b49efda5003b91146
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460394"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault som Event Grid källa

Den här artikeln innehåller egenskaper och schema för händelser i [Azure Key Vault](../key-vault/index.yml). En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md).

## <a name="event-grid-event-schema"></a>Event Grid-händelseschema

### <a name="available-event-types"></a>Tillgängliga händelse typer

Ett Azure Key Vault konto genererar följande händelse typer:

| Fullständigt händelse namn | Visnings namn för händelse | Description |
| ---------- | ----------- |---|
| Microsoft. CertificateNewVersionCreated | En ny version av certifikatet har skapats | Utlöses när ett nytt certifikat eller en ny certifikat version skapas. |
| Microsoft. CertificateNearExpiry | Certifikat snart upphör Ande | Utlöses när den aktuella versionen av certifikatet upphör att gälla. (Händelsen utlöses 30 dagar före förfallo datumet.) |
| Microsoft. CertificateExpired | Certifikatet har utgått | Utlöses när certifikatet har upphört att gälla. |
| Microsoft. KeyNewVersionCreated | Den nya nyckel versionen har skapats | Utlöses när en ny nyckel eller ny nyckel version skapas. |
| Microsoft. KeyNearExpiry | Nyckelns nära förfallo datum | Utlöses när den aktuella versionen av en nyckel håller på att gå ut. (Händelsen utlöses 30 dagar före förfallo datumet.) |
| Microsoft. nyckel-valvet har upphört att gälla | Nyckel upphörde | Utlöses när en nyckel har upphört att gälla. |
| Microsoft. SecretNewVersionCreated | Den hemliga nya versionen har skapats | Utlöses när en ny hemlighet eller ny hemlig version skapas. |
| Microsoft. SecretNearExpiry | Hemligt upphör Ande snart | Utlöses när den aktuella versionen av en hemlighet håller på att gå ut. (Händelsen utlöses 30 dagar före förfallo datumet.) |
| Microsoft. SecretExpired | Hemligt upphör Ande | Utlöses när en hemlighet har upphört att gälla. |
| Microsoft. VaultAccessPolicyChanged | Åtkomst principen för valvet har ändrats | Utlöses när en åtkomst princip på Key Vault ändrats. Det innehåller ett scenario när Key Vault behörighets modell ändras till/från Azure RBAC  |

### <a name="event-examples"></a>Händelse exempel

I följande exempel visas schema för **Microsoft. nyckel valv. SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

### <a name="event-properties"></a>Händelse egenskaper

En händelse har följande data på översta nivån:

| Egenskap | Typ | Description |
| ---------- | ----------- |---|
| id | sträng | ID för objektet som utlöste händelsen |
| vaultName | sträng | Nyckel valv namnet för objektet som utlöste den här händelsen |
| objectType | sträng | Typ av objekt som utlöste händelsen |
| objectName | sträng | Namnet på objektet som utlöste händelsen |
| version | sträng | Den version av objektet som utlöste händelsen |
| NBF | antal | Ej före-datum i sekunder sedan 1970-01-01T00:00:00Z för objektet som utlöste händelsen |
| exp | antal | Utgångs datumet i sekunder sedan 1970-01-01T00:00:00Z för objektet som utlöste händelsen |

## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner
|Rubrik  |Beskrivning  |
|---------|---------|
| [Övervaka Key Vault händelser med Azure Event Grid](../key-vault/general/event-grid-overview.md) | Översikt över att integrera Key Vault med Event Grid. |
| [Självstudie: skapa och övervaka Key Vault händelser med Event Grid](../key-vault/general/event-grid-logicapps.md) | Lär dig hur du konfigurerar Event Grid-meddelanden för Key Vault. |


## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
* Mer information om Key Vault finns i [Vad är Azure Key Vault?](../key-vault/general/overview.md)


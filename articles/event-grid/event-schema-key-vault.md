---
title: Händelse schema för Azure Event Grid Azure Key Vault
description: Beskriver de egenskaper och schema som anges för Azure Key Vault händelser med Azure Event Grid
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: c92352dd28b870c5f58dec0b82a8000f14a8e62d
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033529"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Event Grid händelse schema för Azure Key Vault (förhands granskning)

Den här artikeln innehåller egenskaper och schema för [Azure Key Vault](../key-vault/index.yml) händelser som för närvarande finns i för hands version. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md).

## <a name="available-event-types"></a>Tillgängliga händelse typer

Ett Azure Key Vault konto avger följande händelse typer:

| Fullständigt händelse namn | Visnings namn för händelse | beskrivning |
| ---------- | ----------- |---|
| Microsoft. CertificateNewVersionCreated | En ny version av certifikatet har skapats | Utlöses när ett nytt certifikat eller en ny certifikat version skapas |
| Microsoft. CertificateNearExpiry | Certifikat snart upphör Ande | Utlöses när den aktuella versionen av certifikatet upphör att gälla (Standardvärdet är 30 dagar före utgångs datum) |
| Microsoft. CertificateExpired | Certifikatet har utgått | Utlöses när certifikatet har förfallit |
| Microsoft. KeyNewVersionCreated | Den nya nyckel versionen har skapats | Utlöses när en ny nyckel eller ny nyckel version skapas |
| Microsoft. KeyNearExpiry | Nyckelns nära förfallo datum | Utlöses när den aktuella versionen av nyckeln håller på att gå ut (Standardvärdet är 30 dagar före utgångs datum) |
| Microsoft. nyckel-valvet har upphört att gälla | Nyckel upphörde | Utlöses när nyckeln har upphört att gälla |
| Microsoft. SecretNewVersionCreated | Den hemliga nya versionen har skapats | Utlöses när ny hemlighet eller ny hemlig version skapas |
| Microsoft. SecretNearExpiry | Hemligt upphör Ande snart | Utlöses när den aktuella versionen av hemligheten håller på att gå ut (Standardvärdet är 30 dagar före utgångs datum) |
| Microsoft. SecretExpired | Hemligt upphör Ande | Utlöses när hemligheten har upphört att gälla |

## <a name="event-examples"></a>Händelse exempel

I följande exempel visas schema för **Microsoft. SecretNewVersionCreated**.

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

## <a name="event-properties"></a>Händelse egenskaper

En händelse har följande data på översta nivån:

| Egenskap | Typ | Beskrivning |
| ---------- | ----------- |---|
| id | sträng | ID för objektet som utlöste händelsen. |
| vaultName | sträng | Nyckel valv namn för det objekt som utlöste händelsen. |
| objectType | sträng | Typ av objekt som utlöste händelsen |
| ObjectName | sträng | Namnet på objektet som utlöste händelsen |
| version | sträng | Den version av objektet som utlöste händelsen |
| NBF | nummer | Inte före datumet i sekunder sedan 1970-01-01T00:00:00Z för objektet som utlöste händelsen |
| EXP | nummer | Utgångs datumet i sekunder sedan 1970-01-01T00:00:00Z för objektet som utlöste händelsen |


## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
* Mer information om Key Vault/Event Grid-integrering finns i [övervaknings Key Vault med Azure Event Grid (för hands version)](../key-vault/event-grid-overview.md)
* En själv studie kurs om Key Vault/Event Grid-integrering finns i [How to: Route Key Vault Events to Automation Runbook (för hands version)](../key-vault/event-grid-tutorial.md).

- [Översikt över Azure Key Vault](../key-vault/key-vault-overview.md)
- [Översikt över Azure Event Grid](overview.md)
- [Övervaka Key Vault med Azure Event Grid (förhands granskning)](../key-vault/event-grid-overview.md)
- Gör [så här: dirigera Key Vault händelser till Automation Runbook (för hands version)](../key-vault/event-grid-tutorial.md).
- [Översikt över Azure Automation](../automation/index.yml)

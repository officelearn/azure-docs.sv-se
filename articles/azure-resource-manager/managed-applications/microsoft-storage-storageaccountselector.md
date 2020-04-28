---
title: StorageAccountSelector-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Storage. StorageAccountSelector UI för Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 4fcaf0e5842ce8a65175d2fc1dfa2483a1203b2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651896"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>GRÄNSSNITTs element för Microsoft. Storage. StorageAccountSelector

En kontroll för att välja ett nytt eller befintligt lagrings konto.

## <a name="ui-sample"></a>UI-exempel

Kontrollen visar standardvärdet.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Kontrollen gör det möjligt för användaren att skapa ett nytt lagrings konto eller välja ett befintligt lagrings konto.

![Microsoft. Storage. StorageAccountSelector New](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempel på utdata

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Anmärkningar

- Om `defaultValue.name` det här värdet anges verifieras automatiskt för unikhet. Om lagrings konto namnet inte är unikt måste användaren ange ett annat namn eller välja ett befintligt lagrings konto.
- Standardvärdet för `defaultValue.type` är **Premium_LRS**.
- Alla typer som inte anges `constraints.allowedTypes` i är dolda och alla typer som inte anges `constraints.excludedTypes` i visas. `constraints.allowedTypes`och `constraints.excludedTypes` är både valfria, men kan inte användas samtidigt.
- Om `options.hideExisting` är **Sant**kan användaren inte välja ett befintligt lagrings konto. Standardvärdet är **falskt**.

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

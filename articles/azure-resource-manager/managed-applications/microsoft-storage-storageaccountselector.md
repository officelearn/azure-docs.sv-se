---
title: UI-element för StorageAccountSelector
description: Beskriver användargränssnittselementet Microsoft.StorageAccountSelector för Azure-portalen.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 4fcaf0e5842ce8a65175d2fc1dfa2483a1203b2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651896"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI element

En kontroll för att välja ett nytt eller befintligt lagringskonto.

## <a name="ui-sample"></a>Exempel på användargränssnitt

Kontrollen visar standardvärdet.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Kontrollen gör det möjligt för användaren att skapa ett nytt lagringskonto eller välja ett befintligt lagringskonto.

![Microsoft.StorageAccountSelector ny](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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

- Om det `defaultValue.name` anges, valideras automatiskt för unikhet. Om lagringskontonamnet inte är unikt måste användaren ange ett annat namn eller välja ett befintligt lagringskonto.
- Standardvärdet för `defaultValue.type` är **Premium_LRS**.
- Alla typer som `constraints.allowedTypes` inte anges i döljs och `constraints.excludedTypes` alla typer som inte har angetts i visas. `constraints.allowedTypes`och `constraints.excludedTypes` är båda valfria, men kan inte användas samtidigt.
- Om `options.hideExisting` är **sant**kan användaren inte välja ett befintligt lagringskonto. Standardvärdet är **falskt**.

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

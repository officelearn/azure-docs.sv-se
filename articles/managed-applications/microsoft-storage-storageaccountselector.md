---
title: Azure StorageAccountSelector UI-element | Microsoft Docs
description: Beskriver Microsoft.Storage.StorageAccountSelector UI-element för Azure-portalen.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 4a18d629b959e4f1abf4ec2df28a31180efec89f
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261045"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI-element
En kontroll för att välja ett nytt eller befintligt lagringskonto.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

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

## <a name="remarks"></a>Kommentarer
- Om anges `defaultValue.name` verifieras automatiskt för unikhet. Om lagringskontonamnet inte är unikt, måste användaren ange ett annat namn eller välj ett befintligt lagringskonto.
- Standardvärdet för `defaultValue.type` är **Premium_LRS**.
- Någon typ som inte har angetts i `constraints.allowedTypes` är dolt och alla typer som inte har angetts i `constraints.excludedTypes` visas.
`constraints.allowedTypes` och `constraints.excludedTypes` både valfria, men kan inte användas samtidigt.
- Om `options.hideExisting` är **SANT**, kan användaren välja ett befintligt lagringskonto. Standardvärdet är **FALSKT**.


## <a name="sample-output"></a>Exempel på utdata
```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

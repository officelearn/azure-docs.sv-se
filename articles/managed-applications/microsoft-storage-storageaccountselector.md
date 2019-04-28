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
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: c6d4ef50645902aecd57ceb9fc48b7d99bf22d53
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104881"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI-element
En kontroll för att välja ett nytt eller befintligt lagringskonto.

## <a name="ui-sample"></a>UI-exempel

Kontrollen visar standardvärdet.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Kontrollen kan användaren skapa ett nytt lagringskonto eller välj ett befintligt lagringskonto.

![Microsoft.Storage.StorageAccountSelector new](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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
- Om anges `defaultValue.name` godkänns automatiskt för unikhet. Om namnet inte är unikt, måste användaren ange ett annat namn eller välj ett befintligt lagringskonto.
- Standardvärdet för `defaultValue.type` är **Premium_LRS**.
- Alla typer som inte har angetts i `constraints.allowedTypes` är dold, och alla typer som inte har angetts i `constraints.excludedTypes` visas. `constraints.allowedTypes` och `constraints.excludedTypes` båda är valfria, men kan inte användas samtidigt.
- Om `options.hideExisting` är **SANT**, användaren kan inte välja ett befintligt lagringskonto. Standardvärdet är **FALSKT**.

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
* En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element som finns i [CreateUiDefinition element](create-uidefinition-elements.md).

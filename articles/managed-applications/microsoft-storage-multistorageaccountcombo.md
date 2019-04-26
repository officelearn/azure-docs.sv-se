---
title: Azure MultiStorageAccountCombo UI-element | Microsoft Docs
description: Beskriver Microsoft.Storage.MultiStorageAccountCombo UI-element för Azure-portalen.
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
ms.openlocfilehash: 08b65770414e9ee1cb5e478427fe7654b2bb9a78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252430"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI element
En grupp av kontroller för att skapa flera lagringskonton med namn som inleds med ett Allmänt prefix.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Värdet för `defaultValue.prefix` är sammanfogat med en eller flera heltal för att generera sekvens av lagringskontonamn. Till exempel om `defaultValue.prefix` är **sa** och `count` är **2**, sedan lagringskontonamn **sa1** och **sa2** genereras. Genererade lagringskontonamn verifieras automatiskt för unikhet.
- Lagringskontonamn genereras lexicographically baserat på `count`. Till exempel om `count` är 10, så lagringskontonamn som avslutas med två siffror heltal (01, 02, 03).
- Standardvärdet för `defaultValue.prefix` är **null**, och för `defaultValue.type` är **Premium_LRS**.
- Alla typer som inte har angetts i `constraints.allowedTypes` är dold, och alla typer som inte har angetts i `constraints.excludedTypes` visas. `constraints.allowedTypes` och `constraints.excludedTypes` båda är valfria, men kan inte användas samtidigt.
- Förutom att generera lagringskontonamn, `count` används för att ange lämplig multiplikatorn för elementet. Den stöder ett statiskt värde som **2**, eller ett dynamiskt värde från ett annat element som `[steps('step1').storageAccountCount]`. Standardvärdet är **1**.

## <a name="sample-output"></a>Exempel på utdata

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element som finns i [CreateUiDefinition element](create-uidefinition-elements.md).

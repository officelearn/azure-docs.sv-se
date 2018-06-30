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
ms.openlocfilehash: f5fa81d53e1728e8f566a2a39aed8311828b20c7
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37108713"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI-element
En grupp av kontroller för att skapa flera lagringskonton med namn som börjar med en gemensam prefix.

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
- Värdet för `defaultValue.prefix` är sammanfogat med en eller flera heltal att generera sekvens av lagringskontonamn. Till exempel om `defaultValue.prefix` är **sa** och `count` är **2**, sedan lagringskontonamn **sa1** och **sa2** genereras. Genererade lagringskontonamn verifiera för unika automatiskt.
- Lagringskontonamn genereras lexicographically baserat på `count`. Till exempel om `count` är 10 och lagringskontonamn sluta med två siffror heltal (01, 02, 03).
- Standardvärdet för `defaultValue.prefix` är **null**, och för `defaultValue.type` är **Premium_LRS**.
- Någon typ som inte har angetts i `constraints.allowedTypes` är dolt och alla typer som inte har angetts i `constraints.excludedTypes` visas. `constraints.allowedTypes` och `constraints.excludedTypes` både valfria, men kan inte användas samtidigt.
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
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

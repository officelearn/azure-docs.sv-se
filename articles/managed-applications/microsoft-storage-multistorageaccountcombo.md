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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a4ec5a97f8655c0b5b53dea129d4648a05f6ef85
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34261164"
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
- Värdet för `defaultValue.prefix` är sammanfogat med en eller flera heltal att generera sekvens av lagringskontonamn. Till exempel om `defaultValue.prefix` är **foobar** och `count` är **2**, sedan lagringskontonamn **foobar1** och **foobar2** genereras. Genererade lagringskontonamn verifiera för unika automatiskt.
- Lagringskontonamn genereras lexicographically baserat på `count`. Till exempel om `count` är 10 och lagringskontonamn avslutas med 2-siffrig heltal (01, 02, 03, etc.).
- Standardvärdet för `defaultValue.prefix` är **null**, och för `defaultValue.type` är **Premium_LRS**.
- Någon typ som inte har angetts i `constraints.allowedTypes` är dolt och alla typer som inte har angetts i `constraints.excludedTypes` visas.
`constraints.allowedTypes` och `constraints.excludedTypes` både valfria, men kan inte användas samtidigt.
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

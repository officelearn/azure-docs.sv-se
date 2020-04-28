---
title: MultiStorageAccountCombo-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Storage. MultiStorageAccountCombo UI för Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75651883"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>GRÄNSSNITTs element för Microsoft. Storage. MultiStorageAccountCombo

En grupp kontroller för att skapa flera lagrings konton med namn som börjar med ett gemensamt prefix.

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

## <a name="sample-output"></a>Exempel på utdata

```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="remarks"></a>Anmärkningar

- Värdet för `defaultValue.prefix` kombineras med ett eller flera heltal för att generera sekvensen av lagrings konto namn. `defaultValue.prefix` Om till exempel är **sa** och `count` är **2**genereras lagrings konto namn **SA1** och **SA2** . Genererade lagrings konto namn verifieras automatiskt för unika data.
- Lagrings konto namnen genereras lexicographically baserat på `count`. Om `count` till exempel är 10 är lagrings kontots namn slut med tvåsiffriga heltal (01, 02, 03).
- Standardvärdet för `defaultValue.prefix` är **Null**och för `defaultValue.type` är **Premium_LRS**.
- Alla typer som inte anges `constraints.allowedTypes` i är dolda och alla typer som inte anges `constraints.excludedTypes` i visas. `constraints.allowedTypes`och `constraints.excludedTypes` är både valfria, men kan inte användas samtidigt.
- Förutom att skapa lagrings konto namn `count` används för att ange lämplig multiplikator för elementet. Det stöder ett statiskt värde, till exempel **2**, eller ett dynamiskt värde från ett annat `[steps('step1').storageAccountCount]`element, t. ex.. Standardvärdet är **1**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

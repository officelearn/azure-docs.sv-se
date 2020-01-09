---
title: MultiStorageAccountCombo-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Storage. MultiStorageAccountCombo UI för Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
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

- Värdet för `defaultValue.prefix` sammanfogas med ett eller flera heltal för att generera sekvensen av lagrings konto namn. Om `defaultValue.prefix` till exempel är **sa** och `count` är **2**genereras lagrings konto namn **SA1** och **SA2** . Genererade lagrings konto namn verifieras automatiskt för unika data.
- Lagrings konto namnen genereras lexicographically baserat på `count`. Om `count` till exempel är 10 är lagrings kontots namn slut med tvåsiffriga heltal (01, 02, 03).
- Standardvärdet för `defaultValue.prefix` är **Null**och för `defaultValue.type` **Premium_LRS**.
- Alla typer som inte anges i `constraints.allowedTypes` är dolda och alla typer som inte anges i `constraints.excludedTypes` visas. `constraints.allowedTypes` och `constraints.excludedTypes` är båda valfria, men kan inte användas samtidigt.
- Förutom att skapa lagrings konto namn används `count` för att ange lämplig multiplikator för elementet. Det stöder ett statiskt värde, till exempel **2**, eller ett dynamiskt värde från ett annat element, som `[steps('step1').storageAccountCount]`. Standardvärdet är **1**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

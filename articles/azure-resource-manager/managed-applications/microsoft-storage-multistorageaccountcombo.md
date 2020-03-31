---
title: MultiStorageAccountCombo UI-element
description: I artikeln beskrivs användargränssnittselementet Microsoft.Storage.MultiStorageAccountCombo för Azure portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 06412a1f08f1f242a3f3bd9be17b795ee09fcf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651883"
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI-element

En grupp kontroller för att skapa flera lagringskonton med namn som börjar med ett gemensamt prefix.

## <a name="ui-sample"></a>Exempel på användargränssnitt

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

- Värdet för `defaultValue.prefix` sammanfogas med ett eller flera heltal för att generera sekvensen av lagringskontonamn. Om till `defaultValue.prefix` exempel är `count` **sa** och är **2**genereras lagringskontonamn **sa1** och **sa2.** Genererade lagringskontonamn valideras automatiskt för unikhet.
- Lagringskontonamnen genereras lexikografiskt `count`baserat på . Om det `count` till exempel är 10 slutar lagringskontonamnen med tvåsiffriga heltal (01, 02, 03).
- Standardvärdet för `defaultValue.prefix` är **null** `defaultValue.type` och för är **Premium_LRS**.
- Alla typer som `constraints.allowedTypes` inte anges i döljs och `constraints.excludedTypes` alla typer som inte har angetts i visas. `constraints.allowedTypes`och `constraints.excludedTypes` är båda valfria, men kan inte användas samtidigt.
- Förutom att generera lagringskontonamn används `count` för att ställa in lämplig multiplikator för elementet. Den stöder ett statiskt värde, som **2**, `[steps('step1').storageAccountCount]`eller ett dynamiskt värde från ett annat element, till exempel . Standardvärdet är **1**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

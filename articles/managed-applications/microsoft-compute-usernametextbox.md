---
title: Azure UserNameTextBox UI-element | Microsoft Docs
description: Beskriver Microsoft.Compute.UserNameTextBox UI-element för Azure-portalen.
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 88ab13329a719ba1e1b8a7b5fba2f7a2d381eca2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700846"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI-element
En textrutekontroll med inbyggda verifieringen för Windows och Linux-användarnamn.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Om `constraints.required` är inställd på **SANT**, textrutan måste ha ett värde kan valideras. Standardvärdet är **SANT**.
- `osPlatform` måste anges och kan vara antingen **Windows** eller **Linux**.
- `constraints.regex` är en JavaScript-mönster för reguljärt uttryck. Om anges måste i textrutan värdet matcha mönstret kan valideras. Standardvärdet är **null**.
- `constraints.validationMessage` är en sträng visas när textrutans värde misslyckas verifieringen som anges av `constraints.regex`. Om den inte anges används den textrutan inbyggda verifieringsmeddelanden. Standardvärdet är **null**.
- Det här elementet har inbyggda verifiering som baseras på det angivna värdet för `osPlatform`. Inbyggda verifieringen kan användas tillsammans med ett anpassat reguljärt uttryck. Om ett värde för `constraints.regex` har angetts, både inbyggda och anpassade verifieringar utlöses.

## <a name="sample-output"></a>Exempel på utdata
```json
"Example name"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element som finns i [CreateUiDefinition element](create-uidefinition-elements.md).

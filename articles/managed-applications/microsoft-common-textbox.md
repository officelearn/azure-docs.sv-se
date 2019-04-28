---
title: Azure TextBox UI-element | Microsoft Docs
description: Beskriver Microsoft.Common.TextBox UI-element för Azure-portalen.
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: b06e8b49efe8b6de720fa9bb819d4720e4f80fb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61044564"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI-element
En kontroll som kan användas för att redigera oformaterad text.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Om `constraints.required` är inställd på **SANT**, textrutan måste ha ett värde kan valideras. Standardvärdet är **FALSKT**.
- `constraints.regex` är en JavaScript-mönster för reguljärt uttryck. Om anges måste i textrutan värdet matcha mönstret kan valideras. Standardvärdet är **null**.
- `constraints.validationMessage` är en sträng visas när textrutans värde misslyckas verifieringen. Om den inte anges används den textrutan inbyggda verifieringsmeddelanden. Standardvärdet är **null**.
- Det är möjligt att ange ett värde för `constraints.regex` när `constraints.required` är inställd på **FALSKT**. I det här scenariot krävs för textrutan kan valideras inte ett värde. Om ett sådant anges måste det matcha mönster för reguljärt uttryck.

## <a name="sample-output"></a>Exempel på utdata

```json
"my text value"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element som finns i [CreateUiDefinition element](create-uidefinition-elements.md).

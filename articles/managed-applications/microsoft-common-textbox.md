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
ms.openlocfilehash: fa3e5fff8080acb9e824ffe27f6c149054804830
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063654"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI-element
En kontroll som används för att redigera oformaterad text.

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
- Om `constraints.required` är inställd på **SANT**, textrutan måste ha ett värde som ska valideras. Standardvärdet är **FALSKT**.
- `constraints.regex` är en JavaScript-mönstret för reguljära uttryck. Om anges måste textrutans värde matcha mönstret som ska valideras. Standardvärdet är **null**.
- `constraints.validationMessage` är en sträng som ska visas när textrutans värde inte verifieringen. Om den inte anges används den textruta inbyggda verifieringsmeddelanden. Standardvärdet är **null**.
- Det är möjligt att ange ett värde för `constraints.regex` när `constraints.required` är inställd på **FALSKT**. I det här scenariot krävs för textrutan kan valideras inte ett värde. Om en anges måste den matcha mönstret för reguljära uttryck.

## <a name="sample-output"></a>Exempel på utdata

```json
"my text value"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

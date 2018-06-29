---
title: Azure OptionsGroup UI-element | Microsoft Docs
description: Beskriver Microsoft.Common.OptionsGroup UI-element för Azure-portalen.
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
ms.openlocfilehash: e51061dc781e4ec6e822cde9cc450887ff3b1368
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061193"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup UI-element
En markering med en rad med tillgängliga alternativ.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Etikett för `constraints.allowedValues` är texten som visas för ett objekt och dess värde är värdet av elementet när du valt.
- Om du standardvärdet måste vara en etikett som finns i `constraints.allowedValues`. Om inget anges det första objektet i `constraints.allowedValues` är markerad som standard. Standardvärdet är **null**.
- `constraints.allowedValues` måste ha minst ett objekt.

## <a name="sample-output"></a>Exempel på utdata
```json
"two"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

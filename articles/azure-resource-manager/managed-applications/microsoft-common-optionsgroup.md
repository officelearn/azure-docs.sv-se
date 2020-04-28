---
title: OptionsGroup-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Common. OptionsGroup UI för Azure Portal. Gör att användarna kan välja bland tillgängliga alternativ när de distribuerar ett hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75652351"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Elementet Microsoft. Common. OptionsGroup UI

En markerings kontroll med en rad med tillgängliga alternativ.

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

## <a name="sample-output"></a>Exempel på utdata

```json
"two"
```

## <a name="remarks"></a>Anmärkningar

- Etiketten för `constraints.allowedValues` är visnings texten för ett objekt och dess värde är utdata-värdet för elementet när det är markerat.
- Om det här alternativet anges måste standardvärdet vara en etikett `constraints.allowedValues`i. Om inget värde anges väljs det första `constraints.allowedValues` objektet i som standard. Standardvärdet är **Null**.
- `constraints.allowedValues`måste ha minst ett objekt.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

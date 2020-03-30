---
title: Gränssnittselement för OptionsGroup
description: I artikeln beskrivs användargränssnittselementet Microsoft.Common.OptionsGroup för Azure-portalen. Gör det möjligt för användare att välja bland tillgängliga alternativ när de distribuerar ett hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652351"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft.Common.OptionsGroup användargränssnittselement

En markeringskontroll med en rad tillgängliga alternativ.

## <a name="ui-sample"></a>Exempel på användargränssnitt

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

- Etiketten för `constraints.allowedValues` är visningstexten för ett objekt och dess värde är utdatavärdet för elementet när det är markerat.
- Om det anges måste standardvärdet vara `constraints.allowedValues`en etikett som finns i . Om inget anges markeras `constraints.allowedValues` det första objektet i som standard. Standardvärdet är **null**.
- `constraints.allowedValues`måste ha minst en vara.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

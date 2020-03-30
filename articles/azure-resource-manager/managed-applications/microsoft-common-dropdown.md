---
title: Gränssnittselement för listruta
description: I artikeln beskrivs användargränssnittselementet Microsoft.Common.DropDown för Azure-portalen. Används för att välja bland tillgängliga alternativ när du distribuerar ett hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652390"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.List-gränssnittselement

En markeringskontroll med en listruta.

## <a name="ui-sample"></a>Exempel på användargränssnitt

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
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
- Om det anges måste standardvärdet vara `constraints.allowedValues`en etikett som finns i . Om inget anges markeras `constraints.allowedValues` det första objektet i. Standardvärdet är **null**.
- `constraints.allowedValues`måste ha minst en vara.
- Om du vill emulera ett värde som inte `""` krävs lägger `constraints.allowedValues`du till ett objekt med en etikett och värdet (tom sträng) i .

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

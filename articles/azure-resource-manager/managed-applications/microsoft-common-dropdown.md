---
title: List Rute GRÄNSSNITTs element
description: Beskriver UI-elementet Microsoft. Common. list Rute gränssnitt för Azure Portal. Använd om du vill välja mellan tillgängliga alternativ när du distribuerar ett hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a09f9695c18f368a585dbcd0d1e654dee4adfa03
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652390"
---
# <a name="microsoftcommondropdown-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. list rutan

En markerings kontroll med en nedrullningsbar lista.

## <a name="ui-sample"></a>UI-exempel

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

- Etiketten för `constraints.allowedValues` är visnings texten för ett objekt och dess värde är utdata-värdet för elementet när det är markerat.
- Om det här alternativet anges måste standardvärdet vara en etikett i `constraints.allowedValues`. Om inget anges väljs det första objektet i `constraints.allowedValues`. Standardvärdet är **Null**.
- `constraints.allowedValues` måste ha minst ett objekt.
- För att emulera ett värde som inte krävs lägger du till ett objekt med en etikett och ett värde för `""` (tom sträng) för att `constraints.allowedValues`.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

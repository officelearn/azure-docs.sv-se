---
title: Gränssnittselement för TextBox
description: I artikeln beskrivs elementet Microsoft.Common.TextBox UI för Azure Portal. Används för att lägga till oformaterad text.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652286"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft.Common.TextBox UI-element

En kontroll som kan användas för att redigera oformaterad text.

## <a name="ui-sample"></a>Exempel på användargränssnitt

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

## <a name="sample-output"></a>Exempel på utdata

```json
"my text value"
```

## <a name="remarks"></a>Anmärkningar

- Om `constraints.required` värdet är **true**måste textrutan ha ett värde för att valideras. Standardvärdet är **falskt**.
- `constraints.regex`är ett JavaScript-mönster för reguljära uttryck. Om det anges måste textrutans värde matcha mönstret för att validera det. Standardvärdet är **null**.
- `constraints.validationMessage`är en sträng som ska visas när textrutans värde misslyckas med valideringen. Om inget anges används textrutans inbyggda valideringsmeddelanden. Standardvärdet är **null**.
- Det är möjligt att ange `constraints.regex` `constraints.required` ett värde för när är inställt på **false**. I det här fallet krävs inte ett värde för att textrutan ska valideras. Om en har angetts måste den matcha mönstret för reguljära uttryck.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

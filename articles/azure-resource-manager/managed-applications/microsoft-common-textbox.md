---
title: UI-element för text rutor
description: Beskriver elementet Microsoft. Common. text Rute gränssnitt för Azure Portal. Använd för att lägga till oformaterad text.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652286"
---
# <a name="microsoftcommontextbox-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. text Rute

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

## <a name="sample-output"></a>Exempel på utdata

```json
"my text value"
```

## <a name="remarks"></a>Kommentarer

- Om `constraints.required` är inställt på **Sant**måste text rutan ha ett värde för att kunna verifiera. Standardvärdet är **falskt**.
- `constraints.regex`är ett mönster för reguljära JavaScript-uttryck. Om det här alternativet anges måste text rutans värde matcha mönstret för att kunna verifieras. Standardvärdet är **Null**.
- `constraints.validationMessage`är en sträng som ska visas när text rutans värde inte kan verifieras. Om inget värde anges används text rutans inbyggda verifierings meddelanden. Standardvärdet är **Null**.
- Det är möjligt att ange ett värde för `constraints.regex` när `constraints.required` är inställt på **falskt**. I det här scenariot krävs inget värde för att text rutan ska verifieras korrekt. Om en anges måste den matcha mönstret för reguljära uttryck.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

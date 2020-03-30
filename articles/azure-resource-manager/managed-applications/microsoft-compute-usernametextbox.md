---
title: Gränssnittselement för UserNameTextBox
description: I artikeln beskrivs användargränssnittselementet Microsoft.Compute.UserNameTextBox för Azure portal. Gör det möjligt för användare att ange Windows- eller Linux-användarnamn.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c7544ae7d872a64547cb6c57ce8af9a09fc6c3d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651909"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI-element

En textrutekontroll med inbyggd validering för Windows- och Linux-användarnamn.

## <a name="ui-sample"></a>Exempel på användargränssnitt

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

## <a name="sample-output"></a>Exempel på utdata

```json
"Example name"
```

## <a name="remarks"></a>Anmärkningar

- Om `constraints.required` värdet är **true**måste textrutan ha ett värde för att valideras. Standardvärdet är **sant**.
- `osPlatform`måste anges, och kan vara antingen **Windows** eller **Linux**.
- `constraints.regex`är ett JavaScript-mönster för reguljära uttryck. Om det anges måste textrutans värde matcha mönstret för att validera det. Standardvärdet är **null**.
- `constraints.validationMessage`är en sträng som ska visas när textrutans `constraints.regex`värde misslyckas med den validering som anges av . Om inget anges används textrutans inbyggda valideringsmeddelanden. Standardvärdet är **null**.
- Det här elementet har inbyggd validering som baseras `osPlatform`på det värde som angetts för . Den inbyggda valideringen kan användas tillsammans med ett anpassat reguljärt uttryck. Om ett `constraints.regex` värde för anges utlöses både de inbyggda och anpassade valideringarna.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

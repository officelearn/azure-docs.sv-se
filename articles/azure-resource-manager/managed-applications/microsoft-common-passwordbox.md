---
title: Gränssnittselement för PasswordBox
description: I artikeln beskrivs användargränssnittselementet Microsoft.Common.PasswordBox för Azure portal. Gör det möjligt för användare att ange ett hemligt värde när de distribuerar hanterade program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652312"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI-element

En kontroll som kan användas för att ange och bekräfta ett lösenord.

## <a name="ui-sample"></a>Exempel på användargränssnitt

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempel på utdata

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Anmärkningar

- Det här elementet `defaultValue` stöder inte egenskapen.
- Information om `constraints`implementering i finns i [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Om `options.hideConfirmation` värdet är **true**döljs den andra textrutan för att bekräfta användarens lösenord. Standardvärdet är **falskt**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

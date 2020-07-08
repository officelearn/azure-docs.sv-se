---
title: GRÄNSSNITTs element för lösen ord
description: Beskriver användar gränssnitts elementet Microsoft. Common. password för Azure Portal. Gör att användarna kan ange ett hemligt värde när de distribuerar hanterade program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652312"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. password

En kontroll som kan användas för att ange och bekräfta ett lösen ord.

## <a name="ui-sample"></a>UI-exempel

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

## <a name="remarks"></a>Kommentarer

- Det här elementet har inte stöd för `defaultValue` egenskapen.
- Information om implementeringen av `constraints` finns i [Microsoft. Common. text ruta](microsoft-common-textbox.md).
- Om `options.hideConfirmation` är inställt på **Sant**döljs den andra text rutan för att bekräfta användarens lösen ord. Standardvärdet är **falskt**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

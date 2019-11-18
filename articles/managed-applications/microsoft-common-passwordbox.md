---
title: GRÄNSSNITTs element för Azure-lösenord | Microsoft Docs
description: Beskriver användar gränssnitts elementet Microsoft. Common. password för Azure Portal. Gör att användarna kan ange ett hemligt värde när de distribuerar hanterade program.
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
ms.openlocfilehash: ab51a4096745c2930199685ac678638a956f21e0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151542"
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

- Det här elementet har inte stöd för egenskapen `defaultValue`.
- Implementerings information om `constraints`finns i [Microsoft. Common. text ruta](microsoft-common-textbox.md).
- Om `options.hideConfirmation` är inställt på **Sant**, döljs den andra text rutan för att bekräfta användarens lösen ord. Standardvärdet är **FALSKT**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

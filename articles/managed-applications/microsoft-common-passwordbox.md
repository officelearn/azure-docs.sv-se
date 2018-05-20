---
title: Azure PasswordBox UI-element | Microsoft Docs
description: Beskriver Microsoft.Common.PasswordBox UI-element för Azure-portalen.
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: eab4bea750c28ac3b156363f5f63d8c7c8f4075a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI-element
En kontroll som kan användas för att ange och bekräfta ett lösenord.

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
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Det här elementet har inte stöd för den `defaultValue` egenskapen.
- För implementeringsinformation om `constraints`, se [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Om `options.hideConfirmation` är inställd på **SANT**, andra textrutan för att bekräfta användarens lösenord är dolt. Standardvärdet är **FALSKT**.

## <a name="sample-output"></a>Exempel på utdata
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

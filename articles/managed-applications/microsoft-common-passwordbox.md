---
title: "Azure hanterade program PasswordBox gränssnittselement | Microsoft Docs"
description: "Beskriver Microsoft.Common.PasswordBox UI-element för hanterade program i Azure"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6d9f2b7cf56375d3a609cff20e928307c13bf2b8
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI-element
En kontroll som kan användas för att ange och bekräfta ett lösenord. Du använder det här elementet när [att skapa ett Azure hanterade program](publish-service-catalog-app.md).

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schemat
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
* En introduktion till hanterade program, se [översikt över Azure Managed Application](overview.md).
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

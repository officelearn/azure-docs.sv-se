---
title: Azure UserNameTextBox UI-element | Microsoft Docs
description: Beskriver Microsoft.Compute.UserNameTextBox UI-element för Azure-portalen.
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
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: b65b62389fbb66c9461430d1dd2df42e71bf6cff
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260730"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI-element
En textruta med inbyggda verifiering för Windows och Linux-användarnamn.

## <a name="ui-sample"></a>UI-exempel
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

## <a name="remarks"></a>Kommentarer
- Om `constraints.required` är inställd på **SANT**, textrutan måste innehålla ett värde som ska valideras. Standardvärdet är **SANT**.
- `osPlatform` måste anges, och kan vara antingen **Windows** eller **Linux**.
- `constraints.regex` är en JavaScript-mönstret för reguljära uttryck. Om anges måste textrutans värde matcha mönstret som ska valideras. Standardvärdet är **null**.
- `constraints.validationMessage` är en sträng som ska visas när textrutans värde inte verifiering som anges av `constraints.regex`. Om den inte anges används den textruta inbyggda verifieringsmeddelanden. Standardvärdet är **null**.
- Det här elementet har inbyggda verifiering som baseras på det angivna värdet för `osPlatform`. Valideringen av inbyggda kan användas tillsammans med ett anpassat reguljärt uttryck.
Om ett värde för `constraints.regex` anges, och sedan båda de inbyggda och anpassade kontroller har utlösts.

## <a name="sample-output"></a>Exempel på utdata
```json
"tabrezm"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

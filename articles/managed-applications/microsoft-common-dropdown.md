---
title: Azure DropDown-UI-element | Microsoft Docs
description: Beskriver Microsoft.Common.DropDown UI-element för Azure-portalen.
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
ms.openlocfilehash: e78fa419b067c0bad48229dcfd8d4e986fc16903
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62117309"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft.Common.DropDown UI element
En valet-kontroll med en listruta.

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

## <a name="remarks"></a>Kommentarer

- Etikett för `constraints.allowedValues` är texten som visas för ett objekt och dess värde är värdet av elementet när du har valt.
- Om anges används standardvärdet måste vara en etikett som finns i `constraints.allowedValues`. Om inte anges det första objektet i `constraints.allowedValues` har valts. Standardvärdet är **null**.
- `constraints.allowedValues` måste ha minst ett objekt.
- För att emulera ett värde som inte krävs, lägger du till ett objekt med en etikett och värdet för `""` (tom sträng) till `constraints.allowedValues`.

## <a name="sample-output"></a>Exempel på utdata
```json
"two"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element som finns i [CreateUiDefinition element](create-uidefinition-elements.md).

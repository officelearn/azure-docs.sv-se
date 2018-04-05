---
title: Gränssnittselement för Azure avsnittet | Microsoft Docs
description: Beskriver Microsoft.Common.Section UI-element för Azure-portalen.
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 46ea2e3d404ac3ec9b7f909257451991dbb55f53
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI-element
En kontroll som grupperar ett eller flera element under en rubrik.

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Schema
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- `elements` måste innehålla minst ett element och kan innehålla alla elementtyper utom `Microsoft.Common.Section`.
- Det här elementet har inte stöd för den `toolTip` egenskapen.

## <a name="sample-output"></a>Exempel på utdata
Åtkomst till utdatavärden av element i `elements`, använda den [basics()](create-uidefinition-functions.md#basics) eller [steps()](create-uidefinition-functions.md#steps) funktioner och punktnotation:

```json
basics('section1').element1
```

Element av typen `Microsoft.Common.Section` har inga utdata värdena.

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

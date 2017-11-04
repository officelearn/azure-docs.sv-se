---
title: "Azure hanterade program avsnittet gränssnittselement | Microsoft Docs"
description: "Beskriver Microsoft.Common.Section UI-element för hanterade program i Azure"
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
ms.openlocfilehash: 5a460fde88982c53a7ef3a1ec444d50c1c482fc4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI-element
En kontroll som grupperar ett eller flera element under en rubrik. Du använder det här elementet när [att skapa ett Azure hanterade program](publish-service-catalog-app.md).

## <a name="ui-sample"></a>UI-exempel
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Schemat
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
- `elements`måste innehålla minst ett element och kan innehålla alla elementtyper utom `Microsoft.Common.Section`.
- Det här elementet har inte stöd för den `toolTip` egenskapen.

## <a name="sample-output"></a>Exempel på utdata
Åtkomst till utdatavärden av element i `elements`, använda den [basics()](create-uidefinition-functions.md#basics) eller [steps()](create-uidefinition-functions.md#steps) funktioner och punktnotation:

```json
basics('section1').element1
```

Element av typen `Microsoft.Common.Section` har inga utdata värdena.

## <a name="next-steps"></a>Nästa steg
* En introduktion till hanterade program, se [översikt över Azure Managed Application](overview.md).
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

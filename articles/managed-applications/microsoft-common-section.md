---
title: Gränssnittselement för Azure avsnittet | Microsoft Docs
description: Beskriver Microsoft.Common.Section UI-element för Azure-portalen.
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
ms.openlocfilehash: 90ffae3dd8b05041c34d766e464eb68f793f6066
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062986"
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
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- `elements` måste ha minst ett element och kan ha alla elementtyper utom `Microsoft.Common.Section`.
- Det här elementet har inte stöd för den `toolTip` egenskapen.

## <a name="sample-output"></a>Exempel på utdata
Åtkomst till utdatavärden av element i `elements`, använda den [basics()](create-uidefinition-functions.md#basics) eller [steps()](create-uidefinition-functions.md#steps) funktioner och punktnotation:

```json
steps('configuration').section1.text1
```

Element av typen `Microsoft.Common.Section` har inga utdata värdena.

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

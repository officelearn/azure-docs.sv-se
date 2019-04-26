---
title: Azure avsnittet UI-element | Microsoft Docs
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
ms.openlocfilehash: c89b45dd4d8e6c2964f3d2bcbb6c3cef445c79e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60251884"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI element
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
- `elements` måste innehålla minst ett element och kan ha alla elementtyper utom `Microsoft.Common.Section`.
- Det här elementet har inte stöd för den `toolTip` egenskapen.

## <a name="sample-output"></a>Exempel på utdata
Åtkomst till utdatavärden för elementen i `elements`, använda den [basics()](create-uidefinition-functions.md#basics) eller [steps()](create-uidefinition-functions.md#steps) funktioner och punktnotation:

```json
steps('configuration').section1.text1
```

Element av typen `Microsoft.Common.Section` har inga utdata-värdena.

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element som finns i [CreateUiDefinition element](create-uidefinition-elements.md).

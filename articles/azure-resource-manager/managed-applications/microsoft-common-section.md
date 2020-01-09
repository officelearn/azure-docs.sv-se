---
title: Avsnitts GRÄNSSNITTs element
description: Beskriver användar gränssnitts elementet Microsoft. Common. section för Azure Portal. Använd för att gruppera element i portalen för att distribuera hanterade program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652260"
---
# <a name="microsoftcommonsection-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. section

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

## <a name="remarks"></a>Anmärkningar

- `elements` måste ha minst ett element och kan ha alla element typer förutom `Microsoft.Common.Section`.
- Det här elementet har inte stöd för egenskapen `toolTip`.

## <a name="sample-output"></a>Exempel på utdata
Om du vill komma åt indatavärdena för element i `elements`använder du funktionerna [Basic ()](create-uidefinition-functions.md#basics) eller Step [()](create-uidefinition-functions.md#steps) och punkt notation:

```json
steps('configuration').section1.text1
```

Element av typen `Microsoft.Common.Section` saknar själva värden för utdata.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

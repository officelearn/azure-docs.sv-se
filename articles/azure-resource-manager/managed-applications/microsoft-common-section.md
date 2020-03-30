---
title: Element för avsnittsgränssnitt
description: I artikeln beskrivs användargränssnittselementet Microsoft.Common.Section för Azure-portalen. Används för att gruppera element i portalen för distribution av hanterade program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652260"
---
# <a name="microsoftcommonsection-ui-element"></a>Gränssnittselement för Microsoft.Common.Section

En kontroll som grupperar ett eller flera element under en rubrik.

## <a name="ui-sample"></a>Exempel på användargränssnitt

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

- `elements`måste ha minst ett element och kan `Microsoft.Common.Section`ha alla elementtyper utom .
- Det här elementet `toolTip` stöder inte egenskapen.

## <a name="sample-output"></a>Exempel på utdata
Om du vill komma `elements`åt utdatavärdena för element i använder du [funktionerna basics()](create-uidefinition-functions.md#basics) eller [steps()](create-uidefinition-functions.md#steps) och punkt notation:

```json
steps('configuration').section1.text1
```

Element av `Microsoft.Common.Section` typen har inga utdatavärden själva.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

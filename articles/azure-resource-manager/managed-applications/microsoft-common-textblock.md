---
title: Gränssnittselement för TextBlock
description: I artikeln beskrivs användargränssnittselementet Microsoft.Common.TextBlock för Azure-portalen. Används för att lägga till text i gränssnittet.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 418056cb149f4441bac49db839a0dba40c2bb42d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652221"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft.Common.TextBlock UI-element

En kontroll som kan användas för att lägga till text i portalgränssnittet.

## <a name="ui-sample"></a>Exempel på användargränssnitt

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textblock.png)

## <a name="schema"></a>Schema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>Exempel på utdata

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

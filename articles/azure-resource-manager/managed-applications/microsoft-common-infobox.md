---
title: Gränssnittselement för InfoBox
description: I artikeln beskrivs användargränssnittselementet Microsoft.Common.InfoBox för Azure portal. Används för att lägga till text eller varningar när du distribuerar hanterade program.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652481"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft.Common.InfoBox UI-element

En kontroll som lägger till en informationsruta. Rutan innehåller viktig text eller varningar som hjälper användarna att förstå de värden de tillhandahåller. Det kan också länka till en URI för mer information.

## <a name="ui-sample"></a>Exempel på användargränssnitt

![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Schema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Exempel på utdata

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Anmärkningar

* För `icon`använder **du Ingen**, **Info,** **Varning**eller **Fel**.
* Boendet `uri` är valfritt.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

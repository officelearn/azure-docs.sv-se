---
title: Användar gränssnitts element för Azure information | Microsoft Docs
description: Beskriver användar gränssnitts elementet Microsoft. Common. info i Azure Portal. Använd för att lägga till text eller varningar när du distribuerar hanterade program.
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
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 67ae05c2ec7b36d76e49f26d5765bbc68b952292
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331696"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Användar gränssnitts element för Microsoft. Common. info
En kontroll som lägger till en informations ruta. Rutan innehåller viktig text eller varningar som hjälper användarna att förstå de värden som de tillhandahåller. Den kan också länka till en URI för mer information.

## <a name="ui-sample"></a>UI-exempel
![Microsoft. Common. info](./media/managed-application-elements/microsoft.common.infobox.png)


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

## <a name="remarks"></a>Kommentarer

* Använd **ingen**, **info**, **warning**eller **Error**för `icon`.
* Egenskapen `uri` är valfri.

## <a name="sample-output"></a>Exempel på utdata

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

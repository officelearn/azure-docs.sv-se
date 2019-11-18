---
title: GRÄNSSNITTs element för Azure List Rute | Microsoft Docs
description: Beskriver UI-elementet Microsoft. Common. list Rute gränssnitt för Azure Portal. Använd om du vill välja mellan tillgängliga alternativ när du distribuerar ett hanterat program.
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
ms.openlocfilehash: e1db91362d17d9100199f0b30119f8024f00a903
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151336"
---
# <a name="microsoftcommondropdown-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. list rutan

En markerings kontroll med en nedrullningsbar lista.

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

## <a name="sample-output"></a>Exempel på utdata

```json
"two"
```

## <a name="remarks"></a>Kommentarer

- Etiketten för `constraints.allowedValues` är visnings texten för ett objekt och dess värde är utdata-värdet för elementet när det är markerat.
- Om det här alternativet anges måste standardvärdet vara en etikett i `constraints.allowedValues`. Om inget anges väljs det första objektet i `constraints.allowedValues`. Standardvärdet är **Null**.
- `constraints.allowedValues` måste ha minst ett objekt.
- För att emulera ett värde som inte krävs lägger du till ett objekt med en etikett och ett värde för `""` (tom sträng) för att `constraints.allowedValues`.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

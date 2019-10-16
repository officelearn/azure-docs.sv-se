---
title: GRÄNSSNITTs element för Azure-TextBox | Microsoft Docs
description: Beskriver elementet Microsoft. Common. text Rute gränssnitt för Azure Portal. Använd för att lägga till oformaterad text.
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
ms.openlocfilehash: b72bfcf06f4c7e256dd227e4edf781c4500c7b19
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331610"
---
# <a name="microsoftcommontextbox-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. text Rute
En kontroll som kan användas för att redigera oformaterad text.

## <a name="ui-sample"></a>UI-exempel
![Text ruta för Microsoft. Common.](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Om `constraints.required` har angetts till **True**måste text rutan ha ett värde för att kunna verifiera. Standardvärdet är **false**.
- `constraints.regex` är ett mönster för reguljära uttryck för Java Script. Om det här alternativet anges måste text rutans värde matcha mönstret för att kunna verifieras. Standardvärdet är **Null**.
- `constraints.validationMessage` är en sträng som ska visas när text rutans värde inte kan verifieras. Om inget värde anges används text rutans inbyggda verifierings meddelanden. Standardvärdet är **Null**.
- Det går att ange ett värde för `constraints.regex` när `constraints.required` har angetts till **false**. I det här scenariot krävs inget värde för att text rutan ska verifieras korrekt. Om en anges måste den matcha mönstret för reguljära uttryck.

## <a name="sample-output"></a>Exempel på utdata

```json
"my text value"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

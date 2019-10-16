---
title: Användar gränssnitts element för Azure-avsnittet | Microsoft Docs
description: Beskriver användar gränssnitts elementet Microsoft. Common. section för Azure Portal. Använd för att gruppera element i portalen för att distribuera hanterade program.
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
ms.openlocfilehash: a48c89785e0a448609026aab53364f6cf704e948
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331658"
---
# <a name="microsoftcommonsection-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. section
En kontroll som grupperar ett eller flera element under en rubrik.

## <a name="ui-sample"></a>UI-exempel
![Avsnittet Microsoft. Common.](./media/managed-application-elements/microsoft.common.section.png)

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
- `elements` måste ha minst ett element och kan ha alla element typer förutom `Microsoft.Common.Section`.
- Det här elementet har inte stöd för egenskapen `toolTip`.

## <a name="sample-output"></a>Exempel på utdata
Använd funktionerna [Basic ()](create-uidefinition-functions.md#basics) eller Step [(](create-uidefinition-functions.md#steps) ) och punkt notation för att få åtkomst till indatavärdena för element i `elements`:

```json
steps('configuration').section1.text1
```

Element av typen `Microsoft.Common.Section` har inga värden för utdata.

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

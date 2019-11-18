---
title: Användar gränssnitts element för Azure TextBlock | Microsoft Docs
description: Beskriver elementet Microsoft. Common. TextBlock UI för Azure Portal. Använd för att lägga till text i gränssnittet.
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
ms.openlocfilehash: 1dbef1e43d9f970665a31f0e4e9270d3193edaba
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151729"
---
# <a name="microsoftcommontextblock-ui-element"></a>Elementet Microsoft. Common. TextBlock UI

En kontroll som kan användas för att lägga till text i Portal gränssnittet.

## <a name="ui-sample"></a>UI-exempel

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

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

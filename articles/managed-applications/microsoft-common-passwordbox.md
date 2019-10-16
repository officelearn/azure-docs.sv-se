---
title: GRÄNSSNITTs element för Azure-lösenord | Microsoft Docs
description: Beskriver användar gränssnitts elementet Microsoft. Common. password för Azure Portal. Gör att användarna kan ange ett hemligt värde när de distribuerar hanterade program.
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
ms.openlocfilehash: 4a8b760d113e29efb0efacbd41dcaa7432ecdcfd
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332808"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. password
En kontroll som kan användas för att ange och bekräfta ett lösen ord.

## <a name="ui-sample"></a>UI-exempel
![Microsoft. Common. password](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Det här elementet har inte stöd för egenskapen `defaultValue`.
- Information om implementering av `constraints` finns i [Microsoft. Common. text ruta](microsoft-common-textbox.md).
- Om `options.hideConfirmation` har angetts till **True**är den andra text rutan för att bekräfta användarens lösen ord dold. Standardvärdet är **false**.

## <a name="sample-output"></a>Exempel på utdata
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

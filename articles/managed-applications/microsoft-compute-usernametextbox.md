---
title: Användar gränssnitts element för Azure UserNameTextBox | Microsoft Docs
description: Beskriver användar gränssnitts elementet Microsoft. Compute. UserNameTextBox för Azure Portal. Gör det möjligt för användare att ange Windows-eller Linux-användarnamn.
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 875c73c546fa52642959e2593d41f9af82c13797
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331584"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>GRÄNSSNITTs element för Microsoft. Compute. UserNameTextBox
En text Rute kontroll med inbyggd verifiering för Windows-och Linux-användarnamn.

## <a name="ui-sample"></a>UI-exempel
![Microsoft. Compute. UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- Om `constraints.required` har angetts till **True**måste text rutan ha ett värde för att kunna verifiera. Standardvärdet är **True**.
- `osPlatform` måste anges och kan vara antingen **Windows** eller **Linux**.
- `constraints.regex` är ett mönster för reguljära uttryck för Java Script. Om det här alternativet anges måste text rutans värde matcha mönstret för att kunna verifieras. Standardvärdet är **Null**.
- `constraints.validationMessage` är en sträng som ska visas när text rutans värde inte uppfyller den verifiering som anges av `constraints.regex`. Om inget värde anges används text rutans inbyggda verifierings meddelanden. Standardvärdet är **Null**.
- Det här elementet har en inbyggd verifiering som baseras på värdet som angetts för `osPlatform`. Den inbyggda verifieringen kan användas tillsammans med ett anpassat reguljärt uttryck. Om ett värde för `constraints.regex` anges utlöses båda de inbyggda och anpassade valideringarna.

## <a name="sample-output"></a>Exempel på utdata
```json
"Example name"
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

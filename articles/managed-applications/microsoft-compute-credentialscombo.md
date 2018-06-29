---
title: Azure CredentialsCombo UI-element | Microsoft Docs
description: Beskriver Microsoft.Compute.CredentialsCombo UI-element för Azure-portalen.
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
ms.openlocfilehash: 183075f7407b0a0ca6ea53871e239ab8c2d89490
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098628"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI-element
En grupp av kontroller med inbyggda verifiering för Windows och Linux-lösenord och offentliga SSH-nycklar.

## <a name="ui-sample"></a>UI-exempel

För Windows Se följande användare:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Användarna ser för Linux med lösenord som har valts

![Microsoft.Compute.CredentialsCombo Linux lösenord](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Användarna ser för Linux med SSH offentlig nyckel som valts

![Microsoft.Compute.CredentialsCombo Linux-nyckel](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schema
För Windows, använder du följande schema:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

För **Linux**, Använd följande schema:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>Kommentarer
- `osPlatform` måste anges, och kan vara antingen **Windows** eller **Linux**.
- Om `constraints.required` är inställd på **SANT**, lösenord eller SSH textrutor för offentlig nyckel måste ha värden som ska valideras. Standardvärdet är **SANT**.
- Om `options.hideConfirmation` är inställd på **SANT**, sedan den andra textrutan för att bekräfta användarens lösenord är dolt. Standardvärdet är **FALSKT**.
- Om `options.hidePassword` är inställd på **SANT**, och sedan på alternativet för att använda en lösenordsautentisering är dolt. Det kan användas endast när `osPlatform` är **Linux**. Standardvärdet är **FALSKT**.
- Ytterligare begränsningar på tillåtna lösenord kan implementeras med hjälp av den `customPasswordRegex` egenskapen. Strängen i `customValidationMessage` visas när ett lösenord inte anpassad verifiering. Standardvärdet för båda egenskaperna är **null**.

## <a name="sample-output"></a>Exempel på utdata
Om `osPlatform` är **Windows**, eller `osPlatform` är **Linux** och användaren har angett ett lösenord i stället för en offentlig SSH-nyckel, kontrollen följande resultat:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

Om `osPlatform` är **Linux** och användaren har angett en offentlig SSH-nyckel, kontrollen följande resultat:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>Nästa steg
* En introduktion till att skapa UI-definitioner, se [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element, se [CreateUiDefinition element](create-uidefinition-elements.md).

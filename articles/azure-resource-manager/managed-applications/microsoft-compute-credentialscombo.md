---
title: Ui-element för CredentialsCombo
description: I artikeln beskrivs användargränssnittselementet Microsoft.Compute.CredentialsCombo för Azure portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: baee83e08505ddabb4680b5bbb4101479010ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652143"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI-element

En grupp kontroller med inbyggd validering för Lösenord för Windows och Linux och SSH-offentliga nycklar.

## <a name="ui-sample"></a>Exempel på användargränssnitt

För Windows läser användarna:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

För Linux med valt lösenord, användare se:

![Microsoft.Compute.CredentialsCombo Linux lösenord](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

För Linux med SSH offentlig nyckel vald, användare se:

![Microsoft.Compute.CredentialsCombo Linux-nyckel](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schema

Använd följande schema i Windows:

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
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

För **Linux**använder du följande schema:

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
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="sample-output"></a>Exempel på utdata

Om `osPlatform` **Windows**är Windows `osPlatform` , eller är **Linux** och användaren angav ett lösenord i stället för en SSH offentlig nyckel, returnerar kontrollen följande utdata:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Om `osPlatform` **är Linux** och användaren som en SSH offentlig nyckel, returnerar kontrollen följande utdata:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Anmärkningar

- `osPlatform`måste anges, och kan vara antingen **Windows** eller **Linux**.
- Om `constraints.required` värdet är **true**måste textrutorna för lösenord eller SSH ha värden för att valideras. Standardvärdet är **sant**.
- Om `options.hideConfirmation` värdet är **true**döljs den andra textrutan för att bekräfta användarens lösenord. Standardvärdet är **falskt**.
- Om `options.hidePassword` värdet är **true**döljs alternativet att använda lösenordsautentisering. Det kan endast `osPlatform` användas när är **Linux**. Standardvärdet är **falskt**.
- Ytterligare begränsningar för tillåtna lösenord kan implementeras `customPasswordRegex` med hjälp av egenskapen. Strängen `customValidationMessage` i visas när ett lösenord misslyckas anpassad validering. Standardvärdet för båda egenskaperna är **null**.

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa gränssnittsdefinitioner finns i [Komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av vanliga egenskaper i gränssnittselement finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

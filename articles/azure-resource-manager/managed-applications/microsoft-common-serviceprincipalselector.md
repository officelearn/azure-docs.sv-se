---
title: ServicePrincipalSelector-GRÄNSSNITTs element
description: Beskriver elementet Microsoft. Common. ServicePrincipalSelector UI för Azure Portal. Innehåller en listruta där du kan välja en program identifierare och en text ruta för att ange ett lösen ord eller tumavtryck för certifikatet.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: tomfitz
ms.openlocfilehash: 06fa5868cf0a907e268a347cfdfa2b5ee20908e3
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571460"
---
# <a name="microsoftcommonserviceprincipalselector-ui-element"></a>Elementet Microsoft. Common. ServicePrincipalSelector UI

En kontroll som låter användarna välja ett befintligt huvud namn för tjänsten eller registrera ett nytt. När du väljer **Skapa ny**går du igenom stegen för att registrera ett nytt program. När du väljer ett befintligt program tillhandahåller kontrollen en text ruta för att ange ett lösen ord eller tumavtryck för certifikatet.

## <a name="ui-sample"></a>UI-exempel

Standardvyn bestäms av värdena i `defaultValue` egenskapen. Om `principalId` egenskapen innehåller en giltig globalt unik identifierare (GUID) söker kontrollen efter programmets objekt-ID. Standardvärdet gäller om användaren inte gör något val i list rutan.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-initial.png" alt-text="Initial vy för Microsoft. Common. ServicePrincipalSelector":::

När du väljer **Skapa ny** eller en befintlig program identifierare från List rutan visas **autentiseringstypen** för att ange ett lösen ord eller certifikat-tumavtryck i text rutan.

:::image type="content" source="./media/managed-application-elements/microsoft-common-serviceprincipal-selection.png" alt-text="Initial vy för Microsoft. Common. ServicePrincipalSelector":::

## <a name="schema"></a>Schema

```json
{
  "name": "ServicePrincipal",
  "type": "Microsoft.Common.ServicePrincipalSelector",
  "label": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type",
    "sectionHeader": "Service Principal"
  },
  "toolTip": {
    "principalId": "App Id",
    "password": "Password",
    "certificateThumbprint": "Certificate thumbprint",
    "authenticationType": "Authentication Type"
  },
  "defaultValue": {
    "principalId": "<default guid>",
    "name": "(New) default App Id"
  },
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideCertificate": false
  },
  "visible": true
}
```

## <a name="remarks"></a>Anmärkningar

- De obligatoriska egenskaperna är:
  - `name`
  - `type`
  - `label`
  - `defaultValue`: Anger standard `principalId` och `name` .

- De valfria egenskaperna är:
  - `toolTip`: Bifogar en knapp Beskrivning `infoBalloon` till varje etikett.
  - `visible`: Dölj eller Visa kontrollen.
  - `options`: Anger om alternativet tumavtryck för certifikatet ska vara tillgängligt eller inte.
  - `constraints`: Regex-begränsningar för lösen ords validering.

## <a name="example"></a>Exempel

Följande är ett exempel på `Microsoft.Common.ServicePrincipalSelector` kontrollen. `defaultValue`Egenskapen anges `principalId` `<default guid>` som en plats hållare för ett standard-ID för program-ID: t.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "SPNcontrol",
        "label": "SPNcontrol",
        "elements": [
          {
            "name": "ServicePrincipal",
            "type": "Microsoft.Common.ServicePrincipalSelector",
            "label": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type",
              "sectionHeader": "Service Principal"
            },
            "toolTip": {
              "principalId": "App Id",
              "password": "Password",
              "certificateThumbprint": "Certificate thumbprint",
              "authenticationType": "Authentication Type"
            },
            "defaultValue": {
              "principalId": "<default guid>",
              "name": "(New) default App Id"
            },
            "constraints": {
              "required": true,
              "regex": "^[a-zA-Z0-9]{8,}$",
              "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
            },
            "options": {
              "hideCertificate": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "appId": "[steps('SPNcontrol').ServicePrincipal.appId]",
      "objectId": "[steps('SPNcontrol').ServicePrincipal.objectId]",
      "password": "[steps('SPNcontrol').ServicePrincipal.password]",
      "certificateThumbprint": "[steps('SPNcontrol').ServicePrincipal.certificateThumbprint]",
      "newOrExisting": "[steps('SPNcontrol').ServicePrincipal.newOrExisting]",
      "authenticationType": "[steps('SPNcontrol').ServicePrincipal.authenticationType]"
    }
  }
}
```

## <a name="example-output"></a>Exempel på utdata

`appId`Är ID: t för den program registrering som du har valt eller skapat. `objectId`Är en matris med objectIds för tjänstens huvud namn som har kon figurer ATS för den valda program registreringen.

När inget val görs från List rutan `newOrExisting` är egenskap svärdet **nytt**:

```json
{
  "appId": {
    "value": "<default guid>"
  },
  "objectId": {
    "value": "<default guid>"
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "new"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

När **Skapa ny** eller en befintlig program identifierare väljs i list rutan `newOrExisting` , är egenskap svärdet **befintlig**:

```json
{
  "appId": {
    "value": "<guid>"
  },
  "objectId": {
    "value": "<guid>"
  },
  "password": {
    "value": "<password>"
  },
  "certificateThumbprint": {
    "value": ""
  },
  "newOrExisting": {
    "value": "existing"
  },
  "authenticationType": {
    "value": "password"
  }
}
```

## <a name="next-steps"></a>Nästa steg

- En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
- En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

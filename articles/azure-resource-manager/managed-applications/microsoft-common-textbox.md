---
title: UI-element för text rutor
description: Beskriver elementet Microsoft. Common. text Rute gränssnitt för Azure Portal. Använd för att lägga till oformaterad text.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c89bc434d9d67144a95b5c2f23e7664078fe7825
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87474316"
---
# <a name="microsoftcommontextbox-ui-element"></a>GRÄNSSNITTs element för Microsoft. Common. text Rute

En kontroll som kan användas för att redigera oformaterad text.

## <a name="ui-sample"></a>UI-exempel

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>Schema

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "placeholder": "",
    "constraints": {
        "required": true,
        "validations": [
            {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
            },
            {
                "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
                "message": "Must start with 'contoso'."
            }
        ]
    },
    "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

```json
"contoso123"
```

## <a name="remarks"></a>Kommentarer

- Om `constraints.required` är inställt på **Sant**måste text rutan ha ett värde för att kunna verifiera. Standardvärdet är **falskt**.
- `validations`Egenskapen är en matris där du lägger till villkor för att kontrol lera värdet som anges i text rutan.
- `regex`Egenskapen är ett mönster för reguljära uttryck i Java Script. Om det här alternativet anges måste text rutans värde matcha mönstret för att kunna verifieras. Standardvärdet är **Null**.
- `isValid`Egenskapen innehåller ett uttryck som utvärderas till true eller false. I uttrycket definierar du villkoret som avgör om text rutan är giltig.
- `message`Egenskapen är en sträng som visas när text rutans värde inte kan verifieras.
- Det är möjligt att ange ett värde för `regex` när `required` är inställt på **falskt**. I det här scenariot krävs inget värde för att text rutan ska verifieras korrekt. Om en anges måste den matcha mönstret för reguljära uttryck.
- `placeholder`Egenskapen är hjälp text som försvinner när användaren börjar redigera. Om `placeholder` och `defaultValue` båda definieras prioriteras `defaultValue` och visas.

## <a name="example"></a>Exempel

I följande exempel används en text ruta med [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) -kontrollen för att kontrol lera tillgängligheten för ett resurs namn.

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

* En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
* En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).

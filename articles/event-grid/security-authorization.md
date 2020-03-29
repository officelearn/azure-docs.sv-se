---
title: Azure Event Grid säkerhet och autentisering
description: Beskriver Azure Event Grid och dess begrepp.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: 03bc2f9de6f50f08c9f62f86a3d1791a067cecd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899287"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Auktorisera åtkomst till resurser för händelserutnät
Med Azure Event Grid kan du styra åtkomstnivån som ges till olika användare för att utföra olika hanteringsåtgärder, till exempel lista händelseprenumerationer, skapa nya och generera nycklar. Event Grid använder Azures rollbaserade åtkomstkontroll (RBAC).

## <a name="operation-types"></a>Åtgärdstyper

Event Grid stöder följande åtgärder:

* Microsoft.EventGrid/*/läs
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventAbonnemang/getFullUrl/åtgärd
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

De senaste tre åtgärderna returnerar potentiellt hemlig information, som filtreras bort från normala läsåtgärder. Vi rekommenderar att du begränsar åtkomsten till dessa åtgärder. 

## <a name="built-in-roles"></a>Inbyggda roller

Event Grid innehåller två inbyggda roller för att hantera händelseprenumerationer. De är viktiga när de implementerar [händelsedomäner](event-domains.md) eftersom de ger användarna de behörigheter de behöver för att prenumerera på ämnen i din evenemangsdomän. Dessa roller är inriktade på händelseprenumerationer och ger inte åtkomst för åtgärder som att skapa ämnen.

Du kan [tilldela dessa roller till en användare eller grupp](../role-based-access-control/quickstart-assign-role-user-portal.md).

**EventGrid EventSubscription Contributor**: hantera prenumerationsåtgärder för Event Grid

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**EventGrid EventSubscription Reader**: läs Event Grid-prenumerationer

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="custom-roles"></a>Anpassade roller

Om du behöver ange behörigheter som skiljer sig från de inbyggda rollerna kan du skapa anpassade roller.

Följande är exempel på rolldefinitioner för händelserutnät som gör det möjligt för användare att vidta olika åtgärder. Dessa anpassade roller skiljer sig från de inbyggda rollerna eftersom de ger bredare åtkomst än bara händelseprenumerationer.

**EventGridReadOnlyRole.json**: Tillåt endast skrivskyddade åtgärder.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: Tillåt begränsade inläggsåtgärder men tillåt inte borttagningsåtgärder.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: Tillåter alla åtgärder för händelserutnät.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Du kan skapa anpassade roller med [PowerShell,](../role-based-access-control/custom-roles-powershell.md) [Azure CLI](../role-based-access-control/custom-roles-cli.md)och [REST](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Vilande kryptering

Alla händelser eller data som skrivs till disk av Event Grid-tjänsten krypteras av en Microsoft-hanterad nyckel som säkerställer att den krypteras i vila. Dessutom är den maximala tidsperioden som händelser eller data lagras 24 timmar i anslutning till principen För återförsök i [händelserutnätet](delivery-and-retry.md). Event Grid tar automatiskt bort alla händelser eller data efter 24 timmar, eller händelsetid för att leva, beroende på vilket som är lägst.

## <a name="next-steps"></a>Nästa steg

* En introduktion till Händelserutnätet finns i [Om händelserutnätet](overview.md)

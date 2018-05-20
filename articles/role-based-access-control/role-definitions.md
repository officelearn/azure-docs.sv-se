---
title: Förstå rolldefinitioner i Azure RBAC | Microsoft Docs
description: Läs mer om rolldefinitioner i rollbaserad åtkomstkontroll (RBAC) och hur du definierar anpassade roller för detaljerad åtkomsthantering av resurser i Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="understand-role-definitions"></a>Förstå rolldefinitioner

Om du försöker att förstå hur en roll fungerar eller om du skapar egna [anpassad roll](custom-roles.md), är det bra att förstå hur roller har definierats. Den här artikeln innehåller information om rolldefinitioner samt några exempel.

## <a name="role-definition-structure"></a>Rollen definition struktur

En *rolldefinitionen* är en uppsättning behörigheter. Den kallas ibland ett *rollen*. En rolldefinition visar de funktioner som kan utföras, t ex läsa, skriva och ta bort. Det kan också en lista över de åtgärder som inte kan utföras. En rolldefinition har följande struktur:

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

Operationer anges med strängar som har följande format:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

Den `{action}` delen av en sträng med åtgärden anger vilken typ av åtgärder du kan utföra på en resurstyp. Till exempel du ser i följande delsträngar `{action}`:

| Åtgärden delsträngen    | Beskrivning         |
| ------------------- | ------------------- |
| `*` | Jokertecknet ger åtkomst till alla åtgärder som motsvarar strängen. |
| `read` | Aktiverar läsåtgärder (GET). |
| `write` | Aktiverar skrivåtgärder (PUT, POST och korrigering). |
| `delete` | Aktiverar delete-åtgärder (ta bort). |

Här är den [deltagare](built-in-roles.md#contributor) rolldefinitionen i JSON-format. Jokertecknet (`*`) åtgärden under `actions` anger att huvudnamn som tilldelats den här rollen kan utföra alla åtgärder eller med andra ord den kan hantera allt. Här ingår åtgärder som definierats i framtiden, som Azure lägger till nya resurstyper. Åtgärder under `notActions` subtraheras från `actions`. För den [deltagare](built-in-roles.md#contributor) roll, `notActions` tar bort den här rollen kan hantera åtkomst till resurser och även tilldela åtkomst till resurser.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>Hanteringsåtgärder

Rollbaserad åtkomstkontroll för hanteringsåtgärder har angetts i den `actions` och `notActions` delar av en rolldefinition. Här följer några exempel på hanteringsåtgärder i Azure:

- Hantera åtkomst till ett lagringskonto
- Skapa, uppdatera eller ta bort en blob-behållare
- Ta bort en resursgrupp och alla dess resurser

Tillgång till ärvs inte till dina data. Den här separationen förhindrar roller med jokertecken (`*`) från att ha obegränsad åtkomst till dina data. Om en användare har till exempel en [Reader](built-in-roles.md#reader) rollen på en prenumeration kan de visa storage-konto, men de kan inte visa underliggande data.

## <a name="actions"></a>åtgärder

Den `actions` behörighet anger vilka hanteringsåtgärder som rollen ger åtkomst. Det är en samling med åtgärden strängar som identifierar skyddbara drift av Azure-resursprovidrar. Här följer några exempel på hanteringsåtgärder som kan användas i `actions`.

| Åtgärden sträng    | Beskrivning         |
| ------------------- | ------------------- |
| `*/read` | ger åtkomst till Läs-och skrivåtgärder för alla typer av resurser för alla Azure-resurs-providers.|
| `Microsoft.Compute/*` | ger åtkomst till alla åtgärder för alla typer av resurser i Microsoft.Compute-resursprovidern.|
| `Microsoft.Network/*/read` | Ger åtkomst till Läs-och skrivåtgärder för alla typer av resurser i resursen Microsoft.Network-providern.|
| `Microsoft.Compute/virtualMachines/*` | ger åtkomst till alla åtgärder för virtuella datorer och dess underordnade resurstyper.|
| `microsoft.web/sites/restart/Action` | Ger åtkomst till att starta om ett webbprogram.|

## <a name="notactions"></a>NotActions

Den `notActions` behörighet anger vilka hanteringsåtgärder som är undantagna från den tillåtna `actions`. Använd den `notActions` tillstånd om en uppsättning åtgärder som du vill tillåta definieras enklare genom att utesluta vissa åtgärder. Åtkomst av en roll (behörigheter) beräknas genom att subtrahera den `notActions` åtgärder från den `actions` åtgärder.

> [!NOTE]
> Om en användare har tilldelats en roll som inte omfattar en åtgärd i `notActions`, och har tilldelats en andra roll som ger åtkomst till samma åtgärd som användaren tillåts att utföra åtgärden. `notActions` är inte en neka regel – det är ett bekvämt sätt att skapa en uppsättning tillåtna åtgärder när specifika åtgärder måste exkluderas.
>

## <a name="assignablescopes"></a>AssignableScopes

Den `assignableScopes` avsnittet anger scope (hanteringsgrupper (för närvarande under förhandsgranskning), prenumerationer, resursgrupper och resurser) att rollen är tillgängliga för tilldelning. Du kan göra rollen tillgängliga för tilldelning i endast prenumerationer eller resursgrupper som kräver och inte oreda användarens upplevelse för resten av prenumerationer eller resursgrupper. Du måste använda minst en grupp, prenumeration, resursgrupp eller resurs-ID.

Inbyggda roller har `assignableScopes` inställd på rotscopet (`"/"`). Rotscopet anger att rollen är tillgängliga för tilldelning i alla scope. Du kan inte använda rotscopet i din egen anpassade roller. Om du försöker att du får ett felmeddelande för auktorisering.

Exempel på giltiga kan tilldelas omfång:

| Scenario | Exempel |
|----------|---------|
| Rollen är tillgängliga för tilldelning i en enda prenumeration | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Rollen är tillgängliga för tilldelning i två prenumerationer | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Rollen är tillgängliga för tilldelning i resursgruppen nätverk | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Rollen är tillgängliga för tilldelning i alla scope | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes och anpassade roller

Den `assignableScopes` avsnittet för en anpassad roll också kontroller som kan skapa, ta bort, ändra eller visa den anpassade rollen.

| Aktivitet | Åtgärd | Beskrivning |
| --- | --- | --- |
| Skapa/ta bort en anpassad roll | `Microsoft.Authorization/ roleDefinition/write` | Användare som fått den här åtgärden på alla de `assignableScopes` för den anpassade rollen som kan skapa (eller ta bort) anpassade roller för användning i sådana omfattningar. Till exempel [ägare](built-in-roles.md#owner) och [användaren åtkomst administratörer](built-in-roles.md#user-access-administrator) för prenumerationer, resursgrupper och resurser. |
| Ändra en anpassad roll | `Microsoft.Authorization/ roleDefinition/write` | Användare som fått den här åtgärden på alla de `assignableScopes` för den anpassade rollen som kan ändra anpassade roller i dessa scope. Till exempel [ägare](built-in-roles.md#owner) och [användaren åtkomst administratörer](built-in-roles.md#user-access-administrator) för prenumerationer, resursgrupper och resurser. |
| Visa en anpassad roll | `Microsoft.Authorization/ roleDefinition/read` | Användare som fått den här åtgärden på ett scope kan visa de anpassade roller som är tillgängliga för tilldelning i detta scope. Alla inbyggda roller Tillåt anpassade roller ska vara tillgängliga för tilldelning. |

## <a name="role-definition-examples"></a>Rollen definition exempel

I följande exempel visas den [Reader](built-in-roles.md#reader) rolldefinition som visas med hjälp av Azure CLI:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

I följande exempel visas en anpassad roll för övervakning och starta om virtuella datorer som visas med hjälp av Azure PowerShell:

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Se också

* [Inbyggda roller](built-in-roles.md)
* [Anpassade roller](custom-roles.md)
* [Provideråtgärder i Azure Resource Manager resurs](resource-provider-operations.md)

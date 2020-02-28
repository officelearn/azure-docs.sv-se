---
title: Anpassade roller för Azure-resurser | Microsoft Docs
description: Lär dig hur du skapar anpassade roller med rollbaserad åtkomst kontroll (RBAC) för detaljerade åtkomst hantering av Azure-resurser.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8c5db13b343783a86dc04b84e09746bc4406186b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660707"
---
# <a name="custom-roles-for-azure-resources"></a>Anpassade roller för Azure-resurser

Om de [inbyggda rollerna för Azure-resurser](built-in-roles.md) inte uppfyller organisationens specifika krav, kan du skapa egna anpassade roller. Precis som inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänstens huvud namn vid prenumeration, resurs grupp och resurs omfång.

Anpassade roller kan delas mellan prenumerationer som har förtroende för samma Azure AD-katalog. Det finns en gräns på **5 000** anpassade roller per katalog. (För specialiserade moln, till exempel Azure Government, Azure Tyskland och Azure Kina 21Vianet, är gränsen 2 000 anpassade roller.) Du kan skapa anpassade roller med hjälp av Azure Portal (för hands version), Azure PowerShell, Azure CLI eller REST API.

## <a name="custom-role-example"></a>Exempel på anpassade roller

Följande visar hur en anpassad roll ser ut som om den visas i JSON-format. Den här anpassade rollen kan användas för övervakning och omstart av virtuella datorer.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

När du skapar en anpassad roll visas den i Azure Portal med en orange resurs ikon.

![Ikon för anpassad roll](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Steg för att skapa en anpassad roll

1. Bestäm hur du vill skapa den anpassade rollen

    Du kan skapa anpassade roller med [Azure Portal](custom-roles-portal.md) (för hands version), [Azure POWERSHELL](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)eller [REST API](custom-roles-rest.md).

1. Bestäm vilka behörigheter du behöver

    När du skapar en anpassad roll måste du veta vilka resurs leverantörs åtgärder som är tillgängliga för att definiera dina behörigheter. Information om hur du visar listan över åtgärder finns i [Azure Resource Manager Resource Provider-åtgärder](resource-provider-operations.md). Du lägger till åtgärder i `Actions`-eller `NotActions` egenskaperna för [roll definitionen](role-definitions.md). Om du har data åtgärder kommer du att lägga till dem i `DataActions`-eller `NotDataActions` egenskaper.

1. Skapa den anpassade rollen

    Normalt börjar du med en befintlig inbyggd roll och ändrar den efter dina behov. Sedan använder du kommandot [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) eller [AZ Role definition Create](/cli/azure/role/definition#az-role-definition-create) för att skapa den anpassade rollen. Om du vill skapa en anpassad roll måste du ha `Microsoft.Authorization/roleDefinitions/write` behörighet för alla `AssignableScopes`, till exempel [ägare](built-in-roles.md#owner) eller [administratör för användar åtkomst](built-in-roles.md#user-access-administrator).

1. Testa den anpassade rollen

    När du har en anpassad roll måste du testa den för att kontrol lera att den fungerar som förväntat. Om du behöver göra justeringar senare kan du uppdatera den anpassade rollen.

En stegvis själv studie kurs om hur du skapar en anpassad roll finns i [Självstudier: skapa en anpassad roll med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md) eller [Självstudier: skapa en anpassad roll med hjälp av Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Egenskaper för anpassad roll

En anpassad roll har följande egenskaper.

| Egenskap | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| `Name` | Ja | Sträng | Visnings namnet för den anpassade rollen. Även om en roll definition är en resurs på en prenumerations nivå kan en roll definition användas i flera prenumerationer som delar samma Azure AD-katalog. Det här visnings namnet måste vara unikt i Azure AD-katalogens omfång. Kan innehålla bokstäver, siffror, blank steg och specialtecken. Maximalt antal tecken är 128. |
| `Id` | Ja | Sträng | Det unika ID: t för den anpassade rollen. För Azure PowerShell och Azure CLI genereras detta ID automatiskt när du skapar en ny roll. |
| `IsCustom` | Ja | Sträng | Anger om det här är en anpassad roll. Ange `true` för anpassade roller. |
| `Description` | Ja | Sträng | Beskrivningen av den anpassade rollen. Kan innehålla bokstäver, siffror, blank steg och specialtecken. Maximalt antal tecken är 1024. |
| `Actions` | Ja | String[] | En sträng mat ris som anger vilka hanterings åtgärder som rollen kan utföra. Mer information finns i [åtgärder](role-definitions.md#actions). |
| `NotActions` | Nej | String[] | En sträng mat ris som anger de hanterings åtgärder som är undantagna från tillåtna `Actions`. Mer information finns i [NotActions](role-definitions.md#notactions). |
| `DataActions` | Nej | String[] | En sträng mat ris som anger de data åtgärder som rollen kan utföra på dina data i objektet. Mer information finns i [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Nej | String[] | En sträng mat ris som anger de data åtgärder som undantas från tillåtna `DataActions`. Mer information finns i [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Ja | String[] | En sträng mat ris som anger de omfång som den anpassade rollen är tillgänglig för tilldelning. För anpassade roller kan du för närvarande inte ange `AssignableScopes` till rot omfånget (`"/"`) eller ett hanterings grupps omfång. Mer information finns i [AssignableScopes](role-definitions.md#assignablescopes) och [organisera dina resurser med Azures hanterings grupper](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Vem kan skapa, ta bort, uppdatera eller Visa en anpassad roll

Precis som inbyggda roller anger egenskapen `AssignableScopes` de omfattningar som rollen är tillgänglig för tilldelning. Egenskapen `AssignableScopes` för en anpassad roll kontrollerar också vem som kan skapa, ta bort, uppdatera eller Visa den anpassade rollen.

| Aktivitet | Åtgärd | Beskrivning |
| --- | --- | --- |
| Skapa/ta bort en anpassad roll | `Microsoft.Authorization/ roleDefinitions/write` | Användare som har tilldelats den här åtgärden på alla `AssignableScopes` av den anpassade rollen kan skapa (eller ta bort) anpassade roller för användning i dessa omfång. Till exempel [ägare](built-in-roles.md#owner) och [användar åtkomst administratörer](built-in-roles.md#user-access-administrator) för prenumerationer, resurs grupper och resurser. |
| Uppdatera en anpassad roll | `Microsoft.Authorization/ roleDefinitions/write` | Användare som har tilldelats den här åtgärden på alla `AssignableScopes` av den anpassade rollen kan uppdatera anpassade roller i dessa scope. Till exempel [ägare](built-in-roles.md#owner) och [användar åtkomst administratörer](built-in-roles.md#user-access-administrator) för prenumerationer, resurs grupper och resurser. |
| Visa en anpassad roll | `Microsoft.Authorization/ roleDefinitions/read` | Användare som har tilldelats den här åtgärden i ett omfång kan visa de anpassade roller som är tillgängliga för tilldelning i det aktuella omfånget. Alla inbyggda roller gör att anpassade roller kan vara tillgängliga för tilldelning. |

## <a name="next-steps"></a>Nästa steg
- [Skapa eller uppdatera anpassade Azure-roller med hjälp av Azure Portal (för hands version)](custom-roles-portal.md)
- [Förstå roll definitioner för Azure-resurser](role-definitions.md)
- [Felsöka RBAC för Azure-resurser](troubleshooting.md)

---
title: Anpassade roller för Azure-resurser | Microsoft Docs
description: Lär dig hur du skapar anpassade roller med rollbaserad åtkomstkontroll (RBAC) för detaljerad åtkomsthantering för Azure-resurser.
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
ms.date: 02/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f795571de275453738d23e80885f4d9006ca3a20
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804458"
---
# <a name="custom-roles-for-azure-resources"></a>Anpassade roller för Azure-resurser

Om den [inbyggda roller för Azure-resurser](built-in-roles.md) inte uppfyller de specifika behoven i din organisation kan du skapa dina egna anpassade roller. Precis som inbyggda roller, kan du tilldela anpassade roller till användare, grupper och tjänstens huvudnamn på prenumerationen, resursgruppen och resurs-scope. Anpassade roller lagras i en katalog i Azure Active Directory (Azure AD) och kan delas mellan prenumerationer. Varje katalog kan ha upp till 2000 anpassade roller. Anpassade roller kan skapas med hjälp av Azure PowerShell, Azure CLI eller REST API.

## <a name="custom-role-example"></a>Exempel på anpassad roll

Nedan visas hur en anpassad roll ser ut som visas i JSON-format. Den här anpassade rollen kan användas för att övervaka och starta om virtuella datorer.

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

När du skapar en anpassad roll, visas den i Azure-portalen med en orange resurs-ikon.

![Ikonen för anpassad roll](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Steg för att skapa en anpassad roll

1. Bestäm hur du vill skapa den anpassade rollen

    Du kan skapa anpassade roller med hjälp av [Azure PowerShell](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md), eller [REST API](custom-roles-rest.md).

1. Avgör vilka behörigheter som du behöver

    När du skapar en anpassad roll som du behöver veta provideråtgärder som är tillgängliga för att definiera dina behörigheter för resursen. Om du vill visa listan över åtgärder finns i den [Azure Resource Manager åtgärder för resursprovider](resource-provider-operations.md). Du ska lägga till åtgärder till den `Actions` eller `NotActions` egenskaperna för den [rolldefinition](role-definitions.md). Om du har dataåtgärder, lägger du till dem till den `DataActions` eller `NotDataActions` egenskaper.

1. Skapa anpassad roll

    Normalt börja med en befintlig inbyggd roll och modifiera den efter dina behov. Kan du använda den [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) eller [skapa az rolldefinitionen](/cli/azure/role/definition#az-role-definition-create) kommandon för att skapa den anpassade rollen. Om du vill skapa en anpassad roll måste du ha den `Microsoft.Authorization/roleDefinitions/write` behörighet på alla `AssignableScopes`, till exempel [ägare](built-in-roles.md#owner) eller [administratör för användaråtkomst](built-in-roles.md#user-access-administrator).

1. Testa den anpassade rollen

    När du har din anpassade roll kan behöva du testa den för att kontrollera att den fungerar som förväntat. Om du behöver göra justeringar senare kan du uppdatera den anpassade rollen.

En stegvis självstudiekurs om hur du skapar en anpassad roll finns i [självstudien: Skapa en anpassad roll med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md) eller [självstudien: Skapa en anpassad roll med hjälp av Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Egenskaper för anpassad roll

En anpassad roll har följande egenskaper.

| Egenskap  | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| `Name` | Ja | String | Visningsnamnet för den anpassade rollen. En rolldefinition är en prenumerationsnivå resurs, kan en rolldefinition användas i flera prenumerationer som delar samma Azure AD-katalog. Det här visningsnamnet måste vara unikt i omfattningen av Azure AD-katalog. Kan innehålla bokstäver, siffror, blanksteg och specialtecken. Maximalt antal tecken är 128. |
| `Id` | Ja | String | Unikt ID för den anpassade rollen. Detta ID genereras automatiskt för Azure PowerShell och Azure CLI, när du skapar en ny roll. |
| `IsCustom` | Ja | String | Anger om detta är en anpassad roll. Ange `true` för anpassade roller. |
| `Description` | Ja | String | Beskrivning av den anpassade rollen. Kan innehålla bokstäver, siffror, blanksteg och specialtecken. Maximalt antal tecken är 1024. |
| `Actions` | Ja | String[] | En matris med strängar som anger vilka hanteringsåtgärder som gör att rollen som ska utföras. Mer information finns i [åtgärder](role-definitions.md#actions). |
| `NotActions` | Nej | String[] | En matris med strängar som anger vilka hanteringsåtgärder som är undantagna från den tillåtna `Actions`. Mer information finns i [NotActions](role-definitions.md#notactions). |
| `DataActions` | Nej | String[] | En matris med strängar som anger de åtgärder som gör att rollen som ska utföras för dina data inom objektet. Mer information finns i [DataActions (förhandsversion)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Nej | String[] | En matris med strängar som anger de åtgärder som är undantagna från den tillåtna `DataActions`. Mer information finns i [NotDataActions (förhandsversion)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Ja | String[] | En matris med strängar som anger scope att den anpassade rollen som är tillgängliga för tilldelning. För närvarande kan inte anges till rotscopet (`"/"`) eller en grupp hanteringsomfång. Mer information finns i [AssignableScopes](role-definitions.md#assignablescopes) och [organisera dina resurser med Azure-hanteringsgrupper](../governance/management-groups/index.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Vem som kan skapa, ta bort, uppdatera eller visa en anpassad roll

Precis som inbyggda roller i `AssignableScopes` egenskap anger scope att rollen är tillgänglig för tilldelning. Den `AssignableScopes` -egenskapen för en anpassad roll kontrollerar också vem som kan skapa, ta bort, uppdatera eller visa den anpassade rollen.

| Aktivitet | Åtgärd | Beskrivning |
| --- | --- | --- |
| Skapa/ta bort en anpassad roll | `Microsoft.Authorization/ roleDefinition/write` | Användare som fått den här åtgärden på alla de `AssignableScopes` av den anpassade rollen kan skapa (eller ta bort) anpassade roller för användning i dessa omfattningar. Till exempel [ägare](built-in-roles.md#owner) och [åtkomst användaradministratörer](built-in-roles.md#user-access-administrator) prenumerationer, resursgrupper och resurser. |
| Uppdatera en anpassad roll | `Microsoft.Authorization/ roleDefinition/write` | Användare som fått den här åtgärden på alla de `AssignableScopes` av den anpassade rollen kan uppdatera anpassade roller i dessa omfattningar. Till exempel [ägare](built-in-roles.md#owner) och [åtkomst användaradministratörer](built-in-roles.md#user-access-administrator) prenumerationer, resursgrupper och resurser. |
| Visa en anpassad roll | `Microsoft.Authorization/ roleDefinition/read` | Användare som fått den här åtgärden i ett omfång kan visa anpassade roller som är tillgängliga för tilldelning i detta omfång. Alla inbyggda roller kan anpassade roller ska vara tillgängliga för tilldelning. |

## <a name="next-steps"></a>Nästa steg
- [Skapa anpassade roller för Azure-resurser med Azure PowerShell](custom-roles-powershell.md)
- [Skapa anpassade roller för Azure-resurser med Azure CLI](custom-roles-cli.md)
- [Förstå rolldefinitioner för Azure-resurser](role-definitions.md)
- [Felsöka RBAC för Azure-resurser](troubleshooting.md)

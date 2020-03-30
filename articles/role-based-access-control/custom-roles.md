---
title: Anpassade roller för Azure-resurser | Microsoft-dokument
description: Lär dig hur du skapar anpassade roller med rollbaserad åtkomstkontroll (RBAC) för finkornig åtkomsthantering av Azure-resurser.
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062175"
---
# <a name="custom-roles-for-azure-resources"></a>Anpassade roller för Azure-resurser

> [!IMPORTANT]
> Att lägga till `AssignableScopes` en hanteringsgrupp i är för närvarande i förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om de [inbyggda rollerna för Azure-resurser](built-in-roles.md) inte uppfyller organisationens specifika krav, kan du skapa egna anpassade roller. Precis som inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänsthuvudnamn i hanteringsgrupp, prenumeration och resursgruppomfång.

Anpassade roller kan delas mellan prenumerationer som litar på samma Azure AD-katalog. Det finns en gräns på **5 000** anpassade roller per katalog. (För Azure Germany och Azure China 21Vianet är gränsen 2 000 anpassade roller.) Anpassade roller kan skapas med hjälp av Azure-portalen (förhandsversion), Azure PowerShell, Azure CLI eller REST API.

## <a name="custom-role-example"></a>Exempel på anpassad roll

Följande visar hur en anpassad roll ser ut som visas i JSON-format. Den här anpassade rollen kan användas för övervakning och omstart av virtuella datorer.

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
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

När du skapar en anpassad roll visas den i Azure-portalen med en orange resursikon.

![Ikon för anpassad roll](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Steg för att skapa en anpassad roll

1. Bestäm hur du vill skapa den anpassade rollen

    Du kan skapa anpassade roller med [Azure Portal](custom-roles-portal.md) (förhandsversion), [Azure PowerShell,](custom-roles-powershell.md) [Azure CLI](custom-roles-cli.md)eller [REST API](custom-roles-rest.md).

1. Ta reda på vilka behörigheter du behöver

    När du skapar en anpassad roll måste du känna till de resursprovideråtgärder som är tillgängliga för att definiera dina behörigheter. Information om hur du visar listan över åtgärder finns i [azure Resource Manager-resursprovideråtgärderna](resource-provider-operations.md). Du lägger till `Actions` åtgärderna `NotActions` i [rolldefinitionens](role-definitions.md)eller egenskaper . Om du har dataåtgärder lägger `DataActions` du `NotDataActions` till dem i egenskaperna eller.

1. Skapa den anpassade rollen

    Vanligtvis börjar du med en befintlig inbyggd roll och ändrar den för dina behov. Sedan kan du använda kommandona [Ny-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) eller [az-rolldefinition](/cli/azure/role/definition#az-role-definition-create) för att skapa den anpassade rollen. Om du vill skapa en `Microsoft.Authorization/roleDefinitions/write` anpassad roll `AssignableScopes`måste du ha behörighet för alla , till exempel [Ägare](built-in-roles.md#owner) eller [Administratör för användaråtkomst](built-in-roles.md#user-access-administrator).

1. Testa den anpassade rollen

    När du har din anpassade roll måste du testa den för att verifiera att den fungerar som förväntat. Om du behöver göra justeringar senare kan du uppdatera den anpassade rollen.

En steg-för-steg-självstudie om hur du skapar en anpassad roll finns i [Självstudiekurs: Skapa en anpassad roll med Azure PowerShell](tutorial-custom-role-powershell.md) eller [Självstudiekurs: Skapa en anpassad roll med Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Egenskaper för anpassad roll

En anpassad roll har följande egenskaper.

| Egenskap | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| `Name` | Ja | String | Visningsnamnet för den anpassade rollen. En rolldefinition är en hanteringsgrupp eller resurs på prenumerationsnivå, men en rolldefinition kan användas i flera prenumerationer som delar samma Azure AD-katalog. Det här visningsnamnet måste vara unikt i Azure AD-katalogens omfattning. Kan innehålla bokstäver, siffror, blanksteg och specialtecken. Maximalt antal tecken är 128. |
| `Id` | Ja | String | Det unika ID:t för den anpassade rollen. För Azure PowerShell och Azure CLI genereras det här ID:et automatiskt när du skapar en ny roll. |
| `IsCustom` | Ja | String | Anger om det är en anpassad roll. Ställ `true` in på för anpassade roller. |
| `Description` | Ja | String | Beskrivningen av den anpassade rollen. Kan innehålla bokstäver, siffror, blanksteg och specialtecken. Maximalt antal tecken är 1024. |
| `Actions` | Ja | Sträng[] | En matris med strängar som anger de hanteringsåtgärder som rollen tillåter att utföras. Mer information finns i [Åtgärder](role-definitions.md#actions). |
| `NotActions` | Inga | Sträng[] | En matris med strängar som anger de hanteringsåtgärder som `Actions`är undantagna från den tillåtna . Mer information finns i [NotActions](role-definitions.md#notactions). |
| `DataActions` | Inga | Sträng[] | En matris med strängar som anger de dataåtgärder som rollen gör det möjligt att utföra till dina data i det objektet. Om du skapar en `DataActions`anpassad roll med kan den rollen inte tilldelas i hanteringsgruppens omfång. Mer information finns i [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Inga | Sträng[] | En matris med strängar som anger de dataåtgärder som `DataActions`är undantagna från den tillåtna . Mer information finns i [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Ja | Sträng[] | En matris med strängar som anger de scope som den anpassade rollen är tillgänglig för tilldelning. Du kan bara definiera `AssignableScopes` en hanteringsgrupp i en anpassad roll. Att lägga till `AssignableScopes` en hanteringsgrupp i är för närvarande i förhandsversion. Mer information finns i [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Vem kan skapa, ta bort, uppdatera eller visa en anpassad roll

Precis som inbyggda roller `AssignableScopes` anger egenskapen de scope som rollen är tillgänglig för tilldelning. Egenskapen `AssignableScopes` för en anpassad roll styr också vem som kan skapa, ta bort, uppdatera eller visa den anpassade rollen.

| Aktivitet | Åtgärd | Beskrivning |
| --- | --- | --- |
| Skapa/ta bort en anpassad roll | `Microsoft.Authorization/ roleDefinitions/write` | Användare som beviljas den `AssignableScopes` här åtgärden för alla anpassade roller kan skapa (eller ta bort) anpassade roller för användning i dessa scope. [Ägare](built-in-roles.md#owner) och [användaråtkomstadministratörer](built-in-roles.md#user-access-administrator) för hanteringsgrupper, prenumerationer och resursgrupper. |
| Uppdatera en anpassad roll | `Microsoft.Authorization/ roleDefinitions/write` | Användare som beviljas den `AssignableScopes` här åtgärden på alla anpassade roller kan uppdatera anpassade roller i dessa scope. [Ägare](built-in-roles.md#owner) och [användaråtkomstadministratörer](built-in-roles.md#user-access-administrator) för hanteringsgrupper, prenumerationer och resursgrupper. |
| Visa en anpassad roll | `Microsoft.Authorization/ roleDefinitions/read` | Användare som beviljas den här åtgärden i ett scope kan visa de anpassade roller som är tillgängliga för tilldelning i det omfånget. Alla inbyggda roller gör att anpassade roller kan vara tillgängliga för tilldelning. |

## <a name="custom-role-limits"></a>Begränsningar för anpassad roll

I följande lista beskrivs gränserna för anpassade roller.

- Varje katalog kan ha upp till **5000** anpassade roller.
- Azure Germany och Azure China 21Vianet kan ha upp till 2000 anpassade roller för varje katalog.
- Du kan `AssignableScopes` inte ange`"/"`rotomfånget ( ).
- Du kan bara definiera `AssignableScopes` en hanteringsgrupp i en anpassad roll. Att lägga till `AssignableScopes` en hanteringsgrupp i är för närvarande i förhandsversion.
- Anpassade roller `DataActions` med kan inte tilldelas i hanteringsgruppens scope.
- Azure Resource Manager validerar inte hanteringsgruppens existens i rolldefinitionens assignable scope.

Mer information om anpassade roller och hanteringsgrupper finns i [Ordna dina resurser med Azure-hanteringsgrupper](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="next-steps"></a>Nästa steg
- [Skapa eller uppdatera anpassade Azure-roller med Azure-portalen (förhandsversion)](custom-roles-portal.md)
- [Förstå rolldefinitioner för Azure-resurser](role-definitions.md)
- [Felsöka RBAC för Azure-resurser](troubleshooting.md)

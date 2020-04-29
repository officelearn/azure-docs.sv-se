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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062175"
---
# <a name="custom-roles-for-azure-resources"></a>Anpassade roller för Azure-resurser

> [!IMPORTANT]
> Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om de [inbyggda rollerna för Azure-resurser](built-in-roles.md) inte uppfyller organisationens specifika krav, kan du skapa egna anpassade roller. Precis som inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänstens huvud namn i hanterings grupp, prenumeration och resurs grupps omfång.

Anpassade roller kan delas mellan prenumerationer som har förtroende för samma Azure AD-katalog. Det finns en gräns på **5 000** anpassade roller per katalog. (För Azure Germany och Azure Kina är gränsen 2 000 anpassade roller.) Du kan skapa anpassade roller med hjälp av Azure Portal (för hands version), Azure PowerShell, Azure CLI eller REST API.

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
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

När du skapar en anpassad roll visas den i Azure Portal med en orange resurs ikon.

![Ikon för anpassad roll](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Steg för att skapa en anpassad roll

1. Bestäm hur du vill skapa den anpassade rollen

    Du kan skapa anpassade roller med [Azure Portal](custom-roles-portal.md) (för hands version), [Azure POWERSHELL](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)eller [REST API](custom-roles-rest.md).

1. Bestäm vilka behörigheter du behöver

    När du skapar en anpassad roll måste du veta vilka resurs leverantörs åtgärder som är tillgängliga för att definiera dina behörigheter. Information om hur du visar listan över åtgärder finns i [Azure Resource Manager Resource Provider-åtgärder](resource-provider-operations.md). Du kommer att lägga till åtgärder i `Actions` `NotActions` [roll definitions](role-definitions.md)-eller-egenskaperna. Om du har data åtgärder kommer du att lägga till dem i `DataActions` egenskaperna `NotDataActions` eller.

1. Skapa den anpassade rollen

    Normalt börjar du med en befintlig inbyggd roll och ändrar den efter dina behov. Sedan använder du kommandot [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) eller [AZ Role definition Create](/cli/azure/role/definition#az-role-definition-create) för att skapa den anpassade rollen. Om du vill skapa en anpassad roll måste du ha `Microsoft.Authorization/roleDefinitions/write` behörighet för alla `AssignableScopes`, till exempel [ägare](built-in-roles.md#owner) eller [administratör för användar åtkomst](built-in-roles.md#user-access-administrator).

1. Testa den anpassade rollen

    När du har en anpassad roll måste du testa den för att kontrol lera att den fungerar som förväntat. Om du behöver göra justeringar senare kan du uppdatera den anpassade rollen.

En stegvis själv studie kurs om hur du skapar en anpassad roll finns i [Självstudier: skapa en anpassad roll med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md) eller [Självstudier: skapa en anpassad roll med hjälp av Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Egenskaper för anpassad roll

En anpassad roll har följande egenskaper.

| Egenskap | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| `Name` | Ja | Sträng | Visnings namnet för den anpassade rollen. Även om en roll definition är en hanterings grupp eller en resurs på en prenumerations nivå kan en roll definition användas i flera prenumerationer som delar samma Azure AD-katalog. Det här visnings namnet måste vara unikt i Azure AD-katalogens omfång. Kan innehålla bokstäver, siffror, blank steg och specialtecken. Maximalt antal tecken är 128. |
| `Id` | Ja | Sträng | Det unika ID: t för den anpassade rollen. För Azure PowerShell och Azure CLI genereras detta ID automatiskt när du skapar en ny roll. |
| `IsCustom` | Ja | Sträng | Anger om det här är en anpassad roll. Ange till `true` för anpassade roller. |
| `Description` | Ja | Sträng | Beskrivningen av den anpassade rollen. Kan innehålla bokstäver, siffror, blank steg och specialtecken. Maximalt antal tecken är 1024. |
| `Actions` | Ja | Sträng [] | En sträng mat ris som anger vilka hanterings åtgärder som rollen kan utföra. Mer information finns i [åtgärder](role-definitions.md#actions). |
| `NotActions` | Nej | Sträng [] | En sträng mat ris som anger vilka hanterings åtgärder som undantas från tillåten `Actions`. Mer information finns i [NotActions](role-definitions.md#notactions). |
| `DataActions` | Nej | Sträng [] | En sträng mat ris som anger de data åtgärder som rollen kan utföra på dina data i objektet. Om du skapar en anpassad roll med `DataActions`kan den rollen inte tilldelas i hanterings gruppens definitions område. Mer information finns i [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Nej | Sträng [] | En sträng mat ris som anger de data åtgärder som undantas från tillåten `DataActions`. Mer information finns i [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Ja | Sträng [] | En sträng mat ris som anger de omfång som den anpassade rollen är tillgänglig för tilldelning. Du kan bara definiera en hanterings grupp `AssignableScopes` i för en anpassad roll. Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version. Mer information finns i [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Vem kan skapa, ta bort, uppdatera eller Visa en anpassad roll

Precis som inbyggda roller anger `AssignableScopes` egenskapen de omfattningar som rollen är tillgänglig för tilldelning. `AssignableScopes` Egenskapen för en anpassad roll styr också vem som kan skapa, ta bort, uppdatera eller Visa den anpassade rollen.

| Uppgift | Åtgärd | Beskrivning |
| --- | --- | --- |
| Skapa/ta bort en anpassad roll | `Microsoft.Authorization/ roleDefinitions/write` | Användare som beviljas den här åtgärden på hela `AssignableScopes` den anpassade rollen kan skapa (eller ta bort) anpassade roller för användning i dessa omfång. Till exempel [ägare](built-in-roles.md#owner) och [användar åtkomst administratörer](built-in-roles.md#user-access-administrator) för hanterings grupper, prenumerationer och resurs grupper. |
| Uppdatera en anpassad roll | `Microsoft.Authorization/ roleDefinitions/write` | Användare som har tilldelats den här åtgärden för `AssignableScopes` en anpassad roll kan uppdatera anpassade roller i dessa omfång. Till exempel [ägare](built-in-roles.md#owner) och [användar åtkomst administratörer](built-in-roles.md#user-access-administrator) för hanterings grupper, prenumerationer och resurs grupper. |
| Visa en anpassad roll | `Microsoft.Authorization/ roleDefinitions/read` | Användare som har tilldelats den här åtgärden i ett omfång kan visa de anpassade roller som är tillgängliga för tilldelning i det aktuella omfånget. Alla inbyggda roller gör att anpassade roller kan vara tillgängliga för tilldelning. |

## <a name="custom-role-limits"></a>Anpassade roll gränser

I följande lista beskrivs gränserna för anpassade roller.

- Varje katalog kan ha upp till **5000** anpassade roller.
- Azure Germany och Azure Kina 21Vianet kan ha upp till 2000 anpassade roller för varje katalog.
- Du kan inte `AssignableScopes` ange rot omfånget`"/"`().
- Du kan bara definiera en hanterings grupp `AssignableScopes` i för en anpassad roll. Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.
- Det går inte `DataActions` att tilldela anpassade roller med hanterings grupps omfånget.
- Azure Resource Manager validerar inte hanterings gruppens existens i roll definitionens tilldelnings omfång.

Mer information om anpassade roller och hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment).

## <a name="next-steps"></a>Nästa steg
- [Skapa eller uppdatera anpassade Azure-roller med hjälp av Azure Portal (för hands version)](custom-roles-portal.md)
- [Förstå roll definitioner för Azure-resurser](role-definitions.md)
- [Felsöka RBAC för Azure-resurser](troubleshooting.md)

---
title: Anpassade roller i Azure | Microsoft Docs
description: Lär dig hur du definierar anpassade roller med Azure rollbaserad åtkomstkontroll (RBAC) för tillgång till ingående åtkomsthantering av resurser i Azure.
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
ms.date: 08/07/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 26f696c2f54d9cb8a176fe079101e26384ac0a6e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034458"
---
# <a name="custom-roles-in-azure"></a>Anpassade roller i Azure

Om de [inbyggda rollerna](built-in-roles.md) inte uppfyller organisationens specifika krav kan du skapa egna, anpassade roller. Precis som inbyggda roller, kan du tilldela anpassade roller till användare, grupper och tjänstens huvudnamn på prenumerationen, resursgruppen och resurs-scope. Anpassade roller lagras i en Azure Active Directory (Azure AD)-klient och kan delas mellan prenumerationer. Varje klient kan ha upp till 2000 anpassade roller. Anpassade roller kan skapas med hjälp av Azure PowerShell, Azure CLI eller REST API.

## <a name="custom-role-example"></a>Exempel på anpassad roll

Nedan visas hur en anpassad roll ser ut som visas i JSON-format. Den här anpassade rollen kan användas för att övervaka och starta om virtuella datorer.

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
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

När du skapar en anpassad roll, visas den i Azure-portalen med en orange resurs-ikon.

![Ikonen för anpassad roll](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Steg för att skapa en anpassad roll

1. Avgör vilka behörigheter som du behöver

    När du skapar en anpassad roll som du behöver veta provideråtgärder som är tillgängliga för att definiera dina behörigheter för resursen. Om du vill visa listan över åtgärder du kan använda den [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) eller [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list) kommandon.
    Om du vill ange behörigheter för din anpassade roll du lägga till åtgärder till den `Actions` eller `NotActions` egenskaperna för den [rolldefinition](role-definitions.md). Om du har dataåtgärder kan du lägga till dem till den `DataActions` eller `NotDataActions` egenskaper.

2. Skapa anpassad roll

    Du kan använda Azure PowerShell eller Azure CLI för att skapa den anpassade rollen. Normalt börja med en befintlig inbyggd roll och modifiera den efter dina behov. Kan du använda den [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) eller [skapa az rolldefinitionen](/cli/azure/role/definition#az-role-definition-create) kommandon för att skapa den anpassade rollen. Om du vill skapa en anpassad roll måste du ha den `Microsoft.Authorization/roleDefinitions/write` behörighet på alla `AssignableScopes`, till exempel [ägare](built-in-roles.md#owner) eller [administratör för användaråtkomst](built-in-roles.md#user-access-administrator).

3. Testa den anpassade rollen

    När du har din anpassade roll kan behöva du testa den för att kontrollera att den fungerar som förväntat. Om du behöver göra justeringar senare kan du uppdatera den anpassade rollen.

En stegvis självstudiekurs om hur du skapar en anpassad roll finns i [självstudie: skapa en anpassad roll med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md) eller [självstudie: skapa en anpassad roll med hjälp av Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Egenskaper för anpassad roll

En anpassad roll har följande egenskaper.

| Egenskap  | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| `Name` | Ja | Sträng | Visningsnamnet för den anpassade rollen. Måste vara unikt för din klient. Kan innehålla bokstäver, siffror, blanksteg och specialtecken. Maximalt antal tecken är 128. |
| `Id` | Ja | Sträng | Unikt ID för den anpassade rollen. Detta ID genereras automatiskt för Azure PowerShell och Azure CLI, när du skapar en ny roll. |
| `IsCustom` | Ja | Sträng | Anger om detta är en anpassad roll. Ange `true` för anpassade roller. |
| `Description` | Ja | Sträng | Beskrivning av den anpassade rollen. Kan innehålla bokstäver, siffror, blanksteg och specialtecken. Maximalt antal tecken är 1024. |
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
- [Skapa anpassade roller med hjälp av Azure PowerShell](custom-roles-powershell.md)
- [Skapa anpassade roller med Azure CLI](custom-roles-cli.md)
- [Förstå rolldefinitioner](role-definitions.md)

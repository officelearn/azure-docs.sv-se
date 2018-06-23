---
title: Anpassade roller i Azure | Microsoft Docs
description: Lär dig att definiera anpassade roller med Azure rollbaserad åtkomstkontroll (RBAC) för detaljerad åtkomsthantering av resurser i Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 074c305cb15bc1fb25dfa5cfc52dcce53b661a7e
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321180"
---
# <a name="custom-roles-in-azure"></a>Anpassade roller i Azure

Om den [inbyggda roller](built-in-roles.md) inte uppfyller de specifika behoven i din organisation kan du skapa egna anpassade roller. Precis som inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänstens huvudnamn på prenumerationen, resursgruppen och resursen scope. Anpassade roller lagras i Azure Active Directory (Azure AD)-klient och kan delas mellan prenumerationer. Varje klient kan ha upp till 2000 anpassade roller. Anpassade roller kan skapas med hjälp av Azure PowerShell, Azure CLI eller REST API.

## <a name="custom-role-example"></a>Anpassad roll-exempel

Nedan visas en anpassad roll för övervakning och starta om virtuella datorer som visas med hjälp av Azure PowerShell:

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

När du har skapat en anpassad roll visas den i Azure-portalen med en orange resurs-ikon.

![Ikon för anpassad roll](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Steg för att skapa en anpassad roll

1. Avgör vilka behörigheter som du behöver

    När du skapar en anpassad roll som du behöver veta provideråtgärder som är tillgängliga för att definiera behörigheter för resursen. Om du vill visa lista över åtgärder som du kan använda den [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) eller [az providerlistan åtgärden](/cli/azure/provider/operation#az-provider-operation-list) kommandon.
    Om du vill ange behörigheter för den anpassade rollen som du lägger till åtgärder till den `actions` eller `notActions` egenskaper för den [rolldefinitionen](role-definitions.md). Om du har dataåtgärder kan du lägga till dem till den `dataActions` eller `notDataActions` egenskaper.

2. Skapa den anpassade rollen

    Du kan använda Azure PowerShell eller Azure CLI för att skapa den anpassade rollen. Normalt börja med en befintlig inbyggda roll och sedan ändra den efter dina behov. Kan du använda den [ny AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) eller [az rolldefinitionen skapa](/cli/azure/role/definition#az-role-definition-create) kommandon för att skapa den anpassade rollen. Om du vill skapa en anpassad roll måste du ha den `Microsoft.Authorization/roleDefinitions/write` behörighet på alla `assignableScopes`, som [ägare](built-in-roles.md#owner) eller [administratör för användaråtkomst](built-in-roles.md#user-access-administrator).

3. Testa den anpassade rollen

    När du har en anpassad roll kan behöva du testa den för att kontrollera att den fungerar som förväntat. Du kan uppdatera den anpassade rollen om justeringar behöver göras.

## <a name="custom-role-properties"></a>Egenskaper för anpassad roll

En anpassad roll har följande egenskaper.

| Egenskap  | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| `Name` | Ja | Sträng | Visningsnamnet för den anpassade rollen. Måste vara unik för din klient. Kan innehålla bokstäver, siffror, blanksteg och specialtecken. Maximalt antal tecken är 128. |
| `Id` | Ja | Sträng | Unikt ID för den anpassade rollen. Detta ID genereras automatiskt för Azure PowerShell och Azure CLI, när du skapar en ny roll. |
| `IsCustom` | Ja | Sträng | Anger om detta är en anpassad roll. Ange till `true` för anpassade roller. |
| `Description` | Ja | Sträng | Beskrivning av anpassad roll. Kan innehålla bokstäver, siffror, blanksteg och specialtecken. Maximalt antal tecken är 1024. |
| `Actions` | Ja | String[] | En matris med strängar som anger vilka hanteringsåtgärder som gör att rollen ska utföras. Mer information finns i [åtgärder](role-definitions.md#actions). |
| `NotActions` | Nej | String[] | En matris med strängar som anger vilka hanteringsåtgärder som är undantagna från den tillåtna `actions`. Mer information finns i [notActions](role-definitions.md#notactions). |
| `DataActions` | Nej | String[] | En matris med strängar som anger dataåtgärder som gör att rollen ska utföras till dina data i objektet. Mer information finns i [dataActions (förhandsgranskning)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Nej | String[] | En matris med strängar som anger hur data som är undantagna från den tillåtna `dataActions`. Mer information finns i [notDataActions (förhandsgranskning)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Ja | String[] | En matris med strängar som anger att den anpassade rollen som är tillgängliga för tilldelning scope. Kan inte anges till rotscopet (`"/"`). Mer information finns i [assignableScopes](role-definitions.md#assignablescopes). |

## <a name="assignablescopes-for-custom-roles"></a>assignableScopes för anpassade roller

Precis som inbyggda roller i `assignableScopes` egenskap anger scope att rollen är tillgängliga för tilldelning. Du kan dock använda rotscopet (`"/"`) i din egen anpassade roller. Om du försöker att du får ett felmeddelande för auktorisering. Den `assignableScopes` -egenskapen för en anpassad roll som också styr som kan skapa, ta bort, ändra eller visa den anpassade rollen.

| Aktivitet | Åtgärd | Beskrivning |
| --- | --- | --- |
| Skapa/ta bort en anpassad roll | `Microsoft.Authorization/ roleDefinition/write` | Användare som fått den här åtgärden på alla de `assignableScopes` för den anpassade rollen som kan skapa (eller ta bort) anpassade roller för användning i sådana omfattningar. Till exempel [ägare](built-in-roles.md#owner) och [användaren åtkomst administratörer](built-in-roles.md#user-access-administrator) för prenumerationer, resursgrupper och resurser. |
| Ändra en anpassad roll | `Microsoft.Authorization/ roleDefinition/write` | Användare som fått den här åtgärden på alla de `assignableScopes` för den anpassade rollen som kan ändra anpassade roller i dessa scope. Till exempel [ägare](built-in-roles.md#owner) och [användaren åtkomst administratörer](built-in-roles.md#user-access-administrator) för prenumerationer, resursgrupper och resurser. |
| Visa en anpassad roll | `Microsoft.Authorization/ roleDefinition/read` | Användare som fått den här åtgärden på ett scope kan visa de anpassade roller som är tillgängliga för tilldelning i detta scope. Alla inbyggda roller Tillåt anpassade roller ska vara tillgängliga för tilldelning. |

## <a name="next-steps"></a>Nästa steg
- [Skapa anpassade roller med hjälp av Azure PowerShell](custom-roles-powershell.md)
- [Skapa anpassade roller med hjälp av Azure CLI](custom-roles-cli.md)
- [Förstå rolldefinitioner](role-definitions.md)

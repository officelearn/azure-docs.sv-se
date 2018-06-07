---
title: Skapa anpassade roller för Azure RBAC | Microsoft Docs
description: Lär dig hur du definierar anpassade roller med rollbaserad åtkomstkontroll för mer exakta Identitetshantering i din Azure-prenumeration.
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
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e75f2cceed83d577730cd29490f80011bdb0a508
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640990"
---
# <a name="create-custom-roles-in-azure"></a>Skapa anpassade roller i Azure

Om den [inbyggda roller](built-in-roles.md) inte uppfyller dina specifika behov kan du skapa egna anpassade roller. Precis som inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänstens huvudnamn på prenumerationen, resursgruppen och resursen scope. Anpassade roller lagras i Azure Active Directory (Azure AD)-klient och kan delas mellan prenumerationer. Varje klient kan ha upp till 2000 anpassade roller. Anpassade roller kan skapas med hjälp av Azure PowerShell, Azure CLI eller REST API.

Den här artikeln beskrivs ett exempel på hur du kommer igång med att skapa anpassade roller med PowerShell och Azure CLI.

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>Skapa en anpassad roll om du vill öppna supportärenden med hjälp av PowerShell

Om du vill skapa en anpassad roll du börja med en inbyggd roll, redigera den och sedan skapa en ny roll. I det här exemplet inbyggt [Reader](built-in-roles.md#reader) roll är anpassad för att skapa en anpassad roll med namnet ”Reader supportärenden åtkomstnivå”. Tillåter användare att visa allt i prenumerationen och även öppna supportärenden.

> [!NOTE]
> Bara två inbyggda roller som tillåter användare att öppna supportärenden är [ägare](built-in-roles.md#owner) och [deltagare](built-in-roles.md#contributor). För en användare för att kunna öppna supportärenden måste han tilldelas en roll definitionsområdet prenumerationen eftersom alla supportärenden skapas baserat på en Azure-prenumeration.

I PowerShell använder den [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) kommando för att exportera den [Reader](built-in-roles.md#reader) roll i JSON-format.

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Följande visar JSON-utdata för den [Reader](built-in-roles.md#reader) roll. En typisk roll består av tre huvudavsnitt `Actions`, `NotActions`, och `AssignableScopes`. Den `Actions` avsnitt visar en lista över tillåtna operationer för rollen. För att utesluta åtgärder från `Actions`, lägga till dem i `NotActions`. Gällande behörigheter beräknas genom att subtrahera den `NotActions` åtgärder från den `Actions` åtgärder.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Därefter kan du redigera JSON-utdata för att skapa din egen roll. I det här fallet att skapa stöd tjänstbiljetter du, den `Microsoft.Support/*` måste läggas till igen. Varje åtgärd görs tillgänglig från en resursleverantör. Du kan använda för att få en lista över åtgärder för en resursleverantör, den [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) kommando eller se [Azure Resource Manager resource provider operations](resource-provider-operations.md).

Det är obligatoriskt att rollen som innehåller explicita prenumerationen ID: N där den används. Prenumerations-ID: N visas under `AssignableScopes`, annars du kommer inte att importera rollen till din prenumeration.

Slutligen måste du ange den `IsCustom` egenskapen `true` att ange att det här är en anpassad roll.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Om du vill skapa den nya anpassade rollen som du använder den [ny AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) kommando och ange definitionsfilen för uppdaterade JSON-rollen.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

När du har kört [ny AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition), den nya anpassade rollen finns i Azure portal och kan tilldelas användare.

![Skärmbild av anpassad roll som importeras i Azure-portalen](./media/custom-roles/18.png)

![Skärmbild av tilldela anpassade importerade roll till användare i samma katalog](./media/custom-roles/19.png)

![Skärmbild av behörigheter för anpassad importerade roll](./media/custom-roles/20.png)

Användare med den här anpassade rollen kan skapa nya supportförfrågningar.

![Skärmbild av anpassade roll som skapar supportärenden](./media/custom-roles/21.png)

Användare med den här anpassade rollen kan inte utföra andra åtgärder, exempelvis skapa virtuella datorer eller skapa resursgrupper.

![Skärmbild av anpassad roll går inte att skapa virtuella datorer](./media/custom-roles/22.png)

![Skärmbild av anpassad roll går inte att skapa nya RGs](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>Skapa en anpassad roll om du vill öppna supportärenden med Azure CLI

Stegen för att skapa en anpassad roll med hjälp av Azure CLI liknar med hjälp av PowerShell, förutom att JSON-utdata är olika.

I det här exemplet kan du börjar med inbyggt [Reader](built-in-roles.md#reader) roll. Att visa en lista med åtgärder för den [Reader](built-in-roles.md#reader) roll, Använd den [az rollen definitionslista](/cli/azure/role/definition#az_role_definition_list) kommando.

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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

Skapa en JSON-fil med följande format. Den `Microsoft.Support/*` åtgärden har lagts till i den `Actions` avsnitt så att användaren kan öppna supportärenden när fortsätter att vara en läsare. Du måste lägga till prenumerations-ID där den här rollen ska användas i den `AssignableScopes` avsnitt.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Använd för att skapa den nya anpassade rollen i [az rolldefinitionen skapa](/cli/azure/role/definition#az_role_definition_create) kommando.

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Den nya anpassa rollen är nu tillgänglig i Azure-portalen och processen för att använda den här rollen är desamma som i föregående avsnitt i PowerShell.

![Azure portal Skärmbild av anpassad roll som skapats med hjälp av CLI 1.0](./media/custom-roles/26.png)


## <a name="see-also"></a>Se också
- [Förstå rolldefinitioner](role-definitions.md)
- [Hantera rollbaserad åtkomstkontroll med AzurePowerShell](role-assignments-powershell.md)
- [Hantera rollbaserad åtkomstkontroll med Azure CLI](role-assignments-cli.md)
- [Hantera rollbaserad åtkomstkontroll med REST API](role-assignments-rest.md)

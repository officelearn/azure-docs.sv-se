---
title: "Skapa anpassade roller för Azure RBAC | Microsoft Docs"
description: "Lär dig hur du definierar anpassade roller med rollbaserad åtkomstkontroll för mer exakta Identitetshantering i din Azure-prenumeration."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2bb671e1870ae22eb515adc36ce0235e1d8ecddd
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Skapa anpassade roller för rollbaserad åtkomstkontroll
Skapa en anpassad roll i rollbaserad åtkomstkontroll (RBAC) om ingen av de inbyggda rollerna uppfyller dina specifika behov. Anpassade roller kan skapas med [Azure PowerShell](role-based-access-control-manage-access-powershell.md), [Azure-kommandoradsgränssnittet](role-based-access-control-manage-access-azure-cli.md) (CLI) och [REST API](role-based-access-control-manage-access-rest.md). Du kan tilldela anpassade roller till användare, grupper och program på prenumerationen, resursgruppen och resursen omfattningar precis som inbyggda roller. Anpassade roller lagras i Azure AD-klient och kan delas mellan prenumerationer.

Varje klient kan skapa upp till 2000 anpassade roller. 

I följande exempel visas en anpassad roll för övervakning och starta om virtuella datorer:

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Åtgärder
Den **åtgärder** -egenskapen för en anpassad roll anger Azure operationer som rollen ger åtkomst. Det är en samling med åtgärden strängar som identifierar skyddbara drift av Azure-resursprovidrar. Åtgärden strängar följa formatet för `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. Åtgärden strängar som innehåller jokertecken (\*) bevilja åtkomst till alla åtgärder som motsvarar den åtgärd-strängen. Exempel:

* `*/read`ger åtkomst till Läs-och skrivåtgärder för alla typer av resurser för alla Azure-resurs-providers.
* `Microsoft.Compute/*`ger åtkomst till alla åtgärder för alla typer av resurser i Microsoft.Compute-resursprovidern.
* `Microsoft.Network/*/read`ger åtkomst till Läs-och skrivåtgärder för alla typer av resurser i resursen Microsoft.Network-providern på Azure.
* `Microsoft.Compute/virtualMachines/*`ger åtkomst till alla åtgärder för virtuella datorer och dess underordnade resurstyper.
* `Microsoft.Web/sites/restart/Action`ger åtkomst till att starta om webbplatser.

Använd `Get-AzureRmProviderOperation` (i PowerShell) eller `azure provider operations show` (i Azure CLI) till Liståtgärder över providers som Azure-resurs. Du kan också använda dessa kommandon för att verifiera att en sträng för åtgärden är giltig och att expandera jokertecken åtgärden strängar.

```powershell
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Skärmbild av PowerShell - Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```azurecli
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Azure CLI skärmbild - azure provider operations visa ”Microsoft.Compute/virtualMachines/ \* /Action” ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Använd den **NotActions** egenskapen om en uppsättning åtgärder som du vill tillåta definieras enklare genom att utesluta vissa åtgärder. Åtkomst av en anpassad roll beräknas genom att subtrahera den **NotActions** åtgärder från den **åtgärder** åtgärder.

> [!NOTE]
> Om en användare har tilldelats en roll som inte omfattar en åtgärd i **NotActions**, och har tilldelats en andra roll som ger åtkomst till samma åtgärd som användaren tillåts att utföra åtgärden. **NotActions** är inte en neka regel – det är ett bekvämt sätt att skapa en uppsättning tillåtna åtgärder när specifika åtgärder måste exkluderas.
>
>

## <a name="assignablescopes"></a>AssignableScopes
Den **AssignableScopes** -egenskapen för den anpassade rollen som anger scope (prenumerationer, resursgrupper och resurser) för den anpassade rollen som är tillgängliga för tilldelning. Du kan se den anpassade rollen som tillgängliga för tilldelning i prenumerationer eller resursgrupper som kräver det och inte skapar oreda användarupplevelsen för resten av prenumerationer eller resursgrupper.

Exempel på giltiga kan tilldelas omfång:

* ”/ subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, ”/ subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624” - gör rollen tillgängliga för tilldelning i två prenumerationer.
* ”/ subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e” - gör rollen tillgängliga för tilldelning i en enda prenumeration.
* ”/ prenumerationer/c276fc76-9cd4-44c9-99a7-4fd71546436e/resursgrupper/nätverk” - tillgängliggör rollen för tilldelning i resursgruppen nätverk.

> [!NOTE]
> Du måste använda minst en prenumeration, resursgrupp eller resurs-ID.
>
>

## <a name="custom-roles-access-control"></a>Anpassade roller åtkomstkontroll
Den **AssignableScopes** -egenskapen för den anpassade rollen som också styr som kan visa, ändra och ta bort rollen.

* Vem som kan skapa en anpassad roll?
    Ägare (och administratörer för användare) för prenumerationer, resursgrupper och resurser kan skapa anpassade roller för användning i dessa scope.
    Användaren som skapar rollen måste kunna utföra `Microsoft.Authorization/roleDefinition/write` igen på alla de **AssignableScopes** av rollen.
* Vem som kan ändra en anpassad roll?
    Ägare (och administratörer för användare) för prenumerationer, resursgrupper och resurser kan ändra anpassade roller i dessa scope. Användarna behöver för att kunna utföra den `Microsoft.Authorization/roleDefinition/write` igen på alla de **AssignableScopes** av en anpassad roll.
* Vem som kan visa anpassade roller?
    Alla inbyggda roller i Azure RBAC Tillåt visning av roller som är tillgängliga för tilldelning. Användare som kan utföra den `Microsoft.Authorization/roleDefinition/read` åtgärden på ett scope kan visa RBAC-roller som är tillgängliga för tilldelning i detta scope.

## <a name="see-also"></a>Se även
* [Rollbaserad åtkomstkontroll](role-based-access-control-configure.md): komma igång med RBAC på Azure-portalen.
* En lista över tillgängliga åtgärder, se [Azure Resource Manager Resource Provider operations](role-based-access-control-resource-provider-operations.md).
* Lär dig mer om att hantera åtkomst med:
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
  * [REST API](role-based-access-control-manage-access-rest.md)
* [Inbyggda roller](role-based-access-built-in-roles.md): få information om de roller som levereras som standard i RBAC.

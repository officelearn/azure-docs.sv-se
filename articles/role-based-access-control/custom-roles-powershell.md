---
title: Skapa anpassade roller för Azure-resurser med Azure PowerShell | Microsoft Docs
description: Lär dig hur du skapar anpassade roller med rollbaserad åtkomstkontroll (RBAC för Azure-resurser med Azure PowerShell). Detta omfattar att lista, skapa, uppdatera och ta bort anpassade roller.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 49c4b74fa554d31d79c32586e8f94ef5cb7bf311
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586927"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-powershell"></a>Skapa anpassade roller för Azure-resurser med Azure PowerShell

Om den [inbyggda roller för Azure-resurser](built-in-roles.md) inte uppfyller de specifika behoven i din organisation kan du skapa dina egna anpassade roller. Den här artikeln beskriver hur du skapar och hanterar anpassade roller med hjälp av Azure PowerShell.

En stegvis självstudiekurs om hur du skapar en anpassad roll finns i [självstudien: Skapa en anpassad roll för Azure-resurser med Azure PowerShell](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa anpassade roller, behöver du:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)
- [Azure Cloudshell](../cloud-shell/overview.md) eller [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Lista anpassade roller

Om du vill visa de roller som kan tilldelas på ett scope, använda den [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) kommando. I följande exempel visar en lista över alla roller som är tillgängliga för tilldelning i den valda prenumerationen.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

I följande exempel visas bara de anpassa roller som är tillgängliga för tilldelning i den valda prenumerationen.

```azurepowershell
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Om den valda prenumerationen finns inte i den `AssignableScopes` av rollen, visas inte i listan den anpassade rollen.

## <a name="list-a-custom-role-definition"></a>Visa en anpassad rolldefinition

Om du vill visa en anpassad rolldefinition använda [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Det här är samma kommando som du använder för en inbyggd roll.

```azurepowershell
Get-AzRoleDefinition <role name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
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
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

I följande exempel visar bara åtgärder av rollen:

```azurepowershell
(Get-AzRoleDefinition <role name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

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
```

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Du kan skapa en anpassad roll med den [New AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) kommando. Det finns två metoder för att strukturera rollen med hjälp av `PSRoleDefinition` objekt eller en JSON-mall. 

### <a name="get-operations-for-a-resource-provider"></a>Hämta åtgärder för en resursprovider

När du skapar anpassade roller, är det viktigt att veta alla möjliga åtgärder från resursleverantörer.
Du kan visa listan över [åtgärder för resursprovider](resource-provider-operations.md) eller så kan du använda den [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) kommando för att hämta den här informationen.
Till exempel om du vill kontrollera alla tillgängliga åtgärder för virtuella datorer, Använd följande kommando:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Skapa en anpassad roll med PSRoleDefinition-objekt

När du använder PowerShell för att skapa en anpassad roll kan du använda en av de [inbyggda roller](built-in-roles.md) som en startpunkt eller du kan börja från början. Det första exemplet i det här avsnittet börjar med en inbyggd roll och anpassar det med fler behörigheter. Redigera attribut att lägga till den `Actions`, `NotActions`, eller `AssignableScopes` som du vill och spara ändringarna som en ny roll.

Följande exempel startar med den [virtuell Datordeltagare](built-in-roles.md#virtual-machine-contributor) inbyggd roll för att skapa en anpassad roll med namnet *VM-operatorn*. Den nya rollen ger åtkomst till alla läsåtgärder av *Microsoft.Compute*, *Microsoft.Storage*, och *Microsoft.Network* leverantörer och ger åtkomst till att starta , starta om och övervaka virtuella datorer. Den anpassade rollen som kan användas i två prenumerationer.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

I följande exempel visas ett annat sätt att skapa den *VM-operatorn* anpassad roll. Startar det genom att skapa en ny `PSRoleDefinition` objekt. Åtgärd-åtgärder har angetts i den `perms` variabeln och ange värdet som den `Actions` egenskapen. Den `NotActions` egenskapen genom att läsa den `NotActions` från den [virtuell Datordeltagare](built-in-roles.md#virtual-machine-contributor) inbyggd roll. Eftersom [virtuell Datordeltagare](built-in-roles.md#virtual-machine-contributor) har inte någon `NotActions`, den här raden är inte obligatoriskt, men den visar hur du kan hämta information från en annan roll.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Skapa en anpassad roll med JSON-mall

En JSON-mall kan användas som källdefinitionen för den anpassade rollen. I följande exempel skapas en anpassad roll som ger läsbehörighet för lagring och beräkning resurser, tillgång till support, och lägger till rollen till två prenumerationer. Skapa en ny fil `C:\CustomRoles\customrole1.json` med följande exempel. ID: T ska vara inställd på `null` inledande rollen skapats som ett nytt ID genereras automatiskt. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Kör följande PowerShell-kommando för att lägga till rollen för prenumerationerna:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

På liknande sätt som du skapar en anpassad roll, kan du ändra en befintlig anpassad roll med hjälp av antingen den `PSRoleDefinition` objekt eller en JSON-mall.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Uppdatera en anpassad roll med PSRoleDefinition-objekt

Använd först om du vill ändra en anpassad roll i [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) kommando för att hämta rolldefinitionen. Dessutom gör ändringarna i rolldefinitionen. Använd slutligen den [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) kommando för att spara ändrade rolldefinitionen.

I följande exempel läggs den `Microsoft.Insights/diagnosticSettings/*` åtgärd för att den *VM-operatorn* anpassad roll.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

I följande exempel lägger till en Azure-prenumeration till tilldelningsbara scope för den *VM-operatorn* anpassad roll.

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Uppdatera en anpassad roll med en JSON-mall

Med den tidigare JSON-mallen kan ändra du enkelt en befintlig anpassad roll om du vill lägga till eller ta bort åtgärder. Uppdatera JSON-mall och lägga till den skrivskyddade åtgärden för nätverk som du ser i följande exempel. De definitioner som förtecknas i mallen tillämpas inte kumulativt till en befintlig definition, vilket innebär att rollen visas exakt som du anger i mallen. Du måste också uppdatera Id-fältet med ID: T för rollen. Om du inte vet vad det här värdet är kan du använda den [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) cmdlet för att hämta den här informationen.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Om du vill uppdatera den befintliga rollen, kör du följande PowerShell-kommando:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

Ta bort en anpassad roll genom att använda den [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) kommando.

I följande exempel tar bort den *VM-operatorn* anpassad roll.

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Nästa steg

- [Självstudier: Skapa en anpassad roll för Azure-resurser med Azure PowerShell](tutorial-custom-role-powershell.md)
- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Azure Resource Manager åtgärder för resursprovider](resource-provider-operations.md)

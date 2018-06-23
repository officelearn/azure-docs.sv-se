---
title: Skapa anpassade roller med hjälp av Azure PowerShell | Microsoft Docs
description: Lär dig hur du skapar anpassade roller för rollbaserad åtkomstkontroll (RBAC) med hjälp av Azure PowerShell. Detta innefattar hur du visa, skapa, uppdatera och ta bort anpassade roller.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6205198d3f9c4ec5bfa2311014cf9b90dcade6dc
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320522"
---
# <a name="create-custom-roles-using-azure-powershell"></a>Skapa anpassade roller med hjälp av Azure PowerShell

Om den [inbyggda roller](built-in-roles.md) inte uppfyller de specifika behoven i din organisation kan du skapa egna anpassade roller. Den här artikeln beskriver hur du skapar och hanterar anpassade roller med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa anpassade roller, behöver du:

- Behörighet att skapa anpassade roller som [ägare](built-in-roles.md#owner) eller [administratör för användaråtkomst](built-in-roles.md#user-access-administrator)
- [Azure PowerShell](/powershell/azure/install-azurerm-ps) installeras lokalt

## <a name="list-custom-roles"></a>Lista över anpassade roller

Om du vill visa de roller som är tillgängliga för tilldelning på scopenivå, använder den [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) kommando. I följande exempel visar en lista över alla roller som är tillgängliga för tilldelning i den valda prenumerationen.

```azurepowershell
Get-AzureRmRoleDefinition | FT Name, IsCustom
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

I följande exempel visar bara de anpassa roller som är tillgängliga för tilldelning i den valda prenumerationen.

```azurepowershell
Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Om den valda prenumerationen finns inte i den `AssignableScopes` av rollen, visas inte i listan den anpassade rollen.

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Du kan skapa en anpassad roll med den [ny AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) kommando. Det finns två metoder för att strukturera rollen med hjälp av `PSRoleDefinition` objekt eller en JSON-mall. 

### <a name="get-operations-for-a-resource-provider"></a>Hämta åtgärder för en resursprovider

När du skapar anpassade roller, är det viktigt att veta alla möjliga åtgärder från resursleverantörer.
Du kan visa listan över [resource provider operations](resource-provider-operations.md) eller så kan du använda den [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) kommando för att hämta informationen.
Till exempel om du vill kontrollera alla tillgängliga åtgärder för virtuella datorer, Använd följande kommando:

```azurepowershell
Get-AzureRMProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Skapa en anpassad roll med PSRoleDefinition-objekt

När du använder PowerShell för att skapa en anpassad roll kan du använda någon av de [inbyggda roller](built-in-roles.md) som en startpunkt eller du kan starta från början. Det första exemplet i det här avsnittet börjar med en inbyggd roll och anpassar den med fler behörigheter. Redigera de attribut du vill lägga till den `Actions`, `NotActions`, eller `AssignableScopes` som du vill använda och sedan spara ändringarna som en ny roll.

Följande exempel startar med den [Virtual Machine-deltagare](built-in-roles.md#virtual-machine-contributor) inbyggd roll för att skapa en anpassad roll med namnet *virtuella operatorn*. Den nya rollen ger åtkomst till alla läsåtgärder av *Microsoft.Compute*, *Microsoft.Storage*, och *Microsoft.Network* providers och ger åtkomst till företagsresurser att starta , starta om och övervaka virtuella datorer. Den anpassade rollen som kan användas i två prenumerationer.

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
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
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzureRmRoleDefinition -Role $role
```

I följande exempel visas ett annat sätt att skapa den *virtuella operatorn* anpassad roll. Startar det genom att skapa en ny `PSRoleDefinition` objekt. Åtgärderna åtgärd har angetts i den `perms` variabeln och ange värdet i `Actions` egenskap. Den `NotActions` egenskapen genom att läsa den `NotActions` från den [Virtual Machine-deltagare](built-in-roles.md#virtual-machine-contributor) inbyggd roll. Eftersom [Virtual Machine-deltagare](built-in-roles.md#virtual-machine-contributor) inte har någon `NotActions`, den här raden är inte obligatoriskt, men den visar hur du kan hämta information från en annan roll.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read','Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzureRmRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzureRmRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Skapa en anpassad roll med JSON-mall

En JSON-mall kan användas som källdefinitionen för den anpassade rollen. I följande exempel skapas en anpassad roll som ger läsbehörighet för lagring och beräkning resurser, åtkomst till stöd för, och lägger till rollen två prenumerationer. Skapa en ny fil `C:\CustomRoles\customrole1.json` med följande exempel. Id som ska anges till `null` inledande rollen skapas som ett nytt ID genereras automatiskt. 

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
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Kör följande PowerShell-kommando för att lägga till rollen till prenumerationerna:

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Uppdatera en anpassad roll

Precis som skapar en anpassad roll kan du ändra en befintlig anpassad roll med hjälp av antingen den `PSRoleDefinition` objekt eller en JSON-mall.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Uppdatera en anpassad roll med PSRoleDefinition-objekt

Använd först om du vill ändra en anpassad roll i [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) kommando för att hämta rolldefinitionen. Andra, gör ändringarna i rolldefinitionen. Använd slutligen den [Set AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) kommando för att spara ändrade rolldefinitionen.

I följande exempel läggs den `Microsoft.Insights/diagnosticSettings/*` igen till den *virtuella operatorn* anpassad roll.

```azurepowershell
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzureRmRoleDefinition -Role $role

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

I följande exempel lägger till en Azure-prenumeration kan tilldelas omfång för den *virtuella operatorn* anpassad roll.

```azurepowershell
Get-AzureRmSubscription -SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzureRmRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzureRmSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzureRmRoleDefinition -Role $role

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

Med mallen tidigare JSON kan ändra du enkelt en befintlig anpassad roll om du vill lägga till eller ta bort åtgärder. Uppdatera JSON-mall och lägga till den skrivskyddade åtgärden för nätverk som visas i följande exempel. De definitioner som förtecknas i mallen tillämpas inte kumulativt på en befintlig definition, vilket innebär att rollen visas exakt som du anger i mallen. Du måste också uppdatera Id-fältet med ID i rollen. Om du är osäker på det här värdet är, kan du använda den [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) för att hämta informationen.

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
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Om du vill uppdatera den befintliga rollen, kör du följande PowerShell-kommando:

```azurepowershell
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll

Ta bort en anpassad roll genom att använda den [ta bort AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) kommando.

I följande exempel tar bort den *virtuella operatorn* anpassad roll.

```azurepowershell
Get-AzureRmRoleDefinition "Virtual Machine Operator"
Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

```Example
PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Nästa steg

- [Självstudier: Skapa en anpassad roll med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md)
- [Anpassade roller i Azure](custom-roles.md)
- [Provideråtgärder i Azure Resource Manager resurs](resource-provider-operations.md)

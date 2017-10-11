---
title: "Hantera rollbaserad åtkomstkontroll (RBAC) med Azure PowerShell | Microsoft Docs"
description: "Hur du hanterar RBAC med Azure PowerShell, inklusive där roller, tilldela roller och ta bort rolltilldelningar."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: d7b11df21650b5cb27f9c3dd8306f8d12664185e
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Hantera rollbaserad åtkomstkontroll med Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST-API](role-based-access-control-manage-access-rest.md)

Du kan använda rollbaserad åtkomstkontroll (RBAC) i Azure-portalen och Azure Resource Management API för att hantera åtkomst till din prenumeration på en detaljerad nivå. Med den här funktionen kan du bevilja åtkomst för Active Directory-användare, grupper eller tjänstens huvudnamn genom att tilldela vissa roller till dem för ett visst område.

Innan du kan använda PowerShell för att hantera RBAC, måste följande krav:

* Azure PowerShell version 0.8.8 eller senare. Om du vill installera den senaste versionen och koppla den till din Azure-prenumeration, se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
* Azure Resource Manager-cmdlets. Installera den [Azure Resource Manager cmdlets](/powershell/azure/overview) i PowerShell.

## <a name="list-roles"></a>Lista roller
### <a name="list-all-available-roles"></a>Visa en lista över alla tillgängliga roller
Lista RBAC-roller som är tillgängliga för tilldelning och inspektera de åtgärder som de beviljar åtkomst, använder `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell-Get AzureRmRoleDefinition – skärmbild](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Lista över åtgärder för en roll
Om du vill visa en lista med åtgärder för en viss roll, Använd `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell-Get AzureRmRoleDefinition för en viss roll – skärmbild](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Se vem som har åtkomst
Använd om du vill visa en lista med RBAC åtkomst tilldelningar `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Lista rolltilldelningar för ett visst område
Du kan se alla access-tilldelningar för en angiven prenumeration, resursgrupp eller resurs. Till exempel om du vill visa alla aktiva tilldelningar för en resursgrupp, använda `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell-Get AzureRmRoleAssignment för en resursgrupp – skärmbild](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Lista roller som tilldelas en användare
Om du vill visa en lista med alla de roller som har tilldelats en angiven användare och roller som är tilldelade till de grupper som användaren tillhör, Använd `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell-Get AzureRmRoleAssignment för en användare – skärmbild](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Lista klassiska tjänstadministratören och coadmin rolltilldelningar
Listan åtkomst tilldelningar för klassiska prenumerationer administratör och coadministrators, Använd:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Bevilja åtkomst
### <a name="search-for-object-ids"></a>Sök efter objekt-ID
Om du vill tilldela en roll som du behöver identifiera både objektet (användare, grupp eller program) och omfång.

Om du inte vet prenumerations-ID, hittar du den i den **prenumerationer** bladet på Azure-portalen. Information om hur man frågar för prenumerations-ID finns [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) på MSDN.

Om du vill hämta objekt-ID för en Azure AD-grupp, använder du:

    Get-AzureRmADGroup -SearchString <group name in quotes>

För att få objekt-ID för ett huvudnamn för tjänsten i Azure AD eller ett program, använder du:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Tilldela en roll till ett program i omfånget för prenumeration
Om du vill bevilja åtkomst till ett program i omfånget för prenumerationen, använder du:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell nya AzureRmRoleAssignment – skärmbild](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Tilldela en roll till en användare på Gruppomfång resurs
Om du vill bevilja åtkomst till en användare på Gruppomfång resurs, använder du:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell nya AzureRmRoleAssignment – skärmbild](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Tilldela en roll till en grupp i omfånget för resurs
Om du vill bevilja åtkomst till en grupp definitionsområdet resurs, använder du:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell nya AzureRmRoleAssignment – skärmbild](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Ta bort åtkomst
Ta bort åtkomst för användare, grupper och program med:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell ta bort AzureRmRoleAssignment – skärmbild](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Skapa en anpassad roll
Du kan skapa en anpassad roll med den ```New-AzureRmRoleDefinition``` kommando. Det finns två metoder för att strukturera rollen med hjälp av PSRoleDefinitionObject eller en JSON-mall. 

## <a name="get-actions-for-a-resource-provider"></a>Hämta åtgärder för en Resursprovider
När du skapar anpassade roller från början, är det viktigt att veta alla möjliga åtgärder från resursleverantörer.
Använd den ```Get-AzureRMProviderOperation``` kommando för att hämta informationen.
Om du vill kontrollera till exempel använda det här kommandot alla tillgängliga åtgärder för den virtuella datorn:

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>Skapa en roll med PSRoleDefinitionObject
När du använder PowerShell för att skapa en anpassad roll kan du börja om från början eller Använd en av de [inbyggda roller](role-based-access-built-in-roles.md) som utgångspunkt. I exemplet i det här avsnittet börjar med en inbyggd roll och anpassar den med mer behörigheter. Redigera de attribut du vill lägga till den *åtgärder*, *notActions*, eller *scope* som du vill använda och sedan spara ändringarna som en ny roll.

Följande exempel startar med den *Virtual Machine-deltagare* roll och använder som att skapa en anpassad roll kallas *virtuella operatorn*. Den nya rollen ger åtkomst till alla läsåtgärder av *Microsoft.Compute*, *Microsoft.Storage*, och *Microsoft.Network* providers och ger åtkomst till företagsresurser att starta , starta om och övervaka virtuella datorer. Den anpassade rollen som kan användas i två prenumerationer.

```
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
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell-Get AzureRmRoleDefinition – skärmbild](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>Skapa en roll med JSON-mall
En JSON-mall kan användas som källdefinitionen för den anpassade rollen. I följande exempel skapas en anpassad roll som ger läsbehörighet för lagring och beräkning resurser, åtkomst till stöd för, och lägger till rollen två prenumerationer. Skapa en ny fil `C:\CustomRoles\customrole1.json` med följande exempel. Id som ska anges till `null` inledande rollen skapas som ett nytt ID genereras automatiskt. 

```
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
Kör följande PowerShell-kommando för att lägga till rollen till prenumerationerna:
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Ändra en anpassad roll
Precis som skapar en anpassad roll kan du ändra en befintlig anpassad roll med hjälp av PSRoleDefinitionObject eller en JSON-mall.

### <a name="modify-role-with-psroledefinitionobject"></a>Ändra roll med PSRoleDefinitionObject
Använd först om du vill ändra en anpassad roll i `Get-AzureRmRoleDefinition` kommando för att hämta rolldefinitionen. Andra, gör ändringarna i rolldefinitionen. Använd slutligen den `Set-AzureRmRoleDefinition` kommando för att spara ändrade rolldefinitionen.

I följande exempel läggs den `Microsoft.Insights/diagnosticSettings/*` igen till den *virtuella operatorn* anpassad roll.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell-Set AzureRmRoleDefinition – skärmbild](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

I följande exempel lägger till en Azure-prenumeration kan tilldelas omfång för den *virtuella operatorn* anpassad roll.

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell-Set AzureRmRoleDefinition – skärmbild](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>Ändra roll med JSON-mall
Med mallen tidigare JSON kan ändra du enkelt en befintlig anpassad roll om du vill lägga till eller ta bort åtgärder. Uppdatera JSON-mall och lägga till den skrivskyddade åtgärden för nätverk som visas i följande exempel. De definitioner som förtecknas i mallen tillämpas inte kumulativt på en befintlig definition, vilket innebär att rollen visas exakt som du anger i mallen. Du måste också uppdatera Id-fältet med ID i rollen. Om du är osäker på det här värdet är, kan du använda den `Get-AzureRmRoleDefinition` för att hämta informationen.

```
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

Om du vill uppdatera den befintliga rollen, kör du följande PowerShell-kommando:
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll
Ta bort en anpassad roll genom att använda den `Remove-AzureRmRoleDefinition` kommando.

I följande exempel tar bort den *virtuella operatorn* anpassad roll.

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell ta bort AzureRmRoleDefinition – skärmbild](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Lista över anpassade roller
Om du vill visa de roller som är tillgängliga för tilldelning på scopenivå, använder den `Get-AzureRmRoleDefinition` kommando.

I följande exempel visar en lista över alla roller som är tillgängliga för tilldelning i den valda prenumerationen.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell-Get AzureRmRoleDefinition – skärmbild](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

I följande exempel visas den *virtuella operatorn* anpassad roll är inte tillgänglig i den *Production4* prenumeration eftersom den prenumerationen finns inte i den **AssignableScopes** av rollen.

![RBAC PowerShell-Get AzureRmRoleDefinition – skärmbild](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Se även
* [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]


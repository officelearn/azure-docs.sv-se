---
title: "Hantera rollbaserad åtkomstkontroll (RBAC) med Azure CLI | Microsoft Docs"
description: "Lär dig mer om att hantera rollbaserad åtkomstkontroll (RBAC) med kommandoradsgränssnittet i Azure genom att ange roller och rollen åtgärder och genom att tilldela roller till prenumeration och application-scope."
services: active-directory
documentationcenter: 
author: andredm7
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: b99264eb69f115db6e334b6aceae6ed897202d56
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Hantera rollbaserad åtkomstkontroll med kommandoradsgränssnittet i Azure
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)


Du kan använda rollbaserad åtkomstkontroll (RBAC) i Azure-portalen och Azure Resource Manager API för att hantera åtkomst till din prenumeration och resurser på en detaljerad nivå. Med den här funktionen kan du bevilja åtkomst för Active Directory-användare, grupper eller tjänstens huvudnamn genom att tilldela vissa roller till dem för ett visst område.

Innan du kan använda Azure-kommandoradsgränssnittet (CLI) för att hantera RBAC, måste du ha följande krav:

* Azure CLI version 0.8.8 eller senare. Om du vill installera den senaste versionen och koppla den till din Azure-prenumeration, se [installera och konfigurera Azure CLI](../cli-install-nodejs.md).
* Azure Resource Manager i Azure CLI. Gå till [med hjälp av Azure CLI med Resource Manager](../xplat-cli-azure-resource-manager.md) för mer information.

## <a name="list-roles"></a>Lista roller
### <a name="list-all-available-roles"></a>Visa en lista över alla tillgängliga roller
Om du vill visa en lista över alla tillgängliga roller, använder du:

        azure role list

I följande exempel visar en lista över *alla tillgängliga roller*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![Azure RBAC kommandoraden - azure rollen list - skärmbild](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>Lista över åtgärder för en roll
Om du vill visa en lista med åtgärder för en roll, använder du:

    azure role show "<role name>"

I följande exempel visas åtgärder för den *deltagare* och *Virtual Machine-deltagare* roller.

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Azure RBAC kommandoraden - azure rollen show - skärmbild](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

## <a name="list-access"></a>Listan åtkomst
### <a name="list-role-assignments-effective-on-a-resource-group"></a>Lista rolltilldelningar gälla för en resursgrupp
Om du vill visa rolltilldelningar som finns i en resursgrupp, använder du:

    azure role assignment list --resource-group <resource group name>

I följande exempel visas rolltilldelningar i den *pharma-försäljning-projecforcast* grupp.

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Skärmbild av Azure RBAC kommandoraden - azure rollen tilldelningslista av grupp-](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>Lista rolltilldelningar för en användare
Om du vill visa rolltilldelningar för en specifik användare och tilldelningar som är tilldelade till en användare, grupper, använder du:

    azure role assignment list --signInName <user email>

Du kan också se rolltilldelningar som ärvs från grupper genom att ändra kommandot:

    azure role assignment list --expandPrincipalGroups --signInName <user email>

I följande exempel visas rolltilldelningar som tilldelats den  *sameert@aaddemo.com*  användare. Detta inkluderar roller som tilldelas direkt till användare och roller som ärvs från grupper.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Azure RBAC kommandoraden - azure rollen tilldelningslista av användare – skärmbild](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

## <a name="grant-access"></a>Bevilja åtkomst
Använd om du vill bevilja åtkomst när du har identifierat den roll som du vill tilldela:

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>Tilldela en roll till gruppen prenumerationsomfattningen
Om du vill tilldela en roll till en grupp i omfånget för prenumerationen, använder du:

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

I följande exempel tilldelas den *Reader* roll *Christine Koch Team* på den *prenumeration* omfång.

![RBAC Azure kommandoraden - azure rolltilldelning Skapa grupp – skärmbild](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Tilldela en roll till ett program i omfånget för prenumeration
Om du vill tilldela en roll till ett program i omfånget för prenumerationen, använder du:

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

I följande exempel beviljas den *deltagare* rollen till en *Azure AD* på den valda prenumerationen.

 ![Kommandoraden för RBAC Azure - azure rolltilldelning skapa av program](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Tilldela en roll till en användare på Gruppomfång resurs
Om du vill tilldela en roll till en användare på Gruppomfång resurs, använder du:

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

I följande exempel beviljas den *Virtual Machine-deltagare* roll  *samert@aaddemo.com*  användare på den *Pharma-försäljning-ProjectForcast* resurs Gruppomfång.

![Kommandoraden för RBAC Azure - azure rolltilldelning skapa av användare – skärmbild](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Tilldela en roll till en grupp i omfånget för resurs
Om du vill tilldela en roll till en grupp definitionsområdet resurs, använder du:

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

I följande exempel beviljas den *Virtual Machine-deltagare* rollen till en *Azure AD* på en *undernät*.

![RBAC Azure kommandoraden - azure rolltilldelning Skapa grupp – skärmbild](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

## <a name="remove-access"></a>Ta bort åtkomst
Ta bort en rolltilldelning med:

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

I följande exempel tar bort den *Virtual Machine-deltagare* rolltilldelningen från den  *sammert@aaddemo.com*  användare på den *Pharma-försäljning-ProjectForcast* resurs grupp.
I exemplet tar bort rolltilldelningen från en grupp i prenumerationen.

![Azure RBAC kommandoraden - azure tilldelning Rollborttagning – skärmbild](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>Skapa en anpassad roll
Använd för att skapa en anpassad roll:

    azure role create --inputfile <file path>

I följande exempel skapas en anpassad roll som kallas *virtuella operatorn*. Den här anpassade rollen ger åtkomst till alla läsåtgärder av *Microsoft.Compute*, *Microsoft.Storage*, och *Microsoft.Network* resursproviders och ger åtkomst till Starta, starta om och övervaka virtuella datorer. Den här anpassade rollen kan användas i två prenumerationer. Det här exemplet används en JSON-fil som indata.

![JSON - anpassad rolldefinition – skärmbild](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![RBAC Azure kommandoraden - azure rollen skapa – skärmbild](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>Ändra en anpassad roll
Om du vill ändra en anpassad roll först använda den `azure role list` kommando för att hämta rolldefinitionen. Andra, gör ändringarna till definitionsfilen för rollen. Använd slutligen `azure role set` att spara ändrade rolldefinitionen.

    azure role set --inputfile <file path>

I följande exempel läggs den *Microsoft.Insights/diagnosticSettings/* åtgärden den **åtgärder**, och en Azure-prenumeration i **AssignableScopes** av den Virtual Machine anpassade operatörsrollen.

![JSON - ändra anpassad rolldefinition – skärmbild](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Azure RBAC kommandoraden - azure rollen set - skärmbild](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>Ta bort en anpassad roll
Ta bort en anpassad roll genom att först använda den `azure role list` kommando för att fastställa den **ID** av rollen. Använd sedan den `azure role delete` kommando för att ta bort rollen genom att ange den **ID**.

I följande exempel tar bort den *virtuella operatorn* anpassad roll.

![Azure RBAC kommandoraden - azure Rollborttagning – skärmbild](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>Lista över anpassade roller
Om du vill visa de roller som är tillgängliga för tilldelning på scopenivå, använder den `azure role list` kommando.

Följande kommando visar alla roller som är tillgängliga för tilldelning i den valda prenumerationen.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![Azure RBAC kommandoraden - azure rollen list - skärmbild](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

I följande exempel visas den *virtuella operatorn* anpassad roll är inte tillgänglig i den *Production4* prenumeration eftersom den prenumerationen finns inte i den **AssignableScopes** av rollen.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![Azure RBAC kommandoraden - azure rollen listan för anpassade roller – skärmbild](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]


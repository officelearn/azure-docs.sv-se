---
title: Så här arbetar du med dina hanteringsgrupper - Azure Governance
description: Lär dig hur du visar, underhåller, uppdaterar och tar bort hanteringsgrupphierarkin.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 94df67888c0ed0ea532844a92a362a181621d3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267943"
---
# <a name="manage-your-resources-with-management-groups"></a>Hantera dina resurser med hanteringsgrupper

Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Med Azures hanteringsgrupper får du en hanteringsnivå över prenumerationsnivån. Du kan ordna prenumerationerna i containrar som kallas hanteringsgrupper och tillämpa styrningsvillkor för hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som tillämpas för hanteringsgruppen.

Hanteringsgrupper tillhandahåller hantering i företagsklass i stor skala oavsett vilken typ av prenumeration du har.  Mer information om hanteringsgrupper finns i [Ordna dina resurser med Azure-hanteringsgrupper](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

>[!IMPORTANT]
>Azure Resource Manager-användartoken och hanteringsgruppcachen varar i 30 minuter innan de tvingas uppdatera.  När du har gjort en åtgärd som att flytta en hanteringsgrupp eller prenumeration kan det ta upp till 30 minuter att visa.  
>För att se uppdateringarna tidigare måste du uppdatera din token genom att uppdatera webbläsaren, logga in och ut eller begära en ny token.  



## <a name="change-the-name-of-a-management-group"></a>Ändra namnet på en hanteringsgrupp

Du kan ändra namnet på hanteringsgruppen med hjälp av portalen, PowerShell eller Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Ändra namnet i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj Alla > **tjänsthanteringsgrupper**. **All services**

1. Välj den hanteringsgrupp som du vill byta namn på.

1. Välj **information**.

1. Välj alternativet **Byt namn** på grupp högst upp på sidan.

   ![Byta namn på gruppalternativ på sidan hanteringsgrupp](./media/detail_action_small.png)

1. När menyn öppnas anger du det nya namnet som du vill ska visa.

   ![Byta namn på gruppfönstret för att byta namn på hanteringsgruppen](./media/rename_context.png)

1. Välj **Spara**.

### <a name="change-the-name-in-powershell"></a>Ändra namnet i PowerShell

Så här uppdaterar du visningsnamnet **Update-AzManagementGroup**. Om du till exempel vill ändra ett hanteringsgruppers visningsnamn från "Contoso IT" till "Contoso Group" kör du följande kommando:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Ändra namnet i Azure CLI

Använd uppdateringskommandot för Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Ta bort en hanteringsgrupp

Om du vill ta bort en hanteringsgrupp måste följande krav vara uppfyllda:

1. Det finns inga underordnade hanteringsgrupper eller prenumerationer under hanteringsgruppen.

   - Information om hur du flyttar en prenumeration eller hanteringsgrupp till en annan [hanteringsgrupp finns i Flytta hanteringsgrupper och prenumerationer i hierarkin](#moving-management-groups-and-subscriptions).

1. Du behöver skrivbehörighet för hanteringsgruppen ("Ägare", "Deltagare" eller "Management Group Contributor"). Om du vill se vilka behörigheter du har markerar du hanteringsgruppen och väljer sedan **IAM**. Mer information om RBAC-roller finns i [Hantera åtkomst och behörigheter med RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Ta bort i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj Alla > **tjänsthanteringsgrupper**. **All services**

1. Välj den hanteringsgrupp som du vill ta bort.

1. Välj **information**.

1. Välj **Ta bort**

    > [!TIP]
    > Om ikonen är inaktiverad visar musväljaren över ikonen orsaken om du håller musväljaren över ikonen.

   ![Alternativet Ta bort grupp](./media/delete.png)

1. Det finns ett fönster som öppnas som bekräftar att du vill ta bort hanteringsgruppen.

   ![Fönstret Ta bort gruppbekräftelse](./media/delete_confirm.png)

1. Välj **Ja**.

### <a name="delete-in-powershell"></a>Ta bort i PowerShell

Använd kommandot **Ta bort AzManagementGroup** i PowerShell för att ta bort hanteringsgrupper.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Borttagning i Azure CLI

Med Azure CLI använder du kommandot az account management-group delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Visa hanteringsgrupper

Du kan visa alla hanteringsgrupper som du har en direkt eller ärvd RBAC-roll på.  

### <a name="view-in-the-portal"></a>Visa i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj Alla > **tjänsthanteringsgrupper**. **All services**

1. Sidan hanteringsgrupphierarki läses in. På den här sidan kan du utforska alla hanteringsgrupper och prenumerationer som du har åtkomst till. Om du väljer gruppnamnet går du ned på en nivå i hierarkin. Navigeringen fungerar på samma sätt som en utforskare gör.

1. Om du vill se information om hanteringsgruppen väljer du **länken (detaljer)** bredvid rubriken för hanteringsgruppen. Om den här länken inte är tillgänglig har du inte behörighet att visa den hanteringsgruppen.

   ![Huvudformulär](./media/main.png)

### <a name="view-in-powershell"></a>Visa i PowerShell

Du använder kommandot Get-AzManagementGroup för att hämta alla grupper.  Se [Az.Resources-moduler](/powershell/module/az.resources/Get-AzManagementGroup) för den fullständiga listan över hanteringsgrupp GET PowerShell-kommandon.  

```azurepowershell-interactive
Get-AzManagementGroup
```

För information om en enskild hanteringsgrupp använder du parametern -GroupName

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Om du vill returnera en viss hanteringsgrupp och alla nivåer i hierarkin under den använder du **parametrarna -Expandera** och **-Recurse.**  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Visa i Azure CLI

Du kan använda kommandot List för att hämta alla grupper.  

```azurecli-interactive
az account management-group list
```

Om du vill ha information om en enskild hanteringsgrupp använder du kommandot visa

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Om du vill returnera en viss hanteringsgrupp och alla nivåer i hierarkin under den använder du **parametrarna -Expandera** och **-Recurse.**

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Flytta hanteringsgrupper och prenumerationer   

En anledning till att skapa en hanteringsgrupp är att bunta ihop prenumerationer. Endast hanteringsgrupper och prenumerationer kan göras till underordnade i en annan ledningsgrupp. En prenumeration som flyttas till en hanteringsgrupp ärver all användaråtkomst och alla principer för användare från den överordnade hanteringsgruppen

När du flyttar en hanteringsgrupp eller prenumeration för att vara underordnad en annan hanteringsgrupp måste tre regler utvärderas som sanna.

Om du gör flytten behöver du: 

-  Skrivbehörighet för hanteringsgrupp och skrivbehörighet för rolltilldelning för den underordnade prenumerationen eller hanteringsgruppen.
    - Det inbyggda rollexempeln **Ägare**
- Skrivbehörighet för ledningsgruppen för ledningsgruppen för hantering av ledningsgruppen för ledningsgruppen.
    - Inbyggt rollexempel: **Ägare**, **Deltagare**, **Management Group Contributor**
- Skrivbehörighet för hanteringsgrupper i den befintliga överordnade hanteringsgruppen.
    - Inbyggt rollexempel: **Ägare**, **Deltagare**, **Management Group Contributor**

**Undantag**: Om målet eller den befintliga överordnade hanteringsgruppen är rothanteringsgruppen gäller inte behörighetskraven. Eftersom rothanteringsgruppen är standardlandningsplatsen för alla nya hanteringsgrupper och prenumerationer behöver du inte behörigheter för att flytta ett objekt.

Om ägarrollen i prenumerationen ärvs från den aktuella hanteringsgruppen är dina flyttmål begränsade. Du kan bara flytta prenumerationen till en annan hanteringsgrupp där du har rollen Ägare. Du kan inte flytta den till en hanteringsgrupp där du är en deltagare eftersom du skulle förlora äganderätten till prenumerationen. Om du är direkt tilldelad ägarrollen för prenumerationen (inte ärvd från hanteringsgruppen) kan du flytta den till en hanteringsgrupp där du är deltagare. 

Om du vill se vilka behörigheter du har i Azure-portalen väljer du hanteringsgruppen och väljer sedan **IAM**. Mer information om RBAC-roller finns i [Hantera åtkomst och behörigheter med RBAC](../../role-based-access-control/overview.md).


## <a name="move-subscriptions"></a>Flytta prenumerationer 

#### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Lägga till en befintlig prenumeration i en hanteringsgrupp i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj Alla > **tjänsthanteringsgrupper**. **All services**

1. Välj den hanteringsgrupp som du planerar att vara överordnad.

1. Högst upp på sidan väljer du **Lägg till prenumeration**.

1. Välj prenumerationen i listan med rätt ID.

   ![Tillgängliga prenumerationer att lägga till i en hanteringsgrupp](./media/add_context_sub.png)

1. Välj "Spara".

#### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Ta bort en prenumeration från en hanteringsgrupp i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj Alla > **tjänsthanteringsgrupper**. **All services**

1. Välj den hanteringsgrupp som du planerar som är den aktuella överordnade.  

1. Markera ellipsen i slutet av raden för prenumerationen i listan som du vill flytta.

   ![Flytta alternativ i en hanteringsgrupp](./media/move_small.png)

1. Välj **Flytta**.

1. Välj den **överordnade hanteringsgruppen**på menyn som öppnas .

   ![Flytta fönsterruta för att ändra överordnad grupp](./media/move_small_context.png)

1. Välj **Spara**.

### <a name="move-subscriptions-in-powershell"></a>Flytta prenumerationer i PowerShell

Om du vill flytta en prenumeration i PowerShell använder du kommandot New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Om du vill ta bort länken mellan och prenumerationen och hanteringsgruppen använder du kommandot Ta bort AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Flytta prenumerationer i Azure CLI

Om du vill flytta en prenumeration i CLI använder du kommandot Lägg till.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Om du vill ta bort prenumerationen från hanteringsgruppen använder du kommandot ta bort prenumeration.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups"></a>Flytta hanteringsgrupper 

### <a name="move-management-groups-in-the-portal"></a>Flytta hanteringsgrupper i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj Alla > **tjänsthanteringsgrupper**. **All services**

1. Välj den hanteringsgrupp som du planerar att vara överordnad.

1. Högst upp på sidan väljer du **Lägg till hanteringsgrupp**.

1. Välj om du vill ha en ny eller använda en befintlig hanteringsgrupp på menyn som öppnas.

   - Om du väljer nytt skapas en ny hanteringsgrupp.
   - Om du väljer en befintlig visas en listruta för alla hanteringsgrupper som du kan flytta till den här hanteringsgruppen.  

   ![Flytta en hanteringsgrupp till en ny eller befintlig grupp](./media/add_context_MG.png)

1. Välj **Spara**.

### <a name="move-management-groups-in-powershell"></a>Flytta hanteringsgrupper i PowerShell

Använd kommandot Update-AzManagementGroup i PowerShell för att flytta en hanteringsgrupp under en annan grupp.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Flytta hanteringsgrupper i Azure CLI

Använd kommandot Update för att flytta en hanteringsgrupp med Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Granska hanteringsgrupper med hjälp av aktivitetsloggar

Hanteringsgrupper kan användas i [Azure-aktivitetsloggar](../../azure-monitor/platform/platform-logs-overview.md). Du kan fråga alla händelser som händer med en hanteringsgrupp på samma centrala plats som andra Azure-resurser.  Du kan till exempel se alla ändringar för rolltilldelningar eller principtilldelningar som gjorts i en viss hanteringsgrupp.

![Aktivitetsloggar med hanteringsgrupper](media/al-mg.png)

När du vill fråga hanteringsgrupper utanför Microsoft Azure-portalen är målområdet för hanteringsgrupper: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Referera till hanteringsgrupper från andra resursleverantörer

När du refererar till hanteringsgrupper från andra resursproviderns åtgärder använder du följande sökväg som scope. Den här sökvägen används när du använder PowerShell-, Azure CLI- och REST-API:er.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Ett exempel på hur du använder den här sökvägen är när du tilldelar en ny rolltilldelning till en hanteringsgrupp i PowerShell

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Samma scopesökväg används när du hämtar en principdefinition i en hanteringsgrupp.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](create.md)
- [Så här ändrar, raderar och hanterar du dina hanteringsgrupper](manage.md)
- [Granska hanteringsgrupper i Azure PowerShell-resursmodulen](/powershell/module/az.resources#resources)
- [Granska hanteringsgrupper i REST API](/rest/api/resources/managementgroups)
- [Granska hanteringsgrupper i Azure CLI](/cli/azure/account/management-group)
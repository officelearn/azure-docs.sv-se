---
title: Ändra, ta bort eller hantera dina hanteringsgrupper - styrning i Azure
description: Lär dig mer om att visa, underhålla, uppdatera och ta bort din grupphierarki för hantering.
author: rthorn17
ms.service: azure-resource-manager
ms.date: 02/20/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: f9e9bf51d50863f2e37a2f579c2db0edcfeca634
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801784"
---
# <a name="manage-your-resources-with-management-groups"></a>Hantera dina resurser med hanteringsgrupper

Om din organisation har många prenumerationer kan det behövas ett effektivt sätt att hantera åtkomst, principer och efterlevnad för prenumerationerna. Med Azures hanteringsgrupper får du en hanteringsnivå över prenumerationsnivån. Du kan ordna prenumerationerna i containrar som kallas hanteringsgrupper och tillämpa styrningsvillkor för hanteringsgrupperna. Alla prenumerationer i en hanteringsgrupp ärver automatiskt de villkor som tillämpas för hanteringsgruppen.

Hanteringsgrupper tillhandahåller hantering i företagsklass i stor skala oavsett vilken typ av prenumeration du har.  Mer information om hanteringsgrupper finns [organisera dina resurser med Azure-hanteringsgrupper](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="change-the-name-of-a-management-group"></a>Ändra namnet på en hanteringsgrupp

Du kan ändra namnet på hanteringsgruppen med hjälp av portalen, PowerShell eller Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Ändra namnet på i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj den hanteringsgrupp som du vill byta namn på.

1. Välj den **Byt namn på grupp** alternativet överst på sidan.

   ![Byt namn på grupp alternativ på sidan med management](./media/detail_action_small.png)

1. När menyn öppnas anger du det nya namnet som du vill ska visas.

   ![Byt namn på grupp-fönstret för att byta namn på hanteringsgrupp](./media/rename_context.png)

1. Välj **Spara**.

### <a name="change-the-name-in-powershell"></a>Ändra namnet i PowerShell

Att uppdatera visningen namn används **uppdatering AzManagementGroup**. Till exempel för att ändra en av grupper visningsnamn från ”Contoso IT” till ”Contoso-grupp”, du kör följande kommando:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Ändra namnet i Azure CLI

För Azure CLI, använder du kommandot update.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Ta bort en hanteringsgrupp

Om du vill ta bort en hanteringsgrupp, måste följande krav uppfyllas:

1. Det finns inga underordnade hanteringsgrupper eller prenumerationer i hanteringsgruppen.

   - Om du vill flytta en prenumeration från en hanteringsgrupp, se [flytta prenumeration till en annan hanteringsgrupp](#move-subscriptions-in-the-hierarchy).

   - Om du vill flytta en hanteringsgrupp till en annan hanteringsgrupp, se [flytta hanteringsgrupper i hierarkin](#move-management-groups-in-the-hierarchy).

1. Du har skrivbehörighet på hanteringsgruppen (”ägare”, ”deltagare” eller ”Management Group bidragsgivare”). För att se vilka behörigheter du har, Välj hanteringsgruppen och välj sedan **IAM**. Läs mer om RBAC-roller i [hantera åtkomst och behörighet med RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Ta bort i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj den hanteringsgrupp som du vill ta bort.

1. Välj **ta bort**

    > [!TIP]
    > Om ikonen är inaktiverad, visas orsaken om du hovrar musen-väljare över ikonen.

   ![Ta bort grupp](./media/delete.png)

1. Det finns ett fönster som öppnas bekräftar du vill ta bort hanteringsgruppen.

   ![Ta bort gruppen bekräftelsefönstret](./media/delete_confirm.png)

1. Välj **Ja**.

### <a name="delete-in-powershell"></a>Ta bort i PowerShell

Använd den **Remove-AzManagementGroup** inom PowerShell för att ta bort hanteringsgrupper.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Borttagning i Azure CLI

Med Azure CLI, använder du kommandot az kontoborttagning-hanteringsgruppen.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Visa hanteringsgrupper

Du kan visa någon hanteringsgrupp som du har en direkt eller ärvda RBAC-roll på.  

### <a name="view-in-the-portal"></a>Visa i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Läser in sidan för hantering av hierarkin. Den här sidan är där du kan utforska alla hanteringsgrupper och prenumerationer som du har åtkomst till. Gruppnamnet kommer du väljer nedåt en nivå i hierarkin. Navigeringen fungerar på samma sätt som gör en Utforskaren.

1. Om du vill se information om hanteringsgruppen, Välj den **(detaljer)** länken bredvid rubriken för hanteringsgruppen. Om den här länken inte är tillgängligt, har inte behörighet att visa den hanteringsgruppen.

   ![Huvud](./media/main.png)

### <a name="view-in-powershell"></a>Visa i PowerShell

Du kan använda kommandot Get-AzManagementGroup för att hämta alla grupper.  Se [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) moduler för en fullständig lista över management gruppen hämta Powershell-kommandon.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Information för en enskild hanteringsgrupp, använder du parametern - gruppnamn

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Du kan returnera en viss hanteringsgrupp och alla nivåer i hierarkin under den med **-Expandera** och **-Recurse** parametrar.  

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

Du kan använda kommandot lista för att hämta alla grupper.  

```azurecli-interactive
az account management-group list
```

Information för en enskild hanteringsgrupp, använder du kommandot show

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Du kan returnera en viss hanteringsgrupp och alla nivåer i hierarkin under den med **-Expandera** och **-Recurse** parametrar.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Flytta prenumerationer i hierarkin

Ett skäl till att skapa en hanteringsgrupp är att slå samman prenumerationer. Endast hanteringsgrupper och prenumerationer kan göras underordnade grupper till en annan hanteringsgrupp. En prenumeration flyttas till en hanteringsgrupp ärver alla användaråtkomst och principer från överordnad hanteringsgrupp.

Alla följande RBAC-behörigheter måste vara uppfyllda för att flytta prenumerationen:

- ”Ägare”-rollen på underordnade prenumerationen.
- ”Ägare”, ”deltagare” eller ”Management Group bidragsgivare”-rollen på målet överordnade management group.*
- ”Ägare”, ”deltagare” eller ”Management Group bidragsgivare”-rollen på den befintliga överordnade management group.*

*: Om inte målet eller den befintliga överordnade hanteringsgruppen är rot-hanteringsgruppen. Eftersom rot-hanteringsgruppen är standard landing upptäcka för alla nya hanteringsgrupper och prenumerationer, behöver inte användarna behörighet på den för att flytta ett objekt.

Se vilka behörigheter du har i Azure portal, Välj management och sedan **IAM**. Läs mer om RBAC-roller i [hantera åtkomst och behörighet med RBAC](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Flytta prenumerationer i portalen

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Lägg till en befintlig prenumeration till en hanteringsgrupp

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj den hanteringsgrupp som du planerar att överordnad.

1. Längst ned på sidan Välj **Lägg till prenumeration**.

1. Välj prenumerationen i listan med rätt-ID.

   ![Tillgängliga prenumerationer att lägga till i en hanteringsgrupp](./media/add_context_sub.png)

1. Välj ”Spara”.

#### <a name="remove-a-subscription-from-a-management-group"></a>Ta bort en prenumeration från en hanteringsgrupp

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj den hanteringsgrupp som du planerar det vill säga den aktuella överordnat.  

1. Välj ellipsen i slutet av raden för prenumerationen i listan som du vill flytta.

   ![Flytta alternativet på en hanteringsgrupp](./media/move_small.png)

1. Välj **flytta**.

1. På den meny som öppnas väljer du den **överordnad hanteringsgrupp**.

   ![Flytta fönstret för att ändra överordnad grupp](./media/move_small_context.png)

1. Välj **Spara**.

### <a name="move-subscriptions-in-powershell"></a>Flytta prenumerationer i PowerShell

Om du vill flytta en prenumeration i PowerShell kan du använda kommandot New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Ta bort länken mellan och -prenumeration och hanteringsgruppen använder du kommandot Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Flytta prenumerationer i Azure CLI

Om du vill flytta en prenumeration i CLI kan du använda kommandot Lägg till.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Använd kommandot remove prenumeration för att ta bort prenumerationen från hanteringsgruppen.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Flytta hanteringsgrupper i hierarkin  

När du flyttar en överordnad hanteringsgrupp flyttas i hierarkin under den gruppen med den.

### <a name="move-management-groups-in-the-portal"></a>Flytta hanteringsgrupper i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj den hanteringsgrupp som du planerar att överordnad.

1. Längst ned på sidan Välj **Lägg till hanteringsgrupp**.

1. I menyn som öppnas, väljer du om du vill att en ny eller använda en befintlig hanteringsgrupp.

   - Att välja ny skapar en ny hanteringsgrupp.
   - Att välja en befintlig presenterar du med en listruta för alla hanteringsgrupper som du kan flytta till den här hanteringsgruppen.  

   ![Flytta en hanteringsgrupp till ny eller befintlig grupp](./media/add_context_MG.png)

1. Välj **Spara**.

### <a name="move-management-groups-in-powershell"></a>Flytta hanteringsgrupper i PowerShell

Använd kommandot Update-AzManagementGroup i PowerShell för att flytta en hanteringsgrupp under en annan grupp.

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'Contoso' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>Flytta hanteringsgrupper i Azure CLI

Använd kommandot update för att flytta en hanteringsgrupp med Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent-id '/providers/Microsoft.Management/managementGroups/ContosoIT'
```

## <a name="audit-management-groups-using-activity-logs"></a>Granska hanteringsgrupper med hjälp av aktivitetsloggar

Hanteringsgrupper kan användas i [Azure-aktivitetsloggar](../../azure-monitor/platform/activity-logs-overview.md). Du kan fråga efter alla händelser som sker till en hanteringsgrupp i samma central plats som andra Azure-resurser.  Du kan till exempel se alla ändringar för rolltilldelningar eller principtilldelningar som gjorts i en viss hanteringsgrupp.

![Aktivitetsloggar med hanteringsgrupper](media/al-mg.png)

När du vill fråga hanteringsgrupper utanför Microsoft Azure-portalen är målområdet för hanteringsgrupper: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Refererar till hanteringsgrupper från andra Resursprovidrar

När du refererar till hanteringsgrupper från andra Resursprovidern åtgärder, kan du använda följande sökväg som omfång. Den här sökvägen används när du använder PowerShell, Azure CLI och REST API: er.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Ett exempel på hur du använder den här sökvägen är när du tilldelar en ny rolltilldelning till en hanteringsgrupp i PowerShell

```powershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Samma omfång sökväg används vid hämtning av en principdefinition i en hanteringsgrupp.

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
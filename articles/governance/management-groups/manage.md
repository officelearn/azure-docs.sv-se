---
title: Ändra, ta bort eller hantera dina hanteringsgrupper i Azure
description: Lär dig mer om att underhålla och uppdatera din grupphierarki för hantering.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: rithorn
ms.openlocfilehash: 627ef0123f05e768dd8a83c197b25da7f161a37c
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853004"
---
# <a name="manage-your-resources-with-management-groups"></a>Hantera dina resurser med hanteringsgrupper

Av hanteringsgrupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Du kan ändra, ta bort och hantera de här behållarna om du vill ha hierarkier som kan användas med [Azure Policy](../policy/overview.md) och [Azure rollbaserad kontroller (RBAC)](../../role-based-access-control/overview.md). Mer information om hanteringsgrupper finns [organisera dina resurser med Azure-hanteringsgrupper](overview.md).

Om du vill göra ändringar i en hanteringsgrupp, måste du ha en rollen som Prenumerationsägare eller deltagare på hanteringsgruppen. För att se vilka behörigheter du har, Välj hanteringsgruppen och välj sedan **IAM**. Läs mer om RBAC-roller i [hantera åtkomst och behörighet med RBAC](../../role-based-access-control/overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Ändra namnet på en hanteringsgrupp

Du kan ändra namnet på hanteringsgruppen med hjälp av portalen, PowerShell eller Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Ändra namnet på i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj den hanteringsgrupp som du vill byta namn på.

1. Välj den **Byt namn på grupp** alternativet överst på sidan.

   ![Byt namn på grupp](./media/detail_action_small.png)

1. När menyn öppnas anger du det nya namnet som du vill ska visas.

   ![Byt namn på grupp](./media/rename_context.png)

1. Välj **Spara**.

### <a name="change-the-name-in-powershell"></a>Ändra namnet i PowerShell

Att uppdatera visningen namn används **uppdatering AzureRmManagementGroup**. Om du vill ändra en grupper hanteringsnamn från ”Contoso IT” till ”Contoso-grupp”, kan du till exempel köra följande kommando:

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Ändra namnet i Azure CLI

För Azure CLI, använder du kommandot update.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Ta bort en hanteringsgrupp

Om du vill ta bort en hanteringsgrupp, måste följande krav uppfyllas:

1. Det finns inga underordnade hanteringsgrupper eller prenumerationer i hanteringsgruppen.

   - Om du vill flytta en prenumeration från en hanteringsgrupp, se [flytta prenumeration till en annan hanteringsgrupp](#Move-subscriptions-in-the-hierarchy).

   - Om du vill flytta en hanteringsgrupp till en annan hanteringsgrupp, se [flytta hanteringsgrupper i hierarkin](#Move-management-groups-in-the-hierarchy).

1. Du har skrivbehörighet på den gruppen ägare eller deltagare hanteringsroll på hanteringsgruppen. För att se vilka behörigheter du har, Välj hanteringsgruppen och välj sedan **IAM**. Läs mer om RBAC-roller i [hantera åtkomst och behörighet med RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Ta bort i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj den hanteringsgrupp som du vill ta bort.

1. Välj **ta bort**

   - Om ikonen är inaktiverad, visas orsaken om du hovrar musen-väljare över ikonen.

   ![ta bort grupp](./media/delete.png)

1. Det finns ett fönster som öppnas bekräftar du vill ta bort hanteringsgruppen.

   ![ta bort grupp](./media/delete_confirm.png)

1. Välj **Ja**.

### <a name="delete-in-powershell"></a>Ta bort i PowerShell

Använd den **Remove-AzureRmManagementGroup** inom PowerShell för att ta bort hanteringsgrupper.

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName 'Contoso'
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

1. Grupphierarki management sidan belastning där du kan utforska alla hanteringsgrupper och prenumerationer som du har åtkomst till. Gruppnamnet kommer du väljer nedåt en nivå i hierarkin. Navigeringen fungerar på samma sätt som gör en Utforskaren.

   ![Huvud](./media/main.png)

1. Om du vill se information om hanteringsgruppen, Välj den **(detaljer)** länken bredvid rubriken för hanteringsgruppen. Om den här länken inte är tillgängligt, har inte behörighet att visa den hanteringsgruppen.  

### <a name="view-in-powershell"></a>Visa i PowerShell

Du kan använda kommandot Get-AzureRmManagementGroup för att hämta alla grupper.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```

Information för en enskild hanteringsgrupp, använder du parametern - gruppnamn

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName 'Contoso'
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

## <a name="move-subscriptions-in-the-hierarchy"></a>Flytta prenumerationer i hierarkin

Ett skäl till att skapa en hanteringsgrupp är att slå samman prenumerationer. Endast hanteringsgrupper och prenumerationer kan göras underordnade grupper till en annan hanteringsgrupp. En prenumeration flyttas till en hanteringsgrupp ärver alla användaråtkomst och principer från överordnad hanteringsgrupp.

Om du vill flytta prenumerationen som finns det några behörigheter måste du ha:

- ”Ägare”-rollen på underordnade prenumerationen.
- Rollen ”ägare” eller ”deltagare” på den nya överordnade hanteringsgruppen.
- Rollen ”ägare” eller ”bidragsgivare” på den gamla överordnad hanteringsgruppen.

För att se vilka behörigheter du har, Välj hanteringsgruppen och välj sedan **IAM**. Läs mer om RBAC-roller i [hantera åtkomst och behörighet med RBAC](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Flytta prenumerationer i portalen

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Lägg till en befintlig prenumeration till en hanteringsgrupp

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj den hanteringsgrupp som du planerar att överordnad.

1. Längst ned på sidan Välj **Lägg till prenumeration**.

1. Välj prenumerationen i listan med rätt-ID.

   ![Underordnade](./media/add_context_sub.png)

1. Välj ”Spara”.

#### <a name="remove-a-subscription-from-a-management-group"></a>Ta bort en prenumeration från en hanteringsgrupp

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj den hanteringsgrupp som du planerar det vill säga den aktuella överordnat.  

1. Välj ellipsen i slutet av raden för prenumerationen i listan som du vill flytta.

   ![Flytta](./media/move_small.png)

1. Välj **flytta**.

1. På den meny som öppnas väljer du den **överordnad hanteringsgrupp**.

   ![Flytta](./media/move_small_context.png)

1. Välj **Spara**.

### <a name="move-subscriptions-in-powershell"></a>Flytta prenumerationer i PowerShell

Om du vill flytta en prenumeration i PowerShell kan du använda kommandot New-AzureRmManagementGroupSubscription.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Ta bort länken mellan och -prenumeration och hanteringsgruppen använder du kommandot Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
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

När du flyttar en överordnad hanteringsgrupp alla underordnade resurser med hanteringsgrupper, prenumerationer, resursgrupper och resurser nu med överordnat.

### <a name="move-management-groups-in-the-portal"></a>Flytta hanteringsgrupper i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj den hanteringsgrupp som du planerar att överordnad.

1. Längst ned på sidan Välj **Lägg till hanteringsgrupp**.

1. I menyn som öppnas, väljer du om du vill att en ny eller använda en befintlig hanteringsgrupp.

   - Att välja ny skapar en ny hanteringsgrupp.
   - Att välja en befintlig presenterar du med en listruta för alla hanteringsgrupper som du kan flytta till den här hanteringsgruppen.  

   ![Flytta](./media/add_context_MG.png)

1. Välj **Spara**.

### <a name="move-management-groups-in-powershell"></a>Flytta hanteringsgrupper i PowerShell

Använd kommandot Update-AzureRmManagementGroup i PowerShell för att flytta en hanteringsgrupp under en annan grupp.

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'Contoso' -ParentName 'ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>Flytta hanteringsgrupper i Azure CLI

Använd kommandot update för att flytta en hanteringsgrupp med Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent 'Contoso Tenant'
```

## <a name="next-steps"></a>Nästa steg

Mer information om hanteringsgrupper finns:

- [Organisera dina resurser med Azure-hanteringsgrupper](overview.md)
- [Skapa hanteringsgrupper för att organisera Azure-resurser](create.md)
- [Installera Azure Powershell-modulen](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Granska specifikationerna för REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Installera Azure CLI-tillägg](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)

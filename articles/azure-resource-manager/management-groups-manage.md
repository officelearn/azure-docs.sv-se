---
title: Ändra, ta bort eller hantera dina hanteringsgrupper - Azure | Microsoft Docs
description: Lär dig mer om att underhålla och uppdatera din grupphierarki för hantering.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/22/2018
ms.author: rithorn
ms.openlocfilehash: 0a13627232904f4b14cdb5cbf5c3ca927d9ea167
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754669"
---
# <a name="manage-your-resources-with-management-groups"></a>Hantera resurser med av hanteringsgrupper 
Hanteringsgrupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Du kan ändra, ta bort och hantera dessa behållare för hierarkier som kan användas med [Azure princip](../azure-policy/azure-policy-introduction.md) och [Azure rollbaserad åtkomst kontroller (RBAC)](../role-based-access-control/overview.md). Läs mer om av hanteringsgrupper i [ordna dina resurser med Azure hanteringsgrupper ](management-groups-overview.md).

Funktionen för hantering av grupp finns i en förhandsversion. Om du vill börja använda hanteringsgrupper, logga in på den [Azure-portalen](https://portal.azure.com) eller så kan du använda [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [Azure CLI](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available), eller [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) till hantera dina hanteringsgrupper.

Om du vill ändra en hanteringsgrupp måste du ha en ägare eller deltagare roll på hanteringsgruppen. För att se vilka behörigheter som du har, Välj hanteringsgruppen och välj sedan **IAM**. Mer information om RBAC-roller finns [hantera åtkomst och behörighet med RBAC](../role-based-access-control/overview.md).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Ändra namnet på en hanteringsgrupp 
Du kan ändra namnet på hanteringsgruppen med hjälp av portalen, PowerShell eller Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Ändra namnet på portalen

1. Logga in på den [Azure-portalen](https://portal.azure.com)
2. Välj **alla tjänster** > **hanteringsgrupper**  
3. Välj den hanteringsgrupp som du vill byta namn på. 
4. Välj den **Byt namn på grupp** alternativet överst på sidan. 

    ![Byt namn på grupp](media/management-groups/detail_action_small.png)
5. Ange det nya namnet som du vill ska visas när menyn öppnas.

    ![Byt namn på grupp](media/management-groups/rename_context.png) 
4. Välj **Spara**. 

### <a name="change-the-name-in-powershell"></a>Ändra namnet i PowerShell

Uppdatera visningen namn används **uppdatering AzureRmManagementGroup**. Om du vill ändra ett grupper namn från ”Contoso IT” till ”Contoso-grupp”, kan du exempelvis kör följande kommando: 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>Ändra namnet i Azure CLI

Använd kommandot update för Azure CLI. 

```azurecli-interactive
az account management-group update --name Contoso --display-name "Contoso Group" 
```

---
 
## <a name="delete-a-management-group"></a>Ta bort en hanteringsgrupp
Om du vill ta bort en hanteringsgrupp, måste följande krav uppfyllas:
1. Det finns inga hanteringsgrupper för underordnade eller prenumerationer under hanteringsgruppen. 
    - Om du vill flytta en prenumeration utanför en hanteringsgrupp finns [flytta prenumerationen till en annan managemnt](#Move-subscriptions-in-the-hierarchy). 
    - Om du vill flytta en hanteringsgrupp till en annan hanteringsgrupp finns [flytta hanteringsgrupper i hierarkin](#Move-management-groups-in-the-hierarchy). 
2. Du har skrivbehörighet för hantering av gruppen ägare eller deltagare rollen i hanteringsgruppen. För att se vilka behörigheter som du har, Välj hanteringsgruppen och välj sedan **IAM**. Mer information på RBAC-roller finns [hantera åtkomst och behörighet med RBAC](../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Ta bort i portalen

1. Logga in på den [Azure-portalen](https://portal.azure.com)
2. Välj **alla tjänster** > **hanteringsgrupper**  
3. Välj den hanteringsgrupp som du vill ta bort. 
    
    ![ta bort grupp](media/management-groups/delete.png)
4. Välj **Ta bort**. 
    - Om ikonen inaktiveras visas din mus selector hovrar över ikonen orsaken. 
5. Det finns ett fönster som öppnas bekräftar du vill ta bort hanteringsgruppen. 

    ![ta bort grupp](media/management-groups/delete_confirm.png) 
6. Välj **Ja** 


### <a name="delete-in-powershell"></a>Ta bort i PowerShell

Använd den **ta bort AzureRmManagementGroup** inom PowerShell för att ta bort hanteringsgrupper. 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>Borttagning i Azure CLI
Med Azure CLI, använder du kommandot az konto-hanteringsgruppen. 

```azurecli-interactive
az account management-group delete --name Contoso
```
---

## <a name="view-management-groups"></a>Visa hanteringsgrupper
Du kan visa en hanteringsgrupp som du har en direkt eller ärvda RBAC roll på.  

### <a name="view-in-the-portal"></a>Visa på portalen
1. Logga in på den [Azure-portalen](https://portal.azure.com)
2. Välj **alla tjänster** > **hanteringsgrupper** 
3. Grupp hanteringshierarkin sidan belastningar där du kan utforska alla hanteringsgrupper och prenumerationer som du har åtkomst till. Att välja gruppnamnet går du ned en nivå i hierarkin. Navigeringen fungerar som en Utforskaren. 
    ![Main](media/management-groups/main.png)
4. Om du vill se information om hanteringsgruppen, Välj den **(detaljer)** länken bredvid rubriken för hanteringsgruppen. Om den här länken inte finns kan har du inte behörighet att visa den hanteringsgruppen.  

### <a name="view-in-powershell"></a>Visa i PowerShell
Du kan använda kommandot Get-AzureRmManagementGroup för att hämta alla grupper.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
Använd parametern - gruppnamn för en enskild hanteringsgrupp information

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>Vyn i Azure CLI
Du kan använda kommandot lista för att hämta alla grupper.  

```azurecli-interactive
az account management-group list
```
Använd kommandot Visa information för en enskild hanteringsgrupp

```azurecli-interactive
az account management-group show --name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>Flytta prenumerationer i hierarkin
En orsak till att skapa en hanteringsgrupp är att samla prenumerationer tillsammans. Endast hanteringsgrupper och prenumerationer kan göras underordnade till en annan hanteringsgrupp. En prenumeration som flyttar till en hanteringsgrupp ärver alla användaråtkomst och principer från den överordnade hanteringsgruppen. 

Om du vill flytta prenumerationen finns några behörigheter måste du ha: 
- ”Ägare”-rollen på underordnade prenumerationen.
- Rollen ”ägare” eller ”bidragsgivare” på den nya överordnade hanteringsgruppen. 
- Rollen ”ägare” eller ”bidragsgivare” på den gamla överordnade hanteringsgruppen.
För att se vilka behörigheter som du har, Välj hanteringsgruppen och välj sedan **IAM**. Mer information på RBAC-roller finns [hantera åtkomst och behörighet med RBAC](../role-based-access-control/overview.md). 

### <a name="move-subscriptions-in-the-portal"></a>Flytta prenumerationer i portalen

**Lägg till en befintlig prenumeration till en hanteringsgrupp**
1. Logga in på den [Azure-portalen](https://portal.azure.com)
2. Välj **alla tjänster** > **hanteringsgrupper** 
3. Välj den hanteringsgrupp som du planerar att överordnad.      
5. Överst på sidan Välj **lägga till befintliga**. 
6. I menyn som öppnas väljer du den **resurstypen** av objektet du försöker flytta vilket **prenumeration**.
7. Välj prenumerationen i listan med rätt-ID. 

    ![Underordnade](media/management-groups/add_context_2.png)
8. Välj ”Spara”

**Ta bort en prenumeration från en hanteringsgrupp**
1. Logga in på den [Azure-portalen](https://portal.azure.com)
2. Välj **alla tjänster** > **hanteringsgrupper** 
3. Välj den hanteringsgrupp som du planerar att den aktuella överordnat.  
4. Välj ellipsen i slutet av raden för prenumerationen i listan som du vill flytta.

    ![Flytta](media/management-groups/move_small.png)
5. Välj **flytta**
6. På menyn som öppnas väljer du den **överordnade hanteringsgruppen**.

    ![Flytta](media/management-groups/move_small_context.png)
7. Välj **spara**

### <a name="move-subscriptions-in-powershell"></a>Flytta prenumerationer i PowerShell
Om du vill flytta en prenumeration i PowerShell, kan du använda kommandot Lägg till AzureRmManagementGroupSubscription.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

Ta bort länken mellan och prenumeration och hanteringsgruppen använder du kommandot Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>Flytta prenumerationer i Azure CLI
Om du vill flytta en prenumeration i CLI, kan du använda kommandot Lägg till. 

```azurecli-interactive
az account management-group subscription add --name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

Använd kommandot prenumeration ta bort för att ta bort prenumerationen från hanteringsgruppen.  

```azurecli-interactive
az account management-group subscription remove --name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>Flytta hanteringsgrupper i hierarkin  
När du flyttar en överordnad hanteringsgrupp alla underordnade resurser som innehåller hanteringsgrupper, prenumerationer, resursgrupper och flytta resurser med överordnat.   

### <a name="move-management-groups-in-the-portal"></a>Flytta hanteringsgrupper i portalen

1. Logga in på den [Azure-portalen](https://portal.azure.com)
2. Välj **alla tjänster** > **hanteringsgrupper** 
3. Välj den hanteringsgrupp som du planerar att överordnad.      
5. Överst på sidan Välj **lägga till befintliga**.
6. I menyn som öppnas väljer du den **resurstypen** av objektet du försöker flytta vilket **Hanteringsgruppen**.
7. Välj hanteringsgruppen med rätt ID och namn.

    ![Flytta](media/management-groups/add_context.png)
8. Välj **spara**

### <a name="move-management-groups-in-powershell"></a>Flytta hanteringsgrupper i PowerShell
Använd kommandot Update AzureRmManagementGroup i PowerShell för att flytta en hanteringsgrupp under en annan grupp.  

```powershell
Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  
### <a name="move-management-groups-in-azure-cli"></a>Flytta hanteringsgrupper i Azure CLI
Använd kommandot update om du vill flytta en hanteringsgrupp med Azure CLI. 

```azurecli-interactive
az account management-group update --name Contoso --parent "Contoso Tenant" 
``` 

---

## <a name="next-steps"></a>Nästa steg 
Mer information om hanteringsgrupper finns: 
- [Ordna dina resurser med Azure hanteringsgrupper ](management-groups-overview.md)
- [Skapa hanteringsgrupper för att organisera Azure-resurser](management-groups-create.md)
- [Installera Azure Powershell-modulen](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Granska REST API-specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Installera tillägg för Azure CLI](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

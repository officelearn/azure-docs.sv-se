---
title: "Skapa hanteringsgrupper för att organisera Azure-resurser | Microsoft Docs"
description: "Lär dig mer om att skapa Azure-hantering av grupper för att hantera flera resurser."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2018
ms.author: rithorn
ms.openlocfilehash: 7c5aeca5afe8921ab39040e9afc2921b1711c447
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Skapa hanteringsgrupper för resursorganisationen och hantering
Hanteringsgrupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa dessa behållare för att bygga en effektiv hierarki som kan användas med [Azure princip](../azure-policy/azure-policy-introduction.md) och [baserat åtkomstkontroller i Azure rollen](../active-directory/role-based-access-control-what-is.md). Mer information om hanteringsgrupper finns [ordna dina resurser med Azure hanteringsgrupper ](management-groups-overview.md). 

Funktionen för hantering av grupp finns i en förhandsversion. Börja använda hantering av grupper, logga in på den [Azure-portalen](https://portal.azure.com) eller så kan du använda [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available), eller [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) till Skapa hanteringsgrupper.   

Den första hanteringsgruppen som skapats i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera tjänsten management-grupper i Azure för din katalog. Du får ett meddelande när uppdateringen är klar.  

## <a name="how-to-create-a-management-group"></a>Så här skapar du en hanteringsgrupp
Du kan skapa hanteringsgruppen med hjälp av portalen, PowerShell eller Azure CLI.

### <a name="create-in-portal"></a>Skapa i portalen

1. Logga in på den [Azure-portalen](http://portal.azure.com).
2. Välj **alla tjänster** > **hanteringsgrupper**.
3. Välj på huvudsidan i **nya hanteringsgruppen.** 

    ![Skapa grupp](media/management-groups/create_main.png) 
4.  Fyll i hantering av grupp-ID-fältet. 
    - Den **Hanteringsgruppens ID** är den unika identifieraren för katalogen som används för att skicka kommandon på den här hanteringsgruppen. Den här identifieraren kan inte redigeras när den har skapats eftersom den används i hela systemet Azure för att identifiera den här gruppen. 
    - Visningsnamnet är det namn som visas i Azure-portalen. Ett separat visningsnamn är valfri när du skapar management gruppen och kan ändras när som helst.  

    ![Skapa](media/management-groups/create_context_menu.png)  
5.  Välj **spara**


### <a name="create-in-powershell"></a>Skapa i PowerShell
I PowerShell, kan du använda Lägg till AzureRmManagementGroups-cmdlets.   

```azurepowershell-interactive
C:\> Add-AzureRmManagementGroup -GroupName Contoso 
```
Den **GroupName** är en unik identifierare som håller på att skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill använda hanteringsgruppen som visar ett annat namn på Azure-portalen kan du lägga till den **DisplayName** parameter med strängen. Om du vill skapa en hanteringsgrupp med GroupName Contoso och visningsnamnet för ”Contoso-grupp” kan du använda följande cmdlet: 

```azurepowershell-interactive
C:\> Add-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
``` 
Använd den **ParentId** parametern ska ha den här hanteringsgruppen skapas under en annan hanteringsserver.  

### <a name="create-in-azure-cli"></a>Skapa i Azure CLI
På Azure CLI, använder du az konto hanteringsgruppen skapa kommando. 

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
``` 

---

## <a name="next-steps"></a>Nästa steg 
Mer information om hanteringsgrupper finns: 
- [Ordna dina resurser med Azure hanteringsgrupper ](management-groups-overview.md)
- [Ändra, ta bort eller hantera dina hanteringsgrupper](management-groups-manage.md)
- [Installera Azure Powershell-modulen](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Granska REST API-specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Installera tillägg för Azure CLI](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

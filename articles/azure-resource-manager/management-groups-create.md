---
title: Skapa hanteringsgrupper för att organisera Azure-resurser | Microsoft Docs
description: Lär dig hur du skapar Azure hanteringsgrupper för att hantera flera resurser.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2018
ms.author: rithorn
ms.openlocfilehash: b35803de2cb503418d4373fe3429b81ec5474de4
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358705"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Skapa hanteringsgrupper för resursorganisationen och hantering

Av hanteringsgrupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa de här behållarna för att skapa en och effektiva hierarki som kan användas med [Azure Policy](../azure-policy/azure-policy-introduction.md) och [baserade åtkomstkontroller i Azure-rollen](../role-based-access-control/overview.md). Mer information om hanteringsgrupper finns i [organisera dina resurser med Azure-hanteringsgrupper ](management-groups-overview.md).

Den första hanteringsgruppen som skapats i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången du konfigurerar tjänsten management-grupper i Azure för din katalog. Du får ett meddelande när processen är klar.  

## <a name="create-a-management-group"></a>Skapa en hanteringsgrupp

Du kan skapa hanteringsgruppen med hjälp av portalen, PowerShell eller Azure CLI.

### <a name="create-in-portal"></a>Skapa i portalen

1. Logga in på [Azure Portal](http://portal.azure.com).
2. Välj **alla tjänster** > **hanteringsgrupper**.
3. Välj på huvudsidan **ny hanteringsgrupp.**

    ![HUVUDGRUPP](media/management-groups/main.png)
4.  Fyll i hantering av grupp-ID-fältet.
    - Den **Hanteringsgruppens ID** är den unika identifieraren för katalogen som används för att skicka kommandon på den här hanteringsgruppen. Den här identifieraren kan inte redigeras när du har skapat eftersom den används i hela Azure-systemet för att identifiera den här gruppen.
    - Visningsnamnet är det namn som visas i Azure-portalen. Ett separat visningsnamn är ett valfritt fält när du skapar hanteringen gruppen och kan ändras när som helst.  

    ![Skapa](media/management-groups/create_context_menu.png)  
5.  Välj **spara**

### <a name="create-in-powershell"></a>Skapa i PowerShell

I PowerShell, kan du använda Lägg till AzureRmManagementGroups-cmdletar:

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso
```

Den **GroupName** är en unik identifierare som håller på att skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill använda hanteringsgruppen som visar ett annat namn i Azure-portalen kan du lägga till den **DisplayName** med strängen för parametern. Om du vill skapa en hanteringsgrupp med GroupName Contoso och visningsnamnet för ”Contoso-grupp”, skulle du till exempel använda följande cmdlet:

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
```

Använd den **ParentId** parametern ska ha den här hanteringsgruppen skapas under en annan hantering.  

### <a name="create-in-azure-cli"></a>Skapa i Azure CLI

På Azure CLI kan du använda az-management-kontogruppen skapar kommandot.

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
```

---

## <a name="next-steps"></a>Nästa steg 
Mer information om hanteringsgrupper finns: 
- [Organisera dina resurser med Azure-hanteringsgrupper ](management-groups-overview.md)
- [Ändra, ta bort eller hantera dina hanteringsgrupper](management-groups-manage.md)
- [Installera Azure Powershell-modulen](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Granska REST API-specifikationen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Installera Azure CLI-tillägg](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

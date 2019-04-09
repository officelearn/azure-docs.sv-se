---
title: Skapa hanteringsgrupper för att organisera Azure-resurser – Azure-styrning
description: Lär dig hur du skapar Azure hanteringsgrupper för att hantera flera resurser med portalen, Azure PowerShell och Azure CLI.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 928cb790bd97270870618534a73316bba5eeb070
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057446"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Skapa hanteringsgrupper för resursorganisationen och hantering

Av hanteringsgrupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa de här behållarna för att skapa en och effektiva hierarki som kan användas med [Azure Policy](../policy/overview.md) och [baserade åtkomstkontroller i Azure-rollen](../../role-based-access-control/overview.md). Mer information om hanteringsgrupper finns i [organisera dina resurser med Azure-hanteringsgrupper](overview.md).

Den första hanteringsgruppen som skapats i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången du konfigurerar tjänsten management-grupper i Azure för din katalog. Du får ett meddelande när processen är klar.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="create-a-management-group"></a>Skapa en hanteringsgrupp

Du kan skapa hanteringsgruppen med hjälp av portalen, PowerShell eller Azure CLI. För närvarande kan använda du inte Resource Manager-mallar för att skapa hanteringsgrupper.

### <a name="create-in-portal"></a>Skapa i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hanteringsgrupper**.

1. Välj på huvudsidan **ny hanteringsgrupp**.

   ![Sidan för att arbeta med hanteringsgrupper](./media/main.png)

1. Fyll i hantering av grupp-ID-fältet.

   - Den **Hanteringsgruppens ID** är den unika identifieraren för katalogen som används för att skicka kommandon på den här hanteringsgruppen. Den här identifieraren är inte redigerbara när du har skapat eftersom den används i hela Azure-systemet för att identifiera den här gruppen. Den [rot-hanteringsgruppen](index.md#root-management-group-for-each-directory) skapas automatiskt med ett ID som är Azure Active Directory-ID. För alla andra hanteringsgrupper, tilldela ett unikt ID.
   - Visningsnamnet är det namn som visas i Azure-portalen. Ett separat visningsnamn är ett valfritt fält när du skapar hanteringen gruppen och kan ändras när som helst.  

   ![Alternativfönster för att skapa en ny hanteringsgrupp](./media/create_context_menu.png)  

1. Välj **Spara**.

### <a name="create-in-powershell"></a>Skapa i PowerShell

I PowerShell kan använda du cmdleten New-AzManagementGroup:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

Den **GroupName** är en unik identifierare som håller på att skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill använda hanteringsgruppen som visar ett annat namn i Azure-portalen kan du lägga till den **DisplayName** med strängen för parametern. Om du vill skapa en hanteringsgrupp med GroupName Contoso och visningsnamnet för ”Contoso-grupp”, skulle du till exempel använda följande cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoTenant'
```

Använd den **ParentId** parametern ska ha den här hanteringsgruppen skapas under en annan hantering.

### <a name="create-in-azure-cli"></a>Skapa i Azure CLI

På Azure CLI kan du använda az-management-kontogruppen skapar kommandot.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](create.md)
- [Ändra, ta bort eller hantera dina hanteringsgrupper](manage.md)
- [Granska av hanteringsgrupper i Azure PowerShell-modulen för resurser](/powershell/module/az.resources#resources)
- [Granska hanteringsgrupper i REST-API](/rest/api/resources/managementgroups)
- [Granska av hanteringsgrupper i Azure CLI](/cli/azure/account/management-group)
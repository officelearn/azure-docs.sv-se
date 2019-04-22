---
title: Skapa hanteringsgrupper för att organisera Azure-resurser – Azure-styrning
description: Lär dig hur du skapar Azure hanteringsgrupper för att hantera flera resurser med portalen, Azure PowerShell och Azure CLI.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 2dd2a6e071533deef47a6482bfb9ed92953864ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59259825"
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

Du använder PowerShell använder den [New AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) cmdlet för att skapa en ny hanteringsgrupp.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

Den **GroupName** är en unik identifierare som håller på att skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill hanteringsgruppen som visar ett annat namn i Azure-portalen, lägger du till den **DisplayName** parametern. Till exempel för att skapa en hanteringsgrupp med GroupName Contoso och visningsnamnet för ”Contoso-grupp”, använder du följande cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

I föregående exempel skapas den nya hanteringsgruppen under rot-hanteringsgruppen. Om du vill ange en annan hanteringsgrupp som det överordnade objektet, använder den **ParentId** parametern.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Skapa i Azure CLI

Azure CLI, använder den [az-management-kontogruppen skapa](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) kommando för att skapa en ny hanteringsgrupp.

```azurecli-interactive
az account management-group create --name Contoso
```

Den **namn** är en unik identifierare som håller på att skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill hanteringsgruppen som visar ett annat namn i Azure-portalen, lägger du till den **visningsnamn** parametern. Till exempel för att skapa en hanteringsgrupp med GroupName Contoso och visningsnamnet för ”Contoso-grupp”, använder du följande kommando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

I föregående exempel skapas den nya hanteringsgruppen under rot-hanteringsgruppen. Om du vill ange en annan hanteringsgrupp som det överordnade objektet, använder den **överordnade** parametern och ange namnet på den överordnade gruppen.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hanteringslösningar här:

- [Skapa hanteringsgrupper för att organisera Azure-resurser](create.md)
- [Så här ändrar, raderar och hanterar du dina hanteringsgrupper](manage.md)
- [Granska hanteringsgrupper i Azure PowerShell-resursmodulen](/powershell/module/az.resources#resources)
- [Granska hanteringsgrupper i REST API](/rest/api/resources/managementgroups)
- [Granska hanteringsgrupper i Azure CLI](/cli/azure/account/management-group)
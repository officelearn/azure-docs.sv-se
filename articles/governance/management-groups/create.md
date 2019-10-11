---
title: Skapa hanterings grupper för att organisera resurser – Azure-styrning
description: Lär dig hur du skapar Azure-hanterings grupper för att hantera flera resurser med hjälp av portalen, Azure PowerShell och Azure CLI.
author: rthorn17
ms.service: governance
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 17154ea72a49ff48a482b1149eeecf0229fcccd7
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254687"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Skapa hanterings grupper för resurs organisation och hantering

Hanterings grupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa de här behållarna för att skapa en effektiv och effektiv hierarki som kan användas med [Azure policy](../policy/overview.md) och [Azure Role-baserade åtkomst kontroller](../../role-based-access-control/overview.md). Mer information om hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](overview.md).

Den första hanterings gruppen som skapas i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera hanterings grupps tjänsten i Azure för din katalog. Du får ett meddelande när processen är klar.

## <a name="create-a-management-group"></a>Skapa en hanteringsgrupp

Du kan skapa hanterings gruppen med hjälp av portalen, PowerShell eller Azure CLI. För närvarande kan du inte använda Resource Manager-mallar för att skapa hanterings grupper.

### <a name="create-in-portal"></a>Skapa i portalen

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj **alla tjänster** > **hantering + styrning**.

1. Välj **Cost Management + fakturering**

1. På sidan Cost Management + fakturerings hanterings grupper väljer du **hanteringsgrupper**

1. Välj **+ Lägg till hanterings grupp**.

   ![Sida för att arbeta med hanterings grupper](./media/main.png)

1. Fyll i fältet hanterings grupp-ID.

   - **Hanterings gruppens ID** är katalogens unika identifierare som används för att skicka kommandon i den här hanterings gruppen. Den här identifieraren kan inte redige ras när den används i hela Azure-systemet för att identifiera den här gruppen. [Rot hanterings gruppen](overview.md#root-management-group-for-each-directory) skapas automatiskt med ett ID som är Azure Active Directory-ID: t. Tilldela ett unikt ID för alla andra hanterings grupper.
   - Fältet visnings namn är det namn som visas i Azure Portal. Ett separat visnings namn är ett valfritt fält när du skapar hanterings gruppen och kan ändras när som helst.  

   ![Alternativ fönster för att skapa en ny hanterings grupp](./media/create_context_menu.png)  

1. Välj **Spara**.

### <a name="create-in-powershell"></a>Skapa i PowerShell

För PowerShell använder du cmdleten [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) för att skapa en ny hanterings grupp.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** är en unik identifierare som skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill att hanterings gruppen ska visa ett annat namn i Azure Portal lägger du till parametern **DisplayName** . Om du till exempel vill skapa en hanterings grupp med GroupName för Contoso och visnings namnet "contoso Group", använder du följande cmdlet:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

I föregående exempel skapas den nya hanterings gruppen under rot hanterings gruppen. Använd **parametern för att ange** en annan hanterings grupp som överordnad.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Skapa i Azure CLI

För Azure CLI använder du kommandot [AZ Account Management-Group Create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) för att skapa en ny hanterings grupp.

```azurecli-interactive
az account management-group create --name Contoso
```

**Namnet** är en unik identifierare som skapas. Detta ID används av andra kommandon för att referera till den här gruppen och kan inte ändras senare.

Om du vill att hanterings gruppen ska visa ett annat namn i Azure Portal lägger du till **visnings namn** parametern. Om du till exempel vill skapa en hanterings grupp med GroupName för Contoso och visnings namnet "contoso Group", använder du följande kommando:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

I föregående exempel skapas den nya hanterings gruppen under rot hanterings gruppen. Om du vill ange en annan hanterings grupp som överordnad använder du den **överordnade** parametern och anger namnet på den överordnade gruppen.

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

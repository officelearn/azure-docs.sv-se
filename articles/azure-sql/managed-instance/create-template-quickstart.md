---
title: 'Azure Resource Manager: skapa en Azure SQL-hanterad instans'
description: Lär dig hur du skapar en Azure SQL-hanterad instans med hjälp av en Azure Resource Manager-mall.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: subject-armqs
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/22/2020
ms.openlocfilehash: 01c6c37d31d41f88b370face372555536724adde
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85256186"
---
# <a name="quickstart-create-an-azure-sql-managed-instance-using-an-azure-resource-manager-template"></a>Snabb start: skapa en Azure SQL-hanterad instans med hjälp av en Azure Resource Manager mall

Den här snabb starten fokuserar på processen att distribuera en Resource Manager-mall för att skapa en hanterad Azure SQL-instans och vNet.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Krav

Inga.

## <a name="create-an-azure-sql-managed-instance"></a>Skapa en Azure SQL-hanterad instans

[Azure SQL Managed instance](sql-managed-instance-paas-overview.md) är en intelligent, fullständigt hanterad, skalbar moln databas med nästan 100% funktions paritet med SQL Server databas motorn.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten är från [Azure snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).

:::code language="json" source="~/quickstart-templates/101-sqlmi-new-vnet/azuredeploy.json":::

Dessa resurser definieras i mallen:

- [**Microsoft. SQL/managedinstances**](/azure/templates/microsoft.sql/managedinstances)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.Network/virtualNetworks)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.Network/routeTables)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.Network/networkSecurityGroups)



Fler mall exempel finns i [snabb starts mallar för Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

Välj **testa** från följande PowerShell-kodblock för att öppna Azure Cloud Shell.

> [!IMPORTANT]
> Att distribuera en hanterad instans är en tids krävande åtgärd. Distribution av den första instansen i under nätet tar vanligt vis mycket längre tid än att distribuera till ett undernät med befintliga hanterade instanser. För genomsnittlig etablerings tid, se [hanterings åtgärder för SQL-hanterad instans](sql-managed-instance-paas-overview.md#management-operations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json"

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sqlmi-new-vnet/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

* * *

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Besök [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) och kontrol lera att den hanterade instansen finns i den valda resurs gruppen. Eftersom det kan ta en stund att skapa en hanterad instans kan du behöva kontrol lera länken **distributioner** på resurs gruppens **översikts** sida.

- En snabb start som visar hur du ansluter till SQL-hanterad instans från en virtuell Azure-dator finns i [Konfigurera en anslutning till en virtuell Azure-dator](connect-vm-instance-configure.md).
- En snabb start som visar hur du ansluter till SQL-hanterad instans från en lokal klient dator med hjälp av en punkt-till-plats-anslutning finns i [Konfigurera en punkt-till-plats](point-to-site-p2s-configure.md)-anslutning.

## <a name="clean-up-resources"></a>Rensa resurser

Behåll den hanterade instansen om du vill gå vidare till [Nästa steg](#next-steps), men ta bort den hanterade instansen och relaterade resurser när du har slutfört eventuella ytterligare självstudier. När du har tagit bort en hanterad instans läser du [ta bort ett undernät när du har tagit bort en hanterad instans](virtual-cluster-delete.md)


Så här tar du bort resursgruppen:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

* * *

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera en virtuell Azure-dator för att ansluta till en Azure SQL-hanterad instans](connect-vm-instance-configure.md)

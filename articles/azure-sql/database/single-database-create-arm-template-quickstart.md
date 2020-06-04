---
title: 'Azure Resource Manager: skapa en enskild databas'
description: Skapa en enda databas i Azure SQL Database med hjälp av Azure Resource Manager-mallen.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/28/2019
ms.openlocfilehash: 02e0947de3e7e5c6ce5110740127571ea393b168
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343871"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Snabb start: skapa en enda databas i Azure SQL Database med hjälp av Azure Resource Manager-mallen

Att skapa en [enskild databas](single-database-overview.md) är det snabbaste och enklaste alternativet för att skapa en databas i Azure SQL Database. Den här snabb starten visar hur du skapar en enskild databas med hjälp av Azure Resource Manager-mallen.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

Inga.

## <a name="create-a-single-database"></a>Skapa en enkel databas

En enkel databas har en definierad uppsättning resurser för beräkning, minne, IO och lagring som använder en av de två [köpmodellerna](purchasing-models.md). När du skapar en enskild databas definierar du också en [Server](logical-servers.md) för att hantera den och placerar den i [Azure-resurs gruppen](../../active-directory-b2c/overview.md) i en angiven region.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten är från [Azure snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-sql-logical-server/).

:::code language="json" source="~/quickstart-templates/101-sql-logical-server/azuredeploy.json" range="1-163" highlight="63-132":::

Dessa resurser definieras i mallen:

- [**Microsoft. SQL/Servers**](/azure/templates/microsoft.sql/servers)
- [**Microsoft. SQL/Servers/firewallRules**](/azure/templates/microsoft.sql/servers/firewallrules)
- [**Microsoft. SQL/Servers/securityAlertPolicies**](/azure/templates/microsoft.sql/servers/securityalertpolicies)
- [**Microsoft. SQL/Servers/vulnerabilityAssessments**](/azure/templates/microsoft.sql/servers/vulnerabilityassessments)
- [**Microsoft. SQL/Servers/connectionPolicies**](/azure/templates/microsoft.sql/servers/connectionpolicies)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft. Storage/storageAccounts/providers/roleAssignments**](/azure/templates/microsoft.authorization/roleassignments)

Fler Azure SQL Database mal sampel finns i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Distribuera mallen

Välj **testa** från följande PowerShell-kodblock för att öppna Azure Cloud Shell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-logical-server/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

az group create --location $location --name $resourceGroupName

az group deployment create -g $resourceGroupName --template-uri "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" `
    --parameters 'projectName=' + $projectName \
                 'administratorLogin=' + $adminUser \
                 'administratorLoginPassword=' + $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

* * *

## <a name="validate-the-deployment"></a>Verifiera distributionen

Information om hur du frågar databasen finns i [fråga databasen](single-database-create-quickstart.md#query-the-database).

## <a name="clean-up-resources"></a>Rensa resurser

Behåll den här resurs gruppen, servern och den enkla databasen om du vill gå vidare till [Nästa steg](#next-steps). Nästa steg visar hur du ansluter och kör frågor mot din databas med olika metoder.

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

- Skapa en brandväggsregel på servernivå för att ansluta till den enkla databasen från lokala eller fjärranslutna verktyg. Mer information finns i [Skapa en brandväggsregel på servernivå](firewall-create-server-level-portal-quickstart.md).
- När du har skapat en brandväggsregel på servernivå [ansluter du till och kör frågor mot](connect-query-content-reference-guide.md) databasen med hjälp av flera olika verktyg och språk.
  - [Ansluta och köra frågor med SQL Server Management Studio](connect-query-ssms.md)
  - [Ansluta och köra frågor med Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Information om hur du skapar en enskild databas med Azure CLI finns i [Azure CLI-exempel](az-cli-script-samples-content-guide.md).
- Om du vill skapa en enskild databas med hjälp av Azure PowerShell, se [Azure PowerShell exempel](powershell-script-content-guide.md).
- Information om hur du skapar Resource Manager-mallar finns i [skapa din första mall](../../azure-resource-manager/templates/template-tutorial-create-first-template.md).
 
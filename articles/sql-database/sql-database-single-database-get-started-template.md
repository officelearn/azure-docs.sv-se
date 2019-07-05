---
title: 'Azure Resource Manager: Skapa en enkel databas – Azure SQL Database | Microsoft Docs'
description: Skapa en enskild databas i Azure SQL Database med hjälp av Azure Resource Manager-mall.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
manager: craigg
ms.date: 06/28/2019
ms.openlocfilehash: 4ef0f9ff6f8620109f2ef6f6bd5f549281b4de54
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472149"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Snabbstart: Skapa en enskild databas i Azure SQL Database med hjälp av Azure Resource Manager-mall

Att skapa en [enkel databas](sql-database-single-database.md) är det snabbaste och enklaste distributionsalternativet för att skapa en databas i Azure SQL Database. Den här snabbstarten visar hur du skapar en enskild databas med Azure Resource Manager-mallen. Mer information finns i [dokumentation om Azure Resource Manager](/azure/azure-resource-manager/).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Skapa en enkel databas

En enkel databas har en definierad uppsättning resurser för beräkning, minne, IO och lagring som använder en av de två [köpmodellerna](sql-database-purchase-models.md). När du skapar en enkel databas definierar du även en [SQL Database-server](sql-database-servers.md) för att hantera den och placera den i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i en specifik region.

Följande JSON-filen är den mall som används i den här artikeln. Mallen finns i [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/SQLServerAndDatabase/azuredeploy.json). Mer Azure SQL database-mallexempel finns i [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

[!code-json[create-azure-sql-database-server-and-database](~/resourcemanager-templates/SQLServerAndDatabase/azuredeploy.json)]

1. Välj **prova** från följande PowerShell-kodblock att öppna Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
    $adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
    $adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

    $resourceGroupName = "${projectName}rg"


    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "D:\GitHub\azure-docs-json-samples\SQLServerAndDatabase\azuredeploy.json" -projectName $projectName -adminUser $adminUser -adminPassword $adminPassword

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

1. Välj **kopia** kopiera PowerShell-skriptet till Urklipp.
1. Högerklicka på shell-fönstret och välj sedan **klistra in**.

    Det tar en stund att skapa databasservern och databasen.

## <a name="query-the-database"></a>Fråga databasen

Om du vill söka i databasen, se [fråga databasen](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Rensa resurser

Behåll den här resursgruppen, databasservern och den enkla databasen om du vill gå till [Nästa steg](#next-steps). Nästa steg visar hur du ansluter och kör frågor mot din databas med olika metoder.

Ta bort resursgruppen med Azure Powershell:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

- Skapa en brandväggsregel på servernivå för att ansluta till den enkla databasen från lokala eller fjärranslutna verktyg. Mer information finns i [Skapa en brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md).
- När du har skapat en brandväggsregel på servernivå [ansluter du till och kör frågor mot](sql-database-connect-query.md) databasen med hjälp av flera olika verktyg och språk.
  - [Ansluta och köra frågor med SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Ansluta och köra frågor med Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- För att skapa en enskild databas med Azure CLI, se [Azure CLI-exempel](sql-database-cli-samples.md).
- För att skapa en enskild databas med Azure PowerShell, se [Azure PowerShell-exempel](sql-database-powershell-samples.md).

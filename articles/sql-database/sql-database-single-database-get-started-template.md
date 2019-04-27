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
ms.date: 04/09/2019
ms.openlocfilehash: 8d060ce60194e47814308bfd67bd14db996650b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709050"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-resource-manager-template"></a>Snabbstart: Skapa en enskild databas i Azure SQL Database med hjälp av Azure Resource Manager-mall

Att skapa en [enkel databas](sql-database-single-database.md) är det snabbaste och enklaste distributionsalternativet för att skapa en databas i Azure SQL Database. Den här snabbstarten visar hur du skapar en enskild databas med Azure Resource Manager-mallen. Mer information finns i [dokumentation om Azure Resource Manager](/azure/azure-resource-manager/).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Skapa en enkel databas

En enkel databas har en definierad uppsättning resurser för beräkning, minne, IO och lagring som använder en av de två [köpmodellerna](sql-database-purchase-models.md). När du skapar en enkel databas definierar du även en [SQL Database-server](sql-database-servers.md) för att hantera den och placera den i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i en specifik region.

Följande JSON-filen är den mall som används i den här artikeln. Mallen lagras i ett Azure Storage-konto. Mer Azure SQL database-mallexempel finns [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serverName": {
      "type": "string",
      "defaultValue": "[concat('server-', uniqueString(resourceGroup().id, deployment().name))]",
      "metadata": {
        "description": "Name for the SQL server"
      }
    },
    "shouldDeployDb": {
      "type": "string",
      "allowedValues": [
        "Yes",
        "No"
      ],
      "defaultValue": "Yes",
      "metadata": {
        "description": "Whether an Azure SQL Database should be deployed under the server"
      }
    },
    "databaseName": {
      "type": "string",
      "defaultValue": "[concat('db-', uniqueString(resourceGroup().id, deployment().name), '-1')]",
      "metadata": {
        "description": "Name for the SQL database under the SQL server"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for server and optional DB"
      }
    },
    "emailAddresses": {
      "type": "array",
      "defaultValue": [
        "user1@example.com",
        "user2@example.com"
      ],
      "metadata": {
        "description": "Email addresses for receiving alerts"
      }
    },
    "adminUser": {
      "type": "string",
      "metadata": {
        "description": "Username for admin"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for admin"
      }
    }
  },
  "variables": {
    "databaseServerName": "[toLower(parameters('serverName'))]",
    "databaseName": "[parameters('databaseName')]",
    "shouldDeployDb": "[parameters('shouldDeployDb')]",
    "databaseServerLocation": "[parameters('location')]",
    "databaseServerAdminLogin": "[parameters('adminUser')]",
    "databaseServerAdminLoginPassword": "[parameters('adminPassword')]",
    "emailAddresses": "[parameters('emailAddresses')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "name": "[variables('databaseServerName')]",
      "location": "[variables('databaseServerLocation')]",
      "apiVersion": "2015-05-01-preview",
      "properties": {
        "administratorLogin": "[variables('databaseServerAdminLogin')]",
        "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
        "version": "12.0"
      },
      "tags": {
        "DisplayName": "[variables('databaseServerName')]"
      },
      "resources": [
        {
          "type": "securityAlertPolicies",
          "name": "DefaultSecurityAlert",
          "apiVersion": "2017-03-01-preview",
          "dependsOn": [
            "[variables('databaseServerName')]"
          ],
          "properties": {
            "state": "Enabled",
            "emailAddresses": "[variables('emailAddresses')]",
            "emailAccountAdmins": true
          }
        }
      ]
    },
    {
      "condition": "[equals(variables('shouldDeployDb'), 'Yes')]",
      "type": "Microsoft.Sql/servers/databases",
      "name": "[concat(string(variables('databaseServerName')), '/', string(variables('databaseName')))]",
      "location": "[variables('databaseServerLocation')]",
      "apiVersion": "2017-10-01-preview",
      "dependsOn": [
        "[concat('Microsoft.Sql/servers/', variables('databaseServerName'))]"
      ],
      "properties": {},
      "tags": {
        "DisplayName": "[variables('databaseServerName')]"
      }
    }
  ]
}
```

1. Välj följande bild för att logga in på Azure och öppna en mall.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatesql%2Fazuredeploy.json"><img src="./media/sql-database-single-database-get-started-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Välj eller ange följande värden.  

    ![Skapa azure sql-databas för Resource Manager-mall](./media/sql-database-single-database-get-started-template/create-azure-sql-database-resource-manager-template.png)

    Om den har angetts Använd standardvärden.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resursgrupp**: Välj **Skapa nytt**, ange ett unikt namn för resursgruppen och klicka sedan på **OK**. 
    * **Plats**: välj en plats.  Välj till exempel **USA, centrala**.
    * **Administratörsanvändare**: Ange ett administratörsanvändarnamn för SQL database-server.
    * **Adminlösenord**: Ange ett administratörslösenord. 
    * **Jag godkänner villkoren ovan**: Välj.
3. Välj **Köp**.

## <a name="query-the-database"></a>Fråga databasen

Om du vill söka i databasen, se [fråga databasen](./sql-database-single-database-get-started.md#query-the-database).

## <a name="clean-up-resources"></a>Rensa resurser

Behåll den här resursgruppen, databasservern och den enkla databasen om du vill gå till [Nästa steg](#next-steps). Nästa steg visar hur du ansluter och kör frågor mot din databas med olika metoder.

Ta bort resursgruppen med hjälp av Azure CLI eller Azure Powershell:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName 
```

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName 
```

## <a name="next-steps"></a>Nästa steg

- Skapa en brandväggsregel på servernivå för att ansluta till den enkla databasen från lokala eller fjärranslutna verktyg. Mer information finns i [Skapa en brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md).
- När du har skapat en brandväggsregel på servernivå [ansluter du till och kör frågor mot](sql-database-connect-query.md) databasen med hjälp av flera olika verktyg och språk.
  - [Ansluta och köra frågor med SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Ansluta och köra frågor med Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Information om hur du skapar enkla databaser med hjälp av Azure CLI finns i [Azure CLI-exempel](sql-database-cli-samples.md).
- Information om hur du skapar enkla databaser med hjälp av Azure PowerShell finns i [Azure PowerShell-exempel](sql-database-powershell-samples.md).

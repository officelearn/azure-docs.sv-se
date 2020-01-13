---
title: Skapa ett Azure Datautforskaren-kluster och-databas med hjälp av en Azure Resource Manager mall
description: Lär dig hur du skapar ett Azure Datautforskaren-kluster och-databas med hjälp av en Azure Resource Manager mall
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 56639d8a29ad8eac465845c8d354d04b31ba6093
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911968"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-an-azure-resource-manager-template"></a>Skapa ett Azure Datautforskaren-kluster och-databas med hjälp av en Azure Resource Manager mall

> [!div class="op_single_selector"]
> * [Portalen](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager-mall](create-cluster-database-resource-manager.md)

Azure Data Explorer är en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata. För att använda Azure Data Explorer skapar du först ett kluster och skapar en eller flera databaser i klustret. Sedan matar du in (läser in) data i databasen så att du kan köra frågor mot den. 

I den här artikeln skapar du ett Azure Datautforskaren-kluster och-databas med hjälp av en [Azure Resource Manager mall](../azure-resource-manager/management/overview.md). Artikeln visar hur du definierar vilka resurser distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav. Information om hur du skapar mallar finns i [redigera Azure Resource Manager mallar](/azure/azure-resource-manager/resource-group-authoring-templates). För JSON-syntax och egenskaper som ska användas i en mall, se [resurs typer för Microsoft. Kusto](/azure/templates/microsoft.kusto/allversions).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="azure-resource-manager-template-for-cluster-and-database-creation"></a>Azure Resource Manager mall för kluster-och databas skapande

I den här artikeln använder du en [befintlig snabb starts mall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "clusters_kustocluster_name": {
          "type": "string",
          "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
          "metadata": {
            "description": "Name of the cluster to create"
          }
      },
      "databases_kustodb_name": {
          "type": "string",
          "defaultValue": "kustodb",
          "metadata": {
            "description": "Name of the database to create"
          }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources."
        }
      }
  },
  "variables": {},
  "resources": [
      {
          "name": "[parameters('clusters_kustocluster_name')]",
          "type": "Microsoft.Kusto/clusters",
          "sku": {
              "name": "Standard_D13_v2",
              "tier": "Standard",
              "capacity": 2
          },
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "tags": {
            "Created By": "GitHub quickstart template"
          }
      },
      {
          "name": "[concat(parameters('clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'))]",
          "type": "Microsoft.Kusto/clusters/databases",
          "apiVersion": "2019-09-07",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[resourceId('Microsoft.Kusto/clusters', parameters('clusters_kustocluster_name'))]"
          ],
          "properties": {
              "softDeletePeriodInDays": 365,
              "hotCachePeriodInDays": 31
          }
      }
  ]
}
```

Mer information om mallar finns i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/).

## <a name="deploy-the-template-and-verify-template-deployment"></a>Distribuera mallen och verifiera distribution av mallar

Du kan distribuera Azure Resource Manager-mallen med [hjälp av Azure Portal](#use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment) eller [med hjälp av PowerShell](#use-powershell-to-deploy-the-template-and-verify-template-deployment).

### <a name="use-the-azure-portal-to-deploy-the-template-and-verify-template-deployment"></a>Använd Azure Portal för att distribuera mallen och verifiera distribution av mallar

1. För att skapa ett kluster och en databas, använder du följande knapp för att starta distributionen. Högerklicka och välj **Öppna i nytt fönster** så att du kan följa resten av stegen i den här artikeln.

    [![Distribuera till Azure](media/create-cluster-database-resource-manager/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-kusto-cluster-database%2Fazuredeploy.json)

    Knappen **Distribuera till Azure** tar dig till Azure-portalen där du kan fylla i ett distributionsformulär.

    ![Distribuera till Azure](media/create-cluster-database-resource-manager/deploy-2-azure.png)

    Du kan [Redigera och distribuera mallen i Azure Portal](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) med hjälp av formuläret.

1. Fullständiga **grundläggande** och **inställnings** avsnitt. Välj unikt kluster och databas namn.
Det tar några minuter att skapa ett Azure Datautforskaren-kluster och-databas.

1. För att verifiera distributionen öppnar du resurs gruppen i [Azure Portal](https://portal.azure.com) för att hitta det nya klustret och databasen. 

### <a name="use-powershell-to-deploy-the-template-and-verify-template-deployment"></a>Använd PowerShell för att distribuera mallen och verifiera distribution av mallar

#### <a name="deploy-the-template-using-powershell"></a>Distribuera mallen med PowerShell

1. Välj **prova** med följande kodblock och följ sedan anvisningarna för att logga in på Azure Cloud Shell.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"
    $clusterName = "${projectName}cluster"
    $parameters = @{}
    $parameters.Add("clusters_kustocluster_name", $clusterName)
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-kusto-cluster-database/azuredeploy.json"
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -TemplateParameterObject $parameters
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Välj **Kopiera** för att kopiera PowerShell-skriptet.
1. Högerklicka på Shell-konsolen och välj sedan **Klistra in**.
Det tar några minuter att skapa ett Azure Datautforskaren-kluster och-databas.

#### <a name="verify-the-deployment-using-powershell"></a>Verifiera distributionen med PowerShell

Verifiera distributionen genom att använda följande Azure PowerShell skript.  Om Cloud Shell fortfarande är öppen, behöver du inte kopiera/köra den första raden (Read-Host). Mer information om hur du hanterar Azure Datautforskaren-resurser i PowerShell finns i [AZ. Kusto](/powershell/module/az.kusto/?view=azps-2.7.0). 

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"

Install-Module -Name Az.Kusto
$resourceGroupName = "${projectName}rg"
$clusterName = "${projectName}cluster"

Get-AzKustoCluster -ResourceGroupName $resourceGroupName -Name $clusterName
Write-Host "Press [ENTER] to continue ..."
```

[!INCLUDE [data-explorer-clean-resources](../../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Nästa steg

[Mata in data i Azure Datautforskaren-kluster och-databas](ingest-data-overview.md)

---
title: Snabbstart – Skapa en Azure Cosmos DB och en behållare med hjälp av Azure Resource Manager-mall
description: Snabbstart som visar hur man använder en Azure Cosmos-databas och en behållare med hjälp av Azure Resource Manager-mall
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 02/27/2020
ms.custom: subject-armqs
ms.openlocfilehash: 7c02cdf772b06f7070071aa7ba35c59b019187cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78301734"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>Snabbstart: Skapa en Azure Cosmos DB och en behållare med hjälp av Azure Resource Manager-mall

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga efter nyckel-/värdedatabaser, dokumentdatabaser och diagramdatabaser. Den här snabbstarten fokuserar på hur du distribuerar en Resource Manager-mall för att skapa en Azure Cosmos-databas och en behållare i databasen. Du kan senare lagra data i den här behållaren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

En Azure-prenumeration eller ett kostnadsfritt utvärderingskonto för Azure Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-an-azure-cosmos-account-database-container"></a>Skapa ett Azure Cosmos-konto, databas, behållare

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/101-cosmosdb-create/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-create/azuredeploy.json":::

Tre Azure-resurser definieras i mallen:

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): Skapa ett Azure Cosmos-konto.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Skapa en Azure Cosmos-databas.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): Skapa en Azure Cosmos-behållare.

Fler Azure Cosmos DB-mallexempel finns i [snabbstartsmallgalleriet](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett Azure Cosmos-konto, en databas och en behållare.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Välj eller ange följande värden.

   ![Resource Manager-mall, Azure Cosmos DB-integrering, distributionsportal](./media/quick-create-template/create-cosmosdb-using-template-portal.png)

    Om det inte anges använder du standardvärdena för att skapa Azure Cosmos-resurserna.

    * **Prenumeration:** välj en Azure-prenumeration.
    * **Resursgrupp:** Välj **Skapa nytt,** ange ett unikt namn för resursgruppen och klicka sedan på **OK**.
    * **Plats**: välj en plats.  Välj till exempel **USA, centrala**.
    * **Kontonamn:** Ange ett namn för Azure Cosmos-kontot. Det måste vara globalt unikt.
    * **Plats**: ange en plats där du vill skapa ditt Azure Cosmos-konto. Azure Cosmos-kontot kan finnas på samma plats som resursgruppen.
    * **Primär region**: Den primära replikregionen för Azure Cosmos-kontot.
    * **Sekundär region**: Den sekundära replikregionen för Azure Cosmos-kontot.
    * **Databasnamn**: Namnet på Azure Cosmos-databasen.
    * **Behållarnamn**: Namnet på Azure Cosmos-behållaren.
    * **Dataflöde:** Genomströmningen för behållaren, minsta dataflödesvärde är 400 RU/s.
    * **Jag godkänner villkoren ovan**: Välj.

3. Välj **Köp**. När Azure Cosmos-kontot har distribuerats får du ett meddelande:

   ![Resource Manager-mall, Cosmos DB-integrering, distribuera portalmeddelanden](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure-portalen används för att distribuera mallen. Förutom Azure-portalen kan du även använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Du kan antingen använda Azure-portalen för att kontrollera Azure Cosmos-kontot, databasen och behållaren eller använda följande Azure CLI- eller Azure PowerShell-skript för att lista den hemliga som skapats.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[Powershell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med efterföljande och handledning, kanske du vill lämna dessa resurser på plats.
När det inte längre behövs tar du bort resursgruppen som tar bort Azure Cosmos-kontot och de relaterade resurserna. Så här tar du bort resursgruppen med hjälp av Azure CLI eller Azure Powershell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[Powershell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du ett Azure Cosmos-konto, en databas och en behållare med hjälp av en Azure Resource Manager-mall och validerade distributionen. Om du vill veta mer om Azure Cosmos DB och Azure Resource Manager fortsätter du till artiklarna nedan.

- Läs en [översikt över Azure Cosmos DB](introduction.md)
- Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Hämta andra [Azure Cosmos DB Resource Manager-mallar](resource-manager-samples.md)
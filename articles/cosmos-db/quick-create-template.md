---
title: Snabb start – skapa en Azure Cosmos DB och en behållare med Azure Resource Manager mall
description: Snabb start visar hur du använder en Azure Cosmos-databas och en behållare med hjälp av Azure Resource Manager mall
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.topic: quickstart
ms.date: 02/27/2020
ms.custom: subject-armqs
ms.openlocfilehash: f524a1e1db426b9b9dafb2fb95d77538a34b04ec
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81605470"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-azure-resource-manager-template"></a>Snabb start: skapa en Azure Cosmos DB och en behållare med Azure Resource Manager mall

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan använda Azure Cosmos DB för att snabbt skapa och fråga nyckel/värde-databaser, dokument databaser och Graf-databaser. Den här snabb starten fokuserar på processen att distribuera en Resource Manager-mall för att skapa en Azure Cosmos-databas och en behållare i databasen. Du kan senare lagra data i den här behållaren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

En Azure-prenumeration eller ett kostnads fritt Azure Cosmos DB utvärderings konto

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-an-azure-cosmos-account-database-container"></a>Skapa ett Azure Cosmos-konto, en databas, en behållare

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-cosmosdb-create/).

:::code language="json" source="~/quickstart-templates/101-cosmosdb-create/azuredeploy.json":::

Tre Azure-resurser definieras i mallen:

* [Microsoft. DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): skapa ett Azure Cosmos-konto.

* [Microsoft. DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): skapa en Azure Cosmos-databas.

* [Microsoft. DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): skapa en Azure Cosmos-behållare.

Du hittar fler Azure Cosmos DB mal sampel i [galleriet snabb starts mal len](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb).

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett Azure Cosmos-konto, en databas och en behållare.

   [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-create%2Fazuredeploy.json)

2. Välj eller ange följande värden.

   ![Resource Manager-mall, Azure Cosmos DB integrering, distribuera Portal](./media/quick-create-template/create-cosmosdb-using-template-portal.png)

    Om den inte anges använder du standardvärdena för att skapa Azure Cosmos-resurser.

    * **Prenumeration**: Välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **Skapa ny**, ange ett unikt namn för resurs gruppen och klicka sedan på **OK**.
    * **Plats**: välj en plats.  Välj till exempel **USA, centrala**.
    * **Konto namn**: Ange ett namn för Azure Cosmos-kontot. Det måste vara globalt unikt.
    * **Plats**: Ange en plats där du vill skapa ditt Azure Cosmos-konto. Azure Cosmos-kontot kan finnas på samma plats som resurs gruppen.
    * **Primär region**: det primära replik området för Azure Cosmos-kontot.
    * **Sekundär region**: det sekundära replik området för Azure Cosmos-kontot.
    * **Databas namn**: namnet på Azure Cosmos-databasen.
    * **Behållar namn**: namnet på Azure Cosmos-behållaren.
    * **Data flöde**: data flödet för behållaren, minsta data flöde svärdet är 400 ru/s.
    * **Jag godkänner villkoren ovan**: Välj.

3. Välj **Köp**. När du har distribuerat Azure Cosmos-kontot får du ett meddelande:

   ![Resource Manager-mall, Cosmos DB integrering, distribuera Portal meddelande](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal används för att distribuera mallen. Förutom Azure Portal kan du också använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Verifiera distributionen

Du kan antingen använda Azure Portal för att kontrol lera Azure Cosmos-kontot,-databasen och-behållaren eller använda följande Azure CLI-eller Azure PowerShell-skript för att visa en lista över hemligheten som skapats.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med efterföljande och själv studie kurser kanske du vill lämna resurserna på plats.
När de inte längre behövs tar du bort resurs gruppen, som tar bort Azure Cosmos-kontot och de relaterade resurserna. Ta bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett Azure Cosmos-konto, en databas och en behållare med hjälp av en Azure Resource Manager mall och verifierade distributionen. Om du vill veta mer om Azure Cosmos DB och Azure Resource Manager fortsätter du till artiklarna nedan.

- Läs en [Översikt över Azure Cosmos DB](introduction.md)
- Läs mer om [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Hämta andra [Azure Cosmos DB Resource Manager-mallar](resource-manager-samples.md)
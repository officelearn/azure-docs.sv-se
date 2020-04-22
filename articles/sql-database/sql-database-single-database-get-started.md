---
title: Skapa en enkel databas
description: Skapa en enda Azure SQL-databas med Azure-portalen, PowerShell eller Azure CLI. Fråga databasen med Frågeredigeraren i Azure-portalen.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 04/19/2020
ms.openlocfilehash: b06c255f94b4d539c7c85e318238b10629cbb907
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685156"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Snabbstart: Skapa en enda Azure SQL Database-databas

I den här snabbstarten använder du Azure-portalen, ett PowerShell-skript eller ett Azure CLI-skript för att skapa en enda Azure SQL-databas. Du frågar sedan databasen med **Frågeredigeraren** i Azure-portalen. 

En [enda databas](sql-database-single-database.md) är det snabbaste och enklaste distributionsalternativet för Azure SQL Database. Du hanterar en enskild databas i en [SQL Database-server](sql-database-servers.md), som finns i en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) i en angiven Azure-region. I den här snabbstarten skapar du en ny resursgrupp och SQL-server för den nya databasen.

Du kan skapa en enskild databas i den etablerade eller *serverlösa* *beräkningsnivån.* En etablerad databas förallokeras en fast mängd beräkningsresurser, inklusive CPU och minne, och använder en av två [inköpsmodeller](sql-database-purchase-models.md). Den här snabbstarten skapar en etablerad databas med hjälp av den [vCore-baserade](sql-database-service-tiers-vcore.md) inköpsmodellen, men du kan också välja en [DTU-baserad](sql-database-service-tiers-DTU.md) modell. 

Den serverlösa beräkningsnivån är endast tillgänglig i den vCore-baserade inköpsmodellen och har ett automatiskt skalat intervall med beräkningsresurser, inklusive CPU och minne. Information om hur du skapar en enskild databas på den serverlösa beräkningsnivån finns i [Skapa en serverlös databas](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).

## <a name="prerequisite"></a>Krav

- En aktiv Azure-prenumeration. Om du inte har en [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Skapa en enkel databas

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Fråga databasen

När databasen har skapats kan du använda den inbyggda **Frågeredigeraren** i Azure-portalen för att ansluta till databasen och fråga data.

1. Sök efter och välj **SQL-databaser**i portalen och välj sedan databasen i listan.
1. På **SQL Database**-sidan för databasen väljer du **Frågeredigeraren (förhandsversion)** i den vänstra menyn.
1. Ange inloggningsinformation för serveradministratören och välj **OK**.
   
   ![Logga in på Frågeredigeraren](./media/sql-database-single-database-get-started/query-editor-login.png)

1. Skriv följande fråga i fönstret **Frågeredigeraren**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Välj **Kör** och granska sedan frågeresultaten i fönstret **Resultat**.

   ![Resultat från Frågeredigeraren](./media/sql-database-single-database-get-started/query-editor-results.png)

1. Stäng sidan med **Frågeredigeraren** och klicka på **OK** för att ta bort de ändringar som inte har sparats.

## <a name="clean-up-resources"></a>Rensa resurser

Behåll resursgruppen, servern och den enskilda databasen för att gå vidare till nästa steg och lär dig hur du ansluter och frågar databasen med olika metoder.

När du är klar med dessa resurser kan du ta bort resursgruppen som du skapade, som också tar bort servern och den enda databasen i den.

### <a name="portal"></a>[Portalen](#tab/azure-portal)

Så här tar du bort **myResourceGroup** och alla dess resurser med Azure-portalen:

1. Sök efter och välj **Resursgrupper**i portalen och välj sedan **myResourceGroup** i listan.
1. På resursgruppssidan väljer du **Ta bort resursgrupp**.
1. Under **Skriv resursgruppsnamnet**anger du *myResourceGroup*och väljer sedan **Ta bort**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort resursgruppen och alla dess resurser kör du följande Azure CLI-kommando med namnet på resursgruppen:

```azurecli-interactive
az group delete --name <your resource group>
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort resursgruppen och alla dess resurser kör du följande PowerShell-cmdlet med namnet på resursgruppen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group>
```

---

## <a name="next-steps"></a>Nästa steg

[Anslut och fråga](sql-database-connect-query.md) databasen med olika verktyg och språk:
> [!div class="nextstepaction"]
> [Ansluta och köra frågor med SQL Server Management Studio](sql-database-connect-query-ssms.md)
> 
> [Ansluta och köra frågor med Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)

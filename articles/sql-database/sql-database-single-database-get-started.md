---
title: Skapa en enkel databas
description: Skapa en Azure SQL Database enskild databas med hjälp av Azure Portal, PowerShell eller Azure CLI. Fråga databasen med Frågeredigeraren i Azure Portal.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81685156"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Snabb start: skapa en Azure SQL Database enskild databas

I den här snabb starten använder du Azure Portal, ett PowerShell-skript eller ett Azure CLI-skript för att skapa en Azure SQL Database enkel databas. Sedan frågar du databasen med hjälp av **Frågeredigeraren** i Azure Portal. 

En [enkel databas](sql-database-single-database.md) är det snabbaste och enklaste distributions alternativet för Azure SQL Database. Du hanterar en enskild databas i en [SQL Database-Server](sql-database-servers.md), som finns i en [Azure-resurs grupp](../azure-resource-manager/management/overview.md) i en angiven Azure-region. I den här snabb starten skapar du en ny resurs grupp och SQL Server för den nya databasen.

Du kan skapa en enskild databas i den *etablerade* eller *Server* lös beräknings nivån. En etablerad databas är i förväg allokerad en fast mängd beräknings resurser, inklusive processor och minne, och använder en av två [inköps modeller](sql-database-purchase-models.md). Den här snabb starten skapar en etablerad databas med den [vCore-baserade](sql-database-service-tiers-vcore.md) inköps modellen, men du kan också välja en [DTU-baserad](sql-database-service-tiers-DTU.md) modell. 

Server lös beräknings nivån är bara tillgänglig i den vCore-baserade inköps modellen och har ett automatiskt skalat beräknings resurser, inklusive processor och minne. Information om hur du skapar en enskild databas i den serverbaserade beräknings nivån finns i [skapa en server lös databas](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).

## <a name="prerequisite"></a>Krav

- En aktiv Azure-prenumeration. Om du inte har en [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/). 

## <a name="create-a-single-database"></a>Skapa en enkel databas

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Fråga databasen

När databasen har skapats kan du använda den inbyggda **Frågeredigeraren** i Azure Portal för att ansluta till databasen och fråga efter data.

1. I portalen söker du efter och väljer **SQL-databaser**och väljer sedan din databas i listan.
1. På **SQL Database**-sidan för databasen väljer du **Frågeredigeraren (förhandsversion)** i den vänstra menyn.
1. Ange inloggnings information för Server administratören och välj **OK**.
   
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

Behåll resurs gruppen, servern och den enskilda databasen för att gå vidare till nästa steg och lär dig hur du ansluter och frågar databasen med olika metoder.

När du är klar med de här resurserna kan du ta bort resurs gruppen som du skapade, vilket även tar bort servern och en enskild databas i den.

### <a name="portal"></a>[Portalen](#tab/azure-portal)

Så här tar du bort **myResourceGroup** och alla dess resurser med hjälp av Azure Portal:

1. I portalen söker du efter och väljer **resurs grupper**och väljer sedan **myResourceGroup** i listan.
1. På sidan resurs grupp väljer du **ta bort resurs grupp**.
1. Under **Ange resurs gruppens namn**skriver du *myResourceGroup*och väljer sedan **ta bort**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort resurs gruppen och alla dess resurser kör du följande Azure CLI-kommando med namnet på din resurs grupp:

```azurecli-interactive
az group delete --name <your resource group>
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort resurs gruppen och alla dess resurser kör du följande PowerShell-cmdlet med namnet på din resurs grupp:

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

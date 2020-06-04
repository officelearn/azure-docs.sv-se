---
title: Skapa en enkel databas
description: Skapa en enda databas i Azure SQL Database med hjälp av Azure Portal, PowerShell eller Azure CLI. Fråga databasen med Frågeredigeraren i Azure Portal.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein, vanto
ms.date: 04/19/2020
ms.openlocfilehash: 1ae3159365e63159b8ed62c09bc20441ba65d8aa
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343854"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Snabb start: skapa en Azure SQL Database enskild databas

I den här snabb starten använder du Azure Portal, ett PowerShell-skript eller ett Azure CLI-skript för att skapa en enda databas i Azure SQL Database. Sedan frågar du databasen med hjälp av **Frågeredigeraren** i Azure Portal.

En [enkel databas](single-database-overview.md) är det snabbaste och enklaste alternativet för Azure SQL Database. Du hanterar en enskild databas på en [Server](logical-servers.md), som finns i en [Azure-resurs grupp](../../active-directory-b2c/overview.md) i en angiven Azure-region. I den här snabb starten skapar du en ny resurs grupp och server för den nya databasen.

Du kan skapa en enskild databas i den *etablerade* eller *Server* lös beräknings nivån. En etablerad databas är i förväg allokerad en fast mängd beräknings resurser, inklusive processor och minne, och använder en av två [inköps modeller](purchasing-models.md). Den här snabb starten skapar en etablerad databas med den [vCore-baserade](service-tiers-vcore.md) inköps modellen, men du kan också välja en [DTU-baserad](service-tiers-dtu.md) modell.

Server lös beräknings nivån är bara tillgänglig i den vCore-baserade inköps modellen och har ett intervall med beräknade beräknings resurser som är autoskalat, inklusive processor och minne. Information om hur du skapar en enskild databas i den serverbaserade beräknings nivån finns i [skapa en server lös databas](serverless-tier-overview.md#create-a-new-database-in-the-serverless-compute-tier).

## <a name="prerequisite"></a>Förutsättning

- En aktiv Azure-prenumeration. Om du inte har en [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Skapa en enkel databas

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Fråga databasen

När databasen har skapats kan du använda den inbyggda **Frågeredigeraren** i Azure Portal för att ansluta till databasen och fråga efter data.

1. I portalen söker du efter och väljer **SQL-databaser**och väljer sedan din databas i listan.
1. På **SQL Database**-sidan för databasen väljer du **Frågeredigeraren (förhandsversion)** i den vänstra menyn.
1. Ange inloggnings information för Server administratören och välj **OK**.

   ![Logga in på Frågeredigeraren](./media/single-database-create-quickstart/query-editor-login.png)

1. Skriv följande fråga i fönstret **Frågeredigeraren**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Välj **Kör** och granska sedan frågeresultaten i fönstret **Resultat**.

   ![Resultat från Frågeredigeraren](./media/single-database-create-quickstart/query-editor-results.png)

1. Stäng sidan med **Frågeredigeraren** och klicka på **OK** för att ta bort de ändringar som inte har sparats.

## <a name="clean-up-resources"></a>Rensa resurser

Behåll resurs gruppen, servern och den enskilda databasen för att gå vidare till nästa steg och lär dig hur du ansluter och frågar databasen med olika metoder.

När du är klar med de här resurserna kan du ta bort resurs gruppen som du skapade, vilket även tar bort servern och en enskild databas i den.

### <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

Så här tar du bort **myResourceGroup** och alla dess resurser med hjälp av Azure Portal:

1. I portalen söker du efter och väljer **resurs grupper**och väljer sedan **myResourceGroup** i listan.
1. På sidan resurs grupp väljer du **ta bort resurs grupp**.
1. Under **Ange resurs gruppens namn**skriver du *myResourceGroup*och väljer sedan **ta bort**.

### <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

[Anslut och fråga](connect-query-content-reference-guide.md) databasen med olika verktyg och språk:
> [!div class="nextstepaction"]
> [Ansluta och köra frågor med SQL Server Management Studio](connect-query-ssms.md)
>
> [Ansluta och köra frågor med Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
 
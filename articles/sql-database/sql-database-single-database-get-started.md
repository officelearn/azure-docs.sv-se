---
title: Skapa en enkel databas – Azure SQL Database | Microsoft Docs
description: Skapa och fråga en enskild databas i Azure SQL Database med hjälp av Azure Portal, PowerShell och Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 07/29/2019
ms.openlocfilehash: f9c2ed77075affdfdf464dbf8a468629fc2e610d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640064"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>Snabbstart: Skapa en enskild databas i Azure SQL Database med hjälp av Azure Portal, PowerShell och Azure CLI

Att skapa en [enkel databas](sql-database-single-database.md) är det snabbaste och enklaste distributionsalternativet för att skapa en databas i Azure SQL Database. Snabbstarten visar hur du skapar och sedan frågar en enkel databas Azure-portalen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 

Logga in på [Azure Portal](https://portal.azure.com/) för alla steg i den här snabbstarten.

## <a name="create-a-single-database"></a>Skapa en enkel databas

En enskild databas kan antingen skapas i beräknings nivån etablerade eller utan server (för hands version).

- En enskild databas i den etablerade beräknings nivån har en definierad mängd förallokerade beräknings resurser plus en uppsättning minnes-och lagrings resurser som använder en av två [inköps modeller](sql-database-purchase-models.md).
- En enkel databas i en server lös beräknings nivå har ett intervall med beräknings resurser som skalas automatiskt plus en viss mängd minne per kärna och en angiven mängd lagrings resurser och är bara tillgänglig i [vCore-baserade inköps modeller](sql-database-service-tiers-vcore.md).

När du skapar en enkel databas definierar du även en [SQL Database-server](sql-database-servers.md) för att hantera den och placera den i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i en specifik region.

> [!NOTE]
> I den här snabb starten används den [vCore-baserade inköps modellen](sql-database-service-tiers-vcore.md), men den [DTU-baserade inköps modellen](sql-database-service-tiers-DTU.md) är också tillgänglig.

Så här skapar du en enkel SQL-databas som innehåller AdventureWorksLT-exempeldata:

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>Fråga databasen

Nu när du har skapat en databas ska du använda det inbyggda frågeverktyget på Azure Portal för att ansluta till databasen och fråga efter data.

1. På **SQL Database**-sidan för databasen väljer du **Frågeredigeraren (förhandsversion)** i den vänstra menyn.

   ![Logga in på Frågeredigeraren](./media/sql-database-get-started-portal/query-editor-login.png)

2. Ange din inloggningsinformation och välj **OK**.
3. Skriv följande fråga i fönstret **Frågeredigeraren**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Välj **Kör** och granska sedan frågeresultaten i fönstret **Resultat**.

   ![Resultat från Frågeredigeraren](./media/sql-database-get-started-portal/query-editor-results.png)

5. Stäng sidan med **Frågeredigeraren** och klicka på **OK** för att ta bort de ändringar som inte har sparats.

## <a name="clean-up-resources"></a>Rensa resurser

Behåll den här resursgruppen, databasservern och den enkla databasen om du vill gå till [Nästa steg](#next-steps). Nästa steg visar hur du ansluter och kör frågor mot din databas med olika metoder.

När du är klar med dessa resurser kan du ta bort dem på följande sätt:

1. På menyn till vänster i Azure Portal klickar du på **Resursgrupper** och sedan på **myResourceGroup**.
2. Välj **Ta bort resursgrupp** på din resursgruppssida.
3. Ange *myResourceGroup* i fältet och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Skapa en brandväggsregel på servernivå för att ansluta till den enkla databasen från lokala eller fjärranslutna verktyg. Mer information finns i [Skapa en brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md).
- När du har skapat en brandväggsregel på servernivå [ansluter du till och kör frågor mot](sql-database-connect-query.md) databasen med hjälp av flera olika verktyg och språk.
  - [Ansluta och köra frågor med SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Ansluta och köra frågor med Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Information om hur du skapar en enskild databas i den etablerade beräknings nivån med Azure CLI finns i [Azure CLI-exempel](sql-database-cli-samples.md).
- Om du vill skapa en enskild databas i den allokerade beräknings nivån med hjälp av Azure PowerShell, se [Azure PowerShell exempel](sql-database-powershell-samples.md).
- Information om hur du skapar en enskild databas i Server lös beräknings nivå med hjälp av Azure PowerShell finns i [skapa server lös databas](sql-database-serverless.md#create-new-database-in-serverless-compute-tier).

---
title: Skapa en Azure SQL-databas med hjälp av portalen | Microsoft Docs
description: Skapa en logisk server och databas för Azure SQL Database på Azure-portalen och ställ frågor till den.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 1/9/2019
ms.openlocfilehash: b11eb08a960e81ab938a9b15a1153c44706231c5
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198295"
---
# <a name="quickstart-create-an-azure-sql-database-in-the-azure-portal"></a>Snabbstart: Skapa en Azure SQL-databas på Azure-portalen

Azure SQL Database är en *databas som erbjuds som en tjänst*. Tjänsten innebär att du kan köra och skala SQL Server-databaser med hög tillgänglighet i molnet. Snabbstarten visar hur du kommer igång genom att skapa och sedan köra frågor mot en Azure SQL-databas med Azure-portalen. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

Logga in på [Azure-portalen](https://portal.azure.com/) för alla steg i den här snabbstarten.

## <a name="create-a-sql-database"></a>Skapa en SQL-databas

Azure SQL-databasen har en definierad uppsättning [beräknings-och lagringsresurser](sql-database-service-tiers-dtu.md). Du skapar databasen i en [logisk Azure SQL Database-server](sql-database-features.md) inom en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md).

Så här skapar du en SQL-databas som innehåller AdventureWorksLT-exempeldata:

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
   
1. Välj **Databaser** och välj sedan **SQL Database**.
   
1. I formuläret **Skapa SQL-databas** skriver eller väljer du följande värden: 
   
   - **Databasnamn**: Ange *mySampleDatabase*.
   - **Prenumeration**: Välj rätt prenumeration i den nedrullningsbara listrutan om den inte visas.
   - **Resursgrupp**: Välj **Skapa ny**, skriv *myResourceGroup* och välj **OK**. 
   - **Välj källa**: Välj **Sample (AdventureWorksLT)** i den nedrullningsbara listrutan.
    
    >[!IMPORTANT]
    >Se till att välja data för **Sample (AdventureWorksLT)** så att du kan följa den här och andra snabbstarter för Azure SQL-databas som använder dessa data.
  
   ![Skapa en Azure SQL.databas](./media/sql-database-get-started-portal/create-database-1.png)
   
1. Under **Server** väljer du **Skapa ny**. 
   
1. I formuläret **Ny server** anger eller väljer du följande värden: 
   
   - **Servernamn**: Ange *mysqlserver*.
   - **Inloggning för serveradministratör**: Skriv *azureuser*. 
   - **Lösenord**: Ange *Azure1234567*. 
   - **Bekräfta lösenord**: Skriv lösenordet igen.
   - **Plats**: Välj valfri giltig plats i den nedrullningsbara listrutan.  
   
   >[!IMPORTANT]
   >Skriv ned serveradministratörens inloggning och lösenord så att du kan logga in på servern och databaserna till denna och andra snabbstarter. Om du glömmer din inloggning eller ditt lösenord kan du hämta inloggningsnamnet eller återställa lösenordet på **SQLServer**-sidan. För att öppna **SQLServer**-sidan väljer du servernamnet i databasens **översiktssida** när databasen har skapats.
   
    ![Skapa server](./media/sql-database-get-started-portal/create-database-server.png)

1. Välj **Välj**.
   
1. I formuläret **SQL Database** väljer du **Prisnivå**. Undersök mängden DTU:er och lagring som är tillgänglig på varje tjänstnivå.
   
   >[!NOTE]
   >I den här snabbstarten används den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md), men den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) är också tillgänglig.
   
   >[!NOTE]
   >Mer än 1 TB lagringsutrymme på Premium-nivån är för närvarande tillgängligt i alla regioner förutom: Storbritannien, norra; USA, västra centrala; Storbritannien, södra 2; Kina, östra; US DoD, centrala; Tyskland, centrala; US DoD, östra; US Gov, sydvästra; US Gov, södra centrala; Tyskland, nordöstra; Kina, norra och US Gov, östra. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB. Mer information finns i [Aktuella begränsningar för P11–P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   
1. För den här snabbstarten väljer du tjänstnivån **Standard** och använder sedan skjutreglaget för att välja **10 DTU:er (S0)** och **1** GB lagring.
   
1. Välj **Använd**.  
   
   ![Välj prisnivå](./media/sql-database-get-started-portal/create-database-s1.png)
   
1. I formuläret **SQL Database** väljer du **Skapa** för att distribuera och etablera resursgruppen, servern och databasen. 
   
   Distributionen tar några minuter. Du kan välja **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

   ![Avisering](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>Söka i SQL-databasen

Nu när du har skapat en Azure SQL-databas ska du använda det inbyggda frågeverktyget på Azure-portalen för att ansluta till databasen och fråga efter data.

1. På **SQL Database**-sidan för databasen väljer du **Frågeredigeraren (förhandsversion)** i den vänstra menyn. 
   
   ![Logga in på Frågeredigeraren](./media/sql-database-get-started-portal/query-editor-login.png)
   
1. Ange din inloggningsinformation och välj **OK**.
   
1. Skriv följande fråga i fönstret **Frågeredigeraren**.
   
   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```
   
1. Välj **Kör** och granska sedan frågeresultaten i fönstret **Resultat**.

   ![Resultat från Frågeredigeraren](./media/sql-database-get-started-portal/query-editor-results.png)
   
1. Stäng sidan med **Frågeredigeraren** och klicka på **OK** för att ta bort de ändringar som inte har sparats.

## <a name="clean-up-resources"></a>Rensa resurser

Behåll den här resursgruppen, SQL-servern och SQL-databasen om du vill gå till [Nästa steg](#next-steps). Nästa steg visar hur du ansluter och kör frågor mot din databas med olika metoder. 

När du är klar med dessa resurser kan du ta bort dem på följande sätt:

1. På menyn till vänster i Azure-portalen klickar du på **Resursgrupper** och sedan på **myResourceGroup**.
1. Välj **Ta bort resursgrupp** på din resursgruppssida. 
1. Ange *myResourceGroup* i fältet och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Skapa en brandväggsregel på servernivå för att ansluta till Azure SQL-databasen från lokala eller fjärranslutna verktyg. Mer information finns i [Skapa en brandväggsregel på servernivå](sql-database-get-started-portal-firewall.md).
- När du har skapat en brandväggsregel på servernivå [ansluter du till och kör frågor mot](sql-database-connect-query.md) databasen med hjälp av flera olika verktyg och språk. 
  - [Ansluta och köra frågor med SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Ansluta och köra frågor med Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Information om hur du skapar Azure SQL-databaser med hjälp av Azure CLI finns i [Azure CLI-exempel](sql-database-cli-samples.md).
- Information om hur du skapar Azure SQL-databaser med hjälp av Azure PowerShell finns i [Azure PowerShell-exempel](sql-database-powershell-samples.md).

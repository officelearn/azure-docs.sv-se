---
title: 'Azure-portalen: Skapa en enkel databas – Azure SQL Database | Microsoft Docs'
description: Skapa och fråga en enkel databas i Azure SQL Database med Azure-portalen.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 66640bd60f6ba8c2f612a6bc9b88a07b13012997
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990042"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>Snabbstart: Skapa en enkel databas i Azure SQL Database med Azure-portalen

Att skapa en [enkel databas](sql-database-single-database.md) är det snabbaste och enklaste distributionsalternativet för att skapa en databas i Azure SQL Database. Snabbstarten visar hur du skapar och sedan frågar en enkel databas Azure-portalen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

Logga in på [Azure Portal](https://portal.azure.com/) för alla steg i den här snabbstarten.

## <a name="create-a-single-database"></a>Skapa en enkel databas

En enkel databas har en definierad uppsättning resurser för beräkning, minne, IO och lagring som använder en av de två [purchasing model(sql-database-purchase-models.md). När du skapar en enkel databas definierar du även en [SQL-databasserver](sql-database-servers.md) för att hantera den och placera den i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i en specifik region.

Så här skapar du en enkel SQL-databas som innehåller AdventureWorksLT-exempeldata:

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Välj **Databaser** och välj sedan **SQL Database**.
3. I formuläret **Skapa SQL-databas** skriver eller väljer du följande värden:

   - **Databasnamn**: Ange *mySampleDatabase*.
   - **Prenumeration**: Välj rätt prenumeration i den nedrullningsbara listrutan om den inte visas.
   - **Resursgrupp**: Välj **Skapa ny**, skriv *myResourceGroup* och välj **OK**.
   - **Välj källa**: Välj **Sample (AdventureWorksLT)** i den nedrullningsbara listrutan.

    >[!IMPORTANT]
    >Se till att välja data för **Sample (AdventureWorksLT)** så att du enkelt kan följa den här och andra snabbstarter för Azure SQL-databas som använder dessa data.
  
   ![Skapa en enkel databas](./media/sql-database-get-started-portal/create-database-1.png)

4. Under **Server** väljer du **Skapa ny**.
5. I formuläret **Ny server** anger eller väljer du följande värden:

   - **Servernamn**: Ange *mysqlserver*.
   - **Inloggning för serveradministratör**: Skriv *azureuser*.
   - **Lösenord**: Ange *Azure1234567*.
   - **Bekräfta lösenord**: Skriv lösenordet igen.
   - **Plats**: Välj valfri giltig plats i den nedrullningsbara listrutan.  

   >[!IMPORTANT]
   >Skriv ned serveradministratörens inloggning och lösenord så att du kan logga in på servern och databaserna till denna och andra snabbstarter. Om du glömmer din inloggning eller ditt lösenord kan du hämta inloggningsnamnet eller återställa lösenordet på **SQLServer**-sidan. För att öppna **SQLServer**-sidan väljer du servernamnet i databasens **översiktssida** när databasen har skapats.

    ![Skapa server](./media/sql-database-get-started-portal/create-database-server.png)

6. Välj **Välj**.
7. I formuläret **SQL Database** väljer du **Prisnivå**. Undersök mängden DTU:er och lagring som är tillgänglig på varje tjänstnivå.

   >[!NOTE]
   >I den här snabbstarten används den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md), men den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) är också tillgänglig.
   >[!IMPORTANT]
   >Mer än 1 TB lagringsutrymme på Premium-nivån är för närvarande tillgängligt i alla regioner förutom: Storbritannien, norra; USA, västra centrala; Storbritannien, södra 2; Kina, östra; US DoD, centrala; Tyskland, centrala; US DoD, östra; US Gov, sydvästra; US Gov, södra centrala; Tyskland, nordöstra; Kina, norra och US Gov, östra. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB. Mer information finns i [Aktuella begränsningar för P11–P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

8. För den här snabbstarten väljer du tjänstnivån **Standard** och använder sedan skjutreglaget för att välja **10 DTU:er (S0)** och **1** GB lagring.
9. Välj **Använd**.  

   ![Välj prisnivå](./media/sql-database-get-started-portal/create-database-s1.png)

10. I formuläret **SQL Database** väljer du **Skapa** för att distribuera och etablera resursgruppen, servern och databasen.

   Distributionen tar några minuter. Du kan välja **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

   ![Avisering](./media/sql-database-get-started-portal/notification.png)

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
- Information om hur du skapar enkla databaser med hjälp av Azure CLI finns i [Azure CLI-exempel](sql-database-cli-samples.md).
- Information om hur du skapar enkla databaser med hjälp av Azure PowerShell finns i [Azure PowerShell-exempel](sql-database-powershell-samples.md).

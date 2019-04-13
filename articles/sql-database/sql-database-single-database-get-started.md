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
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 04/11/2019
ms.openlocfilehash: a5fbc58feea8779ba8a7a61dfc89158e20bd2c92
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544304"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>Snabbstart: Skapa en enkel databas i Azure SQL Database med Azure-portalen

Att skapa en [enkel databas](sql-database-single-database.md) är det snabbaste och enklaste distributionsalternativet för att skapa en databas i Azure SQL Database. Snabbstarten visar hur du skapar och sedan frågar en enkel databas Azure-portalen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

Logga in på [Azure Portal](https://portal.azure.com/) för alla steg i den här snabbstarten.

## <a name="create-a-single-database"></a>Skapa en enkel databas

En enkel databas har en definierad uppsättning resurser för beräkning, minne, IO och lagring som använder en av de två [inköpsmodellerna](sql-database-purchase-models.md). När du skapar en enkel databas definierar du även en [SQL-databasserver](sql-database-servers.md) för att hantera den och placera den i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) i en specifik region.

Så här skapar du en enkel SQL-databas som innehåller AdventureWorksLT-exempeldata:

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Välj **databaser** och välj sedan **SQL Database** att öppna den **skapa SQL-databas** sidan. 

   ![Skapa en enkel databas](./media/sql-database-get-started-portal/create-database-1.png)

1. På den **grunderna** fliken den **projektinformation** avsnittet, skriver eller Välj följande värden:

   - **Prenumeration**: Välj rätt prenumeration i den nedrullningsbara listrutan om den inte visas.
   - **Resursgrupp**: Välj **Skapa nytt**, typ `myResourceGroup`, och välj **OK**.

   ![Ny SQL database - fliken Grundläggande](media/sql-database-get-started-portal/new-sql-database-basics.png)


1. I den **databasinformation** avsnittet, skriver eller Välj följande värden: 

   - **Databasnamn**: Ange `mySampleDatabase`.
   - **Server**: Välj **Skapa nytt** och ange följande värden och välj sedan **Välj**. 
       - **Servernamn**: Typ `mysqlserver`; tillsammans med vissa nummer för unikhet. 
       - **Inloggning för serveradministratör**: Skriv `azureuser`.
       - **Lösenord**: Ange ett komplext lösenord som uppfyller lösenordskraven för. 
       - **Plats**: Välj en plats i listrutan, till exempel `West US 2`. 

       ![Ny server](media/sql-database-get-started-portal/new-server.png)

        > [!IMPORTANT]
        > Skriv ned serveradministratörens inloggning och lösenord så att du kan logga in på servern och databaserna till denna och andra snabbstarter. Om du glömmer din inloggning eller ditt lösenord kan du hämta inloggningsnamnet eller återställa lösenordet på **SQLServer**-sidan. För att öppna **SQLServer**-sidan väljer du servernamnet i databasens **översiktssida** när databasen har skapats.

      ![Information om SQL-databas](media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Vill du använda SQL-databaspool**: Välj den **nr** alternativet. 
   - **Beräkning + lagring**: Välj **konfigurera databasen** och för den här snabbstarten väljer du den **Standard** tjänstnivån och använder sedan skjutreglaget för att välja **10 dtu: er (S0)** och **1** GB lagring. Välj **Använd**. 

    ![Konfigurera nivå](media/sql-database-get-started-portal/create-database-s1.png) 


      > [!NOTE]
      > I den här snabbstarten används den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md), men den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) är också tillgänglig.
      > [!IMPORTANT]
      > Mer än 1 TB lagringsutrymme på Premium-nivån är för närvarande tillgängligt i alla regioner förutom: Kina, östra; Kina, norra; Tyskland, centrala; Tyskland, nordöstra; USA, västra centrala; US DoD-regioner samt US Government Central. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i [Aktuella begränsningar för P11–P15](sql-database-single-database-scale.md#dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

    



1. Välj den **ytterligare inställningar** fliken. 
1. I den **datakälla** under avsnittet **använda befintliga data**väljer `Sample`. 

   ![Ytterligare SQL DB-inställningar](media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Se till att välja data för **Sample (AdventureWorksLT)** så att du enkelt kan följa den här och andra snabbstarter för Azure SQL-databas som använder dessa data.

1. Lämna resten av värdena som standard och välj **granska + skapa** längst ned i formuläret. 
1. Granska de slutliga inställningarna och välj **skapa**. 

8. I formuläret **SQL Database** väljer du **Skapa** för att distribuera och etablera resursgruppen, servern och databasen.


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
- För att skapa en enskild databas med Azure CLI, se [Azure CLI-exempel](sql-database-cli-samples.md).
- För att skapa en enskild databas med Azure PowerShell, se [Azure PowerShell-exempel](sql-database-powershell-samples.md).

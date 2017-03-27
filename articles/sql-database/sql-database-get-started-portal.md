---
title: 'Azure Portal: Skapa en SQL-databas | Microsoft Docs'
description: "Lär dig att skapa en logisk SQL Database-server, brandväggsregel på servernivå och databaser i Azure Portal. Du får också lära dig att skicka frågor till en Azure SQL-databas med Azure Portal."
keywords: "sql database-självstudier, skapa en sql-databas"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 1c087f3ecbdd6956b50a8ef9b7e81340f83ff0cf
ms.lasthandoff: 03/21/2017


---
# <a name="create-and-query-a-single-azure-sql-database-in-the-azure-portal"></a>Skapa och skicka fråga till en enskild Azure SQL-databas i Azure Portal

Det går att skapa Azure SQL-databaser via Azure Portal. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa och konfigurera Azure SQL-databaser och alla relaterade Azure-resurser.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Skapa en SQL-databas

Azure SQL-databasen skapas med en definierad uppsättning [beräknings-och lagringsresurser](sql-database-service-tiers.md). Databasen skapas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) och i en [logisk Azure SQL Database-server](sql-database-features.md). 

Följ de här stegen om du vill skapa en SQL-databas som innehåller Adventure Works LT-exempeldata. 

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

2. Välj **Databaser** på bladet **Nytt** och välj **SQL Database** på sidan **Databaser**.

3. Fyll i SQL Database-formuläret med informationen som krävs: 
   - Databasnamn: Ange ett databasnamn
   - Prenumeration: Välj din prenumeration
   - Resursgrupp: Välj en ny eller en befintlig
   - Källa: Välj **Exempel (AdventureWorksLT)**
   - Server: Skapa en ny server (**servernamnet** måste vara globalt unikt)
   - Elastisk pool: Välj **Not now** (Inte nu) för den här snabbstarten
   - Prisnivå: Välj **20 DTU:er** och **250** GB lagring
   - Sortering: Du kan inte ändra det här värdet när du importerar exempeldatabasen 
   - Fäst på instrumentpanelen: Markera den här kryssrutan

      ![skapa databas](./media/sql-database-get-started/create-database-s1.png)

4. Klicka på **Skapa** när du är klar. Etableringen tar några minuter.
5. När distribueringen av SQL-databasen är slutförd markerar du **	SQL-databaser** på instrumentpanelen eller **SQL-databaser** på menyn till vänster och klickar på din nya databas på sidan **SQL-databaser**. En översiktssida för databasen öppnas, där du kan se det fullständigt kvalificerade servernamnet (som **mynewserver20170313.database.windows.net**) och alternativ för ytterligare konfiguration.

      ![new-sql database](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

SQL Database-tjänsten skapar en brandvägg som hindrar externa program och verktyg att ansluta till servern och databasen. Följ de här stegen om du vill skapa en [brandväggsregel för din IP-adress på SQL Database-servernivå](sql-database-firewall-configure.md) genom att aktivera extern anslutning genom SQL-databasens brandvägg. 

1. Klicka på **Konfigurera serverns brandvägg** i databasens verktygsfält. Sidan **Brandväggsinställningar** för SQL Database-servern öppnas. 

      ![brandväggsregler för server](./media/sql-database-get-started/server-firewall-rule.png) 

2. Klicka på **Lägg till klient-IP** i verktygsfältet och klicka på **Spara**. En brandväggsregel på servernivå skapas för din aktuella IP-adress.

3. Klicka på **OK** och sedan på **X** för att stänga sidan med brandväggsinställningar.

Nu kan du ansluta till databasen och dess server med SQL Server Management Studio eller något annat verktyg.

## <a name="query-the-sql-database"></a>Söka i SQL-databasen

Följ stegen i för att söka i databasen med frågeredigeraren i Azure Portal. 

1. På SQL Database-sidan för din databas klickar du på **Verktyg** i verktygsfältet. Förhandsgranskningssidan **Frågeredigerare** öppnas.

     ![verktygsmeny](./media/sql-database-get-started/tools-menu.png) 

2. Klicka på **Frågeredigerare (förhandsgranskning)**, klicka i kryssrutan **Villkor för förhandsgranskning** och sedan på **OK**. Frågeredigeraren öppnas.

3. Klicka på **Logga in** och markera sedan **SQL-serverautentisering** när du blir uppmanad. Ange sedan inloggning och lösenord för serveradministratör.
4. Logga in genom att klicka på **OK**.

5. När du är autentiserad skriver du en valfri fråga i frågefönstret, som följande fråga:

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Klicka på **Kör** och granska frågeresultaten i fönstret **Resultat**.

    ![resultat från frågeredigeraren](./media/sql-database-get-started/query-editor-results.png)

7. Klicka på **X** för att stänga frågeredigerarsidan.

## <a name="clean-up-resources"></a>Rensa resurser

Snabbstarterna **Anslut med** i den här samlingen och självstudierna i självstudiesamlingen bygger på denna snabbstart. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure Portal.

1. På menyn till vänster i Azure Portal klickar du på **Resursgrupper** och sedan på **myResourceGroup**. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver **myResourceGroup** i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Om du vill ansluta och fråga med SQL Server Management Studio kan du läsa [Anslut och fråga med SSMS](sql-database-connect-query-ssms.md)
- Om du vill ansluta med Visual Studio kan du läsa [Ansluta och fråga med Visual Studio](sql-database-connect-query.md).
- En teknisk översikt över SQL Database finns i [Om SQL Database-tjänsten](sql-database-technical-overview.md).


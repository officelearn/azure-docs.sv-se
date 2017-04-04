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
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: be5839e04fae457b889db11dffe56f31afe723a5
ms.lasthandoff: 03/28/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Skapa en Azure SQL-databas på Azure Portal

Den här snabbstarten visar hur du skapar en SQL-databas i Azure.  Azure SQL Database är en databas som erbjuds en tjänst. Tjänsten innebär att du kan köra och skala SQL Server-databaser med hög tillgänglighet i molnet.  Snabbstarten visar hur du kommer igång genom att skapa en ny SQL-databas på Azure Portal.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>Skapa en SQL-databas

Azure SQL-databasen skapas med en definierad uppsättning [beräknings-och lagringsresurser](sql-database-service-tiers.md). Databasen skapas i en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) och i en [logisk Azure SQL Database-server](sql-database-features.md). 

Följ de här stegen om du vill skapa en SQL-databas som innehåller Adventure Works LT-exempeldata. 

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.

2. Välj **Databaser** på bladet **Nytt** och välj **SQL Database** på sidan **Databaser**.

    ![skapa databas-1](./media/sql-database-get-started/create-database-1.png)

3. Fyll i följande information i SQL Database-formuläret (se föregående bild): 
   - Databasnamn: Använd **mySampleDatabase**
   - Resursgrupp: Använd **myResourceGroup**
   - Källa: Välj **Exempel (AdventureWorksLT)**

4. Klicka på **Server** för att skapa och konfigurera en ny server för den nya databasen. Fyll i den **formuläret för ny server**. Ange ett globalt unikt servernamn, ett namn för inloggning för serveradministratör och ett lösenord. 

    ![skapa databas-server](./media/sql-database-get-started/create-database-server.png)
5. Klicka på **Välj**.

6. Klicka på **Prisnivå** för att ange tjänstenivå och prestandanivå för den nya databasen. För den här snabbstarten väljer du **20 DTU:er** och **250** GB lagringsutrymme

    ![skapa databas-s1](./media/sql-database-get-started/create-database-s1.png)

7. Klicka på **Använd**.  

8. Klicka på **Skapa** för att etablera databasen. Etableringen tar några minuter. 

9. Klicka på **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

    ![avisering](./media/sql-database-get-started/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

SQL Database-tjänsten skapar en brandvägg på servernivå som hindrar externa program och verktyg från att ansluta till servern eller databaser på servern, såvida inte en brandväggsregel har konfigurerats som öppnar brandväggen för specifika IP-adresser. Följ de här stegen om du vill skapa en [brandväggsregel på SQL Database-servernivå](sql-database-firewall-configure.md) för din klients IP-adress och aktivera extern anslutning genom SQL Database-brandväggen endast för din IP-adress. 

1. När distributionen är klar klickar du på **SQL-databaser** på menyn till vänster och på din nya databas, **mySampleDatabase** på sidan **SQL-databaser**. Översiktssidan för databasen öppnas, där du kan se det fullständigt kvalificerade servernamnet (som **mynewserver20170327.database.windows.net**) och alternativ för ytterligare konfiguration.

      ![brandväggsregler för server](./media/sql-database-get-started/server-firewall-rule.png) 

2. Klicka på **Konfigurera serverns brandvägg** i verktygsfältet (se föregående bild). Sidan **Brandväggsinställningar** för SQL Database-servern öppnas. 

3. Klicka på **Lägg till klient-IP** i verktygsfältet och klicka på **Spara**. En brandväggsregel på servernivå skapas för din aktuella IP-adress.

      ![ange brandväggsregel för server](./media/sql-database-get-started/server-firewall-rule-set.png) 

4. Klicka på **OK** och sedan på **X** för att stänga sidan **Brandväggsinställningar**.

Nu kan du ansluta till databasen och dess server med SQL Server Management Studio eller något annat verktyg.

## <a name="query-the-sql-database"></a>Söka i SQL-databasen

När vi skapade SQL-databasen fyllde vi den med **AdventureWorksLT**-exempeldatabasen (det här var ett av alternativen vi valde i användargränssnittet för att skapa tidigare i den här snabbstarten). Nu kan vi fråga efter data med hjälp av det inbyggda frågeverktyget på Azure Portal. 

1. På SQL Database-sidan för din databas klickar du på **Verktyg** i verktygsfältet. Sidan **Verktyg** öppnas.

     ![verktygsmeny](./media/sql-database-get-started/tools-menu.png) 

2. Klicka på **Frågeredigerare (förhandsgranskning)**, klicka i kryssrutan **Villkor för förhandsgranskning** och sedan på **OK**. Sidan Frågeredigerare öppnas.

3. Klicka på **Logga in** och markera sedan **SQL-serverautentisering** när du blir uppmanad. Ange sedan inloggning och lösenord för serveradministratör som du skapade tidigare.

    ![inloggning](./media/sql-database-get-started/login.png) 

4. Logga in genom att klicka på **OK**.

5. När du är autentiserad skriver du följande fråga i fönstret för frågeredigeraren.

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Klicka på **Kör** och granska frågeresultaten i fönstret **Resultat**.

    ![resultat från frågeredigeraren](./media/sql-database-get-started/query-editor-results.png)

7. Klicka på **X** för att stänga sidan **Frågeredigerare** och klicka på **X** igen för att stänga sidan **Verktyg**.

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten i Azure Portal.

1. På menyn till vänster i Azure Portal klickar du på **Resursgrupper** och sedan på **myResourceGroup**. 
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver **myResourceGroup** i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Om du vill ansluta och fråga med SQL Server Management Studio kan du läsa [Anslut och fråga med SSMS](sql-database-connect-query-ssms.md)
- Om du vill ansluta med Visual Studio kan du läsa [Ansluta och fråga med Visual Studio](sql-database-connect-query.md).
- En teknisk översikt över SQL Database finns i [Om SQL Database-tjänsten](sql-database-technical-overview.md).


---
title: Anslut Excel till SQL Database | Microsoft Docs
description: Lär dig hur man ansluter Microsoft Excel till en Azure SQL-databas i molnet. Importera data till Excel för rapportering och dataundersökning.
services: sql-database
keywords: ansluta excel till sql, importera data till excel
author: joseidz
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 03/10/2017
ms.author: craigg
ms.openlocfilehash: 6f2894d65240580346b99d203f8289652d8e6618
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Anslut Excel till en Azure SQL database och skapa en rapport

Anslut Excel till en SQL-databas i molnet och importera data och skapa tabeller och diagram baserat på värdena i databasen. I de här självstudierna kommer du att ställa in anslutningen mellan Excel och en databastabell, spara filen som lagrar data och anslutningsinformationen för Excel och sedan skapa ett pivotdiagram från databasvärdena.

Du behöver en SQL-databas i Azure innan du börjar. Om du inte har någon, kan du se [Skapa din första SQL-databas](sql-database-get-started-portal.md) för att starta en databas med exempeldata på några minuter. I den här artikeln, ska du importera exempeldata till Excel från artikeln, men du kan följa liknande steg med dina egna data.

Du kommer också behöva en kopia av Excel. Den här artikeln använder [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Anslut Excel till SQL-databasen och Läs in data
1. För att ansluta Excel till SQL-databasen, öppnar du Excel och skapar en ny arbetsbok, eller öppnar en befintlig Excel-arbetsbok.
2. I menyraden överst på sidan, Välj den **Data** väljer **hämta Data**, Välj från Azure och välj sedan **från Azure SQL Database**. 
   
   ![Välj datakälla: Anslut Excel till SQL-databas.](./media/sql-database-connect-excel/excel_data_source.png)
   
   Dataanslutningsguiden öppnas.
3. I dialogrutan **Anslut till databasserver**, skriver du **Servernamnet** för den SQL Database du vill ansluta till i formatet <*servernamn*>**. database.windows.net**. Till exempel **msftestserver.database.windows.net**. Alternativt kan du ange namnet på databasen. Välj **OK** att öppna fönstret autentiseringsuppgifter. 

   ![Server-name.png](media/sql-database-connect-excel/server-name.png)

1. I den **SQL Server-databas** dialogrutan **databasen** på vänster sida och anger i din **användarnamn** och **lösenord** för den SQL-databasservern som du vill ansluta till. Välj **Anslut** att öppna den **Navigator**. 

  ![Ange servernamn och inloggningsuppgifter](./media/sql-database-connect-excel/connect-to-server.png)
   
  > [!TIP]
  > Beroende på din nätverksmiljö, är det möjligt att du inte kan ansluta, eller så kan du tappa anslutningen om SQL Database-servern inte tillåter trafik från din klient-IP-adress. Gå till [Azure-portalen](https://portal.azure.com/), klicka på SQL-servrar, klicka på din server, klicka på brandvägg under inställningar och lägg till din klient-IP-adress. Se [Så här konfigurerar du brandväggsinställningar](sql-database-configure-firewall-settings.md) för mer information.
   
   
5. I den **Navigator**, markera databasen som du vill arbeta med i listan, Välj de tabeller eller vyer som du vill arbeta med (vi valde **vGetAllCategories**), och välj sedan **belastningen**att flytta data från SQL Azure-databas till din excel-kalkylblad.
   
    ![Välj en databas och en tabell.](./media/sql-database-connect-excel/select-database-and-table.png)
   

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importera data till Excel och skapa ett pivotdiagram
Nu när du har etablerat anslutningen har flera olika alternativ med hur du läser in data. Följande steg kan exempelvis skapa ett pivotdiagram baserat på den information som finns i SQL-databasen. 

1. Följ stegen i föregående avsnitt, men den här gången istället för att välja **belastningen**väljer **läsa in till** från den **belastningen** nedrullningsbara.
2. Välj sedan hur du vill visa data i arbetsboken. Vi valde **PivotChart**. Du kan också välja att skapa ett **Nytt kalkylblad** eller **Lägg till den här datan i en Datamodell**. Mer information om datamodeller finns i [Skapa en datamodell i Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 
   
    ![Välja dataformat i Excel](./media/sql-database-connect-excel/import-data.png)
   
    Kalkylbladet har nu en tom pivottabell och diagram.
2. Under **PivotTable-fält** väljer du alla kryssrutor för fälten du vill visa.
   
    ![Konfigurera databasrapporten.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Om du vill ansluta andra Excel-arbetsböcker och kalkylblad till databasen, väljer du den **Data** fliken och markera **de senaste källorna** att starta den **de senaste källorna** dialogrutan. Därifrån väljer den anslutning som du skapade i listan och klicka sedan på **öppna**.
> ![Senaste anslutningar](media/sql-database-connect-excel/recent-connections.png)
 
## <a name="create-a-permanent-connection-using-odc-file"></a>Skapa en permanent anslutning med ODC-filen
Om du vill spara anslutningsinformationen permanent kan du skapa en ODC-fil och gör den här anslutningen ett valbart alternativ inom den **befintliga anslutningar** dialogrutan. 

1. I menyraden överst på sidan, Välj den **Data** och välj sedan **befintliga anslutningar** att starta den **befintliga anslutningar** dialogrutan. 
    1. Välj **Bläddra efter fler** att öppna den **Välj datakälla** dialogrutan.   
    2. Välj den **+NewSqlServerConnection.odc** filen och välj sedan **öppna** att öppna den **Dataanslutningsguiden**.

    ![Ny anslutning](media/sql-database-connect-excel/new-connection.png)

2. I den **Dataanslutningsguiden**, ange servernamnet och autentiseringsuppgifterna för SQL-databas. Välj **Nästa**. 
    1. Markera den databas som innehåller dina data från listrutan. 
    2. Välj den tabell eller vy som du är intresserad av. Vi valde vGetAllCategories.
    3. Välj **Nästa**. 

    ![Guiden](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Välj plats för din fil på **filnamn**, och **eget namn** på nästa skärm i guiden. Du kan också välja att spara lösenordet i filen, även om det här kan utsätta dina data till obehöriga. Välj **Slutför** när redo. 

    ![Spara dataanslutningen](media/sql-database-connect-excel/save-data-connection.png)

4. Välj hur du vill importera dina data. Vi valde att göra en pivottabell. Du kan också ändra egenskaperna för anslutningen väljer **egenskaper**. Välj **OK** när redo. Om du inte har valt att spara lösenordet med filen, sedan uppmanas du att ange dina autentiseringsuppgifter. 

    ![Importera Data](media/sql-database-connect-excel/import-data2.png)

5. Kontrollera att den nya anslutningen har sparats genom att expandera den **Data** fliken och markera **befintliga anslutningar**. 

    ![Befintlig anslutning](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [Ansluter till SQL Database med SQL Server Management Studio](sql-database-connect-query-ssms.md) för avancerade frågor och analys.
* Lär dig mer om fördelarna med [elastiska pooler](sql-database-elastic-pool.md).
* Lär dig hur du [skapar en webbapp som ansluter till SQL Database på serverdelen](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).


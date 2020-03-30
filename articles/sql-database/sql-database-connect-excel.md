---
title: Ansluta Excel till en enda databas
description: Lär dig hur du ansluter Microsoft Excel till en enda databas i Azure SQL-databas. Importera data till Excel för rapportering och dataundersökning.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: de5f23bf1e8acd8a5fcd0cf8e1526f88667800c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827120"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Ansluta Excel till en enda databas i Azure SQL-databas och skapa en rapport

Anslut Excel till en enda databas i Azure SQL Database och importera data och skapa tabeller och diagram baserat på värden i databasen. I de här självstudierna kommer du att ställa in anslutningen mellan Excel och en databastabell, spara filen som lagrar data och anslutningsinformationen för Excel och sedan skapa ett pivotdiagram från databasvärdena.

Du behöver en enda databas innan du börjar. Om du inte har någon läser du [Skapa en enda databas](sql-database-single-database-get-started.md) och Skapa [IP-brandvägg på servernivå](sql-database-server-level-firewall-rule.md) för att få igång en enda databas med exempeldata igång på några minuter.

I den här artikeln ska du importera exempeldata till Excel från den artikeln, men du kan följa liknande steg med dina egna data.

Du kommer också behöva en kopia av Excel. Den här artikeln använder [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Ansluta Excel till en SQL-databas och läsa in data

1. För att ansluta Excel till SQL-databasen, öppnar du Excel och skapar en ny arbetsbok, eller öppnar en befintlig Excel-arbetsbok.
2. I menyraden högst upp på sidan väljer du fliken **Data,** väljer **Hämta data**, väljer Från Azure och väljer sedan Från Azure **SQL Database**. 

   ![Välj datakälla: Anslut Excel till SQL-databas.](./media/sql-database-connect-excel/excel_data_source.png)

   Dataanslutningsguiden öppnas.
3. I dialogrutan **Anslut till databasserver**, skriver du **Servernamnet** för den SQL Database du vill ansluta till i formatet <*servernamn*>**. database.windows.net**. Till exempel **msftestserver.database.windows.net**. Du kan också ange namnet på databasen. Välj **OK** för att öppna autentiseringsfönstret. 

   ![Dialogrutan Anslut till databasserver](media/sql-database-connect-excel/server-name.png)

4. I dialogrutan **SQL Server Database** väljer du **Databas** till vänster och anger sedan i **ditt användarnamn** och **lösenord** för den SQL Database-server som du vill ansluta till. Välj **Anslut** för att öppna **Navigator**. 

   ![Ange servernamn och inloggningsuppgifter](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > Beroende på din nätverksmiljö, är det möjligt att du inte kan ansluta, eller så kan du tappa anslutningen om SQL Database-servern inte tillåter trafik från din klient-IP-adress. Gå till [Azure-portalen](https://portal.azure.com/), klicka på SQL-servrar, klicka på din server, klicka på brandvägg under inställningar och lägg till din klient-IP-adress. Se [Så här konfigurerar du brandväggsinställningar](sql-database-configure-firewall-settings.md) för mer information.

5. Välj den databas som du vill arbeta med i listan i **Navigator,** markera de tabeller eller vyer som du vill arbeta med (vi valde **vGetAllCategories**) och välj sedan **Läs in** för att flytta data från databasen till Excel-kalkylbladet.

    ![Välj en databas och en tabell.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importera data till Excel och skapa ett pivotdiagram

Nu när du har upprättat anslutningen har du flera olika alternativ när du läser in data. Följande steg skapar till exempel ett pivotdiagram baserat på de data som finns i SQL Database. 

1. Följ stegen i föregående avsnitt, men den här **Load**gången väljer du **Läs in i** listrutan Ladda till i listrutan **Belastning.**
2. Välj sedan hur du vill visa dessa data i arbetsboken. Vi valde **PivotChart**. Du kan också välja att skapa ett **Nytt kalkylblad** eller **Lägg till den här datan i en Datamodell**. Mer information om datamodeller finns i [Skapa en datamodell i Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Välja dataformat i Excel](./media/sql-database-connect-excel/import-data.png)

    Kalkylbladet har nu en tom pivottabell och diagram.
3. Under **PivotTable-fält** väljer du alla kryssrutor för fälten du vill visa.

    ![Konfigurera databasrapporten.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Om du vill ansluta andra Excel-arbetsböcker och kalkylblad till databasen markerar du fliken **Data** och väljer **Senaste källor** för att starta dialogrutan **Senaste källor.** Därifrån väljer du den anslutning som du skapade i listan och klickar sedan på **Öppna**.
> ![Dialogrutan Senaste källor](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Skapa en permanent anslutning med ODC-fil

Om du vill spara anslutningsinformationen permanent kan du skapa en ODC-fil och göra den här anslutningen till ett valbart alternativ i dialogrutan **Befintliga anslutningar.** 

1. Markera fliken **Data** högst upp på sidan och välj sedan **Befintliga anslutningar** för att starta dialogrutan Befintliga **anslutningar.** 
   1. Välj **Bläddra efter mer om** du vill öppna dialogrutan Välj **datakälla.**   
   2. Markera filen **+NewSqlServerConnection.odc** och välj sedan **Öppna** för att öppna **guiden Dataanslutning**.

      ![Dialogrutan Ny anslutning](media/sql-database-connect-excel/new-connection.png)

2. Skriv in servernamnet och SQL Database-autentiseringsuppgifterna i **guiden Dataanslutning.** Välj **Nästa**. 
   1. Välj den databas som innehåller dina data i listrutan. 
   2. Välj den tabell eller vy du är intresserad av. Vi valde vGetAllCategories.
   3. Välj **Nästa**. 

      ![Guiden Dataanslutning](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Välj platsen för filen, **filnamnet**och det **egna namnet** på nästa skärm i guiden Dataanslutning. Du kan också välja att spara lösenordet i filen, men det kan potentiellt utsätta dina data för oönskad åtkomst. Välj **Slutför** när du är klar. 

    ![Spara dataanslutning](media/sql-database-connect-excel/save-data-connection.png)

4. Välj hur du vill importera dina data. Vi valde att göra en pivottabell. Du kan också ändra anslutningens egenskaper genom att välja **Egenskaper**. Välj **OK** när du är klar. Om du inte har valt att spara lösenordet med filen uppmanas du att ange dina autentiseringsuppgifter. 

    ![Importera data](media/sql-database-connect-excel/import-data2.png)

5. Kontrollera att den nya anslutningen har sparats genom att expandera fliken **Data** och välja **Befintliga anslutningar**. 

    ![Befintlig anslutning](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [Ansluter till SQL Database med SQL Server Management Studio](sql-database-connect-query-ssms.md) för avancerade frågor och analys.
* Lär dig mer om fördelarna med [elastiska pooler](sql-database-elastic-pool.md).
* Lär dig hur du [skapar en webbapp som ansluter till SQL Database på serverdelen](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).

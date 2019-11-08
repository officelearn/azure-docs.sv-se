---
title: Ansluta Excel till en enskild databas
description: Lär dig hur du ansluter Microsoft Excel till en enda databas i Azure SQL Database. Importera data till Excel för rapportering och dataundersökning.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827120"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Anslut Excel till en enda databas i Azure SQL Database och skapa en rapport

Anslut Excel till en enda databas i Azure SQL Database och importera data och skapa tabeller och diagram baserat på värden i databasen. I de här självstudierna kommer du att ställa in anslutningen mellan Excel och en databastabell, spara filen som lagrar data och anslutningsinformationen för Excel och sedan skapa ett pivotdiagram från databasvärdena.

Du behöver en enda databas innan du börjar. Om du inte har någon kan du läsa [skapa en enskild databas](sql-database-single-database-get-started.md) och [skapa IP-brandvägg på server nivå](sql-database-server-level-firewall-rule.md) för att hämta en enda databas med exempel data igång på några få minuter.

I den här artikeln ska du importera exempel data till Excel från den artikeln, men du kan följa liknande steg med dina egna data.

Du kommer också behöva en kopia av Excel. Den här artikeln använder [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Ansluta Excel till en SQL-databas och läsa in data

1. För att ansluta Excel till SQL-databasen, öppnar du Excel och skapar en ny arbetsbok, eller öppnar en befintlig Excel-arbetsbok.
2. Välj fliken **data** på Meny raden överst på sidan, Välj **Hämta data**, Välj från Azure och välj sedan **från Azure SQL Database**. 

   ![Välj datakälla: Anslut Excel till SQL-databas.](./media/sql-database-connect-excel/excel_data_source.png)

   Dataanslutningsguiden öppnas.
3. I dialogrutan **Anslut till databasserver**, skriver du **Servernamnet** för den SQL Database du vill ansluta till i formatet <*servernamn*> **. database.windows.net**. Till exempel **msftestserver.Database.Windows.net**. Du kan också ange namnet på din databas. Välj **OK** för att öppna fönstret autentiseringsuppgifter. 

   ![Dialog rutan Anslut till databas server](media/sql-database-connect-excel/server-name.png)

4. I dialog rutan **SQL Server databas** väljer du **databas** till vänster och anger sedan **användar namn** och **lösen ord** för den SQL Database-Server som du vill ansluta till. Välj **Anslut** för att öppna **navigatören**. 

   ![Ange servernamn och inloggningsuppgifter](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > Beroende på din nätverksmiljö, är det möjligt att du inte kan ansluta, eller så kan du tappa anslutningen om SQL Database-servern inte tillåter trafik från din klient-IP-adress. Gå till [Azure-portalen](https://portal.azure.com/), klicka på SQL-servrar, klicka på din server, klicka på brandvägg under inställningar och lägg till din klient-IP-adress. Se [Så här konfigurerar du brandväggsinställningar](sql-database-configure-firewall-settings.md) för mer information.

5. I **navigerings**fönstret väljer du den databas som du vill arbeta med i listan, väljer de tabeller eller vyer som du vill arbeta med (vi valde **vGetAllCategories**) och väljer sedan **load** för att flytta data från databasen till ditt Excel-kalkylblad.

    ![Välj en databas och en tabell.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importera data till Excel och skapa ett pivotdiagram

Nu när du har upprättat anslutningen har du flera olika alternativ för hur du läser in data. Följande steg skapar till exempel ett pivot-diagram baserat på de data som finns i SQL Database. 

1. Följ stegen i föregående avsnitt, men i stället för att välja **Läs**in väljer **du Läs in till** **från List rutan för inläsning.**
2. Välj sedan hur du vill visa data i din arbets bok. Vi valde **PivotChart**. Du kan också välja att skapa ett **Nytt kalkylblad** eller **Lägg till den här datan i en Datamodell**. Mer information om datamodeller finns i [Skapa en datamodell i Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![Välja dataformat i Excel](./media/sql-database-connect-excel/import-data.png)

    Kalkylbladet har nu en tom pivottabell och diagram.
3. Under **PivotTable-fält** väljer du alla kryssrutor för fälten du vill visa.

    ![Konfigurera databasrapporten.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Om du vill ansluta andra Excel-arbetsböcker och kalkyl blad till databasen väljer du fliken **data** och väljer sedan **senaste källor** för att starta dialog rutan **senaste källor** . Därifrån väljer du den anslutning som du skapade i listan och klickar sedan på **Öppna**.
> ![dialog rutan senaste källor](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Skapa en permanent anslutning med hjälp av. ODC-fil

Om du vill spara anslutnings informationen permanent kan du skapa en. ODC-fil och göra anslutningen till ett valbart alternativ i dialog rutan **befintliga anslutningar** . 

1. I meny raden överst på sidan väljer du fliken **data** och väljer sedan **befintliga anslutningar** för att starta dialog rutan **befintliga anslutningar** . 
   1. Välj **Bläddra för mer** för att öppna dialog rutan **Välj data källa** .   
   2. Välj filen **+ NewSqlServerConnection. ODC** och välj sedan **Öppna** för att öppna **guiden data anslutning**.

      ![Dialog rutan ny anslutning](media/sql-database-connect-excel/new-connection.png)

2. I **guiden data anslutning**anger du Server namnet och dina SQL Database autentiseringsuppgifter. Välj **Nästa**. 
   1. Välj den databas som innehåller dina data från List rutan. 
   2. Välj den tabell eller vy som du är intresse rad av. Vi valde vGetAllCategories.
   3. Välj **Nästa**. 

      ![Guiden data anslutning](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Välj platsen för filen, **fil namnet**och det **egna namnet** på nästa skärm i guiden data anslutning. Du kan också välja att spara lösen ordet i filen, men det kan eventuellt exponera dina data till oönskad åtkomst. Välj **Slutför** när du är klar. 

    ![Spara data anslutning](media/sql-database-connect-excel/save-data-connection.png)

4. Välj hur du vill importera dina data. Vi valde att göra en pivottabell. Du kan också ändra egenskaperna för anslutningen genom att välja **Egenskaper**. Välj **OK** när du är klar. Om du inte väljer att spara lösen ordet med filen uppmanas du att ange dina autentiseringsuppgifter. 

    ![Importera data](media/sql-database-connect-excel/import-data2.png)

5. Kontrol lera att din nya anslutning har sparats genom att expandera fliken **data** och välja **befintliga anslutningar**. 

    ![Befintlig anslutning](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [Ansluter till SQL Database med SQL Server Management Studio](sql-database-connect-query-ssms.md) för avancerade frågor och analys.
* Lär dig mer om fördelarna med [elastiska pooler](sql-database-elastic-pool.md).
* Lär dig hur du [skapar en webbapp som ansluter till SQL Database på serverdelen](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).

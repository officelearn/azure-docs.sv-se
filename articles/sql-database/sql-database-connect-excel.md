---
title: Anslut Excel till SQL Database | Microsoft Docs
description: Lär dig hur man ansluter Microsoft Excel till en Azure SQL-databas i molnet. Importera data till Excel för rapportering och dataundersökning.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
manager: craigg
ms.date: 03/10/2017
ms.openlocfilehash: 8750552499a5112b1a46b2cb4929c029d5e7e3a0
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063837"
---
# <a name="connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Ansluta Excel till en Azure SQL-databas och skapa en rapport

Ansluta Excel till en SQL-databas i molnet och importera data och skapa tabeller och diagram baserat på värdena i databasen. I de här självstudierna kommer du att ställa in anslutningen mellan Excel och en databastabell, spara filen som lagrar data och anslutningsinformationen för Excel och sedan skapa ett pivotdiagram från databasvärdena.

Du behöver en SQL-databas i Azure innan du börjar. Om du inte har någon, kan du se [Skapa din första SQL-databas](sql-database-get-started-portal.md) för att starta en databas med exempeldata på några minuter. I den här artikeln ska du importera exempeldata till Excel från den här artikeln, men du kan följa liknande steg med dina egna data.

Du kommer också behöva en kopia av Excel. Den här artikeln använder [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Ansluta Excel till en SQL-databas och läsa in data
1. För att ansluta Excel till SQL-databasen, öppnar du Excel och skapar en ny arbetsbok, eller öppnar en befintlig Excel-arbetsbok.
2. I menyraden överst på sidan väljer du den **Data** fliken **hämta Data**, Välj från Azure och välj sedan **från Azure SQL Database**. 
   
   ![Välj datakälla: Anslut Excel till SQL-databas.](./media/sql-database-connect-excel/excel_data_source.png)
   
   Dataanslutningsguiden öppnas.
3. I dialogrutan **Anslut till databasserver**, skriver du **Servernamnet** för den SQL Database du vill ansluta till i formatet <*servernamn*>**. database.windows.net**. Till exempel **msftestserver.database.windows.net**. Alternativt kan du ange namnet på din databas. Välj **OK** att öppna fönstret autentiseringsuppgifter. 

   ![Server-name.png](media/sql-database-connect-excel/server-name.png)

1. I den **SQL Server-databas** dialogrutan **databasen** till vänster sida och sedan ange i din **användarnamn** och **lösenord** för den SQL database-server du vill ansluta till. Välj **Connect** att öppna den **Navigator**. 

  ![Ange servernamn och inloggningsuppgifter](./media/sql-database-connect-excel/connect-to-server.png)
   
  > [!TIP]
  > Beroende på din nätverksmiljö, är det möjligt att du inte kan ansluta, eller så kan du tappa anslutningen om SQL Database-servern inte tillåter trafik från din klient-IP-adress. Gå till [Azure-portalen](https://portal.azure.com/), klicka på SQL-servrar, klicka på din server, klicka på brandvägg under inställningar och lägg till din klient-IP-adress. Se [Så här konfigurerar du brandväggsinställningar](sql-database-configure-firewall-settings.md) för mer information.
   
   
5. I den **Navigator**, Välj den databas som du vill arbeta med i listan, Välj de tabeller eller vyer som du vill arbeta med (vi valde **vGetAllCategories**), och välj sedan **belastningen**att flytta data från dina SQL Azure-databas till din excel-kalkylblad.
   
    ![Välj en databas och en tabell.](./media/sql-database-connect-excel/select-database-and-table.png)
   

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importera data till Excel och skapa ett pivotdiagram
Nu när du har skapat anslutningen, har du flera olika alternativ för hur du läser in data. Följande steg kan till exempel skapa ett pivotdiagram baserat på data i din SQL-databas. 

1. Följer du stegen i föregående avsnitt, men den här gången, istället för att välja **belastningen**väljer **in till** från den **belastningen** utskriftsjobb.
2. Välj sedan hur du vill visa data i din arbetsbok. Vi valde **PivotChart**. Du kan också välja att skapa ett **Nytt kalkylblad** eller **Lägg till den här datan i en Datamodell**. Mer information om datamodeller finns i [Skapa en datamodell i Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 
   
    ![Välja dataformat i Excel](./media/sql-database-connect-excel/import-data.png)
   
    Kalkylbladet har nu en tom pivottabell och diagram.
2. Under **PivotTable-fält** väljer du alla kryssrutor för fälten du vill visa.
   
    ![Konfigurera databasrapporten.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Om du vill ansluta andra Excel-arbetsböcker och kalkylblad till databasen, väljer du den **Data** och sedan **senast använda källor** att starta den **senast använda källor** dialogrutan. Därifrån väljer du den anslutning du skapade i listan och klicka sedan på **öppna**.
> ![Senaste anslutningar](media/sql-database-connect-excel/recent-connections.png)
 
## <a name="create-a-permanent-connection-using-odc-file"></a>Skapa en permanent anslutning med ODC-fil
Om du vill spara anslutningsinformationen permanent kan du skapa en ODC-fil och göra den här anslutningen ett valbart alternativ inom den **befintliga anslutningar** dialogrutan. 

1. I menyraden överst på sidan väljer du den **Data** fliken och välj sedan **befintliga anslutningar** att starta den **befintliga anslutningar** dialogrutan. 
    1. Välj **äddra** att öppna den **Välj datakälla** dialogrutan.   
    2. Välj den **+NewSqlServerConnection.odc** filen och välj sedan **öppna** att öppna den **Dataanslutningsguiden**.

    ![Ny anslutning](media/sql-database-connect-excel/new-connection.png)

2. I den **Dataanslutningsguiden**, anger du namnet på servern och dina autentiseringsuppgifter för SQL-databas. Välj **Nästa**. 
    1. Välj den databas som innehåller dina data från listrutan. 
    2. Välj tabellen eller vyn som du är intresserad av. Vi valde vGetAllCategories.
    3. Välj **Nästa**. 

    ![Dataanslutningsguiden](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Välj platsen för din fil på **filnamn**, och **eget namn** på nästa skärm i guiden. Du kan också välja att spara lösenordet i filen, även om detta kan potentiellt exponera dina data för oönskad åtkomst. Välj **Slutför** när redo. 

    ![Spara dataanslutning](media/sql-database-connect-excel/save-data-connection.png)

4. Välj hur du vill importera dina data. Vi valde att göra en pivottabell. Du kan också ändra egenskaperna för anslutningen genom att välja **egenskaper**. Välj **OK** när redo. Om du inte har valt att spara lösenordet med filen, sedan uppmanas du att ange dina autentiseringsuppgifter. 

    ![Importera Data](media/sql-database-connect-excel/import-data2.png)

5. Kontrollera att den nya anslutningen har sparats genom att expandera den **Data** fliken och välja **befintliga anslutningar**. 

    ![Befintlig anslutning](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [Ansluter till SQL Database med SQL Server Management Studio](sql-database-connect-query-ssms.md) för avancerade frågor och analys.
* Lär dig mer om fördelarna med [elastiska pooler](sql-database-elastic-pool.md).
* Lär dig hur du [skapar en webbapp som ansluter till SQL Database på serverdelen](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).


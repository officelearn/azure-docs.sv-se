---
title: Anslut Excel till SQL Database | Microsoft Docs
description: "Lär dig hur man ansluter Microsoft Excel till en Azure SQL-databas i molnet. Importera data till Excel för rapportering och dataundersökning."
services: sql-database
keywords: ansluta excel till sql, importera data till excel
documentationcenter: 
author: joseidz
manager: jhubbard
editor: 
ms.assetid: 906924bc-2707-48d3-bac6-397976a0409d
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: jhubbard
ms.openlocfilehash: 34ff5c479cfcf1e861a82205eef93dfee01cb4a2
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="connect-excel-to-an-azure-sql-database-and-create-a-report"></a>Anslut Excel till en Azure SQL database och skapa en rapport

Anslut Excel till en SQL-databas i molnet och importera data och skapa tabeller och diagram baserat på värdena i databasen. I de här självstudierna kommer du att ställa in anslutningen mellan Excel och en databastabell, spara filen som lagrar data och anslutningsinformationen för Excel och sedan skapa ett pivotdiagram från databasvärdena.

Du behöver en SQL-databas i Azure innan du börjar. Om du inte har någon, kan du se [Skapa din första SQL-databas](sql-database-get-started-portal.md) för att starta en databas med exempeldata på några minuter. I den här artikeln, ska du importera exempeldata till Excel från artikeln, men du kan följa liknande steg med dina egna data.

Du kommer också behöva en kopia av Excel. Den här artikeln använder [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-create-an-odc-file"></a>Anslut Excel till en SQL-databas och skapa en odc-fil
1. För att ansluta Excel till SQL-databasen, öppnar du Excel och skapar en ny arbetsbok, eller öppnar en befintlig Excel-arbetsbok.
2. I menyraden överst på sidan, klickar du på **Data**, klickar på **Från andra källor** och klickar sedan på **Från SQL Server**.
   
   ![Välj datakälla: Anslut Excel till SQL-databas.](./media/sql-database-connect-excel/excel_data_source.png)
   
   Dataanslutningsguiden öppnas.
3. I dialogrutan **Anslut till databasserver**, skriver du **Servernamnet** för den SQL Database du vill ansluta till i formatet <*servernamn*>**. database.windows.net**. Till exempel **adworkserver.database.windows.net**.
4. Under **Inloggningsuppgifter**, klickar du på **Använd följande användarnamn och lösenord** och fyller i det **Användarnamn** och **Lösenord** du ställde in för SQL Database-servern när du skapade den och klickar sedan på **Nästa**.
   
   ![Ange servernamn och inloggningsuppgifter](./media/sql-database-connect-excel/connect-to-server.png)
   
   > [!TIP]
   > Beroende på din nätverksmiljö, är det möjligt att du inte kan ansluta, eller så kan du tappa anslutningen om SQL Database-servern inte tillåter trafik från din klient-IP-adress. Gå till [Azure-portalen](https://portal.azure.com/), klicka på SQL-servrar, klicka på din server, klicka på brandvägg under inställningar och lägg till din klient-IP-adress. Se [Så här konfigurerar du brandväggsinställningar](sql-database-configure-firewall-settings.md) för mer information.
   > 
   > 
5. I dialogen **Välj databas och tabell** väljer du den databas du vill arbeta med från listan och klickar sedan på de tabeller eller vyer som du vill arbeta med (vi valde **vGetAllCategories**) och klickar sedan på **Nästa**.
   
    ![Välj en databas och en tabell.](./media/sql-database-connect-excel/select-database-and-table.png)
   
    Dialogrutan **Spara dataanslutningsfil och slutför** öppnas. Där anger du information om Office-databasanslutningen (*.odc)-filen som Excel använder sig av. Du kan lämna standardvärdena eller anpassa dina val.
6. Du kan lämna standardvärdena, men lägg speciellt märke till **Filnamn**. En **Beskrivning**, ett **Eget namn**, och **Sökord** hjälper dig och andra användare att komma ihåg vad du ansluter till och hitta anslutningen. Klicka på **Försök alltid använda den här filen för datauppdateringar** om du vill att anslutningsinformation lagras i filen så att den kan uppdatera när du ansluter till den och klicka sedan på **Slutför**.
   
    ![Spara en odc-fil](./media/sql-database-connect-excel/save-odc-file.png)
   
    Dialogrutan **Importera data** visas.

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importera data till Excel och skapa ett pivotdiagram
Nu när du har etablerat anslutningen och skapat filen med data och anslutningsinformation, är du redo att börja importera data.

1. I dialogen **Importera data**, klickar du på alternativet som du vill ha för att presentera dina data i kalkylbladet och klickar sedan på **OK**. Vi valde **PivotChart**. Du kan också välja att skapa ett **Nytt kalkylblad** eller **Lägg till den här datan i en Datamodell**. Mer information om datamodeller finns i [Skapa en datamodell i Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). Klicka på **Egenskaper** för att utforska information om odc-filen som du skapade i föregående steg och för att välja alternativ för datauppdatering.
   
    ![Välja dataformat i Excel](./media/sql-database-connect-excel/import-data.png)
   
    Kalkylbladet har nu en tom pivottabell och diagram.
2. Under **PivotTable-fält** väljer du alla kryssrutor för fälten du vill visa.
   
    ![Konfigurera databasrapporten.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Om du vill ansluta andra Excel-arbetsböcker och kalkylblad till databasen, klickar du på **Data**, klickar på **Anslutningar**, klickar på **Lägg till**, väljer den anslutning som du skapade i listan och klickar sedan på **Öppna**.
> ![Öppna en anslutning från en annan arbetsbok](./media/sql-database-connect-excel/open-from-another-workbook.png)
> 
> 

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [Ansluter till SQL Database med SQL Server Management Studio](sql-database-connect-query-ssms.md) för avancerade frågor och analys.
* Lär dig mer om fördelarna med [elastiska pooler](sql-database-elastic-pool.md).
* Lär dig hur du [skapar en webbapp som ansluter till SQL Database på serverdelen](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).


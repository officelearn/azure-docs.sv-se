---
title: Visualisera SQL Data Warehouse-data med Power BI Microsoft Azure
description: Visualisera SQL Data Warehouse-data med Power BI
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: d7fb89d1-da1d-4788-a111-68d0e3fda799
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f498f4546e8c23f2141d8d30160a360fa0fc2514


---
# <a name="visualize-data-with-power-bi"></a>Visualisera data med Power BI
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

De här självstudierna visar hur du använder Power BI för att ansluta till SQL Data Warehouse och skapa några grundläggande visualiseringar.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>Krav
För att gå igenom de här självstudierna, behöver du:

* En SQL Data Warehouse förinstallerad med AdventureWorksDW-databasen. Om du vill distribuera detta, se [Skapa ett SQL Data Warehouse][Skapa ett SQL Data Warehouse] och välj att läsa in exempeldata. Om du redan har ett Data Warehouse men inte har exempeldata, kan du [läsa in exempeldata manuellt][läsa in exempeldata manuellt].

## <a name="1-connect-to-your-database"></a>1. Ansluta till databasen
Om du vill öppna Power BI och ansluta till databasen AdventureWorksDW:

1. Logga in på [Azure-portalen][Azure-portalen].
2. Klicka på **SQL-databaser** och välj din AdventureWorks SQL Data Warehouse-databas.
   
    ![Hitta din databas][1]
3. Klicka på knappen Öppna i Power BI.
   
    ![Power BI-knappen][2]
4. Du bör nu se SQL Data Warehouse-anslutningssidan som visar webbadressen för databasen. Klicka på nästa.
   
    ![Power BI-anslutning][3]
5. Ange ditt användarnamn och lösenord för Azure SQL-servern så kommer du att anslutas fullständigt till SQL Data Warehouse-databasen.
   
    ![Power BI-inloggning][4]
6. Klicka på AdventureWorksDW-datauppsättningen på det vänstra bladet när du har loggat in på Power BI. Databasen öppnas.
   
    ![Power BI öppna AdventureWorksDW][5]

## <a name="2-create-a-report"></a>2. Skapa en rapport
Du är nu redo att använda Power BI för att analysera dina AdventureWorksDW-exempeldata. För att utföra analysen har AdventureWorksDW en vy som heter AggregateSales. Vyn innehåller några nyckelvärden för att analysera företagets försäljning.

1. Klicka på vyn AggregateSales i högra fältet för att expandera den och skapa en karta över försäljningsbelopp enligt postnummer. Klicka på kolumnerna PostalCode och SalesAmount för att markera dem.
   
    ![Power BI välj AggregateSales][6]
   
    Power BI identifierar automatiskt dessa geografiska data och placerar dem på en karta för dig.
   
    ![Power BI-karta][7]
2. Det här steget skapar ett stapeldiagram som visar försäljningsintäkter efter kund. För att skapa detta går du till den expanderade AggregateSales-vyn. Klicka på fältet SalesAmount. Dra fältet Customer Income till vänster och släpp det i axeln.
   
    ![Power BI välj axel][8]
   
    Vi har flyttat över stapeldiagrammet till vänster.
   
    ![Power BI-stapel][9]
3. Det här steget skapar ett linjediagram som visar försäljning per beställningsdatum. För att skapa detta går du till den expanderade AggregateSales-vyn. Klicka på SalesAmount och OrderDate. I kolumnen Visualiseringar klickar du på Linjediagram-ikonen. Den första ikonen i den andra raden under visualiseringar.
   
    ![Power BI välj linjediagram][10]
   
    Nu har du en rapport som visar tre olika datavisualiseringar.
   
    ![Power BI-linje][11]

Du kan spara ditt arbete när som helst genom att klicka på **Arkiv** och välja **Spara**.

## <a name="next-steps"></a>Nästa steg
Nu när du har värmt upp med exempeldata, kan du se mer information om att [utveckla][utveckla], [ladda][läsa in], eller [migrera][migrera]. Eller ta en titt på [Power BI-webbplatsen][Power BI-webbplatsen].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrerar]: sql-data-warehouse-overview-migrate.md
[utvecklar]: sql-data-warehouse-overview-develop.md
[läser in]: sql-data-warehouse-overview-load.md
[läsa in exempeldata manuellt]: sql-data-warehouse-load-sample-databases.md
[ansluta till SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Skapa ett SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure Portal]: https://portal.azure.com/
[Power BI-webbplatsen]: http://www.powerbi.com/



<!--HONumber=Nov16_HO2-->



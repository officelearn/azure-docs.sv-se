<properties
   pageTitle="Visualisera SQL Data Warehouse-data med Power BI Microsoft Azure"
   description="Visualisera SQL Data Warehouse-data med Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />


# Visualisera data med Power BI

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

De här självstudierna visar hur du använder Power BI för att ansluta till SQL Data Warehouse och skapa några grundläggande visualiseringar.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## Krav

För att gå igenom de här självstudierna, behöver du:

- En SQL Data Warehouse förinstallerad med AdventureWorksDW-databasen. Om du vill distribuera detta, se [Skapa ett SQL Data Warehouse][] och välj att läsa in exempeldata. Om du redan har ett Data Warehouse men inte har exempeldata, kan du [läsa in exempeldata manuellt][].


## 1. Ansluta till databasen

Om du vill öppna Power BI och ansluta till databasen AdventureWorksDW:

1. Logga in på [Azure-portalen][].
2. Klicka på **SQL-databaser** och välj din AdventureWorks SQL Data Warehouse-databas.

    ![Hitta din databas][1]

3. Klicka på knappen Öppna i Power BI.

    ![Power BI-knappen][2]

4. Du bör nu se SQL Data Warehouse-anslutningssidan som visar webbadressen för databasen. Klicka på nästa.

    ![Power BI-anslutning][3]

6. Ange ditt användarnamn och lösenord för Azure SQL-servern så kommer du att anslutas fullständigt till SQL Data Warehouse-databasen.

    ![Power BI-inloggning][4]

7. Klicka på AdventureWorksDW-datauppsättningen på det vänstra bladet när du har loggat in på Power BI. Databasen öppnas.

    ![Power BI öppna AdventureWorksDW][5]



## 2. Skapa en rapport

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

## Nästa steg
Nu när du har värmt upp med exempeldata, kan du se mer information om att [utveckla][], [läsa in][] eller [migrera][]. Eller ta en titt på [Power BI-webbplatsen][].

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
[migrera]: sql-data-warehouse-overview-migrate.md
[utveckla]: sql-data-warehouse-overview-develop.md
[läsa in]: sql-data-warehouse-overview-load.md
[läsa in exempeldata manuellt]: sql-data-warehouse-load-sample-databases.md
[ansluta till SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Skapa ett SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure-portalen]: https://portal.azure.com/
[Power BI-webbplatsen]: http://www.powerbi.com/



<!--HONumber=Sep16_HO3-->



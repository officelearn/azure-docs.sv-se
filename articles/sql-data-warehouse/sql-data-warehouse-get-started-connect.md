<properties
   pageTitle="Anslut till SQL Data Warehouse med Visual Studio | Microsoft Azure"
   description="Kom igång med att ansluta till SQL Data Warehouse och köra några frågor."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/13/2016"
   ms.author="sonyama;barbkess"/>

# Anslut till SQL Data Warehouse med Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)
- [AAD](sql-data-warehouse-get-started-connect-aad-authentication.md)

Den här genomgången visar hur du ansluter till ett Azure SQL Data Warehouse på bara några minuter med hjälp av SQL Server Data Tools-tillägget (SSDT) i Visual Studio. När du väl är ansluten, ska du köra en enkel fråga.

## Förutsättningar

+ AdventureWorksDW exempeldata i SQL Data Warehouse. Se [Skapa ett SQL Data Warehouse][] för att se hur man skapar det.
+ SQL Server Data Tools för Visual Studio Installationsinstruktioner och alternativ finns i [Installera Visual Studio och SSDT][].

## Steg 1: Hitta det fullständigt kvalificerade Azure SQL-servernamnet

Din SQL Data Warehouse-databas är kopplad till en Azure SQL Server. För att ansluta till databasen så behöver du det fullständigt kvalificerade namnet på servern (**servername**.database.windows.net*).

För att hitta det fullständigt kvalificerade servernamnet.

1. Gå till [Azure-portalen][].
2. Klicka på **SQL-databaser** och klicka på den databas du vill ansluta till. Det här exemplet använder exempeldatabasen AdventureWorksDW.
3. Leta upp det fullständiga servernamnet.

    ![Fullständigt servernamn][1]

## Steg 2: Anslut till din SQL Data Warehouse

1. Öppna Visual Studio 2013 eller 2015.
2. Öppna SQL Server Object Explorer. Gör det genom att markera **Visa** > **SQL Server Object Explorer**.

    ![SQL Server Object Explorer][2]

3. Klicka på ikonen **lägg till SQL Server**.

    ![Lägg till SQL Server][3]

4. Fyll i fälten i fönstret anslut till server.

    ![Anslut till server][4]

    - **Servernamn**. Ange det **servernamn** som du identifierade tidigare.
    - **Autentisering**. Välj **SQL Server-autentisering** eller **Active Directory-integrerad autentisering**.
    - **Användarnamn** och **lösenord**. Ange användarnamn och lösenord om du valde SQL Server-autentisering ovan.
    - Klicka på **anslut**.

5. Expandera din Azure SQL-server för att utforska. Du kan se de databaser som är associerade med servern. Expandera AdventureWorksDW för att se tabellerna i din exempeldatabas.

    ![Utforska AdventureWorksDW][5]

## Steg 3: Kör en exempelfråga

När du nu etablerat en anslutning till din databas, är det dags att skriva en fråga.

1. Högerklicka på din databas i SQL Server Object Explorer.

2. Välj **ny fråga**. Ett nytt frågefönster öppnas.

    ![Ny fråga][6]

3. Kopiera den här TSQL-frågan till frågefönstret:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Kör frågan. För att göra det, klickar du på den gröna pilen eller använder följande genväg: `CTRL`+`SHIFT`+`E`.

    ![Kör frågan][7]

5. Titta på frågeresultaten. I det här exemplet har tabellen FactInternetSales 60398 rader.

    ![Frågeresultat][8]

## Nästa steg

Nu när du anslutit och frågat, kan du [visualisera dina data med PowerBI][].

För att konfigurera din miljö för Windows-autentisering, se [Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering][].

<!--Arcticles-->
[Skapa ett SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Installera Visual Studio och SSDT]: sql-data-warehouse-install-visual-studio.md
[Ansluta till SQL Database eller SQL Data Warehouse med Azure Active Directory-autentisering]: ../sql-data-warehouse/sql-data-warehouse-get-started-connect-aad-authentication.md
[visualisera dina data med PowerBI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure-portalen]: https://portal.azure.com

<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png



<!--HONumber=Jun16_HO2-->



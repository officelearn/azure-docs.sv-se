<properties
    pageTitle="Anslut till SQL Database med en C#-fråga | Microsoft Azure"
    description="Skriv ett program i C# för att fråga och ansluta till SQL Database. Information om IP-adresser, anslutningssträngar, säker inloggning och kostnadsfri Visual Studio."
    services="sql-database"
    keywords="c# database query, c# query, connect to database, SQL C#"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="04/25/2016"
    ms.author="annemill"/>


# Anslut till SQL Database med Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Lär dig hur man ansluter till en Azure SQL Database i Visual Studio. 

## Förutsättningar


För att ansluta till SQL Database med Visual Studio, behöver du: 


- Ett Azure-konto och prenumeration. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).


- En **AdventureWorksLT**-demonstrationsdatabas på Azure SQL Database-tjänsten.
 - [Skapa demodatabasen](sql-database-get-started.md) på några minuter.


- Visual Studio 2013 uppdatering 4 (eller senare). Microsoft erbjuder nu Visual Studio Community helt *kostnadsfritt*.
 - [Visual Studio Community, hämta](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Fler alternativ för kostnadsfri Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)
 - Eller låt [steget](#InstallVSForFree) lite längre fram i det här ämnet beskriva hur [Azure Portal](https://portal.azure.com/) hjälper dig att installera Visual Studio.


<a name="InstallVSForFree" id="InstallVSForFree"></a>

&nbsp;

## Steg 1: Installera Visual Studio Community kostnadsfritt


Om du behöver installera Visual Studio, kan du:

- Installera Visual Studio Community kostnadsfritt genom att navigera din webbläsare till produktsidorna för Visual Studio, där du får en kostnadsfri nedladdning och andra alternativ, eller
- Låt [Azure Portal](https://portal.azure.com/) guida dig till hämtningssidan, vilket beskrivs härnäst.


### Visual Studio via Azure Portal


1. Öppna och logga in på [Azure Portal](https://portal.azure.com/), http://portal.azure.com.

2. Klicka på **BLÄDDRA* ALLA** > **SQL-databaser**. Det öppnas ett blad som söker efter databaser.

3. I filter-textrutan högt uppe, skriver du in namnet på **AdventureWorksLT**-databasen.

4. När du ser raden för databasen på din server, klickar du på den. Ett blad öppnas för din databas.

5. Klicka på minimera-kontrollen på alla föregående blad, för bekvämlighetens skull.

6. Klicka på knappen **Öppna i Visual Studio** längst upp på ditt databas-blad. Ett nytt blad om Visual Studio öppnas med länkar till installationsplatser för Visual Studio.

    ![Öppna i Visual Studio-knappen][20-OpenInVisualStudioButton]

7. Klicka på länken **Community (kostnadsfritt)**, eller någon liknande länk. En ny webbsida läggs till.

8. Använd länkarna på den nya webbsidan för att installera Visual Studio.

9. När Visual Studio är installerat, går du till **Öppna i Visual Studio**-bladet och klickar på knappen **Öppna i Visual Studio**. Visual Studio öppnas.

10. På grund av sitt **SQL Server Object Explorer**-fönster, ber Visual Studio dig att fylla i anslutningssträng-fälten i en dialogruta.
 - Väl **SQL Server-autentisering** och inte **Windows-autentisering**.
 - Kom ihåg att ange din **AdventureWorksLT**-databas (**Alternativ** > **Anslutningsegenskaper** i dialogrutan).

11. I **SQL Server Object Explorer**, expanderar du noden för din databas.


## Steg 2: Kör exempelfrågor

Efter att du ansluter till din logiska server, kan du ansluta till en databas och köra en exempelfråga. 

1. I **Object Explorer**, navigerar du till en databas på den server där du har behörighet, som exempeldatabasen **AdventureWorks**.
2. Högerklicka på databasen och välj **Ny fråga**.

    ![Ny fråga. Anslut till SQL Database-server: SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. Kopiera och klistra in följande kod i frågefönstret.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Klicka på **Kör**-knappen.  Följande skärmbild visar en lyckad fråga.

    ![Lyckades. Anslut till SQL Database-server: SVisual Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Nästa steg

[Anslut till SQL Database med .NET (C#)](sql-database-develop-dotnet-simple.md) 


<!-- Image references. -->

[20-OpenInVisualStudioButton]: ./media/sql-database-connect-query/connqry-free-vs-e.png




<!--HONumber=Jun16_HO2-->



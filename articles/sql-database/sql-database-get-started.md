<properties
    pageTitle="SQL Database-självstudier: Skapa en SQL-databas | Microsoft Azure"
    description="Lär dig hur man ställer in en SQL Database-logisk server, en server-brandväggsregel, en SQL-databas, exempeldata, ansluter med klientverktyg, samt hur man konfigurerar användare och databas-brandväggsregler."
    keywords="sql database tutorial, create a sql database"
    services="sql-database"
    documentationCenter=""
    authors="carlrabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="04/14/2016"
    ms.author="carlrab"/>

# SQL Database-självstudier: Skapa en SQL-databas på bara några minuter med hjälp av Azure-portalen

**Enskild databas**

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

I de här självstudierna kommer du att lära dig hur man använder Azure-portalen för att:

- Skapa en logisk SQL Database-server som är värd för SQL-databaser
- Skapa en SQL-databas utan data, med exempeldata eller med data från en säkerhetskopia av en SQL-databas.
- Skapa en brandväggsregel på servernivå för en enskild IP-adress eller för IP-adressintervall.

Använd de här länkarna för att utföra samma åtgärder, antingen med [C#](sql-database-get-started-csharp.md) eller [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-create-new-server-portal.md)]

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-portal.md)]

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## Nästa steg
Nu när du har slutfört de här SQL Database-självstudierna och skapat en databas med lite exempeldata, är du redo att börja använda dina favoritverktyg.

- Om du är bekant med Transact-SQL och SQL Server Management Studio, lär du dig hur man [Ansluter och frågar en SQL-databas med SSMS](sql-database-connect-query-ssms.md).

- Om du känner till Excel, lär du dig hur man [Ansluter till en SQL-databas med Excel](sql-database-connect-excel.md).

- Om du är redo att börja koda, väljer du programmeringsspråk på [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)

- Om du vill flytta dina lokala SQL Server-databaser till Azure, se [Migrera en databas till Azure SQL Database](sql-database-cloud-migrate.md) för mer information.


## Läs mer

[Vad är SQL Database?](sql-database-technical-overview.md)




<!--HONumber=Jun16_HO2-->



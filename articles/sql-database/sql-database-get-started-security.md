<properties
    pageTitle="SQL Database-självstudier: Komma igång med säkerhet"
    description="Lär dig skapa användarkonton för att få åtkomst till och hantera en databas."
    keywords=""
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
    ms.date="05/03/2016"
    ms.author="carlrab"/>

# SQL Database-självstudier: Skapa användarkonton för en SQL-databas för att få åtkomst till och hantera databasen med Azure-portalen

I de här självstudierna kommer du att lära dig hur man använder Azure-portalen för att:

- Ansluta till SQL Database med en huvudsaklig inloggning på servernivå
- Skapa ett användarkonto för SQL Database
- Ge dbo-behörigheter till ett användarkonto för SQL Database inom en användardatabas
- Ansluta till en SQL-databas med ett användarkonto som inte är ett huvudsakligt användardkonto på servernivå. 

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## Nästa steg
Nu när du har slutfört de här SQL Database-självstudierna och skapat ett användarkonto samt gett användarkontot dbo-behörigheter, är du redo att lära dig mer om [SQL Database-säkerhet](sql-database-manage-logins.md).





<!--HONumber=Jun16_HO2-->



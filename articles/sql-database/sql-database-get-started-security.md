<properties
    pageTitle="SQL Database-självstudier: Komma igång med säkerhet"
    description="Lär dig skapa användarkonton för att få åtkomst till och hantera en databas."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="carlrab"/>

# SQL Database-självstudier: Skapa användarkonton för en SQL-databas för att få åtkomst till och hantera en databas


> [AZURE.SELECTOR]
- [Självstudier för att komma igång](sql-database-get-started-security.md)
- [Bevilja åtkomst](sql-database-manage-logins.md)

I de här självstudierna får du lära dig hur du använder SQL Server Management Studio (SSMS) för att:

- Logga in till SQL Database med en huvudsaklig inloggning på servernivå.
- Skapa ett användarkonto för SQL Database.
- Ge den nya databasanvändaren [db_owner-behörigheter](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0).
- Anslut till en SQL-databas med ett användarkonto som inte är ett huvudsakligt användardkonto på servernivå.

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## Nästa steg
Nu när du har slutfört de här SQL Database-självstudierna och skapat ett användarkonto samt gett användarkontot dbo-behörigheter, är du redo att lära dig mer om [SQL Database-säkerhet](sql-database-manage-logins.md).





<!--HONumber=sep16_HO1-->



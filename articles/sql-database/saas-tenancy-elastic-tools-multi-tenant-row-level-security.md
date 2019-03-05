---
title: Appar för flera klienter med RLS och elastiska Databasverktyg | Microsoft Docs
description: Använd elastiska Databasverktyg med säkerhet på radnivå för att skapa ett program med en mycket skalbar datanivå.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 71d2d542d71977f9d8dfe07370dffd7fe508bc92
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314967"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Program för flera innehavare med elastic database-verktyg och säkerhet på radnivå

[Elastiska Databasverktyg](sql-database-elastic-scale-get-started.md) och [rad säkerhet på radnivå (RLS)] [ rls] samarbeta för att aktivera skalning av datanivån för ett program med flera innehavare med Azure SQL Database. Ihop dessa tekniker hjälper dig att skapa ett program som har en hög skalbar datanivå. Datanivån har stöd för flera klientorganisationer fragmenten och använder **ADO.NET SqlClient** eller **Entity Framework**. Mer information finns i [designmönster för SaaS-program för flera klientorganisationer med Azure SQL Database](saas-tenancy-app-design-patterns.md).

- **Elastiska Databasverktyg** möjligt för utvecklare att skala ut datanivån med delningsprinciper som är standard, med hjälp av .NET-bibliotek och mallar för Azure-tjänsten. Hantera shards med hjälp av den [klientbibliotek för elastiska databaser] [ s-d-elastic-database-client-library] hjälper till att automatisera och förenkla infrastrukturen uppgifter som vanligtvis associeras med horisontell partitionering.
- **Säkerhet på radnivå** ger utvecklare möjlighet att på ett säkert sätt lagra data för flera klienter i samma databas. RLS säkerhetsprinciper filtrerar ut rader som inte tillhör den klient som kör en fråga. Centralisera filter logiken i databasen förenklar underhåll och minskar risken för ett säkerhetsfel. Med alternativet att förlita dig på alla klientkod till att höja säkerheten är riskfyllda.

Genom att använda de här funktionerna tillsammans, kan ett program lagra data för flera klienter i samma shard-databas. Det kostar mindre per klient när innehavarna som delar en databas. Ännu kan samma program också erbjuda dess premium-klienter möjlighet att betala för sina egna dedikerade enda klient-fragment. En fördel med enda klient isolering är firmer prestanda garantier. Det finns ingen annan klient som konkurrerar om resurser i en enda klient-databas.

Målet är att använda elastic database-klientbibliotek [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md) API: er för att automatiskt ansluta varje viss klient till rätt fragment-databasen. Endast en shard innehåller viss TenantId värde för den angivna klienten. TenantId är den *shardingnyckel*. När anslutningen har upprättats, säkerställer en säkerhetsprincip för RLS i databasen att viss klient har åtkomst till de datarader som innehåller dess TenantId.

> [!NOTE]
> Klient-ID kan bestå av fler än en kolumn. För enkelhetens är den här diskussionen, förutsätter vi informellt TenantId för en kolumn.

![Blogga app-arkitektur][1]

## <a name="download-the-sample-project"></a>Ladda ned exempelprojektet

### <a name="prerequisites"></a>Förutsättningar

- Använd Visual Studio (2012 eller senare)
- Skapa tre Azure SQL-databaser
- Ladda ned exempelprojektet: [Verktyg för elastisk databas för Azure SQL - fragment för flera innehavare](https://go.microsoft.com/?linkid=9888163)
  - Fyll i information för dina databaser i början av **Program.cs**

Det här projektet utökar det som beskrivs i [elastiska DB-verktyg för Azure SQL - integrering av Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) genom att lägga till stöd för flera innehavare fragment databaser. Projektet bygger ett enkelt konsolprogram för att skapa bloggar och inlägg. Projektet innehåller fyra klienter, samt två databaser för flera innehavare fragment. Den här konfigurationen visas i föregående diagram.

Skapa och kör programmet. Den här körningen startar karthanteraren för elastic database-verktyg och utför följande test:

1. Med Entity Framework och LINQ kan skapa en ny blogg och visar sedan alla bloggar för varje klient
2. Med ADO.NET SqlClient kan visa alla bloggar för en klient
3. Försök att infoga en blogg att verifiera att ett fel inträffar fel klient

Observera att eftersom RLS inte har ännu aktiverats i fragment-databaser, var och en av de här testerna visar ett problem: klienter ska kunna visa bloggar som inte tillhör dem och programmet inte hindras från att infoga en blogg för fel klient. Resten av den här artikeln beskriver hur du löser dessa problem genom att genomdriva klientisolering med RLS. Det finns två steg:

1. **Programnivå**: Ändra programkoden alltid ange den aktuella TenantId i sessionen\_KONTEXT när du har öppnat en anslutning. Exempelprojektet anger TenantId redan det här sättet.
2. **Datanivå**: Skapa en säkerhetsprincip för RLS i varje shard-databasen för att filtrera rader baserat på TenantId lagras i sessionen\_KONTEXT. Skapa en princip för varje shard-databaser, annars rader i flera fragment inte är att filtreras.

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Programnivå: Ange TenantId i sessionen\_KONTEXT

Först ansluta du till en shard-databas med hjälp av databeroende routning API: er av klientbiblioteket för elastiska databaser. Programmet fortfarande tala om för databasen som TenantId med hjälp av anslutningen. TenantId berättar säkerhetsprincip RLS vilka rader som måste ha filtrerats ut. som tillhör andra klienter. Store aktuella TenantId i den [SESSION\_KONTEXT](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) för anslutningen.

Ett alternativ till sessionen\_KONTEXT är att använda [KONTEXT\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Men sessionen\_KONTEXTEN är ett bättre alternativ. SESSIONEN\_KONTEXTEN är enklare att använda NULL returnerar som standard och den har stöd för nyckel / värde-par.

### <a name="entity-framework"></a>Entity Framework

För program som använder Entity Framework, den enklaste metoden är att ange sessionen\_KONTEXTEN i ElasticScaleContext åsidosättningen som beskrivs i [databeroende routning med hjälp av EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Skapa och köra en SqlCommand som anger TenantId i sessionen\_KONTEXT till shardingKey som angetts för anslutningen. Returnera anslutningen asynkrona via databeroende routning. På så sätt kan du bara behöva skriver koden en gång för att ställa in sessionen\_KONTEXT.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

Nu sessionen\_KONTEXTEN ställs automatiskt med den angivna TenantId när ElasticScaleContext har anropats:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

Skapa en funktion för omslutning runt metoden ShardMap.OpenConnectionForKey för program med hjälp av ADO.NET SqlClient. Har omslutningen automatiskt in TenantId i sessionen\_KONTEXT till aktuella TenantId innan det returneras en anslutning. Att se till att den aktuella sessionen\_KONTEXTEN har alltid värdet, bör du bara öppna anslutningar som använder den här wrapper-funktionen.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Datanivå: Skapa princip för säkerhet på radnivå

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Skapa en säkerhetsprincip för att filtrera de rader som har åtkomst till varje klient

Nu när sessionen har angetts av programmet\_KONTEXT med aktuella TenantId innan en säkerhetsprincip för RLS kan filtrera frågor och utelämna rader som har en annan TenantId.

RLS har implementerats i Transact-SQL. En användardefinierad funktion definierar logik för åtkomst och en säkerhetsprincip Binder den här funktionen till valfritt antal tabeller. För det här projektet:

1. Funktionen kontrollerar att programmet är anslutet till databasen och att TenantId lagras i en SESSION\_KONTEXT överensstämmer med TenantId på en viss rad.
    - Programmet är ansluten, i stället för en annan SQL-användare.

2. Ett filterpredikat kan rader som uppfyller TenantId filtret som ska passera för väljer du UPPDATERINGEN och ta bort frågor.
    - Ett BLOCK-predikat förhindrar rader som inte filter från att vara infogad eller uppdaterad.
    - Om sessionen\_KONTEXTEN har inte angetts, returnerar funktionen NULL och inga rader är synlig eller kan infogas.

Om du vill aktivera RLS på alla shards, kör du följande T-SQL med hjälp av Visual Studio (SSDT), SSMS eller PowerShell-skript som ingår i projektet. Eller om du använder [elastiska Databasjobb](sql-database-elastic-jobs-overview.md), kan du automatisera körningen av den här T-SQL i alla shards.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> I ett komplext projekt som du kan behöva lägga till predikatet på hundratals olika tabeller, vilket kan orsaka tedious. Det finns en helper lagrade procedur som automatiskt genererar en säkerhetsprincip och lägger till ett predikat för alla tabeller i ett schema. Mer information finns i blogginlägget vid [gäller säkerhet på radnivå till alla tabeller - helper-skript (blogg)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script).

Nu se klienter endast de rader som hör till dem om du kör exempelprogrammet igen. Dessutom kan kan inte programmet infoga rader som hör till klienter andra än den som är anslutna till den shard-databasen. Appen kan inte också uppdatera TenantId i alla rader som den kan se. Om appen försöker göra något, utlöses en DbUpdateException.

Om du lägger till en ny tabell senare ändra säkerhetsprincipen för att lägga till FILTER och BLOCK-predikat på den nya tabellen.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Lägg till standardbegränsningar för att automatiskt fylla i TenantId för infogningar

Du kan placera en Standardbegränsning i varje tabell för att automatiskt fylla TenantId med det värde som för närvarande lagras i sessionen\_KONTEXT när du infogar rader. Ett exempel visas nedan.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Nu programmet inte behöver ange en TenantId när du infogar rader:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Om du använder standardbegränsningar för ett projekt med Entity Framework, rekommenderar vi att du *inte* innehåller TenantId-kolumnen i datamodellen EF. Den här rekommendationen är eftersom Entity Framework-frågor automatiskt ange standardvärden som åsidosätter standardbegränsningar skapas i T-SQL som använder SESSION\_KONTEXT.
> Om du vill använda standardbegränsningar i exempelprojektet, till exempel bör du ta bort TenantId från DataClasses.cs (och kör Add-migrering i Package Manager-konsolen) och använder T-SQL så att det endast finns på fältet i databastabeller. På så sätt kan EF automatiskt ange felaktiga standardvärden vid infogning av data.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Valfritt) Aktivera en *superanvändare* att få åtkomst till alla rader

Vissa program kanske vill skapa en *superanvändare* vem som kan komma åt alla rader. En superanvändare kan aktivera rapportering för alla klienter på alla shards. Eller en superanvändare kan utföra åtgärder för dela / sammanslå i fragment som flyttar klient rader mellan databaser.

Om du vill aktivera en superanvändare, skapa en ny SQL-användare (`superuser` i det här exemplet) i varje shard-databas. Sedan ändra säkerhetsprincipen med en ny predikat funktion som gör att användaren åtkomst till alla rader. Därefter får en sådan funktion.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Underhåll

- **Att lägga till nya fragmenten**: Kör T-SQL-skript om du vill aktivera RLS på alla nya fragmenten, annars frågor på dessa fragment inte är att filtreras.
- **Att lägga till nya tabeller**: Lägg till ett FILTER och BLOCK-predikat i säkerhetsprincipen på alla shards när en ny tabell skapas. Frågor om den nya tabellen är annars inte filtreras. Det här tillägget kan automatiseras med hjälp av en DDL-utlösare, enligt beskrivningen i [gäller säkerhet på radnivå automatiskt till nya tabeller (blogg)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Sammanfattning

Verktyg för elastiska databaser och säkerhet på radnivå kan finnas tillsammans användas för att skala ut datanivån för ett program med stöd för både flera innehavare och enstaka klientorganisationer fragmenten. Flera klientorganisationer fragmenten kan användas för att lagra data mer effektivt. Den här effektiviteten uttalas där ett stort antal klienter har bara några rader med data. Premium-klienter som har strängare prestanda och isoleringskrav har stöd för enstaka klientorganisationer fragmenten. Mer information finns i [säkerhet på radnivå referens][rls].

## <a name="additional-resources"></a>Ytterligare resurser

- [Vad är en elastisk pool i Azure?](sql-database-elastic-pool.md)
- [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md)
- [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Autentisering i appar med flera klienter, med hjälp av Azure AD och OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Tailspin Surveys-programmet](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Frågor och funktionsförfrågningar

Frågor kan du kontakta oss på den [SQL Database-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Och Lägg till eventuella funktionsbegäranden till den [SQL Database-Feedbackforum](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

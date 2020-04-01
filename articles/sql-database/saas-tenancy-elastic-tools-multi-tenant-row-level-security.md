---
title: Appar med flera innehavare med RLS och elastiska databasverktyg
description: Använd elastiska databasverktyg med säkerhet på radnivå för att skapa ett program med en mycket skalbar datanivå.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 4cf260620d4e907fdb9190a052155fa22f1c7985
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398329"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Program med flera innehavare med elastiska databasverktyg och säkerhet på radnivå

[Elastiska databasverktyg](sql-database-elastic-scale-get-started.md) och [RLS (row-level security)][rls] samarbetar för att göra det möjligt att skala datanivån för ett program med flera innehavare med Azure SQL Database. Tillsammans hjälper dessa tekniker dig att skapa ett program som har en mycket skalbar datanivå. Datanivån stöder shards för flera innehavare och använder **ADO.NET SqlClient** eller **Entity Framework**. Mer information finns i [Designmönster för SaaS-program med flera innehavare med Azure SQL Database](saas-tenancy-app-design-patterns.md).

- **Elastiska databasverktyg** gör det möjligt för utvecklare att skala ut datanivån med standardshardingsmetoder med hjälp av .NET-bibliotek och Azure-tjänstmallar. Hantera shards med hjälp av [elastiska databasklientbiblioteket][s-d-elastic-database-client-library] hjälper till att automatisera och effektivisera många av de infrastrukturuppgifter som vanligtvis associeras med sharding.
- **Med säkerhet på radnivå** kan utvecklare lagra data för flera klienter i samma databas på ett säkert sätt. RLS-säkerhetsprinciper filtrerar bort rader som inte tillhör klienten som kör en fråga. Genom att centralisera filterlogiken i databasen förenklas underhållet och risken för ett säkerhetsfel minskar risken för ett säkerhetsfel. Alternativet att förlita sig på all klientkod för att upprätthålla säkerhet är riskabelt.

Genom att använda dessa funktioner tillsammans kan ett program lagra data för flera klienter i samma fragmentdatabas. Det kostar mindre per klient när klienterna delar en databas. Men samma ansökan kan också erbjuda sina premium hyresgäster möjlighet att betala för sina egna dedikerade en-hyresgäst skärva. En fördel med isolering av en enda klient är fastare prestandagarantier. I en databas med en enda klient finns det ingen annan klient som konkurrerar om resurser.

Målet är att använda databeroende routnings-API:er för elastiskt databasklientbibliotek för att automatiskt ansluta varje given klient till rätt fragmentdatabas. [data-dependent routing](sql-database-elastic-scale-data-dependent-routing.md) Endast en fragment innehåller ett visst TenantId-värde för den angivna klienten. TenantId är *sharding-nyckeln*. När anslutningen har upprättats säkerställer en RLS-säkerhetsprinciper i databasen att den angivna klienten endast kan komma åt de datarader som innehåller dess TenantId.

> [!NOTE]
> Klientidentifieraren kan bestå av mer än en kolumn. För enkelhetens skull är denna diskussion antar vi informellt en enda kolumn TenantId.

![Blogga apparkitektur][1]

## <a name="download-the-sample-project"></a>Ladda ner exempelprojektet

### <a name="prerequisites"></a>Krav

- Använda Visual Studio (2012 eller senare)
- Skapa tre Azure SQL-databaser
- Hämta exempelprojekt: [Elastiska DB-verktyg för Azure SQL - Shards för flera innehavare](https://go.microsoft.com/?linkid=9888163)
  - Fyll i informationen för dina databaser i början av **Program.cs**

Det här projektet utökar det som beskrivs i [Elastiska DB-verktyg för Azure SQL - Entity Framework Integration](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) genom att lägga till stöd för shard-databaser med flera innehavare. Projektet bygger en enkel konsol program för att skapa bloggar och inlägg. Projektet omfattar fyra klienter, plus två sharddatabaser med flera innehavare. Den här konfigurationen illustreras i föregående diagram.

Skapa och kör programmet. Den här körningen startar den elastiska databasverktygens skärva karthanterare och utför följande tester:

1. Använda Entity Framework och LINQ, skapa en ny blogg och visa sedan alla bloggar för varje klient
2. Använda ADO.NET SqlClient, visa alla bloggar för en klient
3. Försök att infoga en blogg för fel klient för att kontrollera att ett fel genereras

Observera att eftersom RLS ännu inte har aktiverats i fragmentdatabaserna, visar var och en av dessa tester ett problem: klienter kan se bloggar som inte tillhör dem, och programmet hindras inte från att infoga en blogg för fel klient. Resten av den här artikeln beskriver hur du löser dessa problem genom att tillämpa klientisolering med RLS. Det finns två steg:

1. **Programnivå**: Ändra programkoden för att alltid ange\_den aktuella TenantId i SESSION CONTEXT när du har öppnat en anslutning. Exempelprojektet anger redan TenantId på det här sättet.
2. **Datanivå**: Skapa en RLS-säkerhetsprincip i varje fragmentdatabas för att\_filtrera rader baserat på den TenantId som lagras i SESSION CONTEXT. Skapa en princip för var och en av dina fragmentdatabaser, annars filtreras inte rader i shards med flera innehavare.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. Programnivå: Ange Klientid\_i SESSION-KONTEXTEN

Först ansluter du till en fragmentdatabas med hjälp av databeroende routnings-API:er för klientbiblioteket för elastiska databaser. Programmet måste fortfarande tala om för databasen vilken TenantId som använder anslutningen. TenantId talar om för RLS-säkerhetsprincipen vilka rader som måste filtreras bort som tillhör andra klienter. Lagra den aktuella TenantId i [SESSION KONTEXT\_](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) för anslutningen.

Ett alternativ\_till SESSION CONTEXT är att använda [CONTEXT\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Men\_SESSION SAMMANHANG är ett bättre alternativ. SESSION\_CONTEXT är lättare att använda, returnerar NULL som standard och stöder nyckelvärdespar.

### <a name="entity-framework"></a>Entity Framework

För program som använder Entity Framework är\_den enklaste metoden att ange SESSION CONTEXT i åsidosättning av ElasticScaleContext som beskrivs i [Databeroende routning med EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Skapa och kör en SqlCommand som\_anger TenantId i SESSION CONTEXT till shardingKey som angetts för anslutningen. Returnera sedan anslutningen som förmedlas via databeroende routning. På så sätt behöver du bara skriva\_kod en gång för att ställa in SESSION CONTEXT.

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

Nu ställs\_SESSION CONTEXT automatiskt in med den angivna TenantId när ElasticScaleContext anropas:

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

Skapa en omslagsfunktion runt metoden ShardMap.OpenConnectionForKey för program som använder ADO.NET SqlClient. Be omslaget automatiskt in TenantId\_i SESSION CONTEXT till den aktuella TenantId innan du returnerar en anslutning. Om du\_vill vara säkra på att SESSION CONTEXT alltid är inställt bör du bara öppna anslutningar med den här omslagsfunktionen.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Datanivå: Skapa säkerhetsprincip på radnivå

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Skapa en säkerhetsprincip för att filtrera de rader som varje klient kan komma åt

Nu när programmet ställer\_in SESSION CONTEXT med den aktuella TenantId innan du frågar, kan en RLS-säkerhetsprincip filtrera frågor och utesluta rader som har en annan TenantId.

RLS implementeras i Transact-SQL. En användardefinierad funktion definierar åtkomstlogiken och en säkerhetsprincip binder den här funktionen till valfritt antal tabeller. För detta projekt:

1. Funktionen verifierar att programmet är anslutet till databasen och att TenantId som lagras i SESSION\_CONTEXT matchar TenantId för en viss rad.
    - Programmet är anslutet i stället för någon annan SQL-användare.

2. Med ett FILTER predikat kan rader som uppfyller TenantId-filtret passera för SELECT-, UPDATE- och DELETE-frågor.
    - Ett BLOCK predikat förhindrar rader som inte gör att filtret blir INSERTed eller UPDATEd.
    - Om\_SESSION CONTEXT inte har ställts in returnerar funktionen NULL och inga rader är synliga eller kan infogas.

Om du vill aktivera RLS på alla shards kör du följande T-SQL med hjälp av Antingen Visual Studio (SSDT), SSMS eller PowerShell-skriptet som ingår i projektet. Eller om du använder [elastiska databasjobb](elastic-jobs-overview.md)kan du automatisera körningen av den här T-SQL-funktionen på alla shards.

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
> I ett komplext projekt kan du behöva lägga till predikatet på hundratals tabeller, vilket kan vara tråkigt. Det finns en lagrad hjälpprocedur som automatiskt genererar en säkerhetsprincip och lägger till ett predikat i alla tabeller i ett schema. Mer information finns i blogginlägget på Tillämpa säkerhet på [radnivå i alla tabeller – hjälpskript (blogg)](https://techcommunity.microsoft.com/t5/sql-server/apply-row-level-security-to-all-tables-helper-script/ba-p/384360).

Om du kör exempelprogrammet igen ser klienter bara rader som tillhör dem. Dessutom kan programmet inte infoga rader som tillhör andra klienter än den som för närvarande är ansluten till fragmentdatabasen. Appen kan inte heller uppdatera TenantId på några rader som den kan se. Om appen försöker göra något av dem höjs ett DbUpdateException.

Om du lägger till en ny tabell senare ändrar du säkerhetsprincipen för att lägga till FILTER- och BLOCK-predikat i den nya tabellen.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Lägga till standardbegränsningar för att automatiskt fylla i TenantId för INSERTs

Du kan placera en standardbegränsning för varje tabell för att automatiskt fylla\_i TenantId med det värde som för närvarande lagras i SESSION CONTEXT när du infogar rader. Ett exempel följer.

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

Nu behöver programmet inte ange en TenantId när rader infogas:

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
> Om du använder standardbegränsningar för ett entitetsramverksprojekt rekommenderar vi att du *INTE* inkluderar kolumnen TenantId i EF-datamodellen. Den här rekommendationen beror på att Entity Framework-frågor automatiskt anger standardvärden som\_åsidosätter standardbegränsningarna som skapats i T-SQL och som använder SESSION CONTEXT.
> Om du till exempel vill använda standardbegränsningar i exempelprojektet bör du ta bort TenantId från DataClasses.cs (och köra tilläggsmigrering i Package Manager-konsolen) och använda T-SQL för att säkerställa att fältet bara finns i databastabellerna. På så sätt anger EF automatiskt felaktiga standardvärden när data infogas.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Valfritt) Aktivera en *superanvändare* för att komma åt alla rader

Vissa program kanske vill skapa en *superanvändare* som kan komma åt alla rader. En superanvändare kan aktivera rapportering över alla klienter på alla shards. Eller en superanvändare kan utföra split-merge-åtgärder på shards som innebär att flytta klientrader mellan databaser.

Om du vill aktivera en superanvändare`superuser` skapar du en ny SQL-användare ( i det här exemplet) i varje fragmentdatabas. Ändra sedan säkerhetsprincipen med en ny predikatfunktion som gör att den här användaren kan komma åt alla rader. En sådan funktion ges härnäst.

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

- **Lägga till nya shards:** Kör T-SQL-skriptet för att aktivera RLS på alla nya shards, annars frågor på dessa shards filtreras inte.
- **Lägga till nya tabeller**: Lägg till ett FILTER- och BLOCK-predikat i säkerhetsprincipen för alla shards när en ny tabell skapas. Annars filtreras inte frågor i den nya tabellen. Det här tillägget kan automatiseras med hjälp av en DDL-utlösare, enligt beskrivningen i [Tillämpa säkerhet på radnivå automatiskt på nyskapade tabeller (blogg).](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx)

## <a name="summary"></a>Sammanfattning

Elastiska databasverktyg och säkerhet på radnivå kan användas tillsammans för att skala ut ett programs datanivå med stöd för shards för både flera innehavare och en enda klient. Shards med flera innehavare kan användas för att lagra data mer effektivt. Denna effektivitet uttalas där ett stort antal klienter bara har några rader med data. Shards med en klient kan stödja premiumklienter som har strängare prestanda- och isoleringskrav. Mer information finns i [Säkerhetsreferens på radnivå][rls].

## <a name="additional-resources"></a>Ytterligare resurser

- [Vad är en elastisk pool i Azure?](sql-database-elastic-pool.md)
- [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md)
- [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Autentisering i appar med flera klienter, med hjälp av Azure AD och OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Tailspin Surveys-programmet](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Frågor och funktionsförfrågningar

För frågor, kontakta oss på [SQL Database forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Och lägga till några funktionsförfrågningar till [SQL Database feedbackforum](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

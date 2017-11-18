---
title: Flera innehavare appar med RLS och elastiska Databasverktyg | Microsoft Docs
description: "Använd elastiska Databasverktyg med säkerhet på radnivå för att skapa ett program med en mycket skalbar datanivå."
metakeywords: azure sql database elastic tools multi tenant row level security rls
services: sql-database
documentationcenter: 
manager: jhubbard
author: tmullaney
ms.assetid: e72d3cfe-e9be-4326-b776-9c6d96c0a18e
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: thmullan;torsteng
ms.openlocfilehash: f874800e8647eac6ed94945d79e904df86207b0f
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Program med flera klienter med elastiska Databasverktyg och säkerhet på radnivå

[Elastisk Databasverktyg](sql-database-elastic-scale-get-started.md) och [på radnivå (RLS) för säkerhet] [ rls] samarbetar för att aktivera skalning datanivå i ett program för flera innehavare med Azure SQL Database. Tillsammans hjälper dessa tekniker dig att skapa ett program som har en mycket skalbar datanivå. Datanivå har stöd för flera innehavare delar och använder **ADO.NET SqlClient** eller **Entity Framework**. Mer information finns i [designmönster för flera innehavare SaaS-program med Azure SQL Database](saas-tenancy-app-design-patterns.md).

- **Elastisk Databasverktyg** ger utvecklare möjligheten att skala ut datanivå med standard horisontell partitionering praxis med hjälp av .NET-bibliotek och mallar för Azure-tjänsten. Hantera shards med hjälp av den [klientbibliotek för elastisk databas] [ s-d-elastic-database-client-library] hjälper till att automatisera och förenkla många av de infrastrukturella uppgifter som vanligtvis är kopplad till horisontell partitionering.
- **Säkerhet för radnivå** gör att utvecklare kan lagra data på ett säkert sätt för flera klienter i samma databas. RLS säkerhetsprinciper filtrerar ut rader som inte tillhör den klient som kör en fråga. Centralisera filter logiken i databasen förenklar du Underhåll och minskar risken för ett säkerhetsfel. Alternativ för att förlita sig på alla klientkod till enfore security är riskfyllda.

Ett program kan lagra data för flera klienter i samma fragment-databas genom att använda dessa funktioner tillsammans. Det kostar mindre per klient när klienterna delar en databas. Ännu kan samma program också erbjuda dess premium-klienter möjlighet att betala för sina egna dedikerade stöd för en innehavare Fragmentera. En fördel med enda klientisolering är firmer prestanda garantier. Det finns någon klientorganisation som konkurrerar om resurser i en enskild klient-databas.

Målet är att använda klientbibliotek för elastisk databas [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md) API: er för varje given klient ansluta automatiskt till rätt Fragmentera-databasen. Endast en Fragmentera innehåller viss TenantId värde för den angivna klienten. TenantId är den *horisontell partitionering nyckeln*. När anslutningen har upprättats, säkerställer en RLS säkerhetsprincip i databasen att viss klient kan komma åt de datarader som innehåller dess TenantId.

> [!NOTE]
> Klient-ID kan bestå av fler än en kolumn. För bekvämlighet är den här diskussionen, förutsätter vi informellt en TenantId för en kolumn.

![Bloggar app-arkitektur][1]

## <a name="download-the-sample-project"></a>Hämta exempelprojektet

### <a name="prerequisites"></a>Krav

- Använda Visual Studio (2012 eller senare) 
- Skapa tre Azure SQL-databaser 
- Hämta exempelprojektet: [elastisk DB-verktyg för Azure SQL - Shards för flera innehavare](http://go.microsoft.com/?linkid=9888163)
  - Fyll i information för dina databaser i början av **Program.cs** 

Det här projektet utökar den som beskrivs i [elastisk DB-verktyg för Azure SQL - Entity Framework-integrering](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) genom att lägga till stöd för flera innehavare Fragmentera databaser. Projektet skapar ett enkelt konsolprogram för att skapa bloggar och tjänster. Projektet innehåller fyra klienter samt två databaser för flera innehavare Fragmentera. Den här konfigurationen visas i föregående diagram. 

Skapa och köra programmet. Det här körs startar elastisk Databasverktyg Fragmentera kartan manager och utför följande test: 

1. Använder Entity Framework och LINQ, skapa en ny blogg och sedan visa alla bloggar för varje klient
2. Med ADO.NET SqlClient kan visa alla bloggar för en klient
3. Försök att infoga en blogg för fel klienten att verifiera att ett fel genereras  

Observera att eftersom RLS inte ännu har aktiverats i Fragmentera databaser, var och en av de här testerna visar att ett problem: klienter ska kunna visa bloggar som inte hör till dem och programmet inte hindras från att lägga till en blogg för innehavaren av fel. Resten av den här artikeln beskriver hur du löser dessa problem genom att genomdriva klientisolering med RLS. Det finns två steg: 

1. **Programmet nivå**: ändra programkoden för att alltid ange den aktuella TenantId i sessionen\_KONTEXT efter att öppna en anslutning. Exempelprojektet anger TenantId redan det här sättet. 
2. **Datanivå**: skapa en säkerhetsprincip för RLS i varje Fragmentera databas för att filtrera rader baserat på TenantId lagras i SESSION\_KONTEXT. Skapa en princip för var och en av dina Fragmentera databaser, annars rader i flera delar vara filtreras inte. 

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. Programmet nivå: Ange TenantId i sessionen\_KONTEXT

Du ansluter till en Fragmentera-databas med hjälp av API: routning data beroende av klientbiblioteket för elastisk databas. Programmet fortfarande tala databasen vilka TenantId använder anslutningen. TenantId visar säkerhetsprincip RLS vilka rader måste filtreras som tillhör andra klienter. Lagrar den aktuella TenantId i den [SESSION\_KONTEXTEN](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) för anslutningen.

Ett alternativ till sessionen\_KONTEXT är att använda [KONTEXTEN\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Men sessionen\_KONTEXT är ett bättre alternativ. SESSIONEN\_KONTEXT är enklare att använda NULL returneras som standard och den stöder nyckel-värdepar.

### <a name="entity-framework"></a>Entity Framework

För program som använder Entity Framework, är det enklaste sättet att sessionen\_KONTEXTEN inom ElasticScaleContext åsidosättningen beskrivs i [Data-beroende routning med EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Skapa och köra en SqlCommand som anger TenantId i sessionen\_KONTEXT för att shardingKey anges för anslutningen. Returnera anslutningen asynkrona via data beroende routning. Det här sättet behöver du bara att skriva kod en gång för att ange sessionen\_KONTEXT. 

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

Nu sessionen\_KONTEXTEN ställs in automatiskt med den angivna TenantId när ElasticScaleContext anropas: 

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

Skapa en wrapper-funktion runt metoden ShardMap.OpenConnectionForKey för program med hjälp av ADO.NET SqlClient. Har wrapper automatiskt in TenantId i sessionen\_KONTEXT för att den aktuella TenantId innan det returneras en anslutning. Att se till att den aktuella sessionen\_SAMMANHANG anges alltid, bör du bara öppna anslutningar som använder den här wrapper-funktionen.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Datanivå: skapa radnivå säkerhetsprincip

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Skapa en säkerhetsprincip för att filtrera de rader som har åtkomst till varje klient

Nu när sessionen har angetts av programmet\_tillsammans med den aktuella TenantId innan en RLS säkerhetsprincip kan filtrera frågor och utelämna rader som har en annan TenantId.  

RLS har implementerats i Transact-SQL. En användardefinierad funktion definierar logik för åtkomst och en säkerhetsprincip Binder den här funktionen till valfritt antal tabeller. För det här projektet:

1. Funktionen verifierar att programmet är ansluten till databasen och att TenantId lagras i en SESSION\_KONTEXTEN matchar TenantId på en viss rad.
    - Programmet är ansluten i stället för en annan SQL-användare.

2. Ett filterpredikat kan rader som uppfyller TenantId filtret som ska passera för SELECT-, UPDATE-, och ta bort frågor.
    - Ett BLOCK-predikat förhindras rader som inte filtret infogad eller uppdaterad.
    - Om sessionen\_KONTEXT har inte angetts, returnerar funktionen NULL och inga rader är synlig eller kan läggas till. 

Om du vill aktivera RLS på alla delar, kör du följande T-SQL med hjälp av Visual Studio (SSDT), SSMS eller PowerShell-skript som ingår i projektet. Eller om du använder [elastiska databasen jobb](sql-database-elastic-jobs-overview.md), kan du automatisera körningen av den här T-SQL i alla delar.

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
> I ett komplext projekt som du kan behöva lägga till predikatet på hundratals olika tabeller, vilket kan vara tråkigt. Det finns en helper lagrade procedur som automatiskt genererar en säkerhetsprincip och lägger till ett predikat för alla tabeller i ett schema. Mer information finns i blogginlägget vid [tillämpas på radnivå säkerhet till alla tabeller - helper-skript (blogg)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Om du kör exempelprogrammet igen finns nu innehavare endast rader som de tillhör. Dessutom kan går inte att programmet infoga rader som hör till innehavare andra än den som för tillfället är anslutna till databasen Fragmentera. Appen kan inte också uppdatera TenantId i några rader som den kan se. Om appen försöker göra något, utlöses en DbUpdateException.

Om du lägger till en ny tabell senare ändra säkerhetsprincip för att lägga till FILTRET och BLOCK-predikat på den nya tabellen.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Lägg till standardbegränsningar för att automatiskt fylla i TenantId för infogningar

Du kan placera en default-begränsning för varje tabell för att automatiskt fylla TenantId med det värde som för närvarande lagras i SESSION\_KONTEXTEN vid infogning av rader. Ett exempel visas nedan. 

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
> Om du använder standardbegränsningar för Entity Framework-projekt, rekommenderar vi att du *inte* innehåller klient-ID-kolumnen i datamodellen EF. Denna rekommendation beror på att Entity Framework frågar automatiskt ange standardvärden som åsidosätter standardbegränsningar skapas i T-SQL som använder sessionen\_KONTEXT.
> Om du vill använda standardbegränsningar i exempelprojektet, exempelvis bör du ta bort TenantId från DataClasses.cs (och kör Add-migrering i Package Manager-konsolen) och använder T-SQL så att det bara finns fältet i databastabeller. Det här sättet EF automatiskt tillhandahåller felaktiga standardvärden vid infogning av data.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Valfritt) Aktivera en *superanvändare* åtkomst till alla rader

Vissa program kanske vill skapa en *superanvändare* vem som kan komma åt alla rader. En superanvändare gick att aktivera rapportering över alla klienter i alla delar. Eller superanvändare kan utföra åtgärder för delade dokument på shards där du flyttar klienten rader mellan databaser.

Om du vill aktivera en superanvändare, skapa en ny SQL-användare (`superuser` i det här exemplet) i varje Fragmentera-databas. Sedan ändra säkerhetsprincipen med en ny predikat funktion som gör att användaren åtkomst till alla rader. En sådan funktion ges nästa.

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

- **Lägga till nya shards**: köra T-SQL-skript för att aktivera RLS på alla nya shards, annars frågor om dessa delar inte att filtreras.
- **Lägga till nya tabeller**: Lägg till ett FILTER och BLOCK-predikat i säkerhetsprincipen på alla delar när en ny tabell skapas. Frågor om den nya tabellen är annars inte filtreras. Det här tillägget kan automatiseras med hjälp av en DDL-utlösare, enligt beskrivningen i [tillämpas på radnivå säkerhet automatiskt till nya tabeller (blogg)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Sammanfattning

Elastisk Databasverktyg och säkerhet på radnivå kan vara användas ihop skalbar datanivå för ett program med stöd för både flera innehavare och stöd för en innehavare delar. Flera innehavare shards kan användas för att lagra data mer effektivt. Den här effektiviteten uttalas där ett stort antal klienter har bara ett fåtal rader med data. Stöd för en innehavare shards har stöd för premium-klienter som har striktare prestanda och isoleringskrav för.  Mer information finns i [säkerhet på radnivå referens][rls].

## <a name="additional-resources"></a>Ytterligare resurser

- [Vad är en Azure elastisk pool?](sql-database-elastic-pool.md)
- [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md)
- [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Autentisering i appar med flera klienter, med hjälp av Azure AD och OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Tailspin Surveys-programmet](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Frågor och Funktionsbegäranden

Om du har frågor kontaktar du oss på den [SQL Database-forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Och Lägg till alla funktionsbegäranden till den [SQL-databas Feedbackforum](https://feedback.azure.com/forums/217321-sql-database/).


<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md


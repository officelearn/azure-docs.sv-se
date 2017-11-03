---
title: "Program med flera klienter med elastiska Databasverktyg och säkerhet på radnivå"
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
ms.date: 05/27/2016
ms.author: thmullan;torsteng
ms.openlocfilehash: 4b4ec29bb53bdce413dadf7cb0751433b9ca686c
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Program med flera klienter med elastiska Databasverktyg och säkerhet på radnivå
[Elastisk Databasverktyg](sql-database-elastic-scale-get-started.md) och [på radnivå (RLS) för säkerhet](https://msdn.microsoft.com/library/dn765131) erbjuder en kraftfull uppsättning funktioner för att effektivt och flexibelt skala datanivå i ett program för flera innehavare med Azure SQL Database. Mer information finns i [designmönster för flera innehavare SaaS-program med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) för mer information. 

Den här artikeln visar hur du kan använda dessa tekniker tillsammans för att skapa ett program med en mycket skalbar datanivå som har stöd för flera innehavare shards, med hjälp av **ADO.NET SqlClient** och/eller **Entity Framework**.  

* **Elastisk Databasverktyg** ger utvecklare möjligheten att skala ut datanivå för ett program via branschstandard horisontell partitionering metoder med en uppsättning .NET-bibliotek och mallar för Azure-tjänsten. Hantera shards med klientbibliotek för elastisk databas kan automatisera och förenkla många av de infrastrukturella uppgifter som vanligtvis är kopplad till horisontell partitionering. 
* **Säkerhet för radnivå** gör att utvecklare kan lagra data för flera klienter i samma databas med hjälp av säkerhetsprinciperna för att filtrera ut rader som inte tillhör den klient som kör en fråga. Centralisera åtkomst logik med RLS i databasen, växer i stället för i programmet förenklar du Underhåll och minskar risken för fel som ett program har codebase. 

Använder de här funktionerna tillsammans, kan ett program dra nytta kostnaden besparingar och effektivitet vinster genom att lagra data för flera klienter i samma fragment-databas. Ett program har fortfarande möjlighet att erbjuda isolerade, enskild klient shards för ”premium-klienter som behöver garantier för strängare prestanda eftersom flera innehavare delar inte kan garantera lika resurs distributionen till klienter på samma gång.  

I elastiska databasen-klientbiblioteket [data beroende routning](sql-database-elastic-scale-data-dependent-routing.md) API: er automatiskt ska ansluta klienter till rätt Fragmentera databasen med sin horisontell partitionering nyckel (vanligtvis en ”TenantId”). När du är ansluten, säkerställer en RLS säkerhetsprincip i databasen att innehavare endast har åtkomst till rader som innehåller sina TenantId. Det förutsätts att alla tabeller som innehåller en kolumn för klient-ID för att ange vilka rader som hör till varje klient. 

![Bloggar app-arkitektur][1]

## <a name="download-the-sample-project"></a>Hämta exempelprojektet
### <a name="prerequisites"></a>Krav
* Använda Visual Studio (2012 eller senare) 
* Skapa tre Azure SQL-databaser 
* Hämta exempelprojektet: [elastisk DB-verktyg för Azure SQL - Shards för flera innehavare](http://go.microsoft.com/?linkid=9888163)
  * Fyll i information för dina databaser i början av **Program.cs** 

Det här projektet utökar den som beskrivs i [elastisk DB-verktyg för Azure SQL - Entity Framework-integrering](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) genom att lägga till stöd för flera innehavare Fragmentera databaser. Den skapar ett enkelt konsolprogram för att skapa bloggar och inlägg, med fyra klienter och två databaser för flera innehavare Fragmentera enligt beskrivningen i föregående diagram. 

Skapa och köra programmet. Detta startar elastisk Databasverktyg Fragmentera kartan manager och kör följande test: 

1. Använder Entity Framework och LINQ, skapa en ny blogg och sedan visa alla bloggar för varje klient
2. Med ADO.NET SqlClient kan visa alla bloggar för en klient
3. Försök att infoga en blogg för fel klienten att verifiera att ett fel genereras  

Observera att eftersom RLS inte ännu har aktiverats i Fragmentera databaser, var och en av de här testerna visar att ett problem: klienter ska kunna visa bloggar som inte hör till dem och programmet inte hindras från att lägga till en blogg för innehavaren av fel. Resten av den här artikeln beskriver hur du löser dessa problem genom att genomdriva klientisolering med RLS. Det finns två steg: 

1. **Programmet nivå**: ändra programkoden för att alltid ange den aktuella TenantId i SESSION_CONTEXT efter öppna en anslutning. Exempelprojektet har gjort det redan. 
2. **Datanivå**: skapa en säkerhetsprincip för RLS i varje Fragmentera databas för att filtrera rader baserat på TenantId lagras i SESSION_CONTEXT. Du måste göra detta för var och en av dina Fragmentera databaser, annars rader i flera innehavare shards inte filtreras. 

## <a name="step-1-application-tier-set-tenantid-in-the-sessioncontext"></a>Steg 1) programmet nivå: Ange TenantId i SESSION_CONTEXT
Efter anslutning till en Fragmentera databas med hjälp av det klientbibliotek för elastisk databas data beroende routning-API: er använder fortfarande programbehov ska berätta för databasen som TenantId anslutningen så att en RLS säkerhetsprincip kan filtrera ut rader som hör till andra klienter. Det rekommenderade sättet att skicka den här informationen är att lagra den aktuella TenantId för anslutningen i den [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx). (Obs: du kan också använda [CONTEXT_INFO](https://msdn.microsoft.com/library/ms180125.aspx), men SESSION_CONTEXT är ett bättre alternativ eftersom det är lättare att använda, returnerar NULL som standard och har stöd för nyckel-värdepar.)

### <a name="entity-framework"></a>Entity Framework
För program som använder Entity Framework, är det enklaste sättet att SESSION_CONTEXT inom ElasticScaleContext åsidosättningen beskrivs i [Data-beroende routning med EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Innan den returnerar anslutningen asynkrona via data beroende routning, skapa och köra en SqlCommand som anger 'TenantId' i SESSION_CONTEXT till shardingKey som angetts för anslutningen. På så sätt kan du endast behöver skriva kod en gång för att ange SESSION_CONTEXT. 

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data-dependent routing. This call opens a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call fails for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
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

Nu ställs SESSION_CONTEXT in automatiskt med den angivna TenantId när ElasticScaleContext anropas: 

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
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
För program som använder ADO.NET SqlClient, är det rekommenderade sättet att skapa ett wrapper-funktionen runt ShardMap.OpenConnectionForKey() som anger automatiskt 'TenantId' i SESSION_CONTEXT till rätt TenantId innan det returneras en anslutning. Du bör endast öppna anslutningar som använder den här wrapper-funktionen för att säkerställa att SESSION_CONTEXT alltid har angetts.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
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

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs are listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="step-2-data-tier-create-row-level-security-policy"></a>Steg 2) Data tjänstnivån: skapa radnivå säkerhetsprincip
### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Skapa en säkerhetsprincip för att filtrera de rader som har åtkomst till varje klient
Nu när programmet har inställningen SESSION_CONTEXT med den aktuella TenantId innan en säkerhetsprincip för RLS filtrera frågor och utelämna rader som har en annan TenantId.  

RLS har implementerats i T-SQL: en användardefinierad funktion definierar logik för åtkomst och en säkerhetsprincip Binder den här funktionen till valfritt antal tabeller. Funktionen verifierar att programmet (i stället för en annan SQL-användare) är anslutet till databasen och att TenantId lagras i SESSION_CONTEXT matchar TenantId på en viss rad för det här projektet. Ett filterpredikat tillåter rader som uppfyller dessa villkor att passera filtret för SELECT-, UPDATE- och DELETE-frågor. och ett block-predikat förhindrar rader som strider mot dessa villkor från att infogad eller uppdaterad. Om SESSION_CONTEXT inte har angetts returnerar NULL och inga rader är synlig eller kan läggas till. 

Om du vill aktivera RLS, kör följande T-SQL på alla delar med Visual Studio (SSDT), SSMS eller PowerShell-skript som ingår i projektet (eller om du använder [elastiska databasen jobb](sql-database-elastic-jobs-overview.md), du kan använda för att automatisera körningen av den här T-SQL på alla shards): 

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult          
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [!TIP]
> Du kan använda en helper lagrade procedur som automatiskt genererar en säkerhetsprincip för att lägga till ett predikat för alla tabeller i ett schema för mer komplexa projekt som behöver lägga till predikatet på hundratals olika tabeller. Mer information finns i [tillämpas på radnivå säkerhet till alla tabeller - helper-skript (blogg)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).  
> 
> 

Om du kör exempelprogrammet igen finns nu innehavare endast rader som de tillhör. Dessutom kan går inte att programmet infoga rader som hör till klienter än som för närvarande ansluten till Fragmentera databasen och det går inte att uppdatera synliga rader om du vill att en annan TenantId. Om programmet försöker göra något, utlöses en DbUpdateException.

Om du lägger till en ny tabell senare ändrar säkerhetsprincipen och Lägg till filter och blockera predikat med den nya tabellen: 

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Lägg till standardbegränsningar för att automatiskt fylla i TenantId för infogningar
Du kan placera en default-begränsning för varje tabell för att automatiskt fylla TenantId med värdet för närvarande lagras i SESSION_CONTEXT vid infogning av rader. Exempel: 

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

Nu programmet inte behöver ange en TenantId när du infogar rader: 

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [!NOTE]
> Om du använder standardbegränsningar för en Entity Framework-projekt, rekommenderas att du inte inkluderar TenantId kolumnen i datamodellen EF. Det beror på att automatiskt ange standardvärden som åsidosätter standardbegränsningar skapas i T-SQL som använder SESSION_CONTEXT i Entity Framework-frågor. Om du vill använda standardbegränsningar i exempelprojektet, exempelvis bör du ta bort TenantId från DataClasses.cs (och kör Add-migrering i Package Manager-konsolen) och använder T-SQL så att det bara finns fältet i databastabeller. Det här sättet EF automatiskt tillhandahåller felaktiga standardvärden vid infogning av data. 
> 
> 

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Valfritt) Aktivera en ”superanvändare” att få åtkomst till alla rader
Vissa program kanske vill skapa en ”superanvändare” som har åtkomst till alla rader, till exempel för att aktivera rapportering över alla klienter i alla delar eller utföra dela/Merge-åtgärder på shards där du flyttar klienten rader mellan databaser. För att möjliggöra detta, bör du skapa en ny SQL-användare (”superanvändare” i det här exemplet) i varje Fragmentera-databasen. Sedan ändra säkerhetsprincipen med en ny predikat funktion som gör att användaren åtkomst till alla rader:

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### <a name="maintenance"></a>Underhåll
* **Lägga till nya shards**: köra T-SQL-skript för att aktivera RLS på alla nya shards, annars frågor om dessa delar inte att filtreras.
* **Lägga till nya tabeller**: lägga till ett filter och block-predikat till säkerhetsprincipen på alla delar när en ny tabell skapas, annars frågor med den nya tabellen inte filtreras. Detta kan automatiseras med hjälp av en DDL-utlösare, enligt beskrivningen i [tillämpas på radnivå säkerhet automatiskt till nya tabeller (blogg)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Sammanfattning
Elastisk Databasverktyg och säkerhet på radnivå kan vara användas ihop skalbar datanivå för ett program med stöd för både flera innehavare och stöd för en innehavare delar. Flera innehavare shards kan användas för att lagra data mer effektivt (särskilt i fall där ett stort antal klienter har bara ett fåtal rader med data) när en klient delar som kan användas för att stöda premium klienter med striktare krav på prestanda och isolering.  Mer information finns i [säkerhet på radnivå referens](https://msdn.microsoft.com/library/dn765131). 

## <a name="additional-resources"></a>Ytterligare resurser
* [Vad är en Azure elastisk pool?](sql-database-elastic-pool.md)
* [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md)
* [Designmönster för SaaS-program med flera klientorganisationer med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Autentisering i appar med flera klienter, med hjälp av Azure AD och OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
* [Tailspin Surveys-programmet](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Frågor och Funktionsbegäranden
För frågor, nå ut till oss på den [SQL Database-forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) och för funktionsbegäranden, lägga till dem i den [SQL-databas Feedbackforum](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->



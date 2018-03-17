---
title: Skala ut en Azure SQL database | Microsoft Docs
description: "Hur du använder ShardMapManager, klientbibliotek för elastisk databas"
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 03/16/2018
ms.author: sstein
ms.openlocfilehash: cf8d4427cddbe6368ac265fe9ecc0f408f7fb1fb
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Skala ut databaser med hanteraren Fragmentera karta
Använda en Fragmentera kartan manager för att enkelt skala ut databaser i SQL Azure. Fragmentera kartan manager är en särskild databas som underhåller globala mappningsinformation om alla shards (databaser) i en Fragmentera. Metadata kan programmet att ansluta till rätt databas baserat på värdet för den **horisontell partitionering nyckeln**. Dessutom kan varje Fragmentera i uppsättningen innehåller mappningar som spårar lokala Fragmentera data (kallas även **shardlets**). 

![Hantering av Fragmentera karta](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Det är viktigt att fragmentera kartan management förstå hur dessa mappningar skapas. Detta görs med hjälp av klassen ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)hittades i den [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md) att hantera Fragmentera maps.  

## <a name="shard-maps-and-shard-mappings"></a>Fragmentera maps och Fragmentera mappningar
Du måste välja vilken typ av Fragmentera kartan för att skapa för varje Fragmentera. Valet är beroende av databasens arkitektur: 

1. En organisation per databas  
2. Flera klienter per databas (två typer):
   1. Lista över-mappning
   2. Mappning av intervallet

En enskild klient-modell, skapa en **lista mappning** Fragmentera kartan. Stöd för en innehavare modellen tilldelar en databas per klient. Detta är en effektiv modell för SaaS-utvecklare som det förenklar hanteringen.

![Lista över-mappning][1]

Modell för flera klienter tilldelas en enskild databas flera innehavare (och du kan distribuera grupper av klienter över flera databaser). Använd den här modellen när du förväntar dig varje innehavare har liten databehov. I den här modellen, tilldela ett antal klienter till en databas med hjälp av **intervallet mappning**. 

![Mappning av intervallet][2]

Eller du kan implementera en databas för flera innehavare modellen med hjälp av en *lista mappning* tilldela flera klienter till en enskild databas. Till exempel DB1 används för att lagra information om klient-ID 1 och 5 och DB2 lagrar data för klienten 7 och 10-klient. 

![Flera klienter på enskild DB][3] 

### <a name="supported-types-for-sharding-keys"></a>Typer som stöds för horisontell partitionering nycklar
Elastisk skalbarhet stöder följande typer som horisontell partitionering nycklar:

| .NET | Java |
| --- | --- |
| heltal |heltal |
| lång |lång |
| GUID |UUID |
| byte  |byte |
| datetime | tidsstämpel |
| TimeSpan | Varaktighet|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Lista och intervallet Fragmentera maps
Fragmentera maps kan konstrueras med **listor över enskilda horisontell partitionering nyckeln värden**, eller så kan de vara konstruerade med **intervallen för horisontell partitionering nyckeln värden**. 

### <a name="list-shard-maps"></a>Lista Fragmentera maps
**Shards** innehåller **shardlets** och mappningen av shardlets till shards underhålls av en Fragmentera karta. En **lista Fragmentera kartan** är en association mellan enskilda nyckelvärden som identifierar shardlets och databaserna som fungerar som delar.  **Visa en lista över mappningar** är explicit och andra viktiga värden kan mappas till samma databas. Till exempel nyckel 1 mappar till en-databas och nyckelvärden 3 och 6 referera databasen B.

| Nyckel | Fragmentera plats |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Intervallet Fragmentera maps
I en **intervallet Fragmentera kartan**, viktiga intervallet anges med ett par **[lågt värde, högt värde)** där den *låg värdet* är den minsta nyckeln i intervallet, och *hög Värdet* är det första värdet som är högre än intervallet. 

Till exempel **[0, 100)** innefattar alla heltal större än eller lika med 0 och mindre än 100. Observera att flera adressintervall peka på samma databas och åtskilda områden som stöds (till exempel [100,200) och [400,600) pekar till databasen C i följande exempel.)

| Nyckel | Fragmentera plats |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Varje tabell som visas ovan är ett grundläggande exempel på en **ShardMap** objekt. Varje rad är ett förenklat exempel på en enskild **PointMapping** (för listan Fragmentera kartan) eller **RangeMapping** (för intervallet Fragmentera kartan) objekt.

## <a name="shard-map-manager"></a>Fragmentera kartan manager
I klientbiblioteket är Fragmentera kartan manager en samling Fragmentera maps. De data som hanteras av en **ShardMapManager** instans sparas på tre platser: 

1. **Globala Fragmentera karta (GSM)**: du anger en databas som fungerar som lagringsplats för alla Fragmentera maps och mappningar. Särskilda tabeller och lagrade procedurer skapas automatiskt för att hantera informationen. Detta är vanligtvis en liten databas och lätt öppnas och ska inte användas för andra behov av programmet. Tabeller är i ett särskilt schema med namnet **__ShardManagement**. 
2. **Lokala Fragmentera karta (LSM)**: alla databaser som du anger ska vara en Fragmentera ändras till att innehålla flera små tabeller och särskilda lagrade procedurer som innehåller och hantera Fragmentera kartan information för att fragmentera. Den här informationen är redundant med informationen i GSM och låter programmet för att verifiera informationen i cachelagrade Fragmentera kartan utan att placera belastning på GSM; programmet använder LSM för att avgöra om en cachelagrad mappning fortfarande är giltigt. Tabeller som motsvarar LSM på varje Fragmentera finns också i schemat **__ShardManagement**.
3. **Cacheminne**: varje instans åtkomst programmet till en **ShardMapManager** objekt upprätthåller en lokal minnescache av dess mappningar. Lagrar den routningsinformation som nyligen har hämtats. 

## <a name="constructing-a-shardmapmanager"></a>Hur du skapar en ShardMapManager
En **ShardMapManager** objektet har skapats med en fabrik ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) mönster. Den **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) metoden tar autentiseringsuppgifter (inklusive servernamnet och databasnamnet hålla GSM) den form av en **ConnectionString** och returnerar en instans av en **ShardMapManager**.  

**Obs:** den **ShardMapManager** instansieras bara en gång per app-domän, inom initieringskoden för ett program. Skapa ytterligare instanser av ShardMapManager i samma programdomän resulterar i ökad minne och CPU-användning av programmet. En **ShardMapManager** kan innehålla valfritt antal Fragmentera maps. Även om en enda Fragmentera karta är tillräcklig för många program, finns det tillfällen när olika uppsättningar av databaser som används för olika schemat eller för unika ändamål. i sådana fall kan det vara bättre att ange flera Fragmentera maps. 

I den här koden, ett program försöker öppna en befintlig **ShardMapManager** med TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) metod. Om objekt som representerar en Global **ShardMapManager** (GSM) inte ännu finns i databasen, klientbiblioteket skapar dem det med hjälp av CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager_factory.createsqlshardmapmanager), [.NET ](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) metoden.

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it. 
ShardMapManager shardMapManager; 
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString, 
                                        ShardMapManagerLoadPolicy.Lazy, 
                                        out shardMapManager); 

if (shardMapManagerExists) 
{ 
    Console.WriteLine("Shard Map Manager already exists");
} 
else
{
    // Create the Shard Map Manager. 
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager"); 

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString, 
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
} 
```

Du kan använda PowerShell för att skapa en ny Fragmentera kartan chef för .NET-version. Ett exempel är tillgänglig [här](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Hämta en RangeShardMap eller ListShardMap
När du har skapat en Fragmentera kartan manager, du kan hämta RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) eller ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807370.aspx)) med hjälp av den TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetrangeshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx)), TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.trygetlistshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)), eller GetShardMap ([ Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager._shard_map_manager.getshardmap), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx)) metoden.

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
} 
```

### <a name="shard-map-administration-credentials"></a>Fragmentera kartan administration autentiseringsuppgifter
Program som administrera och ändra Fragmentera maps skiljer sig från de som använder Fragmentera mappar till flödet anslutningar. 

Att administrera Fragmentera maps (lägga till eller ändra shards, Fragmentera kartor, Fragmentera mappningar osv) måste du initiera den **ShardMapManager** med **autentiseringsuppgifter som har behörighet för båda GSM databasen och på varje för läsning och skrivning databasen som fungerar som en Fragmentera**. Autentiseringsuppgifterna måste tillåta skrivningar till tabeller i både GSM och LSM som Fragmentera kartan information anges eller ändras, samt som skapar tabeller för LSM på nya delar.  

Se [autentiseringsuppgifter används för att komma åt klientbibliotek för elastisk databas](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Endast de metadata som påverkas
Metoder som används för att fylla eller ändra den **ShardMapManager** data inte ändrar de data som lagras i själva shards. Till exempel metoder som **CreateShard**, **DeleteShard**, **UpdateMapping**osv påverkar den Fragmentera kartan endast metadata. Ta inte bort, lägga till eller ändra informationen i delar. I stället dessa metoder är avsedda att användas tillsammans med olika åtgärder som du utför för att skapa eller ta bort faktiska databaser eller flytta rader från en Fragmentera till en annan att balansera delat miljö.  (Den **delade dokument** verktyget som medföljer elastisk Databasverktyg gör användning av API: erna tillsammans med samordna faktiska dataflytten mellan shards.) Se [skalning med hjälp av verktyget för elastisk databas delade dokument](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Databeroende routning
Fragmentera kartan manager används i program som kräver databasanslutningar utföra dataåtgärder som app-specifik. Dessa anslutningar måste vara kopplad till rätt databas. Detta kallas **Data beroende routning**. Initiera en Fragmentera kartan manager objekt från fabriken med hjälp av autentiseringsuppgifter som har skrivskyddad åtkomst på GSM databasen för dessa program. Enskilda förfrågningar för senare anslutningar ange autentiseringsuppgifter som krävs för att ansluta till databasen lämpliga Fragmentera.

Observera att dessa program (med hjälp av **ShardMapManager** öppnas med skrivskyddad autentiseringsuppgifter) kan inte göra ändringar i maps eller mappningar. Skapa administrativa specifika program eller PowerShell-skript som anger högre Privilegierade autentiseringsuppgifter som tidigare diskuterats för dessa behov. Se [autentiseringsuppgifter används för att komma åt klientbibliotek för elastisk databas](sql-database-elastic-scale-manage-credentials.md).

Mer information finns i [Data beroende routning](sql-database-elastic-scale-data-dependent-routing.md). 

## <a name="modifying-a-shard-map"></a>Ändra en Fragmentera karta
En Fragmentera karta kan ändras på olika sätt. Alla följande metoder ändra de metadata som beskriver delar och deras mappningar, men de kan inte ändra data i shards fysiskt eller gör de skapa eller ta bort faktiska databaser.  Vissa åtgärder på kartan Fragmentera beskrivs nedan kan behöva samordnas med administrativa åtgärder som fysiskt flytta data eller att lägga till och ta bort databaser som fungerar som delar.

Dessa metoder fungerar tillsammans som byggblock som är tillgängliga för att ändra den övergripande fördelningen av data i din databasmiljö för delat.  

* Lägg till eller ta bort delar: använda **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)) och **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.deleteshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard.aspx)) för Shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.aspx)) klass. 
  
    Servern och databasen som representerar målet Fragmentera måste redan finnas för dessa åtgärder att köra. Dessa metoder har inte någon effekt på databaserna som fristående, endast för metadata i kartan Fragmentera.
* Skapa eller ta bort punkter eller intervall som mappas till delar: använda **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn841993.aspx)), **DeleteMapping** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.deletemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824200.aspx)) för RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn807318.aspx)) klass, och **CreatePointMapping**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map.createpointmapping), [.NET](https://msdn.microsoft.com/library/azure/dn807218.aspx)) för ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._list_shard_map), [.NET](https://msdn.microsoft.com/library/azure/dn842123.aspx)) klass.
  
    Många olika punkter eller intervall kan mappas till samma Fragmentera. Dessa metoder påverkar endast metadata - de påverkar inte data som kanske redan finns i shards. Om data måste tas bort från databasen för att överensstämma med **DeleteMapping** åtgärder du utför dessa åtgärder separat men tillsammans med hjälp av dessa metoder.  
* Att dela befintliga områden i två eller sammanfoga intilliggande adressintervall till ett: använda **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) och **MergeMappings** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Observera att dela och slå samman operations **inte ändra Fragmentera som nyckelvärden mappas**. En delning delar ett befintligt intervall i två delar, men lämnar både som mappas till samma Fragmentera. En koppling fungerar på två angränsande områden som redan är mappade till samma fragment, mottagarsidan dem till ett område.  Punkter eller intervall själva mellan shards måste samordnas med hjälp av **UpdateMapping** tillsammans med faktiska dataflytten.  Du kan använda den **dela/Merge** tjänst som är en del av elastiska Databasverktyg för att samordna Fragmentera kartan ändringar med dataflyttning flytt krävs. 
* Att mappa (eller flytta) enskilda punkter eller intervall för olika delar: använda **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.updatemapping), [.NET](https://msdn.microsoft.com/library/azure/dn824207.aspx)).  
  
    Eftersom data kan behöva flyttas från en Fragmentera till en annan för att överensstämma med **UpdateMapping** åtgärder, måste du utföra den flytt separat men tillsammans med hjälp av dessa metoder.
* Göra mappningar online och offline: använda **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingoffline), [.NET](https://msdn.microsoft.com/library/azure/dn824202.aspx)) och **MarkMappingOnline** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.markmappingonline), [.NET](https://msdn.microsoft.com/library/azure/dn807225.aspx)) att styra online tillståndet för en mappning. 
  
    Vissa åtgärder på Fragmentera mappningar tillåts endast när en mappning är i tillståndet ”offline” inklusive **UpdateMapping** och **DeleteMapping**. När en mappning är offline, returneras ett fel i en data-beroende begäran baserat på en nyckel som ingår i mappningen. När ett intervall först kopplas från avslutats dessutom alla anslutningar till den berörda Fragmentera automatiskt för att förhindra inkonsekvent eller ofullständig resultat för frågor som riktar sig mot områden som ändras. 

Mappningar är oföränderliga objekt i .net.  Alla de metoder som ändrar mappningar ogiltig även alla referenser till dem i din kod. Om du vill göra det enklare att utföra sekvenser av åtgärder som ändrar tillstånd för en mappning, returnera alla metoder som ändrar en mappning för en ny mappning referens så kan vara att härleda operations. Om du vill ta bort en befintlig mappning i shardmap sm som innehåller nyckeln 25, kan du till exempel köra följande: 

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Lägga till en Fragmentera
Program behöver ofta lägga till nya delar för att hantera data som förväntas av nya nycklar eller nyckelintervall för en Fragmentera som redan finns. Till exempel ett delat program genom att klient-ID kan behöva etablera en ny Fragmentera för en ny klient eller varje månad delat data måste en ny Fragmentera etablerats före varje ny månad. 

Om nya värdeintervallet nycklar inte är en del av en befintlig mappning och inga dataflyttning krävs, är det enkelt att lägga till nya Fragmentera och associera den nya nyckeln eller området till att fragmentera. Mer information om att lägga till nya shards finns [att lägga till en ny Fragmentera](sql-database-elastic-scale-add-a-shard.md).

För scenarier som kräver dataflyttning dock behövs verktyget delade dokument för att samordna dataförflyttning mellan shards i kombination med de nödvändiga Fragmentera karta uppdateringarna. Mer information om hur du använder verktyget delade dokument finns [översikt över delade dokument](sql-database-elastic-scale-overview-split-and-merge.md) 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png

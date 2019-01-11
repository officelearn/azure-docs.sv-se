---
title: Skala ut en Azure SQL database | Microsoft Docs
description: Hur du använder ShardMapManager, klientbibliotek för elastiska databaser
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 77ef1fed7bbdb53898a688f14944b9b6b16773c7
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200980"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Skala ut databaser med fragmentkartehanteraren

Använd en karthanteraren för att enkelt skala ut databaser på SQL Azure. Fragmentkartehanteraren är en särskild databas som upprätthåller globala mappningsinformation om alla shards (databaser) i en shard. Metadata som gör att program att ansluta till rätt databas baserat på värdet för den **shardingnyckel**. Dessutom kan varje fragment i uppsättningen innehåller mappningar som spårar lokala Fragmentera data (kallas även **shardletar**).

![Fragmentkarthantering](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Det är viktigt att fragmentkarthantering att förstå hur dessa mappningar skapas. Detta görs med hjälp av klassen ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), som finns i den [Elastic Database-klientbiblioteket](sql-database-elastic-database-client-library.md) att hantera fragmentkartor.  

## <a name="shard-maps-and-shard-mappings"></a>Fragmentkartor och mappningar för shard

Du måste välja vilken typ av fragmentkartan för att skapa för varje fragment. Valet beror på databasens arkitektur:

1. För enskilda innehavare per databas  
2. Flera klienter per databas (två typer):
   1. Lista mappning
   2. Mappning av intervall

För en enda klient-modell, skapar du en **lista-mappning** fragmentkartan. Enskild klientmodell tilldelar en databas per klient. Det här är en effektiv modell för SaaS-utvecklare som den förenklar hanteringen.

![Lista mappning][1]

Modell för flera klienter tilldelas flera klienter till en enskild databas (och du kan distribuera grupper av klienter över flera databaser). Använd den här modellen när du förväntar dig varje klient har liten databehov. I den här modellen kan du tilldela olika klienter till en databas med **intervallet mappning**.

![Mappning av intervall][2]

Eller du kan implementera en databas för flera innehavare modellen med hjälp av en *lista mappning* att tilldela flera klienter till en enskild databas. Till exempel DB1 används för att lagra information om klient-ID 1 och 5 och DB2 lagrar data för 7-klient- och klienttrafik 10.

![Flera klienter i en enda DB][3]

### <a name="supported-types-for-sharding-keys"></a>Typer som stöds för horisontell partitionering nycklar

Elastisk skalning stöder följande typer som horisontell partitionering nycklar:

| .NET | Java |
| --- | --- |
| heltal |heltal |
| lång |lång |
| GUID |uuid |
| byte  |byte |
| datetime | tidsstämpel |
| Tidsintervall | varaktighet|
| DateTimeOffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Lista och intervallet shardkartor

Fragmentkartor kan konstrueras med **listor över enskilda horisontell partitionering viktiga värden**, eller de kan konstrueras med **intervallen för horisontell partitionering viktiga värden**.

### <a name="list-shard-maps"></a>Lista shardkartor

**Shards** innehålla **shardletar** och mappningen av shardletar till shards underhålls av en skärvkarta. En **listfragmentkarta** är en association mellan enskild nyckelvärdena som identifierar shardletar och de databaser som fungerar som shards.  **Lista över mappningar** är explicit och olika viktiga värden kan mappas till samma databas. Till exempel mappar nyckelvärdet 1 till databasen A, och de nyckelvärden 3 och 6 båda mappar till databasen B.

| Nyckel | Fragmentets placering |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Intervallet shardkartor

I en **intervallfragmentkarta**, viktiga intervallet anges med ett par **[låg värde, högt värde)** där den *låg värdet* är den minsta nyckeln i intervallet, och *hög Värdet* är det första värdet som är högre än intervallet.

Till exempel **[0, 100)** innefattar alla heltal större än eller lika med 0 och mindre än 100. Observera att flera värden kan peka på samma databas och åtskilt intervall som stöds (t.ex. [100,200) och [400,600) pekar databasen C i exemplet nedan.)

| Nyckel | Fragmentets placering |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

I tabellerna ovan är en konceptuell exempel på en **ShardMap** objekt. Varje rad är ett förenklat exempel på en enskild **PointMapping** (för listfragmentkarta) eller **RangeMapping** (för intervallfragmentkarta) objekt.

## <a name="shard-map-manager"></a>Karthanteraren

I klientbiblioteket består fragmentkartehanteraren av fragmentkartor. De data som hanteras av en **ShardMapManager** instans sparas på tre platser:

1. **Global Fragmentkartan (GSM)**: Du anger en databas som fungerar som lagringsplats för alla dess fragmentkartor och mappningar. Särskilda tabeller och lagrade procedurer skapas automatiskt för att hantera informationen. Detta är vanligtvis en liten databas och lätt öppnas och ska inte användas för andra behov av programmet. Tabeller är i ett särskilt schema med namnet **__ShardManagement**.
2. **Lokala Fragmentkartan (LSM)**: Varje databas som du anger för att vara en shard ändras så att den innehåller flera små tabeller och särskilda lagrade procedurer som innehåller och hantera fragment kartan information om specifika för fragmentet. Den här informationen är redundant med informationen i GSM och det gör att programmet för att verifiera informationen i cachelagrade fragment kartan utan att placera all belastning på GSM; programmet använder LSM för att avgöra om en cachelagrad mappning är fortfarande giltig. Tabeller som motsvarar LSM på varje shard finns också i schemat **__ShardManagement**.
3. **Programcache**: Varje instans åtkomst program till en **ShardMapManager** objekt upprätthåller en lokal minnescache över dess mappningar. Registret lagrar routningsinformationen som nyligen har hämtats.

## <a name="constructing-a-shardmapmanager"></a>Skapa en ShardMapManager

En **ShardMapManager** objektet konstrueras med en fabrik ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) mönster. Den **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) metoden tar autentiseringsuppgifter (inklusive servernamnet och databasnamnet som innehåller GSM) den form av en **ConnectionString** och returnerar en instans av en **ShardMapManager**.  

**Obs!** Den **ShardMapManager** instansieras bara en gång per appdomän i initieringskoden för ett program. Skapa ytterligare instanser av ShardMapManager i samma app domän resulterar i ökad minne och CPU-användning av programmet. En **ShardMapManager** kan innehålla valfritt antal fragmentkartor. Även om en enda fragmentkartan är tillräckligt för många program, finns det tillfällen när olika uppsättningar av databaser används för olika schema eller för unika ändamål. i sådana fall kan det vara bättre att ange flera fragmentkartor.

I den här koden, ett program försöker att öppna en befintlig **ShardMapManager** med TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) metod. Om objekt som representerar en Global **ShardMapManager** (GSM) inte ännu finns inne i databasen, klientbiblioteket skapar dem med hjälp av CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) metoden.

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

Du kan använda PowerShell för .NET-version för att skapa en ny Karthanteraren. Ett exempel är tillgängliga [här](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Hämta en RangeShardMap eller ListShardMap

När du har skapat ett fragment kartan manager, kan du få RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) eller ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) med hjälp av den TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), eller GetShardMap ([ Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) metoden.

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

### <a name="shard-map-administration-credentials"></a>Autentiseringsuppgifter för fragment karta

Program som administrerar och manipulera fragmentkartor skiljer sig från de som använder fragmentkartor att dirigera anslutningar.

Att administrera fragmentkartor (lägga till eller ändra shards, fragmentkartor, fragment mappningar, etc.) du måste skapa en instans av den **ShardMapManager** med **autentiseringsuppgifter som har full behörighet för båda för GSM-databasen och på varje databas som fungerar som en shard**. Autentiseringsuppgifterna måste tillåta för skrivningar i tabellerna i GSM såväl LSM som fragment kartan information anges eller ändras, samt för att skapa LSM tabeller på nya fragmenten.  

Se [autentiseringsuppgifter används för åtkomst till klientbiblioteket för elastiska databaser](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Endast metadata som påverkas

Metoder som används för att fylla i eller ändra den **ShardMapManager** data inte ändrar de data som lagras i shards själva. Till exempel metoder som **CreateShard**, **DeleteShard**, **UpdateMapping**och så vidare påverkar fragment kartan metadata endast. Ta inte bort, lägga till eller ändra användardata i shards. I stället dessa metoder är avsedd att användas tillsammans med separata åtgärder som du utför för att skapa eller ta bort faktiska databaser eller som flytta rader från en shard till en annan att balansera om en shardad miljö.  (Den **dela / sammanslå** verktyg i elastiska Databasverktyg gör att använda dessa API: er tillsammans med samordna faktiska dataflytten mellan fragment.) Se [skala med verktyget elastiska databaser dela och slå samman](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Databeroende routning

Fragmentkartehanteraren används i program som kräver databasanslutningar utföra dataåtgärder som app-specifika. Dessa anslutningar måste vara kopplad till rätt databas. Detta kallas **databeroende routning**. För dessa program du skapa en instans av ett fragment kartan manager-objekt från den fabriken med hjälp av autentiseringsuppgifter som har läsbehörighet för GSM-databasen. Enskilda förfrågningar för senare anslutningar ange autentiseringsuppgifter som krävs för att ansluta till rätt fragment-databasen.

Observera att dessa program (med hjälp av **ShardMapManager** öppnas med skrivskyddade autentiseringsuppgifter) kan inte göra ändringar i maps eller mappningar. Skapa administrativ-specifika program eller PowerShell-skript som ange autentiseringsuppgifter för högre privilegier som beskrivs ovan för dessa behov. Se [autentiseringsuppgifter används för åtkomst till klientbiblioteket för elastiska databaser](sql-database-elastic-scale-manage-credentials.md).

Mer information finns i [databeroende routning](sql-database-elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Ändra en skärvkarta

En skärvkarta kan ändras på olika sätt. Alla av följande metoder ändra metadata som beskriver shards och deras mappningar, men de fysiskt ändrar inte data i shards och göra de skapa eller ta bort de faktiska databaserna.  Några av åtgärderna på fragmentkartan som beskrivs nedan kan behöva samordnas med administrativa åtgärder som fysiskt flytta data eller att lägga till och ta bort databaser som fungerar som shards.

Dessa metoder fungerar tillsammans som byggblock som är tillgängliga för att ändra den övergripande distributionen av data i databasmiljön fragmenterade (sharded.  

* Att lägga till eller ta bort fragment: använda **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) och **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) av shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)) klass.
  
    Servern och databasen som representerar målet fragment måste redan finnas för de här åtgärderna ska köras. Dessa metoder har inte någon inverkan på databaserna som själva, endast på metadata i fragmentkartan.
* Skapa eller ta bort punkter eller intervall som mappas till shards: använda **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) av RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) klass, och **CreatePointMapping**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) av ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) klass.
  
    Många olika punkter eller intervall kan mappas till samma fragment. Dessa metoder påverkar endast metadata – de påverkar inte data som kanske redan finns i fragment. Om data ska tas bort från databasen för att överensstämma med **DeleteMapping** åtgärder kan du utföra dessa åtgärder separat men tillsammans med hjälp av dessa metoder.  
* Dela befintliga områden i två eller slå ihop angränsande områden i en: använda **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) och **MergeMappings** () [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Observera att dela och slå samman operations **ändra inte den shard som nyckelvärden mappas**. En delning delar upp ett befintligt intervall i två delar, men lämnar både som mappas till samma fragment. En sammanfogning körs på två angränsande områden som redan är mappade till samma fragment, RSC dem i ett enda område.  Förflyttning av punkter eller sträcker sig själva mellan shards behöver samordnas med hjälp av **UpdateMapping** tillsammans med faktiska dataflytten.  Du kan använda den **dela/Sammanslå** tjänst som är en del av elastiska Databasverktyg för att samordna fragment kartan ändringar med dataförflyttning, förflyttning krävs.
* Att mappa (eller flytta) enskilda punkter eller intervall till olika shards: använda **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Eftersom data kan behöva flyttas från en shard till en annan för att överensstämma med **UpdateMapping** åtgärder, som du behöver utföra rörelsen separat men tillsammans med hjälp av dessa metoder.

* Att ta mappningar online och offline: använda **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) och **MarkMappingOnline** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) att styra online tillståndet för en mappning.
  
    Vissa åtgärder på fragment mappningar tillåts endast när en mappning är i tillståndet ”offline” inklusive **UpdateMapping** och **DeleteMapping**. När en mappning är offline, returneras ett fel i en databeroende begäran baserat på en nyckel som ingår i mappningen. När ett intervall kopplas först, avslutades dessutom alla anslutningar till den berörda sharden automatiskt för att förhindra att inkonsekventa eller ofullständiga resultat för frågor som är riktad mot intervall som håller på att ändras.

Mappningar är oföränderligt objekt i .net.  Alla de metoder som ändrar mappningar också ogiltigförklaras alla referenser till dem i din kod. Om du vill göra det enklare att utföra sekvenser av åtgärder som ändrar tillståndet för en mappning, returnera alla metoder som ändrar en mappning en ny mappning-referens, så kan vara härledda åtgärder. Om du vill ta bort en befintlig mappning i shardmap sm som innehåller nyckeln 25, kan du till exempel köra följande:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Att lägga till en shard

Program behöver ofta att lägga till nya fragmenten för att hantera data som förväntas från nya nycklar eller nyckelintervall för en skärvkarta som redan finns. Till exempel ett delat program genom att klient-ID kan behöva etablera en ny shard för en ny klient eller varje månad shardade data måste en ny shard etableras innan början av varje ny månad.

Om det nya området med nyckelvärden som inte är en del av en befintlig mappning och inga dataförflyttning krävs, är det enkelt att lägga till det nya fragmentet och associera den nya nyckeln eller området till fragmentet. Mer information om att lägga till nya fragmenten finns [att lägga till en ny shard](sql-database-elastic-scale-add-a-shard.md).

För scenarier som kräver dataförflyttning, men behövs verktyget Dela / sammanslå för att organisera dataförflyttning mellan fragment i kombination med de nödvändiga fragment karta uppdateringarna. Mer information om hur du använder verktyget för dela / sammanslå finns i [översikt över dela / sammanslå](sql-database-elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png

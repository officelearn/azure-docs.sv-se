---
title: Skala ut en databas
description: Så här använder du klientbiblioteket ShardMapManager, elastisk databasklient
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 8175563d8c1c2ec59b4195b2ede06f6e1dbf8556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256268"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Skala ut databaser med fragmentkarthanteraren

Om du enkelt vill skala ut databaser på SQL Azure använder du en fragmentkarthanterare. Fragmentkarthanteraren är en särskild databas som upprätthåller global mappningsinformation om alla shards (databaser) i en fragmentuppsättning. Metadata gör det möjligt för ett program att ansluta till rätt databas baserat på värdet av **sharding-nyckeln**. Dessutom innehåller varje fragment i uppsättningen kartor som spårar lokala fragmentdata (så kallade **shardlets**).

![Karthantering för shard](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Att förstå hur dessa kartor är konstruerade är viktigt för att skapa karthantering. Detta görs med klassen ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager) [, .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), som finns i [klientbiblioteket för elastisk databas](sql-database-elastic-database-client-library.md) för att hantera fragmentmappningar.  

## <a name="shard-maps-and-shard-mappings"></a>Fragmentkartor och fragmentmappningar

För varje fragment måste du välja vilken typ av fragmentmappning som ska skapas. Valet beror på databasarkitekturen:

1. En enda klient per databas  
2. Flera klienter per databas (två typer):
   1. Mappning av lista
   2. Mappning av område

Skapa en fragmentkarta för **en mappningsmappning** för en enskild klientmodell. Modellen med en klient tilldelar en databas per klient. Detta är en effektiv modell för SaaS-utvecklare eftersom det förenklar hanteringen.

![Mappning av lista][1]

Modellen med flera innehavare tilldelar flera klienter till en enskild databas (och du kan distribuera grupper av klienter över flera databaser). Använd den här modellen när du förväntar dig att varje klient ska ha små databehov. I den här modellen tilldelar du ett antal klienter till en databas med hjälp av **områdesmappning**.

![Mappning av område][2]

Du kan också implementera en databasmodell med flera innehavare med hjälp av en *listmappning* för att tilldela flera klienter till en enskild databas. DB1 används till exempel för att lagra information om klient-ID 1 och 5, och DB2 lagrar data för klient 7 och klient 10.

![Flera klienter på en enda DB][3]

### <a name="supported-types-for-sharding-keys"></a>Typer som stöds för fragmentering av nycklar

Elastisk skala stöder följande typer som sharding nycklar:

| .NET | Java |
| --- | --- |
| heltal |heltal |
| long |long |
| Guid |uuid |
| byte[]  |byte[] |
| datetime | timestamp |
| Gått | varaktighet|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Lista- och intervallsarvagkartor

Fragmentkartor kan konstrueras med hjälp **av listor över enskilda sharding-nyckelvärden,** eller så kan de konstrueras med hjälp av områden med **sharding-nyckelvärden**.

### <a name="list-shard-maps"></a>Lista fragmentkartor

**Shards** innehåller **shardlets** och mappningen av shardlets till shards underhålls av en fragmentkarta. En **listshard-karta** är en koppling mellan de enskilda nyckelvärden som identifierar fragmenten och databaserna som fungerar som shards.  **Listmappningar** är explicita och olika nyckelvärden kan mappas till samma databas. Nyckelvärde 1 mappas till databas A och nyckelvärdena 3 och 6 mappas båda till databas B.

| Nyckel | Fragmentplats |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Avstånd shard kartor

I en **intervallsvantövergentning**beskrivs nyckelområdet av ett par **[Lågt värde, högt värde)** där *det lägsta värdet* är miniminyckeln i intervallet och det höga *värdet* är det första värdet högre än intervallet.

Till exempel **[0, 100)** innehåller alla heltal som är större än eller lika med 0 och mindre än 100. Observera att flera områden kan peka på samma databas, och osammanhängande områden stöds (till exempel [100 200) och [400 600) pekar båda på databas C i följande exempel.)

| Nyckel | Fragmentplats |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Var och en av tabellerna som visas ovan är ett begreppsmässigt exempel på ett **ShardMap-objekt.** Varje rad är ett förenklat exempel på ett enskilt **PointMapping** -objekt (för listarvmappning) eller **RangeMapping** (för områdesshard-kartan).

## <a name="shard-map-manager"></a>Karthanterare för shard

I klientbiblioteket är fragmentkarthanteraren en samling fragmentkartor. Data som hanteras av en **ShardMapManager-instans** lagras på tre ställen:

1. **Global Shard Map (GSM)**: Du anger en databas som ska fungera som databas för alla dess fragmentkartor och mappningar. Särskilda tabeller och lagrade procedurer skapas automatiskt för att hantera informationen. Detta är vanligtvis en liten databas och lätt åtkomlig, och den bör inte användas för andra behov av programmet. Tabellerna finns i ett särskilt schema med namnet **__ShardManagement**.
2. **Lsm (Local Shard Map)**: Varje databas som du anger ska vara en shard ändras så att den innehåller flera små tabeller och särskilda lagrade procedurer som innehåller och hanterar fragmentkartinformation som är specifik för fragmentet. Den här informationen är överflödig med informationen i GSM och gör det möjligt för programmet att validera cachelagrade fragmentkartinformation utan att placera någon belastning på GSM. Programmet använder LSM för att avgöra om en cachelagrad mappning fortfarande är giltig. De tabeller som motsvarar LSM för varje shard finns också i schemat **__ShardManagement**.
3. **Programcache**: Varje programinstans som ansluter till ett **ShardMapManager-objekt** upprätthåller en lokal cache i minnet av sina mappningar. Den lagrar routningsinformation som nyligen har hämtats.

## <a name="constructing-a-shardmapmanager"></a>Konstruera en ShardMapManager

Ett **ShardMapManager-objekt** konstrueras med hjälp av ett fabriksmönster ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory) [, .NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory) **Metoden ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) har autentiseringsuppgifter (inklusive servernamnet och databasnamnet som innehåller GSM) i form av en **ConnectionString** och returnerar en instans av en **ShardMapManager**.  

**Observera:** **ShardMapManager** bör instansieras endast en gång per appdomän, inom initieringskoden för ett program. Skapande av ytterligare instanser av ShardMapManager i samma appdomän resulterar i ökat minne och CPU-användning av programmet. En **ShardMapManager** kan innehålla valfritt antal fragmentkartor. En enskild fragmentkarta kan vara tillräcklig för många program, men det finns tillfällen då olika uppsättningar databaser används för olika scheman eller för unika ändamål. i dessa fall flera fragment kartor kan vara att föredra.

I den här koden försöker ett program öppna en befintlig **ShardMapManager** med metoden TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET.](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) Om det ännu inte finns objekt som representerar en Global **ShardMapManager** (GSM) i databasen, skapas de i klientbiblioteket med metoden CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)).

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

För .NET-versionen kan du använda PowerShell för att skapa en ny Shard Map Manager. Ett exempel finns [här](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Hämta en RangeShardMap eller ListShardMap

När du har skapat en shard map manager kan du hämta RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) eller ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) med metoden TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), tryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) eller GetShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)).

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

### <a name="shard-map-administration-credentials"></a>Autentiseringsuppgifter för fragmentkartan

Program som administrerar och manipulerar fragmentkartor skiljer sig från dem som använder fragmentkartor för att dirigera anslutningar.

Om du vill administrera fragmentkartor (lägga till eller ändra shards, fragmentmappningar, fragmentmappningar osv.) måste du **instansiera ShardMapManager** med hjälp av **autentiseringsuppgifter som har läs-/skrivbehörighet på både GSM-databasen och i varje databas som fungerar som en fragment.** Autentiseringsuppgifterna måste möjliggöra skrivningar mot tabellerna i både GSM och LSM när fragmentkartinformation anges eller ändras, samt för att skapa LSM-tabeller på nya shards.  

Se [Autentiseringsuppgifter som används för att komma åt klientbiblioteket för elastisk databas](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Endast metadata som påverkas

Metoder som används för att fylla i eller ändra **ShardMapManager-data** ändrar inte användardata som lagras i själva shards. Metoder som **CreateShard**, **DeleteShard**, **UpdateMapping**osv. De tar inte bort, lägger till eller ändrar användardata som finns i shards. I stället är dessa metoder utformade för att användas tillsammans med separata åtgärder som du utför för att skapa eller ta bort faktiska databaser, eller som flyttar rader från en fragment till en annan för att balansera en fragmenterad miljö.  (Det **verktyg för delad koppling** som ingår i elastiska databasverktyg använder dessa API:er tillsammans med att dirigera faktisk dataförflyttning mellan shards.) Se [Skala med verktyget Delad sammanfogning av elastisk databas](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Databeroende routning

Fragmentkarthanteraren används i program som kräver databasanslutningar för att utföra de appspecifika dataåtgärderna. Dessa anslutningar måste associeras med rätt databas. Detta kallas **databeroende routning**. För dessa program instansierar du ett fragmentkarthanterare från fabriken med hjälp av autentiseringsuppgifter som har skrivskyddad åtkomst i GSM-databasen. Enskilda begäranden om senare anslutningar anger autentiseringsuppgifter som krävs för att ansluta till lämplig fragmentdatabas.

Observera att dessa program (med **ShardMapManager** som öppnas med skrivskyddade autentiseringsuppgifter) inte kan göra ändringar i kartorna eller mappningarna. Skapa administrativa specifika program eller PowerShell-skript som tillhandahåller autentiseringsuppgifter med högre behörighet som diskuterats tidigare för dessa behov. Se [Autentiseringsuppgifter som används för att komma åt klientbiblioteket för elastisk databas](sql-database-elastic-scale-manage-credentials.md).

Mer information finns i [Databeroende routning](sql-database-elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Ändra en fragmentkarta

En fragmentkarta kan ändras på olika sätt. Alla följande metoder ändrar metadata som beskriver shards och deras mappningar, men de ändrar inte fysiskt data i shards, inte heller skapar eller tar de bort de faktiska databaserna.  Vissa av åtgärderna på fragmentkartan som beskrivs nedan kan behöva samordnas med administrativa åtgärder som fysiskt flyttar data eller som lägger till och tar bort databaser som fungerar som shards.

Dessa metoder fungerar tillsammans som de byggstenar som är tillgängliga för att ändra den totala fördelningen av data i den fragmenterade databasmiljön.  

* Så här lägger du till eller tar bort shards: use **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) och **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) i klassen shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)).
  
    Servern och databasen som representerar målsvantvanten måste redan finnas för att dessa åtgärder ska kunna köras. Dessa metoder har ingen inverkan på själva databaserna, bara på metadata i fragmentkartan.
* Om du vill skapa eller ta bort punkter eller intervall som mappas till shards: använd **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) för klassen RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) och **CreatePointMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) i klassen ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)).
  
    Många olika punkter eller intervall kan mappas till samma fragment. Dessa metoder påverkar bara metadata - de påverkar inte data som kanske redan finns i shards. Om data måste tas bort från databasen för att vara konsekventa med **DeleteMapping-åtgärder,** utför du dessa åtgärder separat men tillsammans med hjälp av dessa metoder.  
* Om du vill dela upp befintliga områden i två eller sammanfoga intilliggande områden till ett: använd **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) och **MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Observera att delnings- och sammanfogningsåtgärder **inte ändrar shard som nyckelvärden mappas till**. En uppdelning bryter ett befintligt område i två delar, men lämnar båda som mappas till samma fragment. En sammanfogning fungerar på två intilliggande områden som redan är mappade till samma fragment, som sammanfogar dem till ett enda intervall.  Förflyttningen av punkter eller intervall själva mellan shards måste samordnas med hjälp av **UpdateMapping** tillsammans med faktiska datarörelser.  Du kan använda tjänsten **Dela/sammanfoga** som ingår i elastiska databasverktyg för att samordna fragmentkartändringar med datarörelser när förflyttning behövs.
* Om du vill mappa om (eller flytta) enskilda punkter eller intervall till olika shards: använd **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Eftersom data kan behöva flyttas från en shard till en annan för att vara konsekventa med **UpdateMapping-åtgärder,** måste du utföra den förflyttningen separat men tillsammans med hjälp av dessa metoder.

* Om du vill ta mappningar online och offline: använd **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) och **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) för att styra onlinetillståndet för en mappning.
  
    Vissa åtgärder på fragmentmappningar tillåts endast när en mappning är i ett "offline"-tillstånd, inklusive **UpdateMapping** och **DeleteMapping**. När en mappning är offline returnerar en databeroende begäran baserad på en nyckel som ingår i mappningen ett fel. När ett intervall först tas offline dödas dessutom alla anslutningar till den berörda shard automatiskt för att förhindra inkonsekventa eller ofullständiga resultat för frågor som riktas mot intervall som ändras.

Mappningar är oföränderliga objekt i .Net.  Alla metoder ovan som ändrar mappningar ogiltigförklarar också alla referenser till dem i koden. För att göra det enklare att utföra sekvenser av åtgärder som ändrar en mappnings tillstånd returnerar alla metoder som ändrar en mappning en ny mappningsreferens, så att åtgärder kan kedjas. Om du till exempel vill ta bort en befintlig mappning i fragmentmap sm som innehåller nyckel 25 kan du köra följande:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Lägga till en shard

Program behöver ofta lägga till nya shards för att hantera data som förväntas från nya nycklar eller nyckelintervall, för en fragmentkarta som redan finns. Ett program som fragmenteras av klient-ID kan till exempel behöva etablera en ny fragment för en ny klient, eller data som fragmenterades varje månad kan behöva en ny fragmentetablering före början av varje ny månad.

Om det nya intervallet med nyckelvärden inte redan är en del av en befintlig mappning och ingen dataförflyttning är nödvändig, är det enkelt att lägga till den nya fragmentet och associera den nya nyckeln eller intervallet till den fragmentet. Mer information om hur du lägger till nya shards finns i [Lägga till en ny shard](sql-database-elastic-scale-add-a-shard.md).

För scenarier som kräver dataförflyttning behövs dock verktyget för delad koppling för att dirigera dataförflyttningen mellan shards i kombination med nödvändiga fragmentmappningsuppdateringar. Mer information om hur du använder verktyget för delad koppling finns i [Översikt över delad koppling](sql-database-elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png

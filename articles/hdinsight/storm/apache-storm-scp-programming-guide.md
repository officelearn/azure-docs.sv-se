---
title: SCP.NET programmeringsguide för Storm i Azure HDInsight
description: Läs om hur du använder SCP.NET för att skapa . NET-baserade Storm-topologier för användning med Storm som körs i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186864"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>SCP-programmeringsguide för Apache Storm i Azure HDInsight

SCP är en plattform för att skapa realtids-, tillförlitliga, konsekventa och högpresterande databehandlingsprogram. Den är byggd ovanpå [Apache Storm](https://storm.incubator.apache.org/), som är ett strömbehandlingssystem utformat av öppen källkod samhällen. Nathan Marz skapade Storm. Det publicerades som öppen källkod av Twitter. Storm använder [Apache ZooKeeper](https://zookeeper.apache.org/), som är ett annat Apache-projekt som möjliggör mycket tillförlitlig distribuerad samordning och statlig hantering.

SCP-projektet har portat inte bara Storm på Windows utan även projekttillägg och anpassning för Windows-miljön. Tilläggen innehåller .NET-utvecklarupplevelsen och .NET-biblioteken. Anpassningen inkluderar Windows-baserad distribution.

Med tillägg och anpassning behöver du inte punga för programvaruprojekt med öppen källkod. Du kan använda härledda miljöer som är byggda ovanpå Storm.

## <a name="processing-model"></a>Bearbetningsmodell

Data i SCP modelleras som kontinuerliga strömmar av tupplar. Typiskt tupplar:

1. Flöda in i en kö.
1. Plockas upp och omvandlas av affärslogik värd inuti en Storm topologi.
1. Antingen har sina utdata piped som tupplar till ett annat SCP-system eller har åtagit sig att butiker som distribuerade filsystem och databaser som SQL Server.

![Ett diagram över en kö som matar data till bearbetning, som i sin tur matar ett datalager](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

I Storm definierar en programtopologi ett beräkningsdiagram. Varje nod i en topologi innehåller bearbetningslogik. Länkar mellan noder indikerar dataflöde.

Noder som injicerar indata i topologin kallas _pip ._ Du kan använda dem för att ordna data. Indata kan komma från en källa som filloggar, en transaktionsdatabas eller en systemprestandaräknare.

Noder som har både indata- och utdataflöden kallas _bultar_. De gör den faktiska datafiltrering, val och aggregering.

SCP stöder bästa insats, minst en gång, och exakt en gång databehandling.

I ett distribuerat databehandlingsprogram kan fel uppstå under databearbetning. Sådana fel inkluderar ett nätverksavbrott, ett datorfel eller ett fel i koden. Minst en gång bearbetning säkerställer att alla data bearbetas minst en gång genom att automatiskt spela upp samma data när ett fel inträffar.

Åtminstone en gång bearbetning är enkel och pålitlig, och det passar många applikationer. Men när ett program kräver exakt räkning är minst en gång bearbetning otillräcklig eftersom samma data kan spelas upp i programtopologin. I så fall, exakt när behandlingen ser till att resultatet är korrekt även när data spelas upp och bearbetas flera gånger.

SCP låter .NET-utvecklare skapa databehandlingsprogram i realtid när du använder en Java Virtual Machine (JVM) med Storm. En JVM och .NET kommunicerar via TCP lokala uttag. Varje pip/bult är ett .NET/Java-processpar, där användarlogiken körs i en .NET-process som ett plugin-program.

Så här skapar du ett databehandlingsprogram ovanpå SCP:

1. Designa och implementera pipar för att hämta data från köer.
1. Designa och implementera bultar som bearbetar indata och spara dem i externa butiker som en databas.
1. Utforma topologin och skicka sedan och kör den.

Topologin definierar hörn och data som flödar mellan dem. SCP tar en topologispecifikation och distribuerar den på ett Storm-kluster, där varje hörn körs på en logisk nod. Aktiviteten Storm-schemaläggaren tar hand om redundans och skalning.

Den här artikeln använder några enkla exempel för att gå igenom hur du skapar databehandlingsprogram med SCP.

## <a name="scp-plug-in-interface"></a>Plugin-gränssnitt för SCP

SCP-plugin-program är fristående program. De kan köras inuti Visual Studio under utveckling och anslutas till Storm-pipelinen efter produktionsdistribution.

Skriva en SCP plug-in är samma sak som att skriva något annat Windows-konsolprogram. Den SCP.NET plattformen deklarerar vissa gränssnitt för pip/bult. Din plugin-kod implementerar dessa gränssnitt. Huvudsyftet med denna design är att låta dig fokusera på din affärslogik samtidigt som SCP.NET plattform hantera andra saker.

Din plugin-kod implementerar något av följande gränssnitt. Vilket gränssnitt beror på om topologin är transaktionell eller icke-transaktionell och om komponenten är en pip eller en bult.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** är det gemensamma gränssnittet för många plug-ins. För närvarande är det en dummy gränssnitt.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** är gränssnittet för en icke-transaktionell pip.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

När **NextTuple** anropas kan din C#-kod avge en eller flera tupplar. Om det inte finns något att släppa ut, bör denna metod tillbaka utan att släppa ut något.

**Metoderna NextTuple**, **Ack**och **Fail** kallas alla i en snäv slinga i en enda tråd i en C#-process. När det inte finns några tupplar att avge, har **NextTuple** sova för en kort tid som 10 millisekunder. Denna sömn hjälper till att undvika att slösa CPU-tillgänglighet.

**Metoderna Ack** och **Fail** anropas endast när en specifikationsfil aktiverar bekräftelsemekanismen. *Parametern seqId* identifierar tuppeln som bekräftas eller har misslyckats. Om bekräftelse är aktiverat i en icke-transaktionell topologi ska följande **Emit-funktion** användas i en pip:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Om en nontransactional topologi inte stöder bekräftelse, **kan Ack** och **Fail** lämnas som tomma funktioner.

Indataparametern *parom* i dessa funktioner anger en tom ordlista och är reserverad för framtida användning.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** är gränssnittet för en icke-transaktionell bult.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

När en ny tuppel är tillgänglig anropas funktionen **Kör** för att bearbeta den.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** är gränssnittet för en transaktionell pip.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Precis som deras nontransactional motsvarigheter, **NextTx**, **Ack**och **Misslyckas** kallas alla i en snäv slinga i en enda tråd av en C # process. När det inte finns några tupplar att avge, har **NextTx** sova för en kort tid som 10 millisekunder. Denna sömn hjälper till att undvika att slösa CPU-tillgänglighet.

När **NextTx** anropas för att starta en ny transaktion identifierar *parametern seqId-utdata* transaktionen. Transaktionen används också i **Ack** och **Fail**. Din **NextTx-metod** kan avge data till Java-sidan. Data lagras i ZooKeeper för att stödja repris. Eftersom ZooKeeper har begränsad kapacitet bör koden endast avge metadata och inte massdata i en transaktionspip.

Eftersom Storm automatiskt spelar upp en misslyckad transaktion anropas **inte Misslyckas.** Men om SCP kan kontrollera metadata som avges av en transaktionspips kan den anropa **Misslyckad** när metadata är ogiltiga.

Indataparametern *parom* i dessa funktioner anger en tom ordlista och är reserverad för framtida användning.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** är gränssnittet för en transaktionsbult.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

**Kör-metoden** anropas när en ny tuppel anländer till bulten. **Metoden FinishBatch** anropas när transaktionen avslutas. Indataparametern *parom* är reserverad för framtida användning.

För en transaktionell topologi är **StormTxAttempt** en viktig klass. Den har två medlemmar: **TxId** och **AttemptId**. **TxId-medlemmen** identifierar en specifik transaktion. En transaktion kan göras flera gånger om den misslyckas och spelas upp igen.

SCP.NET skapar ett nytt **ISCPBatchBolt-objekt** för att bearbeta varje **StormTxAttempt-objekt,** precis som vad Storm gör i Java. Denna design syfte är att stödja parallella transaktionsbearbetning. När ett transaktionsförsök har slutförts förstörs motsvarande **ISCPBatchBolt-objekt** och skräp samlas in.

## <a name="object-model"></a>Objektmodell

SCP.NET ger också en enkel uppsättning viktiga objekt för utvecklare att programmera med. Objekten är **Context**, **StateStore**och **SCPRuntime**. De diskuteras i det här avsnittet.

### <a name="context"></a>Kontext

**Context-objektet** tillhandahåller en miljö som körs till ett program. Varje **ISCPPlugin-instans** av **ISCPSpout**, **ISCPBolt,** **ISCPTxSpout**eller **ISCPBatchBolt** har en motsvarande **context-instans.** Funktionaliteten i **Context** är uppdelad i dessa två delar:

* Den statiska delen, som finns i hela C# processen
* Den dynamiska delen, som endast är tillgänglig för den specifika **kontextinstansen**

### <a name="static-part"></a>Statisk del

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

**Logger-objektet** tillhandahålls för loggning.

**PluginType-objektet** anger plugin-in-typen av C#-processen. Om processen körs i lokalt testläge utan Java är plugin-programmet **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

**Egenskapen Config** får konfigurationsparametrar från Java-sidan, som passerar dem när ett C#-plugin-program initieras. **Config** parametrarna är indelade i två delar: **stormConf** och **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

Den **stormConf** delen är parametrar som definieras av Storm, och **pluginConf** delen är parametrar som definieras av SCP. Här är ett exempel:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

Typen **TopologyContext** hämtar topologikontexten. Det är mest användbart för flera parallella komponenter. Här är ett exempel:

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>Dynamisk del

Följande gränssnitt är relevanta för en viss **Context-instans,** som skapas av SCP.NET-plattformen och skickas till din kod:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

För en icke-transaktionell pip som stöder bekräftelse, tillhandahålls följande metod:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

En nontransactional bult som stöder bekräftelse bör uttryckligen ringa **Ack** eller **Misslyckas** med tupel den fått. När en ny tuppel avges måste bulten också ange tupels ankare. Följande metoder finns:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore (statliga)

**StateStore-objektet** tillhandahåller metadatatjänster, monotonisk sekvensgenerering och väntfri samordning. Du kan skapa distribuerade samtidighetsabstraktioner på högre nivå på **StateStore**. Dessa abstraktioner inkluderar distribuerade lås, distribuerade köer, hinder och transaktionstjänster.

SCP-program kan använda **tillståndsobjektet** för att serialisera information i [Apache ZooKeeper](https://zookeeper.apache.org/). Denna förmåga är särskilt värdefull för en transaktionell topologi. Om en transaktionspip slutar svara och startas om kan **tillstånd** hämta nödvändig information från ZooKeeper och starta om pipelinen.

**StateStore-objektet** har följande huvudsakliga metoder:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

**Tillståndsobjektet** har följande huvudsakliga metoder:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

När **simpleMode** är inställt på **true**tas motsvarande ZNode-metoden bort i ZooKeeper. **Commit** Annars tas den aktuella ZNode-metoden bort och en\_ny nod läggs till i DEN BEKRÄFTADE SÖKVÄGEN.

### <a name="scpruntime"></a>SCPRuntime

Klassen **SCPRuntime** innehåller följande två metoder:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Metoden **Initialize** initierar SCP-körningsmiljön. I den här metoden ansluter C#-processen till Java-sidan för att hämta konfigurationsparametrar och topologikontext.

**Metoden LaunchPlugin** startar meddelandebehandlingsloopen. I den här loopen tar C#-plugin-programmet emot meddelanden från Java-sidan. Dessa meddelanden inkluderar tupplar och styrsignaler. Plugin-programmet bearbetar sedan meddelandena, kanske genom att anropa gränssnittsmetoden som tillhandahålls av koden.

Indataparametern för **LaunchPlugin** är ett ombud. Metoden kan returnera ett objekt som implementerar **GRÄNSSNITTET ISCPSpout**, **ISCPBolt**, **ISCPTxSpout**eller **ISCPBatchBolt.**

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

För **ISCPBatchBolt**kan du hämta ett **StormTxAttempt-objekt** från *parms-parametern* och använda det för att bedöma om försöket är ett replayed-försök. Kontrollen för ett reprisförsök görs ofta vid commit-bulten. Den HelloWorldTx exempel senare i den här artikeln visar denna kontroll.

SCP-plugin-program kan vanligtvis köras i två lägen: lokalt testläge och regelbundet läge.

#### <a name="local-test-mode"></a>Lokalt testläge

I det här läget körs SCP-plugin-programmen i C#-koden i Visual Studio under utvecklingsfasen. Du kan använda **ILocalContext-gränssnittet** i det här läget. Gränssnittet innehåller metoder för att serialisera de utsända tupplar till lokala filer och läsa dem tillbaka till RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Regelbundet läge

I det här läget kör Storm Java-processen SCP-plugin-programmen. Här är ett exempel:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Språk för topologispecifikation

SCP Topologi Specification är ett domänspecifikt språk (DSL) för att beskriva och konfigurera SCP-topologier. Den är baserad på [Storm's Clojure DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) och utökas med SCP.

Du kan skicka topologispecifikationer direkt till ett Storm-kluster för körning via kommandot **runSpec.**

SCP.NET har lagt till följande funktioner för att definiera transaktionstopologier:

| Ny funktion | Parametrar | Beskrivning |
| --- | --- | --- |
| **tx-topolopy** |*topologi-namn*<br />*pip-karta*<br />*bult-karta* |Definierar en transaktionstopologi med topologins namn, definitionskartan för pipar och bultar. |
| **scp-tx-pip** |*exec-namn*<br />*args*<br />*Fält* |Definierar en transaktionspip. Funktionen kör det program som anges av *exec-name* och använder *args*.<br /><br />Parametern *fields* anger utdatafälten för pipen. |
| **scp-tx-batch-bult** |*exec-namn*<br />*args*<br />*Fält* |Definierar en transaktionsbatchbult. Funktionen kör det program som anges av *exec-name* och använder *args.*<br /><br />Parametern *fields* anger utdatafälten för bulten. |
| **scp-tx-commit-bult** |*exec-namn*<br />*args*<br />*Fält* |Definierar en transaktionell commit-bult. Funktionen kör det program som anges av *exec-name* och använder *args*.<br /><br />Parametern *fields* anger utdatafälten för bulten. |
| **nontx-topologi** |*topologi-namn*<br />*pip-karta*<br />*bult-karta* |Definierar en icke-transaktionell topologi med topologins namn, definitionskartan för pipar och bultar. |
| **scp-pip** |*exec-namn*<br />*args*<br />*Fält*<br />*Parametrar* |Definierar en icke-transaktionell pip. Funktionen kör det program som anges av *exec-name* och använder *args*.<br /><br />Parametern *fields* anger utdatafälten för pipen.<br /><br />*Parameterparametern* är valfri. Använd den för att ange parametrar som "nontransactional.ack.enabled". |
| **scp-bult** |*exec-namn*<br />*args*<br />*Fält*<br />*Parametrar* |Definierar en icke-transaktionell bult. Funktionen kör det program som anges av *exec-name* och använder *args*.<br /><br />Parametern *fields* anger utdatafälten för bulten<br /><br />*Parameterparametern* är valfri. Använd den för att ange parametrar som "nontransactional.ack.enabled". |

SCP.NET definierar följande nyckelord:

| Sökord | Beskrivning |
| --- | --- |
| **:namn** |Topologins namn |
| **:topologi** |Topologin med hjälp av funktionerna i föregående tabell och inbyggda funktioner |
| **:p** |Parallellismtipset för varje pip eller bult |
| **:config (konfekt)** |Om parametrar ska konfigureras eller uppdateras |
| **:schema** |Schemat för strömmen |

SCP.NET definierar också dessa ofta använda parametrar:

| Parameter | Beskrivning |
| --- | --- |
| "plugin.name" |Filnamnet på .exe-plugin-programmet för C# |
| "plugin.args" |Plugin-argumenten |
| "output.schema" |Utdataschemat |
| "nontransactional.ack.enabled" |Om bekräftelse är aktiverat för en icke-transaktionell topologi |

Kommandot **runSpec** distribueras tillsammans med bitarna. Här är kommandot användning:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Parametern *resource-dir* är valfri. Ange det när du vill koppla in ett C#-program. Den angivna katalogen innehåller programmet, beroenden och konfigurationer.

Parametern *classpath* är också valfri. Den anger Java classpath om specifikationsfilen innehåller en Java-pip eller bult.

## <a name="miscellaneous-features"></a>Diverse funktioner

### <a name="input-and-output-schema-declarations"></a>Schemadeklarationer för in- och utdata

Dina C# processer kan avge tupplar. För att göra det serialiserar plattformen tupplar till **byte[]** objekt och överför objekten till Java-sidan. Storm överför sedan dessa tupplar till målen.

I nedströmskomponenter får C#-processer tupplar tillbaka från Java-sidan och konverterar dem till plattformens ursprungliga typer. Alla dessa operationer döljs av plattformen.

För att stödja serialisering och avserialisering måste koden deklarera schemat för indata och utdata. Schemat definieras som en ordlista. Stream-ID är ordlistenyckeln. Nyckelvärdet är de typer av kolumner. En komponent kan deklarera flera strömmar.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

Följande funktion läggs till **Context** i ett context-objekt:

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Utvecklare måste se till att de utgivna tupplar lyda schemat som definierats för en ström. Annars kommer systemet att kasta ett körningsundantag.

### <a name="multistream-support"></a>Stöd för Fleraström

Med SCP kan koden avge till eller ta emot från flera olika strömmar samtidigt. **Kontextobjektet** återspeglar det här stödet som **emit-metodens** valfria dataström-ID-parameter.

Två metoder i SCP.NET **context-objektet** har lagts till. De avger en eller flera tupplar till specifika strömmar. Parametern *streamId* är en sträng. Dess värde måste vara detsamma i både C#-koden och topologidefinitionsspecifikationen.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Om du släpper ut till en obefintlig ström orsakas körningsund undantag.

### <a name="fields-grouping"></a>Gruppering av fält

Den inbyggda fältgrupperingen i Storm fungerar inte korrekt i SCP.NET. På Java-proxysidan är datatypen för alla fält faktiskt **byte[]**. Fältgrupperingen använder **byte[]** objektets hash-kod för att utföra gruppningen. The hash code is the address of this object in RAM. Så gruppningen blir fel för multibyte-objekt som delar samma innehåll men inte samma adress.

SCP.NET lägger till en anpassad grupperingsmetod och använder innehållet i **byte[]** objektet för att göra gruppningen. I en specifikationsfil ser syntaxen ut så här:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

I föregående specifikationsfil:

* `scp-field-group`anger att gruppningen är en anpassad fältgruppering som implementerats av SCP.
* `:tx`eller `:non-tx` anger om topologin är transaktionell. Du behöver den här informationen eftersom startindexet skiljer sig mellan transaktions- och icke-transaktionella topologier.
* `[0,1]`anger en hash-uppsättning fält-ID:er som börjar med noll.

### <a name="hybrid-topology"></a>Hybridtopologi

Native Storm-koden är skriven i Java. SCP.NET har förbättrat Storm så att du kan skriva C#-kod för att hantera din affärslogik. Men SCP.NET stöder också hybrid topologier, som innehåller inte bara C # pipar / bultar utan även Java pipar / bultar.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Ange Java-pip/bult i en specifikationsfil

Du kan använda **scp-pip** och **scp-bult** i en specifikationsfil för att ange Java-pipar och bultar. Här är ett exempel:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Här `microsoft.scp.example.HybridTopology.Generator` är namnet på Java-pipklassen.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Ange Java-klasssökvägen i ett runSpec-kommando

Om du vill skicka topologi som innehåller Java-pipar eller bultar sammanställer du dem först för att producera JAR-filer. Ange sedan den java-klassökväg som innehåller JAR-filerna när du skickar topologi. Här är ett exempel:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Här `examples\HybridTopology\java\target\` är mappen som innehåller Java pip / bult JAR-fil.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialisering och deserialisering mellan Java och C #

En SCP-komponent innehåller Java-sidan och C#-sidan. För att interagera med inhemska Java-pipar/bultar måste serialisering och avserialisering ske mellan Java-sidan och C#-sidan, vilket illustreras i följande diagram:

![Diagram över Java-komponent som skickar till SCP-komponent, som sedan skickar till en annan Java-komponent](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serialisering i Java-sidan och deserialization i C# sidan

Ange först standardimplementering för serialisering på Java-sidan och deserialisering på C#-sidan.

Ange Java-sidans serialiseringsmetod i en specifikationsfil.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Ange C#-sidans deserialiseringsmetod i C#-koden.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Om datatypen inte är för komplex bör den här standardimplementeringen hantera de flesta fall. Här är fall där du kan koppla in din egen implementering:

* Datatypen är för komplex för standardimplementeringen.
* Resultatet för standardimplementeringen uppfyller inte dina krav.

Serialiseringsgränssnittet på Java-sidan definieras som:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

Deserialiseringsgränssnittet på C#-sidan definieras som:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serialisering i C# sidan och deserialization i Java sidan

Ange C#-sidans serialiseringsmetod i C#-koden.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Ange Java-sidans deserialiseringsmetod i en specifikationsfil.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Här `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` är namnet på deserializer, `"microsoft.scp.example.HybridTopology.Person"` och är målet klass data avserialiseras till.

Du kan också koppla in din egen implementering av en C# serializer och en Java deserializer.

Den här koden är gränssnittet för C#serializer:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Denna kod är gränssnittet för Java deserializer:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Värdläge för SCP

I det här läget kan du kompilera koden som en DLL och använda SCPHost.exe enligt SCP för att skicka en topologi. En specifikationsfil ser ut som den här koden:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Här `"plugin.name"` anges som `"SCPHost.exe"`, som tillhandahålls av SCP SDK. SCPHost.exe accepterar tre parametrar i följande ordning:

1. DLL-namnet, som `"HelloWorld.dll"` finns i det här exemplet.
1. Klassnamnet, som `"Scp.App.HelloWorld.Generator"` finns i det här exemplet.
1. Namnet på en offentlig statisk metod, som kan anropas för att hämta en instans av **ISCPPlugin**.

I värdläge kompilerar du koden som en DLL för anrop av SCP-plattformen. Eftersom plattformen sedan kan få full kontroll över hela bearbetningslogiken rekommenderar vi att du skickar topologi i SCP-värdläge. Detta förenklar utvecklingsupplevelsen. Det ger dig också mer flexibilitet och bättre bakåtkompatibilitet för senare versioner.

## <a name="scp-programming-examples"></a>Exempel på SCP-programmering

### <a name="helloworld"></a>HelloWorld (en)

Följande enkla HelloWorld exempel visar en smak av SCP.NET. Den använder en nontransactional topologi med en pip som kallas **generator** och två bultar som kallas **splitter** och **räknare**. **Generatorn** pipen genererar slumpmässigt meningar och avger dessa meningar till **splitter**. Splitterbulten delar meningarna i ord och avger dessa ord till **splitter** **räknarbulten.** **Räknarbulten** använder en ordlista för att registrera förekomsten av varje ord.

Detta exempel har två specifikationsfiler: HelloWorld.spec och HelloWorld\_EnableAck.spec. C#-koden kan ta reda på om bekräftelse `pluginConf` är aktiverad genom att hämta objektet från Java-sidan.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Om bekräftelse är aktiverat i pipen cachelagrar en ordlista de tupplar som inte har bekräftats. Om `Fail` anropas spelas den misslyckade tuppeln upp.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx (på andra ort)

Följande HelloWorldTx exempel visar hur man genomför transaktionstopologi. Exemplet har en pip som kallas **generator,** en batchbult som kallas partiellt antal och en **commit-bult**som kallas **antal-summa**. Exemplet innehåller också tre befintliga textfiler: DataSource0.txt, DataSource1.txt och DataSource2.txt.

I varje transaktion väljer **generatorpipen** slumpmässigt två filer från de befintliga tre filerna och avger de två filnamnen till **delräkningsbulten.** Den **partiella räknebulten:**

1. Hämtar ett filnamn från den mottagna tuppeln.
1. Öppnar motsvarande fil.
1. Räknar antalet ord i filen.
1. Avger ordräkningen till **räknesumman.**

**Räkna-summa bult** sammanfattar det totala antalet.

För att uppnå exakt en gång semantik, **räkna-summa** begå bult måste bedöma om det är en replayed transaktion. I det här exemplet har den följande statiska medlemsvariabel:

```csharp
public static long lastCommittedTxId = -1; 
```

När en **ISCPBatchBolt-instans** skapas hämtas `txAttempt` värdet för objektet från indataparametrar.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

När `FinishBatch` anropas, `lastCommittedTxId` uppdateras om det inte är en repriserad transaktion.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Denna topologi innehåller en Java pip och en C # bult. Den använder standard-serialisering och deserialization implementering som tillhandahålls av SCP-plattformen. Se filen HybridTopology.spec i\\exemplen HybridTopology mapp för specifikation filinformation. Se också SubmitTopology.bat för hur du anger Java classpath.

### <a name="scphostdemo"></a>SCPHostDemo

Detta exempel är i huvudsak samma som HelloWorld. Den enda skillnaden är att koden kompileras som en DLL och topologin skickas med hjälp av SCPHost.exe. Mer detaljerad förklaring finns i avsnittet SCP-värdläge.

## <a name="next-steps"></a>Nästa steg

Exempel på Apache Storm-topologier som skapats med hjälp av SCP finns i följande artiklar:

* [Utveckla C# topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Bearbeta fordonssensordata från eventhubbar med Apache Storm på HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrahera, transformera och läsa in (ETL) från Azure Event Hubs till Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)

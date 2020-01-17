---
title: Programmerings guide för SCP.NET för storm i Azure HDInsight
description: Lär dig hur du använder SCP.NET för att skapa. NET-baserade Storm-topologier för användning med storm som körs i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: f462fd88acf04fc8dced3db739a555c371c184ab
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154490"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Programmerings guide för SCP för Apache Storm i Azure HDInsight

SCP är en plattform för att bygga real tids, tillförlitlig, konsekvent och högpresterande data behandlings program. Den är byggd ovanpå [Apache Storm](https://storm.incubator.apache.org/) – ett data ström bearbetnings system som har utformats av oss-communities. Storm har utformats av Nathan Marz och har öppnats av Twitter. Det utnyttjar [Apache ZooKeeper](https://zookeeper.apache.org/)ett annat Apache-projekt för att möjliggöra mycket tillförlitlig distribuerad samordning och tillstånds hantering.

Det är inte bara SCP-projektet som har hamnat storm i Windows, utan även projekt tillägg och anpassningar för Windows-eko systemet. Tilläggen omfattar .NET-utvecklare och-bibliotek, anpassningen innehåller Windows-baserad distribution.

Tillägget och anpassningen görs på ett sådant sätt att vi inte behöver förgrena in OSS-projekt och vi kan utnyttja härledda eko system som bygger på storm.

## <a name="processing-model"></a>Bearbetar modell

Data i SCP är modellerade som kontinuerliga strömmar av tupler. Vanligt vis flödar tupelarna till en kö först och sedan hämtas och omvandlas av affärs logiken i en Storm-topologi. Slutligen kan utdata bli skickas som tupler till ett annat SCP-system, eller allokeras till butiker som distribuerade fil system eller databaser som SQL Server.

![Ett diagram över en kös matnings data till bearbetning, som feeds innehåller ett data lager](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

I storm definierar en Programtopologi ett diagram över beräkning. Varje nod i en topologi innehåller bearbetnings logiken och länkar mellan noder indikerar data flödet. Noderna som används för att mata in indata i topologin kallas _kanaler_, som kan användas för att sekvensera data. Indata kan finnas i fil loggar, transaktions databas, system prestanda räknare och så vidare. Noderna med både indata och utdata för data flöden kallas _bultar_som utför den faktiska data filtreringen och de val och sammansättning.

SCP stöder bästa insatser, minst en gång och exakt en gång för data bearbetning. I ett program för distribuerad streaming-bearbetning kan olika fel inträffa under data bearbetning, till exempel nätverks avbrott, dator fel eller användar kod fel och så vidare. Vid minst en bearbetning ser du till att alla data bearbetas minst en gång genom att spela upp automatiskt samma data när fel inträffar. Minst en bearbetning är enkel och tillförlitlig och passar många program. Men när ett program kräver exakt inventering, är det inte tillräckligt med minst en bearbetning eftersom samma data kan spelas upp i programtopologin. I så fall är det exakt när bearbetningen har utformats för att se till att resultatet är korrekt även när data kan spelas upp och bearbetas flera gånger.

SCP gör det möjligt för .NET-utvecklare att utveckla real tids data bearbetnings program samtidigt som de utnyttjar Java Virtual Machine (JVM) med storm i omslag. .NET-och JVM kommunicerar via TCP-lokala Sockets. I princip är varje kanalen/bult ett .NET/Java-process par där användar logiken körs i .NET-processen som ett plugin-program.

För att bygga ett data bearbetnings program ovanpå SCP krävs flera steg:

* Utforma och implementera kanaler för att hämta data från kön.
* Utforma och implementera bultar för att bearbeta indata och spara data till externa lager, till exempel en databas.
* Utforma topologin och skicka och kör sedan topologin. Topologin definierar formhörn och data flödena mellan hörnen. SCP tar topologins specifikation och distribuerar den på ett Storm-kluster där varje hörn körs på en logisk nod. Redundansväxlingen och skalningen kommer att skötas av storm-Schemaläggaren.

Det här dokumentet använder några enkla exempel för att gå igenom hur du skapar data bearbetnings program med SCP.

## <a name="scp-plugin-interface"></a>SCP-plugin-gränssnitt

SCP-plugin-program (eller-program) är fristående EXEs som kan köras i Visual Studio under utvecklings fasen och vara anslutna till storm-pipelinen efter distributionen i produktionen. Att skriva SCP-plugin-programmet är bara detsamma som att skriva andra standard program för Windows-konsolen. SCP.NET-plattformen deklarerar vissa gränssnitt för kanalen/bult och användar-plugin-koden ska implementera dessa gränssnitt. Huvud syftet med den här designen är att användaren kan fokusera på sina egna affärs logiker och lämna andra saker som ska hanteras av SCP.NET-plattformen.

Användar-plugin-koden ska implementera något av följande gränssnitt, beroende på om topologin är transaktionell eller icke-transaktionell, och om komponenten är en kanalen eller en bult.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin

ISCPPlugin är det gemensamma gränssnittet för alla typer av plugin-program. För närvarande är det ett dummy-gränssnitt.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout

ISCPSpout är gränssnittet för icke-transaktionell kanalen.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

När `NextTuple()` anropas kan C# användar koden generera en eller flera tupler. Om det inte finns något att skicka, bör den här metoden returnera utan att skicka något. Det bör noteras att `NextTuple()`, `Ack()`och `Fail()` anropas i en tätt slinga i en enda tråd i C# processen. När det inte finns några tupleer att generera, är det courteous att ha NextTuple vilo läge under en kort tids period (till exempel 10 millisekunder) så att det inte tar onödigt mycket processor kraft.

`Ack()` och `Fail()` anropas bara när ack-mekanismen har Aktiver ATS i Specifikations filen. `seqId` används för att identifiera den tupel som har bekräftats eller misslyckats. Så om ack är aktiverat i icke-transaktionell topologi, ska följande genereringsområden användas i kanalen:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Om ack inte stöds i en icke-transaktionell topologi, kan `Ack()` och `Fail()` lämnas som Tom funktion.

Den `parms` Indataparametern i dessa funktioner är en tom ord lista, den är reserverad för framtida användning.

### <a name="iscpbolt"></a>ISCPBolt

ISCPBolt är gränssnittet för icke-transaktionell bult.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

När ny tupel är tillgänglig anropas funktionen `Execute()` för att bearbeta den.

### <a name="iscptxspout"></a>ISCPTxSpout

ISCPTxSpout är gränssnittet för transaktions kanalen.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Precis som icke-transaktionella räknar-, `NextTx()`, `Ack()`och `Fail()` anropas i en tätt slinga i en enda tråd i C# processen. När det inte finns några data att generera, är det courteous att `NextTx` vilo läge under en kort tids period (10 millisekunder) så att det inte tar onödigt mycket CPU.

`NextTx()` anropas för att starta en ny transaktion, används parametern `seqId` för att identifiera transaktionen, som också används i `Ack()` och `Fail()`. I `NextTx()`kan användaren generera data till Java-sidan. Data lagras i ZooKeeper för att ge stöd för uppspelning. Eftersom kapaciteten för ZooKeeper är begränsad ska användaren bara generera metadata, inte Mass data i transaktions kanalen.

Storm kommer att spela upp en transaktion automatiskt om det Miss lyckas, så `Fail()` bör inte anropas i normal fallet. Men om SCP: n kan kontrol lera de metadata som genereras av transaktionell kanalen, kan det anropa `Fail()` när metadata är ogiltiga.

Den `parms` Indataparametern i dessa funktioner är en tom ord lista, den är reserverad för framtida användning.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

ISCPBatchBolt är gränssnittet för transaktions bult.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` anropas när det finns ny tupel som kommer till blixten. `FinishBatch()` anropas när den här transaktionen avslutas. Parametern `parms` indataparametrar är reserverad för framtida användning.

För transaktionell topologi finns det ett viktigt begrepp – `StormTxAttempt`. Det finns två fält `TxId` och `AttemptId`. `TxId` används för att identifiera en specifik transaktion och för en specifik transaktion kan det finnas flera försök om transaktionen Miss lyckas och spelas upp. SCP.NET skapar ett nytt ISCPBatchBolt-objekt för att bearbeta varje `StormTxAttempt`, precis som vad Storm gör i Java. Syftet med den här designen är att stödja bearbetning av parallella transaktioner. Användaren bör vara medveten om att om transaktions försöket är klart, förstörs motsvarande ISCPBatchBolt-objekt och skräp insamlas.

## <a name="object-model"></a>Objekt modell

SCP.NET innehåller också en enkel uppsättning viktiga objekt som utvecklare kan använda för att program mera. De är **kontext**, **StateStore**och **SCPRuntime**. De beskrivs i resten av det här avsnittet.

### <a name="context"></a>Kontext

Kontexten är en miljö som körs för programmet. Varje ISCPPlugin-instans (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) har en motsvarande kontext instans. De funktioner som tillhandahålls av kontexten kan delas in i två delar: (1) den statiska delen, som är tillgänglig i C# hela processen, (2) den dynamiska delen, som endast är tillgänglig för den speciella kontext instansen.

### <a name="static-part"></a>Statisk del

    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` tillhandahålls för logg ändamål.

`pluginType` används för att ange C# processens plugin-typ. Om C# processen körs i det lokala testläget (utan Java) är plugin-typen `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` tillhandahålls för att hämta konfigurations parametrar från Java-sidan. Parametrarna skickas från Java-sidan när C# plugin-programmet initieras. `Config` parametrar är uppdelade i två delar: `stormConf` och `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` är parametrar som definieras av storm och `pluginConf` är de parametrar som definierats av SCP. Ett exempel:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` har angetts för att hämta topologins kontext, är det mest användbart för komponenter med flera paralleller. Här är ett exempel:

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

### <a name="dynamic-part"></a>Dynamisk del

Följande gränssnitt är relevanta för en viss kontext instans. Kontext instansen skapas av SCP.NET-plattformen och skickas till användar koden:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

För icke-transaktionella kanalen som stöder ack anges följande metod:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

För icke-transaktionell bult som stöder ack bör det uttryckligen `Ack()` eller `Fail()` den tupel som tagits emot. Och vid sändning av ny tupel måste även den nya tupelens ankare anges. Följande metoder har angetts.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore

`StateStore` tillhandahåller metadata-tjänster, enkel, enkel sekvensers generering och en väntande samordning. Distribuerade samtidiga samtidiga concurrency-abstraktioner kan skapas på `StateStore`, inklusive distribuerade lås, distribuerade köer, barriärer och transaktions tjänster.

SCP-program kan använda `State`-objektet för att bevara viss information i [Apache ZooKeeper](https://zookeeper.apache.org/), särskilt för transaktionell topologi. Om transaktionell kanalen kraschar och startas om, kan den hämta nödvändig information från ZooKeeper och starta om pipelinen.

`StateStore`-objektet har huvudsakligen följande metoder:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

`State`-objektet har huvudsakligen följande metoder:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

Om simpleMode har angetts till true för metoden `Commit()`, tas motsvarande ZNode bort i ZooKeeper. Annars tar den bort den aktuella ZNode och lägger till en ny nod i den ALLOKERAde\_sökvägen.

### <a name="scpruntime"></a>SCPRuntime

SCPRuntime tillhandahåller följande två metoder:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` används för att initiera SCP-körnings miljön. I den här metoden ansluter C# processen till Java-sidan och hämtar konfigurations parametrar och topologi-kontext.

`LaunchPlugin()` används för att starta bearbetnings slingen för meddelanden. I den här slingan C# tar plugin-programmet emot meddelande formulär Java-sida (inklusive tupler och kontroll signaler) och bearbetar sedan meddelandena, kanske anropar-gränssnitts metoden som anger användar koden. Indataparametern för metoden `LaunchPlugin()` är en delegat som kan returnera ett objekt som implementerar ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt-gränssnittet.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

För ISCPBatchBolt kan vi få `StormTxAttempt` från `parms`och använda det för att bedöma om det är ett omspelat försök. Kontrollen av ett omuppspelnings försök görs ofta i commit-blixten och visas i `HelloWorldTx` exemplet.

I allmänhet kan SCP-plugin-program köras i två lägen:

1. Lokalt test läge: i det här läget körs SCP-plugin- C# programmet (användar koden) i Visual Studio under utvecklings fasen. `LocalContext` kan användas i det här läget, vilket ger metoden att serialisera de utgivna tupelarna till lokala filer och läsa tillbaka dem till minnet.

        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }

2. Normalt läge: i det här läget lanseras SCP-plugin-program av storm Java-processen.

    Här är ett exempel på att starta SCP-plugin:

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

## <a name="topology-specification-language"></a>Språk för topologi specifikation

Specifikationen av SCP-topologin är ett domänanslutet språk för att beskriva och konfigurera SCP-topologier. Den baseras på Storms clojure DSL (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) och utökas av SCP.

Topology-specifikationer kan skickas direkt till Storm-kluster för körning via ***runspec*** -kommandot.

SCP.NET har lagt till följande funktioner för att definiera transaktionella topologier:

| Nya funktioner | Parametrar | Beskrivning |
| --- | --- | --- |
| TX-topolopy |topologi-namn<br />kanalen – mappa<br />bult-karta |Definiera en transaktionell topologi med topologins namn, &nbsp;kanaler definitions karta och bultar definitions karta |
| SCP-TX-kanalen |exec-namn<br />args<br />fält |Definiera en transaktionell kanalen. Den kör programmet med ett ***exec-namn*** med hjälp av ***argument***.<br /><br />***Fälten*** är utdatakolumner för kanalen |
| SCP-TX-batch-bult |exec-namn<br />args<br />fält |Definiera en transaktionell batch-bult. Den kör programmet med ett ***exec-namn*** med hjälp av ***argument.***<br /><br />Fälten är utmatnings fält för bult. |
| SCP-TX-commit-bult |exec-namn<br />args<br />fält |Definiera en transaktionell commit-bult. Den kör programmet med ett ***exec-namn*** med hjälp av ***argument***.<br /><br />***Fälten*** är utmatnings fält för bult |
| nontx-topolopy |topologi-namn<br />kanalen – mappa<br />bult-karta |Definiera en transaktionell topologi med topologins namn,&nbsp; kanaler definitions karta och bultar definitions karta |
| SCP – kanalen |exec-namn<br />args<br />fält<br />parameters |Definiera en kanalen som inte är en transaktion. Den kör programmet med ett ***exec-namn*** med hjälp av ***argument***.<br /><br />***Fälten*** är utdatakolumner för kanalen<br /><br />***Parametrarna*** är valfria och använder den för att ange vissa parametrar, till exempel "icke-transaktion. ack. enabled". |
| SCP-bult |exec-namn<br />args<br />fält<br />parameters |Definiera en ej transaktions bult. Den kör programmet med ett ***exec-namn*** med hjälp av ***argument***.<br /><br />***Fälten*** är utmatnings fält för bult<br /><br />***Parametrarna*** är valfria och använder den för att ange vissa parametrar, till exempel "icke-transaktion. ack. enabled". |

SCP.NET har följande definierade nyckelord:

| Nyckelord | Beskrivning |
| --- | --- |
| : namn |Definiera Topology-namnet |
| : topologi |Definiera topologin med hjälp av de tidigare funktionerna och skapa i dem. |
| :p |Definiera parallellt tips för varje kanalen eller bult. |
| : config |Definiera parametern konfigurera eller uppdatera befintliga |
| : schema |Definiera schemat för Stream. |

Parametrar som används ofta:

| Parameter | Beskrivning |
| --- | --- |
| "plugin.name" |exe-filnamn för C# plugin-programmet |
| "plugin. args" |plugin-argument |
| "output. schema" |Schema för utdata |
| "transaktionell. ack. enabled" |Om ack är aktiverat för en transaktionell topologi |

Runspec-kommandot distribueras tillsammans med bitarna, och användningen är som:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

The ***resource-dir*** parameter is optional, you need to specify it when you want to plug a C# application, and this directory contains the application, the dependencies, and configurations.

The ***classpath*** parameter is also optional. It's used to specify the Java classpath if the spec file contains Java Spout or Bolt.

## <a name="miscellaneous-features"></a>Miscellaneous Features

### <a name="input-and-output-schema-declaration"></a>Input and Output Schema Declaration

Users can emit tuples in C# processes, the platform needs to serialize the tuple into byte[], transfer to Java side, and Storm will transfer this tuple to the targets. Meanwhile in downstream components, C# processes will receive tuples back from java side, and convert it to the original types by platform, all these operations are hidden by the Platform.

To support the serialization and deserialization, user code needs to declare the schema of the inputs and outputs.

The input/output stream schema is defined as a dictionary. The key is the StreamId. The value is the Types of the columns. The component can have multi-streams declared.

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


In Context object, we have the following API added:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Developers must ensure that the tuples emitted obey the schema defined for that stream, otherwise the system will throw a runtime exception.

### <a name="multi-stream-support"></a>Multi-Stream Support

SCP supports user code to emit or receive from multiple distinct streams at the same time. The support reflects in the Context object as the Emit method takes an optional stream ID parameter.

Two methods in the SCP.NET Context object have been added. They're used to emit Tuple or Tuples to specify StreamId. The StreamId is a string and it needs to be consistent in both C# and the Topology Definition Spec.

    /* Emit tuple to the specific stream. */
    public abstract void Emit(string streamId, List<object> values);

    /* for non-transactional Spout only */
    public abstract void Emit(string streamId, List<object> values, long seqId);

The emitting to a non-existing stream causes runtime exceptions.

### <a name="fields-grouping"></a>Fields Grouping

The built-in Fields Grouping in Storm isn't working properly in SCP.NET. On the Java Proxy side, all the fields data types are actually byte[], and the fields grouping uses the byte[] object hash code to perform the grouping. The byte[] object hash code is the address of this object in memory. So the grouping will be wrong for two-byte objects that share the same content but not the same address.

SCP.NET adds a customized grouping method, and it uses the content of the byte[] to do the grouping. In **SPEC** file, the syntax is like:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )

Here,

1. "scp-field-group" means "Customized field grouping implemented by SCP".
2. ":tx" or ":non-tx" means if it’s transactional topology. We need this information since the starting index is different in tx vs. non-tx topologies.
3. [0,1] means a hash set of field Ids, starting from 0.

### <a name="hybrid-topology"></a>Hybrid topology

The native Storm is written in Java. And SCP.NET has enhanced it to enable C# developers to write C# code to handle their business logic. But it also supports hybrid topologies, which contain not only C# spouts/bolts, but also Java Spout/Bolts.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Specify Java Spout/Bolt in spec file

In spec file, "scp-spout" and "scp-bolt" can also be used to specify Java Spouts and Bolts, here is an example:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Here `microsoft.scp.example.HybridTopology.Generator` is the name of the Java Spout class.

### <a name="specify-java-classpath-in-runspec-command"></a>Specify Java Classpath in runSpec Command

If you want to submit topology containing Java Spouts or Bolts, you need to first compile the Java Spouts or Bolts and get the Jar files. Then you should specify the java classpath that contains the Jar files when submitting topology. Här är ett exempel:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Here **examples\\HybridTopology\\java\\target\\** is the folder containing the Java Spout/Bolt Jar file.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialization and Deserialization between Java and C#

SCP component includes Java side and C# side. In order to interact with native Java Spouts/Bolts, Serialization/Deserialization must be carried out between Java side and C# side, as illustrated in the following graph.

![diagram of java component sending to SCP component sending to Java component](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. Serialization in Java side and Deserialization in C# side

   First we provide default implementation for serialization in Java side and deserialization in C# side. The serialization method in Java side can be specified in SPEC file:

       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })

   The deserialization method in C# side should be specified in C# user code:

       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            

   This default implementation should handle most cases provided the data type isn't too complex. For certain cases, either because the user data type is too complex, or because the performance of our default implementation doesn't meet the user's requirement, users can plug in their own implementation.

   The serialize interface in java side is defined as:

       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }

   The deserialize interface in C# side is defined as:

   public interface ICustomizedInteropCSharpDeserializer

       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. Serialization in C# side and Deserialization in Java side

   The serialization method in C# side should be specified in C# user code:

       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 

   Avserialiserings metoden i Java-sidan ska anges i SPECIFIKATIONs filen:

    ```
    (scp-spout
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       }
    )
    ```

   Här "Microsoft. SCP. Storm. Multilang. CustomizedInteropJSONDeserializer" är namnet på deserialiseraren och "Microsoft. SCP. example. HybridTopology. person" är mål klassen som data deserialiseras till.

   Användaren kan också ansluta till en egen implementering av C# serialiseraren och Java-deserialiseraren. Den här koden är gränssnittet för C# serialiserare:

       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }

   Den här koden är gränssnittet för Java-deserialisering:

       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Värd läge för SCP

I det här läget kan användaren kompilera sina koder till DLL och använda SCPHost. exe som tillhandahålls av SCP för att skicka topologi. Specifikations filen ser ut så här:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Här anges `plugin.name` som `SCPHost.exe` tillhandahålls av SCP SDK. SCPHost. exe accepterar tre parametrar:

1. Det första är DLL-namnet, som är `"HelloWorld.dll"` i det här exemplet.
2. Den andra är klass namnet, som är `"Scp.App.HelloWorld.Generator"` i det här exemplet.
3. Det tredje är namnet på en offentlig statisk metod som kan anropas för att hämta en instans av ISCPPlugin.

I värd läge kompileras användar kod som DLL och anropas av SCP-plattformen. Därför kan SCP-plattformen få fullständig kontroll över hela bearbetnings logiken. Vi rekommenderar att våra kunder skickar topologi i värd läget för SCP eftersom det kan förenkla utvecklings upplevelsen och göra oss mer flexibel och bättre bakåtkompatibilitet för senare versioner.

## <a name="scp-programming-examples"></a>Programmerings exempel för SCP

### <a name="helloworld"></a>HelloWorld

**HelloWorld** är ett enkelt exempel för att visa en smak på SCP.net. Den använder en icke-transaktionell topologi, med en kanalen som kallas **Generator**och två bultar som kallas **delare** och **räknare**. Kanalen **Generator** skapar slumpmässigt meningar och genererar dessa meningar till **delare**. Delnings listen för bult * * delar upp meningarna i ord och avger dessa ord till **Counter** bult. Bult "Counter" använder en ord lista för att registrera förekomst numret för varje ord.

Det finns två spec-filer, **HelloWorld. spec** och **HelloWorld\_EnableAck. spec** i det här exemplet. I C# koden kan den ta reda på om ack är aktiverat genom att hämta PluginConf från Java-sidan.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Om ack är aktiverat i kanalen används en ord lista för att cachelagra de tupler som inte har bekräftats. Om fel () anropas spelas den felaktiga tuppeln upp:

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

### <a name="helloworldtx"></a>HelloWorldTx

**HelloWorldTx** -exemplet visar hur du implementerar en transaktionell topologi. Den har en kanalen som kallas **Generator**, en batch-bult som kallas för **partiellt antal**och en commit bult som heter **Count-sum**. Det finns också tre i förväg skapade txt-filer: **DataSource0. txt**, **DataSource1. txt**och **DataSource2. txt**.

I varje transaktion väljer kanalen- **Generator** slumpmässigt två filer från de i förväg skapade tre filerna och skickar de två fil namnen till den **partiella-Count-** bulten. Funktionen i **delar** av bult får fil namnet från den mottagna tuppeln och öppnar sedan filen och räknar antalet ord i filen och genererar sedan ordet Number till **Count-sum-** bult. **Count-sum-** blixten sammanfattar det totala antalet.

Om du vill uppnå **exakt en** semantik måste du bedöma om det är en upprepad transaktion genom att använda commit bult **-Sum** . I det här exemplet har den en statisk medlems variabel:

    public static long lastCommittedTxId = -1; 

När en ISCPBatchBolt-instans skapas hämtar den `txAttempt` från indataparametrar:

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

När `FinishBatch()` anropas kommer `lastCommittedTxId` att uppdateras om det inte är en omspelad transaktion.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }

### <a name="hybridtopology"></a>HybridTopology

Den här topologin innehåller en Java- C# kanalen och en bult. Den använder den standardinställda serialisering och deserialiserings-implementering som tillhandahålls av SCP-plattformen. Se **HybridTopology. spec** i **exempel\\HybridTopology** -mappen för information om Specifikations filen och **SubmitTopology. bat** för att ange Java-classpath.

### <a name="scphostdemo"></a>SCPHostDemo

Det här exemplet är samma som HelloWorld i grunden. Den enda skillnaden är att användar koden kompileras som DLL och att topologin skickas med hjälp av SCPHost. exe. Se avsnittet "SCP-värd läge" för mer detaljerad förklaring.

## <a name="next-steps"></a>Efterföljande moment

Exempel på Apache Storm topologier som skapats med SCP finns i följande dokument:

* [Utveckla C# topologier för Apache storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Bearbeta fordons sensor data från Event Hubs med Apache Storm i HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrahera, transformera och läsa in (ETL) från Azure Event Hubs till Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)

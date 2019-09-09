---
title: Programmerings guide för SCP.NET för storm i Azure HDInsight
description: Lär dig hur du använder SCP.NET för att skapa. NET-baserade Storm-topologier för användning med storm som körs i Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: b7bb26cd35daf67a3337907aded18e3302b19d81
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70813881"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Programmerings guide för SCP för Apache Storm i Azure HDInsight

SCP är en plattform för att bygga real tids, tillförlitlig, konsekvent och högpresterande data behandlings program. Den är byggd ovanpå [Apache Storm](https://storm.incubator.apache.org/) – ett data ström bearbetnings system som har utformats av oss-communities. Storm har utformats av Nathan Marz och har öppnats av Twitter. Det utnyttjar [Apache ZooKeeper](https://zookeeper.apache.org/)ett annat Apache-projekt för att möjliggöra mycket tillförlitlig distribuerad samordning och tillstånds hantering. 

Det är inte bara SCP-projektet som har hamnat storm i Windows, utan även projekt tillägg och anpassningar för Windows-eko systemet. Tilläggen omfattar .NET-utvecklare och-bibliotek, anpassningen innehåller Windows-baserad distribution. 

Tillägget och anpassningen görs på ett sådant sätt att vi inte behöver förgrena in OSS-projekt och vi kan utnyttja härledda eko system som bygger på storm.

## <a name="processing-model"></a>Bearbetar modell
Data i SCP är modellerade som kontinuerliga strömmar av tupler. Vanligt vis flödar tupelarna till en kö först och sedan hämtas och omvandlas av affärs logiken i en Storm-topologi. Slutligen kan utdata bli skickas som tupler till ett annat SCP-system, eller allokeras till butiker som distribuerade fil system eller databaser som SQL Server.

![Ett diagram över en kös matnings data till bearbetning, som feeds innehåller ett data lager](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

I storm definierar en Programtopologi ett diagram över beräkning. Varje nod i en topologi innehåller bearbetnings logiken och länkar mellan noder indikerar data flödet. Noderna som används för att mata in indata i topologin kallas _kanaler_, som kan användas för att sekvensera data. Indata kan finnas i fil loggar, transaktions databas, system prestanda räknare osv. Noderna med både indata och utdata för data flöden kallas _bultar_som utför den faktiska data filtreringen och de val och sammansättning.

SCP stöder bästa insatser, minst en gång och exakt en data bearbetning. I ett program för distribuerad strömnings bearbetning kan olika fel inträffa under data bearbetning, till exempel nätverks avbrott, dator fel eller fel i användar kod osv. Vid minst en bearbetning ser du till att alla data bearbetas minst en gång genom att spela upp automatiskt samma data när fel inträffar. Minst en bearbetning är enkel och tillförlitlig och passar bra för många program. Men när ett program kräver exakt inventering, är det inte tillräckligt med minst en bearbetning eftersom samma data kan spelas upp i programtopologin. I så fall är bearbetningen av exakt en gång utformad för att säkerställa att resultatet är korrekt även när data kan spelas upp och bearbetas flera gånger.

SCP gör det möjligt för .NET-utvecklare att utveckla real tids data process program samtidigt som de utnyttjar Java Virtual Machine (JVM) med storm i omslag. .NET-och JVM kommunicerar via TCP-lokala Sockets. I princip är varje kanalen/bult ett .NET/Java-process par där användar logiken körs i .NET-processen som ett plugin-program.

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

När `NextTuple()` anropas, kan C\# -användar koden generera en eller flera tupler. Om det inte finns något att generera, ska den här metoden returneras utan att skicka något. Det bör noteras att `NextTuple()`, `Ack()`och `Fail()` alla anropas i en tätt slinga i en enda tråd i C\# -process. När det inte finns några tupleer att generera, är det courteous att ha NextTuple vilo läge under en kort tids period (till exempel 10 millisekunder) så att det inte tar onödigt mycket CPU.

`Ack()`och `Fail()` anropas bara när ack-mekanismen är aktive rad i Specifikations filen. `seqId` Används för att identifiera den tupel som har bekräftats eller misslyckats. Så om ack är aktiverat i icke-transaktionell topologi, ska följande genereringsområden användas i kanalen:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Om ack inte stöds i en icke-transaktionell topologi, `Ack()` kan och `Fail()` lämnas som Tom funktion.

`parms` Indataparametern i dessa funktioner är en tom ord lista, den är reserverad för framtida användning.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt är gränssnittet för icke-transaktionell bult.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

När en ny tupel är tillgänglig `Execute()` anropas funktionen för att bearbeta den.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout är gränssnittet för transaktions kanalen.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Precis som deras icke-transaktionella `NextTx()`räknare-del, `Ack()`, och `Fail()` kallas alla i en tätt slinga i en enda tråd i C\# -process. Om det inte finns några data att generera, är det courteous att `NextTx` ha en kort tid (10 millisekunder) så att det inte tar onödigt mycket CPU.

`NextTx()`anropas för att starta en ny transaktion. parametern `seqId` out används för att identifiera transaktionen, som också används i `Ack()` och `Fail()`. I `NextTx()`kan användaren generera data till Java-sidan. Data lagras i ZooKeeper för att ge stöd för uppspelning. Eftersom kapaciteten för ZooKeeper är begränsad ska användaren bara generera metadata, inte Mass data i transaktions kanalen.

Storm kommer att spela upp en transaktion automatiskt om det Miss lyckas `Fail()` , så ska inte anropas i normal fallet. Men om SCP: n kan kontrol lera de metadata som genereras av transaktionell kanalen, kan `Fail()` det anropas när metadata är ogiltiga.

`parms` Indataparametern i dessa funktioner är en tom ord lista, den är reserverad för framtida användning.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt är gränssnittet för transaktions bult.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()`anropas när det finns en ny tupel som anländer till bulten. `FinishBatch()`anropas när den här transaktionen avslutas. `parms` Indataparametern är reserverad för framtida användning.

Det finns ett viktigt begrepp för transaktionella topologier – `StormTxAttempt`. Det har två fält `TxId` och. `AttemptId` `TxId`används för att identifiera en specifik transaktion och för en angiven transaktion kan det finnas flera försök om transaktionen Miss lyckas och spelas upp. SCP.net skapar ett nytt ISCPBatchBolt-objekt för att `StormTxAttempt`bearbeta var och en, precis som vad Storm gör i Java. Syftet med den här designen är att stödja bearbetning av parallella transaktioner. Användaren bör vara medveten om att om transaktions försöket är klart, förstörs motsvarande ISCPBatchBolt-objekt och skräp insamlas.

## <a name="object-model"></a>Objekt modell
SCP.NET innehåller också en enkel uppsättning viktiga objekt som utvecklare kan använda för att program mera. De är **kontext**, **StateStore**och **SCPRuntime**. De beskrivs i resten av det här avsnittet.

### <a name="context"></a>Kontext
Kontexten är en miljö som körs för programmet. Varje ISCPPlugin-instans (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) har en motsvarande kontext instans. De funktioner som tillhandahålls av kontexten kan delas in i två delar: (1) den statiska delen, som är tillgänglig i hela C\# -processen, (2) den dynamiska delen, som endast är tillgänglig för den speciella kontext instansen.

### <a name="static-part"></a>Statisk del
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger`tillhandahålls för logg ändamål.

`pluginType`används för att ange plugin-typen för C\# -processen. Om C\# -processen körs i lokalt test läge (utan Java) är `SCP_NET_LOCAL`plugin-typen.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config`tillhandahålls för att hämta konfigurations parametrar från Java-sidan. Parametrarna skickas från Java-sidan när C\# -plugin-programmet initieras. Parametrarna är indelade i två delar `stormConf` : `pluginConf`och. `Config`

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf`är parametrar som definieras av storm `pluginConf` och är de parametrar som definierats av SCP. Exempel:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext`har angetts för att hämta topologins kontext, är det mest användbart för komponenter med flera paralleller. Här är ett exempel:

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

För icke-transaktionell bult som stöder ack bör den explicit `Ack()` eller `Fail()` den tupel som tagits emot. Och vid sändning av ny tupel måste även den nya tupelens ankare anges. Följande metoder har angetts.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore`tillhandahåller metadata-tjänster, enkel och dubbelriktad sekvens-generering och en väntande fri samordning. Överordnad samtidighet för samtidighet kan byggas på `StateStore`, inklusive distribuerade lås, distribuerade köer, barriärer och transaktions tjänster.

SCP-program kan använda `State` objektet för att bevara viss information i [Apache ZooKeeper](https://zookeeper.apache.org/), särskilt för transaktionell topologi. Om transaktionell kanalen kraschar och startas om, kan den hämta nödvändig information från ZooKeeper och starta om pipelinen.

`StateStore` Objektet har huvudsakligen följande metoder:

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

`State` Objektet har huvudsakligen följande metoder:

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

Om simpleMode har angetts till true för metoden,tasmotsvarandeZNodebortiZooKeeper.`Commit()` Annars tar den bort den aktuella ZNode och lägger till en ny nod i den allokerade\_sökvägen.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime tillhandahåller följande två metoder:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()`används för att initiera SCP-körnings miljön. I den här metoden ansluter C\# -processen till Java-sidan och hämtar konfigurations parametrar och topologi-kontext.

`LaunchPlugin()`används för att starta en slinga för meddelande bearbetning. I den här slingan tar\# C-plugin-programmet emot meddelanden från Java-sida (inklusive tupler och kontroll signaler) och bearbetar sedan meddelandena, som kanske anropar-gränssnitts metoden som anger användar koden. Indataparametern för metoden `LaunchPlugin()` är en delegat som kan returnera ett objekt som implementerar ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt-gränssnittet.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

För ISCPBatchBolt kan vi hämta `StormTxAttempt` från `parms`och använda det för att bedöma om det är ett omspelat försök. Kontrollen av ett omuppspelnings försök görs ofta i commit-blixten och visas i `HelloWorldTx` exemplet.

I allmänhet kan SCP-plugin-program köras i två lägen:

1. Lokalt test läge: I det här läget körs SCP-plugin-program\# (C-användar koden) i Visual Studio under utvecklings fasen. `LocalContext`kan användas i det här läget, vilket ger metoden att serialisera de utgivna tupelarna till lokala filer och läsa tillbaka dem till minnet.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Normalt läge: I det här läget lanseras SCP-plugin-program av storm Java-processen.
   
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

| **Nya funktioner** | **Parametrar** | **Beskrivning** |
| --- | --- | --- |
| **TX-topolopy** |topologi-namn<br />kanalen – mappa<br />bult-karta |Definiera en transaktionell topologi med topologins namn, &nbsp;kanaler definitions karta och bultens definitions karta |
| **scp-tx-spout** |exec-namn<br />args<br />fält |Definiera en transaktionell kanalen. Den kör programmet med ett ***exec-namn*** med hjälp av ***argument***.<br /><br />***Fälten*** är utdatakolumner för kanalen |
| **scp-tx-batch-bolt** |exec-namn<br />args<br />fält |Definiera en transaktionell batch-bult. Den kör programmet med ett ***exec-namn*** med hjälp av ***argument.***<br /><br />Fälten är utmatnings fält för bult. |
| **SCP-TX-commit-bult** |exec-namn<br />args<br />fält |Definiera en transaktionell commit-bult. Den kör programmet med ett ***exec-namn*** med hjälp av ***argument***.<br /><br />***Fälten*** är utmatnings fält för bult |
| **nontx-topolopy** |topologi-namn<br />kanalen – mappa<br />bult-karta |Definiera en transaktionell topologi med topologins namn,&nbsp; kanaler definitions karta och bultens definitions karta |
| **scp-spout** |exec-namn<br />args<br />fält<br />parameters |Definiera en kanalen som inte är en transaktion. Den kör programmet med ett ***exec-namn*** med hjälp av ***argument***.<br /><br />***Fälten*** är utdatakolumner för kanalen<br /><br />***Parametrarna*** är valfria och använder den för att ange vissa parametrar, till exempel "icke-transaktion. ack. enabled". |
| **scp-bolt** |exec-namn<br />args<br />fält<br />parameters |Definiera en ej transaktions bult. Den kör programmet med ett ***exec-namn*** med hjälp av ***argument***.<br /><br />***Fälten*** är utmatnings fält för bult<br /><br />***Parametrarna*** är valfria och använder den för att ange vissa parametrar, till exempel "icke-transaktion. ack. enabled". |

SCP.NET har följande definierade nyckelord:

| **nyckelord** | **Beskrivning** |
| --- | --- |
| **: namn** |Definiera Topology-namnet |
| **: topologi** |Definiera topologin med hjälp av de tidigare funktionerna och skapa i dem. |
| **:p** |Definiera parallellt tips för varje kanalen eller bult. |
| **:config** |Definiera parametern konfigurera eller uppdatera befintliga |
| **: schema** |Definiera schemat för Stream. |

Parametrar som används ofta:

| **Parametern** | **Beskrivning** |
| --- | --- |
| **"plugin.name"** |exe-filnamn för C# plugin-programmet |
| **"plugin.args"** |plugin-argument |
| **"output.schema"** |Schema för utdata |
| **"nontransactional.ack.enabled"** |Om ack är aktiverat för en transaktionell topologi |

Runspec-kommandot distribueras tillsammans med bitarna, och användningen är som:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Parametern ***resurs-Kat*** är valfri, du måste ange den när du vill ansluta ett C\# -program, och den här katalogen innehåller programmet, beroenden och konfigurationer.

***Classpath*** -parametern är också valfri. Den används för att ange Java-classpath om Specifikations filen innehåller Java-kanalen eller bult.

## <a name="miscellaneous-features"></a>Diverse funktioner
### <a name="input-and-output-schema-declaration"></a>Schema deklaration för indata och utdata
Användare kan generera tupler i C\# -processer, plattformen måste serialisera tuppeln till byte [], överföra till Java-sida och Storm överför denna tupel till målen. I underordnade komponenter kommer C\# -processer att ta emot tupler tillbaka från Java-sidan och konvertera den till de ursprungliga typerna efter plattform. alla dessa åtgärder döljs av plattformen.

För att stödja serialisering och deserialisering måste användar koden deklarera schemat för indata och utdata.

Schemat för indata/utdata-ström definieras som en ord lista. Nyckeln är StreamId. Värdet är kolumnernas typer. Komponenten kan ha deklarerats med flera data strömmar.

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


I context-objekt har vi följande API tillagt:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Utvecklare måste se till att tupelarna som genereras följer schemat som definierats för den data strömmen, annars kommer systemet att utlösa ett körnings undantag.

### <a name="multi-stream-support"></a>Stöd för flera data strömmar
SCP stöder användar kod för att skicka eller ta emot från flera distinkta strömmar samtidigt. Supporten reflekteras i context-objektet eftersom genereringsområden tar en valfri Stream ID-parameter.

Två metoder i SCP.NET-kontextens objekt har lagts till. De används för att generera tupel eller tupler för att ange StreamId. StreamId är en sträng och måste vara konsekvent i både C\# -och topologins definitions specifikation.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Sändningen till en icke-befintlig data ström orsakar körnings undantag.

### <a name="fields-grouping"></a>Fält grupper
De inbyggda fälten gruppering i storm fungerar inte korrekt i SCP.NET. På Java-proxyservern är alla fält data typerna faktiskt byte [], och fälten gruppering använder byte []-objektets hash-kod för att utföra grupperingen. Hash-koden för objektet byte [] är adressen till det här objektet i minnet. Därför är grupperingen av fel för två byte []-objekt som delar samma innehåll men inte samma adress.

SCP.NET lägger till en anpassad grupperingstyp och använder innehållet i byte [] för att gruppera. I **Specifikations** filen liknar syntaxen:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Här

1. "SCP-Field-Group" innebär "anpassad fält grupp som implementeras av SCP".
2. ": TX" eller ": icke-TX" innebär att om det är en transaktionell topologi. Vi behöver den här informationen eftersom start indexet skiljer sig i överföring jämfört med icke-TX-topologier.
3. [0, 1] innebär en hash-uppsättning av fält-ID: n, med början från 0.

### <a name="hybrid-topology"></a>Hybrid topologi
Den ursprungliga stormen skrivs i Java. Och SCP.net har förbättrat det för att göra\# det möjligt för c\# -utvecklare att skriva c-kod för att hantera sin affärs logik. Men det stöder även hybrid topologier som inte bara innehåller C\# -kanaler/-bultar, utan även Java-kanalen/bultar.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Ange Java-kanalen/bult i Specifikations filen
I Specifikations filen kan "SCP-kanalen" och "SCP-bult" också användas för att ange Java-kanaler och bultar. här är ett exempel:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Här `microsoft.scp.example.HybridTopology.Generator` är namnet på Java kanalen-klassen.

### <a name="specify-java-classpath-in-runspec-command"></a>Ange Java-classpath i runSpec-kommandot
Om du vill skicka en topologi som innehåller Java-kanaler eller bultar måste du först kompilera Java-kanaler eller-bultarna och hämta jar-filerna. Ange sedan den Java-classpath som innehåller jar-filerna när topologin skickas. Här är ett exempel:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Här **är\\exempel\\HybridTopologyJava\\target\\**  : mappen som innehåller jar-filen för Java-kanalen/bult.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialisering och deserialisering mellan Java och C\#
SCP-komponenten inkluderar Java sida och\# C-sida. För att interagera med interna Java-kanaler/-bultar måste serialisering/deserialisering utföras mellan Java-sida och C\# -sidan, vilket visas i följande diagram.

![diagram över Java-komponent som skickar till en SCP-komponent som skickar till Java-komponenten](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serialisering i Java-sida och deserialisering på C\# -Sidan**
   
   Först tillhandahåller vi standard implementering för serialisering i Java-sida och deserialisering på C\# -sidan. Serialiserings metoden i Java-sidan kan anges i SPECIFIKATIONs filen:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   Avserialiserings metoden i c\# -sidan ska anges i c\# -användar koden:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Den här standard implementeringen ska hantera de flesta fall, förutsatt att data typen inte är för komplex. I vissa fall, antingen på grund av att användar data typen är för komplex, eller om prestandan för vår standard implementering inte uppfyller användarens krav, kan användarna sätta in sin egen implementering.
   
   Serialiserings gränssnittet på Java-sidan definieras som:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   Gränssnittet deserialisera på C\# -sidan definieras som:
   
   ICustomizedInteropCSharpDeserializer för offentliga gränssnitt
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Serialisering i C\# -sida och deserialisering på Java-sida**
   
   Serialiserings metoden i c\# -sidan ska anges i c\# -användar koden:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Avserialiserings metoden i Java-sidan ska anges i SPECIFIKATIONs filen:
   
     (SCP-kanalen
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Här "Microsoft. SCP. Storm. Multilang. CustomizedInteropJSONDeserializer" är namnet på deserialiseraren och "Microsoft. SCP. example. HybridTopology. person" är mål klassen som data deserialiseras till.
   
   Användaren kan också ansluta till en egen implementering av C\# -serialiseraren och Java-deserialiseraren. Den här koden är gränssnittet för C\# -serialiseraren:
   
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

Här anges som `SCPHost.exe` tillhandahålls av SCP SDK. `plugin.name` SCPHost. exe accepterar tre parametrar:

1. Det första är DLL-namnet, som är `"HelloWorld.dll"` i det här exemplet.
2. Den andra är klass namnet, som är `"Scp.App.HelloWorld.Generator"` i det här exemplet.
3. Det tredje är namnet på en offentlig statisk metod som kan anropas för att hämta en instans av ISCPPlugin.

I värd läge kompileras användar kod som DLL och anropas av SCP-plattformen. Därför kan SCP-plattformen få fullständig kontroll över hela bearbetnings logiken. Vi rekommenderar att våra kunder skickar topologi i värd läget för SCP eftersom det kan förenkla utvecklings upplevelsen och göra oss mer flexibel och bättre bakåtkompatibilitet för senare versioner.

## <a name="scp-programming-examples"></a>Programmerings exempel för SCP
### <a name="helloworld"></a>HelloWorld
**HelloWorld** är ett enkelt exempel för att visa en smak på SCP.net. Den använder en icke-transaktionell topologi, med en kanalen som kallas **Generator**och två bultar som kallas **delare** och **räknare**. Kanalen **Generator** skapar slumpmässigt meningar och genererar dessa meningar till **delare**. Bult- **delningen** delar upp meningarna i ord och genererar dessa ord till **Counter** bult. Bult "Counter" använder en ord lista för att registrera förekomst numret för varje ord.

Det finns två spec-filer, **HelloWorld. spec** och **\_HelloWorld EnableAck. spec** i det här exemplet. I C\# -koden kan det ta reda på om ack är aktiverat genom att hämta pluginConf från Java-sidan.

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

För att uppnå **exakt en** semantik måste antalet commit bult **-belopp** bedöma om det är en upprepad transaktion. I det här exemplet har den en statisk medlems variabel:

    public static long lastCommittedTxId = -1; 

När en ISCPBatchBolt-instans skapas hämtar `txAttempt` den från indataparametrarna:

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

När `FinishBatch()` anropas `lastCommittedTxId` , kommer att uppdateras om det inte är en omspelad transaktion.

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
Den här topologin innehåller en Java-kanalen\# och en C-bult. Den använder den standardinställda serialisering och deserialiserings-implementering som tillhandahålls av SCP-plattformen. Se filen **HybridTopology. spec** i **mappen\\exempel HybridTopology** för information om Specifikations filen och **SubmitTopology. bat** för att ange Java-classpath.

### <a name="scphostdemo"></a>SCPHostDemo
Det här exemplet är samma som HelloWorld i grunden. Den enda skillnaden är att användar koden kompileras som DLL och att topologin skickas med hjälp av SCPHost. exe. Se avsnittet "SCP-värd läge" för mer detaljerad förklaring.

## <a name="next-steps"></a>Nästa steg
Exempel på Apache Storm topologier som skapats med SCP finns i följande dokument:

* [Utveckla C# topologier för Apache storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Bearbeta fordons sensor data från Event Hubs med Apache Storm i HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrahera, transformera och läsa in (ETL) från Azure Event Hubs till Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)

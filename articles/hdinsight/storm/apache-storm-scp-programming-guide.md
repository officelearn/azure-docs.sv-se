---
title: Programmeringsguide för SCP.NET för Storm i Azure HDInsight
description: Lär dig hur du använder SCP.NET för att skapa. NET-baserade Storm-topologier för användning med Storm som körs i Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2016
ms.openlocfilehash: e6025ba2645c284cca87483b48b2d79a9558d574
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012538"
---
# <a name="scp-programming-guide"></a>Programmeringsguide för SCP
SCP är en plattform för att skapa realtid, tillförlitliga och konsekventa, och bearbetning av högpresterande program. Det är byggt ovanpå [Apache Storm](http://storm.incubator.apache.org/) – ett system som utformats av OSS-communities för strömbearbetning. Storm är utformad av Nathan Marz och har öppen källkod med Twitter. Den använder [Apache ZooKeeper](http://zookeeper.apache.org/), en annan Apache-projektet för att möjliggöra tillförlitliga distribuerade samordning och tillstånd. 

Inte bara SCP-projektet porteras Storm på Windows men även projektet till tillägg och anpassning av Windows-ekosystemet. Tilläggen innehåller utvecklarupplevelse för .NET och bibliotek, anpassningar kan vara Windows-baserade distributionen. 

Tillägget och anpassning görs så att vi inte behöver Förgrena projekt för OSS och vi kan nu använda härledda ekosystem som bygger på Storm.

## <a name="processing-model"></a>Processmodell
Data i SCP modelleras som kontinuerliga strömmar av tupplar. Vanligtvis tuppeln flöda till vissa kö först och sedan plockas och transformeras av affärslogik som finns i en Storm-topologi, slutligen utdata kan skickas som tupplar till ett annat SCP-system eller bekräftas till datalager som distribuerat filsystem eller databaser som SQL Server.

![Ett diagram över en kö skicka data för bearbetning av som ett datalager-flöden](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

I Storm definierar en programtopologin ett diagram över beräkning. Varje nod i en topologi innehåller standardbearbetningslogiken och länkar mellan noder ange dataflöde. Noder att mata in indata i topologin kallas _spouts_, som kan användas för att ordna data. Indata kan finnas i filen loggar, transaktionell databas, system-prestandaräknaren osv. Noder med både inkommande och utgående dataflöden kallas _bolts_, vilket gör det faktiska data som filtrering och val och aggregering.

SCP stöder bästa för att på minst en gång och exakt – en gång databearbetning. I ett distribuerat strömmande bearbetning-program kan olika fel inträffa under databearbetning, till exempel nätverksavbrott, maskinvarufel eller användarfel kod osv. På minst en gång garanterar alla data bearbetas minst en gång genom att spela upp automatiskt samma data när fel inträffar. Bearbetning på minst en gång är enkel och tillförlitlig och passar bra många program. När ett program kräver exakt inventering, är bearbetning på minst en gång dock inte tillräckligt med eftersom samma data kan potentiellt spelas upp i programtopologin. I så fall, exakt-när bearbetningen är utformad för att kontrollera att resultatet är korrekt även när data kan vara återupprepas och bearbeta flera gånger.

SCP gör det möjligt för .NET-utvecklare att utveckla tillämpningar och realtid data samtidigt som du använder på Java Virtual Machine (JVM) med Storm under försättsbladen. .NET och JVM kommunicerar via TCP lokala sockets. I praktiken är varje Spout/bult paret .net/Java-processen där användaren logik körs i .net-process som ett plugin-program.

Om du vill skapa ett program för bearbetning av ovanpå SCP måste behövs flera steg:

* Utforma och implementera Spouts att dra in data från kön.
* Utforma och implementera bultar för att bearbeta indata och spara data till externa datalager, till exempel en databas.
* Designa topologin, skicka och köra topologin. Topologin definierar hörn och data flöden mellan dess noder. SCP tar topologi-specifikationen och distribuera den på ett Storm-kluster, där varje brytpunkt som körs på en logisk nod. Redundans och skalning kommer typen av Schemaläggaren Storm.

Det här dokumentet används några enkla exempel för att gå igenom hur du skapar program för databearbetning med SCP.

## <a name="scp-plugin-interface"></a>SCP-plugin-programmet gränssnitt
SCP-plugin-program (eller program) är fristående exe-filerna som kan båda körs i Visual Studio under utvecklingsfasen och anslutas till Storm-pipeline efter distributionen i produktionsmiljön. Skriva SCP-plugin-programmet är på samma sätt som att skriva alla andra standard Windows-konsolprogram. SCP.NET plattform deklarerar vissa gränssnitt för spout/bult och plugin-programmet användarkod bör implementera dessa gränssnitt. Det huvudsakliga syftet med den här designen är att användaren kan fokusera på sina egna företag logics och låta andra saker som ska hanteras av SCP.NET-plattformen.

Plugin-programmet användarkod bör implementera en av de följande gränssnitt, beror på om topologin är transaktionell eller icke-transaktionell och om komponenten som är en kanal eller en bult.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin är gemensamt gränssnitt för alla typer av plugin-program. Det är för närvarande ett dummy-gränssnitt.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout är gränssnittet för icke-transaktionell kanal.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

När `NextTuple()` anropas, C\# användarkod kan generera en eller flera tupplar. Om det finns inget att skapa ska den här metoden returnera utan avger något. Det bör noteras som `NextTuple()`, `Ack()`, och `Fail()` kallas i en tät loop i en enda tråd i C\# processen. När det finns inga tupplar att generera, är det Välkommen företagspolicy ha NextTuple strömsparläge under en kort tidsperiod (till exempel 10 millisekunder) utan att slösa bort för mycket CPU.

`Ack()` och `Fail()` kallas endast när ack mekanism är aktiverat i klientfilsspecifik-filen. Den `seqId` används för att identifiera den tuppel som är bekräftat eller misslyckades. Så om ack är aktiverad i icke-transaktionell topologi, bör följande begär funktion användas i kanal:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Om ack inte stöds i icke-transaktionell topologi, den `Ack()` och `Fail()` stå kvar som tom-funktion.

Den `parms` indataparameter i dessa funktioner är en tom ordlista, den är reserverad för framtida användning.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt är gränssnittet för icke-transaktionell bulten.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

När nya tuppel är tillgänglig, den `Execute()` funktionen anropas för att bearbeta den.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout är gränssnittet för transaktionell kanal.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Precis som icke-transaktionell motparterna själva, `NextTx()`, `Ack()`, och `Fail()` kallas i en tät loop i en enda tråd i C\# processen. När det finns inga data att generera, det är Välkommen företagspolicy ha `NextTx` strömsparläge under en kort tidsperiod (10 millisekunder) utan att slösa bort för mycket CPU.

`NextTx()` anropas för att starta en ny transaktion Utdataparametern `seqId` används för att identifiera den transaktion som också används i `Ack()` och `Fail()`. I `NextTx()`, användaren kan generera data till Java-sida. Data lagras i ZooKeeper för repetitionsattacker. Eftersom kapaciteten för ZooKeeper är begränsad, bör användaren bara generera metadata, inte bulk data i transaktionella kanal.

Storm ska spela upp en transaktion automatiskt om det misslyckas så `Fail()` ska inte anropas i normala fall. Men om SCP kan kontrollera de metadata som orsakats av transaktionella kanal, kan det anropa `Fail()` när metadata är ogiltiga.

Den `parms` indataparameter i dessa funktioner är en tom ordlista, den är reserverad för framtida användning.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt är gränssnittet för transaktionell bulten.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` anropas när det finns nya tuppel anländer till bulten. `FinishBatch()` anropas när transaktionen avslutas. Den `parms` Indataparametern är reserverad för framtida användning.

För transaktionell topologi, är ett viktigt begrepp – `StormTxAttempt`. Den har två fält, `TxId` och `AttemptId`. `TxId` används för att identifiera en viss transaktion och för ett antal givna transaktioner, det kan finnas flera försök om transaktionen misslyckas och spelas upp en gång. SCP.NET skapar ett nytt ISCPBatchBolt-objekt för att bearbeta varje `StormTxAttempt`precis som vad Storm gör i Java. Syftet med den här designen är att stödja parallella transaktioner bearbetning. Användaren bör ha det i åtanke om transaktionen försök är slutförd, förstörs motsvarande ISCPBatchBolt objekt och in som skräp.

## <a name="object-model"></a>Objektmodell
SCP.NET innehåller också en enkel uppsättning objekt med nycklar för utvecklare att programmera med. De är **kontext**, **StateStore**, och **SCPRuntime**. De beskrivs i den rest-delen av det här avsnittet.

### <a name="context"></a>Kontext
Kontexten är en aktiv miljö till programmet. Varje ISCPPlugin-instans (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) har en motsvarande kontext-instans. Funktionerna i kontexten kan delas in i två delar: (1) den statiska delen, som är tillgänglig i hela C\# bearbeta, (2) den dynamiska delen som bara är tillgänglig för den specifika kontext-instansen.

### <a name="static-part"></a>Statiska del
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` tillhandahålls för loggning.

`pluginType` används för att ange vilken plugin-program i C\# processen. Om C\# processen körs i läget för lokal testning (utan Java), plugin-programtyp är `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` tillhandahålls för att hämta konfigurationsparametrar från Java-sida. Parametrarna som skickas från Java-sida när C\# plugin-programmet har initierats. Den `Config` parametrar är indelade i två delar: `stormConf` och `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` är parametrar som definierats av Storm och `pluginConf` är de parametrar som definierats av SCP. Exempel:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` har angetts för att få kontexten topologi, är det mest användbara för komponenter med flera parallellitet. Här är ett exempel:

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

### <a name="dynamic-part"></a>Dynamiska del
Följande gränssnitt är relevanta för en viss kontext-instans. Kontext-instans skapas av SCP.NET-plattformen och skickas till användarkoden:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

För icke-transaktionell kanal har stöd för ack, tillhandahåller följande metod:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

För icke-transaktionell bult stöder ack, bör det uttryckligen `Ack()` eller `Fail()` tuppel togs emot. Och när de nya tuppel, det måste även ange ankare nya parets. Följande metoder tillhandahålls.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore` innehåller metadatatjänster Monoton sekvens generation och vänta är kostnadsfria samordning. Distribuerade samtidighet abstraktioner på högre nivåer kan baseras på `StateStore`, inklusive distribuerade lås, distribuerade köer, hinder och Transaktionstjänster.

SCP-program kan använda den `State` objekt för att spara information i ZooKeeper, särskilt för transaktionell topologi. Om du gör det om transaktionell spout kraschar och startar om, den kan hämta nödvändig information från ZooKeeper och starta om pipelinen.

Den `StateStore` objekt främst med dessa metoder:

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
    /// <returns>Uncommited States</returns>
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
    public IEnumerable<Registry> Commited();

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

Den `State` objekt främst med dessa metoder:

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

För den `Commit()` metod när simpleMode har angetts till SANT, tar bort motsvarande ZNode i ZooKeeper. I annat fall raderas den aktuella ZNode och lägga till en ny nod i GENOMFÖRD\_sökväg.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime innehåller följande två metoder:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` används för att initiera för SCP-körningsmiljön. I den här metoden kan du C\# processen ansluter till den Java-sidan och hämtar konfigurationsparametrar och topologi kontext.

`LaunchPlugin()` används för att sätta igång meddelandeloopen för bearbetning. I den här loopen C\# plugin-programmet tar emot meddelanden formuläret Java-sida (inklusive tupplar och kontroll signaler) och sedan bearbeta meddelanden, kanske anropsmetod gränssnittet ger av användarkoden. Indataparametern för metoden `LaunchPlugin()` är en delegat som kan returnera ett objekt som implementerar gränssnittet ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

För ISCPBatchBolt, får vi `StormTxAttempt` från `parms`, och använda den för att bedöma om det är ett uppspelat försök. Sök efter ett repetitionsattacker försök sker ofta på commit bulten och det visas i den `HelloWorldTx` exempel.

Generellt sett kan SCP-plugin-program köras i två lägen här:

1. Lokalt testläge: I det här läget SCP-plugin-program (C\# användarkod) körs i Visual Studio under utvecklingsfasen. `LocalContext` kan användas i det här läget, vilket ger metod för att serialisera utgivna tuppeln till lokala filer och läsa dem tillbaka till minnet.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Standardläge: I det här läget SCP-plugin-program öppnas i storm java-processen.
   
    Här är ett exempel på Starta SCP-plugin-programmet:
   
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

## <a name="topology-specification-language"></a>Topologi-språket
SCP-topologi, är ett domänspecifika språk för att beskriva och konfigurera SCP-topologier. Den är baserad på Storm's Clojure DSL (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) och utökas med SCP.

Topologi specifikationer kan skickas direkt till storm-kluster för körning via den ***runspec*** kommando.

SCP.NET har lagt till följande funktioner för att definiera transaktionella topologier:

| **Nya funktioner** | **Parametrar** | **Beskrivning** |
| --- | --- | --- |
| **TX topolopy** |topologi-name<br />kanal-karta<br />bult karta |Definiera en transaktionell topologi med namnet topologi &nbsp;spouts definition kartan och kartan bultar definition |
| **SCP-tx-kanal** |Exec-name<br />args<br />fält |Definiera en transaktionell kanal. Den körs programmet med ***exec-name*** med ***argument***.<br /><br />Den ***fält*** är de fält som utdata för kanal |
| **SCP-tx-batch-bult** |Exec-name<br />args<br />fält |Definiera en transaktionell Batch bult. Den körs programmet med ***exec-name*** med ***argument.***<br /><br />Fälten är de fält som utdata för bulten. |
| **SCP-tx-commit-bult** |Exec-name<br />args<br />fält |Definiera en transaktionell commit bult. Den körs programmet med ***exec-name*** med ***argument***.<br /><br />Den ***fält*** är de fält som utdata för bult |
| **nontx topolopy** |topologi-name<br />kanal-karta<br />bult karta |Definiera en icke-transaktionell topologi med namnet topologi&nbsp; spouts definition kartan och kartan bultar definition |
| **SCP-kanal** |Exec-name<br />args<br />fält<br />parameters |Definiera en icke-transaktionell-kanal. Den körs programmet med ***exec-name*** med ***argument***.<br /><br />Den ***fält*** är de fält som utdata för kanal<br /><br />Den ***parametrar*** är valfria, använder det för att ange vissa parametrar, till exempel ”nontransactional.ack.enabled”. |
| **SCP-bult** |Exec-name<br />args<br />fält<br />parameters |Definiera en icke-transaktionell bulten. Den körs programmet med ***exec-name*** med ***argument***.<br /><br />Den ***fält*** är de fält som utdata för bult<br /><br />Den ***parametrar*** är valfria, använder det för att ange vissa parametrar, till exempel ”nontransactional.ack.enabled”. |

SCP.NET har följande nyckelord definierats:

| **nyckelord** | **Beskrivning** |
| --- | --- |
| **: namn** |Definiera namnet topologi |
| **: topologi** |Definiera topologin med hjälp av föregående funktioner och bygga in sådana. |
| **: p** |Definiera parallellitet-tipset för varje kanal eller en bult. |
| **: config** |Definiera konfigurera parameter eller uppdatera befintliga |
| **: schema** |Definiera schemat för Stream. |

Och vanliga parametrar:

| **Parametern** | **Beskrivning** |
| --- | --- |
| **”plugin.name”** |namnet på EXE-filen i C# plugin-programmet |
| **”plugin.args”** |plugin-argument |
| **”output.schema”** |Utdata-schemat |
| **”nontransactional.ack.enabled”** |Om ack är aktiverat för icke-transaktionell topologi |

Kommandot runspec distribueras tillsammans med bits, användningen är t.ex.:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Den ***resource dir*** parametern är valfri, måste du ange det när du vill ansluta en C\# programmet och den här katalogen innehåller programmet, beroenden och konfigurationer.

Den ***klassökvägen*** parametern också är valfri. Den används för att ange klassökväg Java om specifikationer-filen innehåller Java-kanalen eller en bult.

## <a name="miscellaneous-features"></a>Övriga funktioner
### <a name="input-and-output-schema-declaration"></a>Indata och utdata Schema-deklaration
Användare kan generera tupplar i C\# processer, plattformen måste serialisera tuppeln till byte [], överföring till Java-sida och Storm överför den här tuppel till mål. Under tiden i underordnade komponenter C\# processer ska ta emot tupplar från java-sida och konvertera den till de ursprungliga typerna av plattformen, alla dessa åtgärder är dolda av plattformen.

Användarkod måste deklarera schemat för indata och utdata för serialisering och deserialisering.

Stream indata/utdata-schemat har definierats som en ordlista. Nyckeln är StreamId. Värdet är typerna av kolumnerna. Komponenten kan ha flera strömmar som deklarerats.

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


Vi har följande API har lagts till i Context-objektet:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Utvecklare måste se till att tupplar som lyder under det schema som definierats för dataströmmen, annars systemet genereras ett undantagsfel för körning.

### <a name="multi-stream-support"></a>Flera Stream-Support
SCP stöder användarkod för att skapa eller ta emot från flera olika strömmar på samma gång. Stödet är medräknad i Context-objektet som begär-metoden tar ett valfritt stream ID-parametern.

Två metoder i SCP.NET Context-objektet har lagts till. De används för att generera tuppeln eller Tupplar om du vill ange StreamId. StreamId är en sträng och det måste vara konsekvent i båda C\# och topologi Definition-specifikationen.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Sänder till en icke-existerande stream gör runtime-undantag.

### <a name="fields-grouping"></a>Fält gruppering
Den inbyggda fält gruppering i Strom inte fungerar korrekt i SCP.NET. Alla fält datatyper är faktiskt byte [] på Java-Proxy-sida och fälten gruppering använder byte [] objekt hash-koden för att utföra grupperingen. Byte [] objekt hash-koden är adressen för det här objektet i minnet. Grupperingen kommer så att fel för två byte [] objekt som delar samma innehåll men inte samma adress.

SCP.NET lägger till en metod för anpassad gruppering och innehållet i byte [] används för att göra grupperingen. I **SPEC** filen syntaxen är t.ex.:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Här

1. ”scp-fältgruppen” innebär ”anpassad fältet gruppering implementeras av SCP”.
2. ”: tx” eller ”: icke-tx” innebär att om det är transaktionell topologi. Vi behöver den här informationen eftersom startIndex är annorlunda i tx jämfört med icke-tx topologier.
3. [0,1] innebär en hash uppsättning fältet ID, från och med 0.

### <a name="hybrid-topology"></a>Hybridtopologi
Den interna Storm är skriven i Java. Och SCP.Net har förbättrad den för att aktivera C\# utvecklare att skriva C\# koden för att hantera sina affärslogik. Men det stöder också hybridtopologier, som innehåller inte bara C\# kanaler/bultar, utan även Java Spout/bultar.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Ange Java Spout/bult i klientfilsspecifik fil
”Scp-kanal” och ”scp-bulten” kan också användas för att ange Java Spouts och Bolts i klientfilsspecifik fil, här är ett exempel:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Här `microsoft.scp.example.HybridTopology.Generator` är namnet på klassen Java-kanalen.

### <a name="specify-java-classpath-in-runspec-command"></a>Ange Java klassökvägen i runSpec kommando
Om du vill skicka topologi som innehåller Java Spouts eller bultar måste du först kompilera Java Spouts eller bultar och hämta Jar-filer. Du bör ange klassökväg java som innehåller Jar-filer när du skickar in topologin. Här är ett exempel:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Här **exempel\\HybridTopology\\java\\target\\**  är den mapp som innehåller Java Spout/bulten Jar-filen.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialisering och deserialisering mellan Java- och C\#
SCP-komponenten innehåller Java-sida och C\# sida. För att interagera med interna Java kanaler/bultar, serialisering/deserialisering måste utföras mellan Java-sida och C\# sida, såsom illustreras i följande diagram.

![diagram över java-komponent som skickas till SCP-komponent som skickas till Java-komponent](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serialisering i Java-sidan och deserialisering i C\# sida**
   
   Först vi ger standardimplementering för serialisering i Java-sida och deserialisering i C\# sida. Serialiseringsmetod i Java-sida kan anges i KLIENTFILSSPECIFIK fil:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   Deserialisering-metod i C\# sida ska anges i C\# användarkod:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Den här standardimplementering ska hantera de flesta fall den angivna datatypen inte är för komplex. För vissa fall, eftersom användardatatypen är för komplex eller eftersom prestandan hos våra standardimplementering inte uppfyller användarens krav, användarna kan plugin-programmet sina egna implementering.
   
   Serialisera gränssnittet i java-sida definieras som:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   Gränssnittet deserialize i C\# sida definieras som:
   
   offentliga gränssnittet ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Serialisering i C\# sida och deserialisering i Java-sida**
   
   Serialiseringsmetod i C\# sida ska anges i C\# användarkod:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Deserialisering-metod i Java-sida måste anges i KLIENTFILSSPECIFIK fil:
   
     (scp-kanal
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   ”Microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer” är här namnet på Deserialiserare och ”microsoft.scp.example.HybridTopology.Person” är målklassen data avserialiseras till.
   
   Användaren kan också ansluta sina egna implementering av C\# serialiserare och Java-Deserialiserare. Den här koden är gränssnittet för C\# serialiserare:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Den här koden är gränssnittet för Java-Deserialiserare:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>SCP-värd-läge
I det här läget kan användare kompilera sina koder för att DLL-filen och använder SCPHost.exe tillhandahålls av SCP för att skicka topologi. Filen klientfilsspecifik ser ut som den här koden:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Här kan `plugin.name` har angetts som `SCPHost.exe` tillhandahålls av SCP-SDK. SCPHost.exe accepterar tre parametrar:

1. Den första är DLL-namnet, vilket är `"HelloWorld.dll"` i det här exemplet.
2. Den andra är klassnamnet, som är `"Scp.App.HelloWorld.Generator"` i det här exemplet.
3. Det tredje är namnet på en offentlig statisk metod som kan anropas för att hämta en instans av ISCPPlugin.

I läget för värden, användarkod kompileras som DLL-filen och anropas av SCP-plattformen. SCP-plattformen kan så få fullständig kontroll över hela standardbearbetningslogiken. Så rekommenderar vi våra kunder att skicka topologi i SCP värd-läge eftersom det kan förenkla upplevelsen för utveckling och tar med större flexibilitet och bättre bakåtkompatibilitet för samt senare version.

## <a name="scp-programming-examples"></a>SCP programmering exempel
### <a name="helloworld"></a>HelloWorld
**HelloWorld** är ett enkelt exempel för att visa en känsla för SCP.Net. Den använder en icke-transaktionell topologi med en kanal som kallas **generator**, och två bultar kallas **delare** och **räknaren**. Spout **generator** genererar meningar slumpmässigt och för att generera dessa meningar till **delare**. Bulten **delare** delar upp meningar till ord och genererar dessa ord att **räknaren** bulten. Bult ”räknaren” använder en ordlista för att registrera vilket förekomsten av varje ord.

Det finns två klientfilsspecifik filer, **HelloWorld.spec** och **HelloWorld\_EnableAck.spec** i det här exemplet. I C\# kod, det kan ta reda på om ack är aktiverat genom att hämta pluginConf från Java-sida.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

I kanal om ack är aktiverad, används en ordlista för att cachelagra tupplar som inte har bekräftat. Om Fail() anropas, spelas den misslyckade tuppeln:

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
Den **HelloWorldTx** exemplet visar hur du implementerar transaktionell topologi. Den har en kanal som kallas **generator**, en batch-bult kallas **partiellt antal**, och en bult commit anropas **antal samlade**. Det finns tre förinställda txt-filer: **DataSource0.txt**, **DataSource1.txt**, och **DataSource2.txt**.

I varje transaktion, spout **generator** slumpmässigt väljer två filer skapats i förväg tre filer och generera de två fil som ska den **partiellt antal** bulten. Bulten **partiellt antal** hämtar filen namnet från den mottagna tuppeln och sedan öppna filen och räknar antalet ord i den här filen och slutligen generera word-tal till det **antal samlade** bulten. Den **antal samlade** bult sammanfattar det totala antalet.

Få **exakt en gång** semantik, commit bulten **antal samlade** behöver att bedöma om det är ett uppspelat transaktion. I det här exemplet har den en statisk medlemsvariabel:

    public static long lastCommittedTxId = -1; 

När en ISCPBatchBolt-instans skapas får den `txAttempt` från indataparametrar:

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

När `FinishBatch()` anropas den `lastCommittedTxId` uppdateras om det inte är ett uppspelat transaktion.

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
Den här topologin innehåller en Java-kanalen och en C\# bulten. Används för serialisering och deserialisering standardimplementering tillhandahålls av SCP-plattformen. Se den **HybridTopology.spec** i **exempel\\HybridTopology** mapp för filinformation specifikationer och **SubmitTopology.bat** för hur du anger Java klassökvägen.

### <a name="scphostdemo"></a>SCPHostDemo
Det här exemplet är samma som HelloWorld i princip. Den enda skillnaden är att användarkod kompileras som DLL-filen och topologin har skickats med hjälp av SCPHost.exe. Se avsnittet ”SCP värd läget” för mer detaljerad förklaring.

## <a name="next-steps"></a>Nästa steg
Exempel på Storm-topologier som skapats med hjälp av SCP finns i följande dokument:

* [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Bearbeta händelser från Azure Event Hubs med Storm på HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Bearbeta fordonssensordata från Event Hubs med Storm på HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrahera, transformera och läsa in (ETL) från Azure Event Hubs till HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)

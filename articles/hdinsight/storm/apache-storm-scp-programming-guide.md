---
title: Programmeringsguide för SCP.NET | Microsoft Docs
description: Lär dig hur du skapar med hjälp av SCP.NET. NET-baserade Storm-topologier för att använda med Storm på HDInsight.
services: hdinsight
documentationcenter: ''
author: raviperi
manager: jhubbard
editor: cgronlun
ms.assetid: 34192ed0-b1d1-4cf7-a3d4-5466301cf307
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.date: 05/16/2016
ms.author: raviperi
ms.openlocfilehash: 0f4c021bc209c99e1b3f34b34bf5ba0549eb48f9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="scp-programming-guide"></a>Programmeringsguide för SCP
SCP är en plattform för att skapa realtid, tillförlitlig och konsekvent, och program för högpresterande databearbetning. Det är byggt ovanpå [Apache Storm](http://storm.incubator.apache.org/) – en dataström som bearbetar system utformas av OSS communities. Storm är utformat av Nathan Marz och har öppen källkod med Twitter. Den använder [Apache ZooKeeper](http://zookeeper.apache.org/), ett annat Apache-projekt att aktivera hög tillförlitliga distribuerade samordning och tillstånd. 

Inte bara SCP-projektet portar Storm på Windows, utan också projektet till tillägg och anpassning av Windows-ekosystemet. Tilläggen innehåller .NET-utvecklare upplevelse och bibliotek, anpassning av innehåller Windows-baserad distribution. 

Tillägget och anpassning görs så att vi inte behöver duplicera projekt för OSS och vi kan utnyttja härledda ekosystem byggda på Storm.

## <a name="processing-model"></a>Processmodell
Data i SCP modelleras som kontinuerliga strömmar av tupplar. Vanligtvis trafikflödet tuppeln i vissa kö först, sedan tas upp och transformeras av affärslogik som finns inuti en Storm-topologi slutligen utdata kan skickas som tupplar till ett annat SCP-system eller allokeras till butiker som distribuerat filsystem eller databaser som SQL Server.

![Ett diagram av en kö mata data för bearbetning, som ett datalager-flöden](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

I Storm definierar en Programtopologi ett diagram över beräkning. Varje nod i en topologi innehåller logik för bearbetning och länkar mellan noder ange dataflöde. Noder att mata in indata i topologin kallas _spouts_, som kan användas för att ordna data. Indata kan finnas i filen loggar, transaktionella databas, system-prestandaräknaren osv. Noder med både inkommande och utgående dataflöden kallas _bolts_, vilket gör den faktiska data filtrering och val och aggregering.

Tjänstanslutningspunkten stöder bästa för på-minst en gång och exakt-behandling av data en gång. I ett distribuerat strömmande bearbetning-program kan olika fel inträffa under bearbetning av data, till exempel nätverksavbrott, maskinvarufel eller användarfel kod osv. På-minst en gång process säkerställer att alla data bearbetas minst en gång genom att spela upp automatiskt samma data när fel inträffar. På-minst en gång bearbetning är enkel och tillförlitlig och passar bra många program. När ett program kräver exakt inventering, är på-minst en gång bearbetning dock inte tillräckligt eftersom samma data kan vara att spela upp i topologin för programmet. I så fall, exakt-när bearbetningen är utformat för att kontrollera att resultatet är korrekt även när data kan spelas och behandlas flera gånger.

SCP gör att .NET-utvecklare kan utveckla program i realtid data processen samtidigt utnyttja på Java Virtual Machine (JVM) med Storm under försättsbladen. .NET och JVM kommunicera via TCP lokala sockets. I praktiken är varje kanal/bult paret .net/Java processen där användaren-logik körs i .net-process som ett plugin-program.

Flera steg krävs för att skapa ett program för databearbetning ovanpå SCP:

* Utforma och implementera kanaler att dra in data från kön.
* Utforma och implementera bultar för att behandla indata och spara data till externa butiker exempelvis en databas.
* Utforma topologi, skicka och kör topologin. Topologin definierar brytpunkter och data som flödar mellan dess noder. SCP tar specifikationen topologi och distribuera den på ett Storm-kluster där varje vertex körs på en logisk nod. Redundans och skalning kommer typen av Schemaläggaren Storm.

Det här dokumentet använder några enkla exempel för att gå igenom hur du skapar program för databearbetning med SCP.

## <a name="scp-plugin-interface"></a>SCP-Plugin-gränssnittet
SCP-plugin-program (eller program) är fristående EXEs som kan både körs i Visual Studio under utvecklingsfasen och anslutas till Storm-pipeline efter distributionen i produktion. Skriva SCP plugin-programmet är på samma sätt som att skriva andra standard Windows konsolprogram. SCP.NET plattform deklarerar vissa gränssnitt för kanal-/ bult och användarkod för plugin-programmet ska implementera dessa gränssnitt. Det huvudsakliga syftet med den här designen är att användaren kan fokusera på sina egna business logics och låta andra saker som ska hanteras av SCP.NET plattform.

Plugin-programmet användarkod ska implementera ett av följande gränssnitt, beror på om topologin är transaktionell eller icke-transaktionell och om komponenten är en kanal eller en bult.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin är gemensamt gränssnitt för alla typer av plugin-program. Det är för närvarande ett dummy gränssnitt.

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

När `NextTuple()` anropas, C\# användarkod kan generera en eller flera tupplar. Om det finns inget att generera ska den här metoden returnera utan avger något. Det bör noteras som `NextTuple()`, `Ack()`, och `Fail()` kallas i en tät loop i en enskild tråd i C\# process. När det finns inga tupplar att generera, är det Välkommen företagspolicy ha NextTuple strömsparläge under en kort tidsperiod (till exempel 10 millisekunder) utan att avfallshantering för mycket CPU.

`Ack()` och `Fail()` anropas endast när ack mekanism är aktiverat i spec filen. Den `seqId` används för att identifiera den tuppel som ADE eller misslyckades. Så om ack är aktiverad i icke-transaktionell topologi, bör följande begär funktion användas i kanal:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Om ack inte stöds i icke-transaktionell topologi, den `Ack()` och `Fail()` kan lämnas tomt funktion.

Den `parms` Indataparametern i dessa funktioner är ett tomt Ordbok, den är reserverad för framtida användning.

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

Precis som i sin icke-transaktionell motparterna del `NextTx()`, `Ack()`, och `Fail()` kallas i en tät loop i en enskild tråd i C\# process. När det finns inga data att generera, är det Välkommen företagspolicy ha `NextTx` strömsparläge under en kort tidsperiod (10 millisekunder) utan att avfallshantering för mycket CPU.

`NextTx()` för att starta en ny transaktion Utdataparametern `seqId` används för att identifiera den transaktion som också används i `Ack()` och `Fail()`. I `NextTx()`, användare som kan sända data till Java-sida. Data lagras i ZooKeeper att stödja repetitionsattacker. Eftersom ZooKeeper kapacitet är begränsad, bör användare genererar endast metadata kan inte masskopiera data i transaktionella kanal.

Storm ska spelas upp en transaktion automatiskt om den misslyckas så `Fail()` ska inte anropas i vanliga fall. Men om SCP kan kontrollera metadata som sänds av transaktionella kanal, kan det anropa `Fail()` när metadata är ogiltiga.

Den `parms` Indataparametern i dessa funktioner är ett tomt Ordbok, den är reserverad för framtida användning.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt är gränssnittet för transaktionell bulten.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` anropas när det finns nya tuppel anländer till bulten. `FinishBatch()` anropas när transaktionen avslutas. Den `parms` indataparameter är reserverad för framtida användning.

För transaktionell topologi, är ett viktigt begrepp – `StormTxAttempt`. Den har två fält `TxId` och `AttemptId`. `TxId` används för att identifiera en viss transaktion och för en given transaktion, det kan finnas flera försök om transaktionen misslyckas och spelas. SCP.NET skapar ett nytt ISCPBatchBolt-objekt för att bearbeta varje `StormTxAttempt`, precis som Storm finns i Java. Syftet med den här designen är att stödja parallella transaktioner bearbetning. Användaren bör ha det i åtanke att motsvarande ISCPBatchBolt objektet förstörs om transaktionen försök är slutförd och skräpinsamlats.

## <a name="object-model"></a>Objektmodell
SCP.NET innehåller också en enkel uppsättning objekt för utvecklare att programmera med nycklar. De är **kontexten**, **StateStore**, och **SCPRuntime**. De beskrivs i rest-delen av det här avsnittet.

### <a name="context"></a>Kontext
Kontexten är en aktiv miljö till programmet. Varje ISCPPlugin-instans (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) har en motsvarande kontext-instans. Funktionerna i kontexten kan delas in i två delar: (1) menyns statiska del som är tillgängliga i hela C\# bearbeta, (2) dynamiska del, som endast är tillgänglig för den specifika instansen i kontexten.

### <a name="static-part"></a>Statiska del
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` finns i loggen syfte.

`pluginType` används för att ange plugin-typ av C\# process. Om C\# processen körs i lokala testläge (utan Java), plugin-typen är `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` finns för att hämta konfigurationsparametrar från Java-sida. Parametrarna som skickas från Java sida när C\# plugin-programmet har initierats. Den `Config` parametrar är uppdelat i två delar: `stormConf` och `pluginConf`.

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

`TopologyContext` har angetts för att få kontexten topologi, är det mest användbar för komponenter med flera parallellitet. Här är ett exempel:

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
Följande gränssnitt är relevant för en viss instans i kontexten. Kontexten instans skapas av SCP.NET plattform och skickas till användarkoden:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

För icke-transaktionell kanal stöder ack, finns följande metod:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

För icke-transaktionell bult stöder ack, bör det explicit `Ack()` eller `Fail()` tuppeln togs emot. Och när sändning nya tuppel, det måste även ange ankare nya parets. Följande metoder tillhandahålls.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore` innehåller metadatatjänster, monotonisk sekvensgenerering och vänta utan samordning. Abstraktioner på högre nivåer distribuerade samtidighet kan baseras på `StateStore`, inklusive distribuerade lås, distribuerade köer, barriärer och Transaktionstjänster.

SCP-program kan använda den `State` objekt att spara information i ZooKeeper, särskilt för transaktionell topologi. Om du gör det transaktionella kanal kraschar och startar om, den kan hämta nödvändig information från ZooKeeper och starta om pipeline.

Den `StateStore` -objektet har huvudsakligen dessa metoder:

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

Den `State` -objektet har huvudsakligen dessa metoder:

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

För den `Commit()` metoden när simpleMode har angetts till true, raderas motsvarande ZNode i ZooKeeper. I annat fall raderas aktuella ZNode och lägga till en ny nod i GENOMFÖRD\_sökväg.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime innehåller följande två metoder:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` används för att initiera SCP-körningsmiljön. Den här metoden C\# processen ansluter till Java-sida och hämtar konfigurationsparametrar och topologi kontext.

`LaunchPlugin()` används för att startar bearbetning meddelandeloop. I den här loop C\# plugin-programmet tar emot meddelanden formuläret Java-sida (inklusive tupplar och kontroll signaler) och sedan bearbeta meddelanden, kanske anropar gränssnittsmetoden ange av användarkoden. Indataparametern för metoden `LaunchPlugin()` är en delegat som kan returnera ett objekt som implementerar ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt-gränssnittet.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

För ISCPBatchBolt, kan vi hämta `StormTxAttempt` från `parms`, och använda den för att bedöma om det är ett uppspelat försök. Sök efter ett replay-försök görs ofta på bulten genomförande och det visas i den `HelloWorldTx` exempel.

Generellt sett kan SCP-plugin-program köras i två lägen här:

1. Lokala testläge: I det här läget SCP-plugin-program (C\# användarkod) körs i Visual Studio under utvecklingsfasen. `LocalContext` kan användas i det här läget, vilket ger en metod för att serialisera skickade tupplar lokala filer och läsa dem tillbaka till minne.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Standardläget: I det här läget SCP-plugin-program startas av storm java-process.
   
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

## <a name="topology-specification-language"></a>Topologi specifikationsspråk
SCP är-topologi en domän-specifika språk för att beskriva och konfigurera SCP topologier. Den är baserad på Storm's Clojure DSL (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) och utökas med SCP.

Topologi specifikationer kan skickas direkt till storm-kluster för körning via den ***runspec*** kommando.

SCP.NET har lagt till följande funktioner för att definiera transaktionella topologier:

| **Nya funktioner** | **Parametrar** | **Beskrivning** |
| --- | --- | --- |
| **TX topolopy** |topologi namn<br />kanal-karta<br />bult-karta |Definiera en transaktionell topologi med namnet topologi &nbsp;spouts definitionen mappning och bultar definitionen mappning |
| **SCP-tx-kanal** |Exec-namn<br />argument<br />fält |Definiera en transaktionell kanal. Det körs programmet med ***exec-name*** med ***argument***.<br /><br />Den ***fält*** är utdatafält för kanal |
| **SCP-tx-batch-bult** |Exec-namn<br />argument<br />fält |Definiera en transaktionell Batch bulten. Det körs programmet med ***exec-name*** med ***argument.***<br /><br />Fälten är utdatafält för bulten. |
| **SCP-tx-commit-bult** |Exec-namn<br />argument<br />fält |Definiera en bult transaktionella genomförande. Det körs programmet med ***exec-name*** med ***argument***.<br /><br />Den ***fält*** är utdatafält för bult |
| **nontx topolopy** |topologi namn<br />kanal-karta<br />bult-karta |Definiera en icke-transaktionell topologi med namnet topologi&nbsp; spouts definitionen mappning och bultar definitionen mappning |
| **SCP-kanal** |Exec-namn<br />argument<br />fält<br />parameters |Definiera en icke-transaktionell kanal. Det körs programmet med ***exec-name*** med ***argument***.<br /><br />Den ***fält*** är utdatafält för kanal<br /><br />Den ***parametrar*** är valfria, använder den för att ange vissa parametrar, till exempel ”nontransactional.ack.enabled”. |
| **SCP-bult** |Exec-namn<br />argument<br />fält<br />parameters |Definiera en icke-transaktionell bulten. Det körs programmet med ***exec-name*** med ***argument***.<br /><br />Den ***fält*** är utdatafält för bult<br /><br />Den ***parametrar*** är valfria, använder den för att ange vissa parametrar, till exempel ”nontransactional.ack.enabled”. |

SCP.NET har definierats följande nyckelord:

| **Nyckelord** | **Beskrivning** |
| --- | --- |
| **: namn** |Definiera namnet topologi |
| **: topologi** |Definiera topologin med hjälp av föregående funktioner och skapar i viktiga. |
| **: p** |Definiera parallellitet-tips för varje kanal eller en bult. |
| **: config** |Definiera konfigurera parameter eller uppdatera befintliga |
| **: schemat** |Definiera schemat för dataströmmen. |

Och vanliga parametrar:

| **Parametern** | **Beskrivning** |
| --- | --- |
| **”plugin.name”** |namnet på C# plugin-programmet exe-filen |
| **”plugin.args”** |plugin-argument |
| **”output.schema”** |Utdataschemat |
| **”nontransactional.ack.enabled”** |Om ack har aktiverats för icke-transaktionell topologi |

Kommandot runspec distribueras tillsammans med bits, användningen är t.ex.:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

Den ***resurs dir*** -parametern är valfri, måste du ange den när du vill ansluta en C\# program och katalogen som innehåller programmet, beroenden och konfigurationer.

Den ***klassökvägen*** parametern också är valfri. Den används för att ange klassökväg Java om spec filen innehåller Java-kanal eller en bult.

## <a name="miscellaneous-features"></a>Diverse funktioner
### <a name="input-and-output-schema-declaration"></a>Indata och utdata Schema-deklaration
Användare kan generera tupplar i C\# processer, plattformen måste serialisera tuppeln till byte [], överföring till Java-sida och Storm överför det här tuppeln till mål. Under tiden i underordnade komponenter C\# processer ska ta emot tupplar tillbaka från java sida och konvertera den till de ursprungliga typerna av plattformen, dessa åtgärder är dolda av plattformen.

Användarkod måste deklarera schemat för indata och utdata för att stödja serialisering och deserialisering.

Schemat för i/o-dataströmmen har definierats som en ordlista. Nyckeln är StreamId. Värdet är typerna av kolumnerna. Komponenten kan ha flera strömmar som deklarerats.

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


Vi har lagt till följande API i Context-objektet:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Utvecklare måste se till att tupplar som orsakat följa det schema som definierats för dataströmmen, annars systemet genereras ett undantagsfel för körning.

### <a name="multi-stream-support"></a>Stöd för flera dataström
Tjänstanslutningspunkten stöder användarkod för att generera eller ta emot från flera olika dataströmmar på samma gång. Stödet visar i Context-objektet som begär metoden tar en valfri dataströmmen ID-parametern.

Två metoder i SCP.NET Context-objektet har lagts till. De används för att generera tuppeln eller Tupplar om du vill ange StreamId. StreamId är en sträng och den måste vara konsekvent i båda C\# och topologi Definition-specifikationen.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

Sändning till en dataström som inte finns gör runtime-undantag.

### <a name="fields-grouping"></a>Fält gruppering
Den inbyggda fält gruppering i Strom inte fungerar korrekt i SCP.NET. Alla fält datatyper är faktiskt byte [] på Java-Proxy-sida och fälten Gruppera använder byte [] objekt hash-kod för att utföra grupperingen. Byte [] objekt hash-koden är adressen till det här objektet i minnet. Grupperingen kommer därför att fel för två byte [] objekt som delar samma innehåll, men inte samma adress.

SCP.NET lägger till en metod för anpassad gruppering och innehållet i byte [] används för att göra grupperingen. I **SPEC** filen syntax är t.ex.:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Här

1. ”scp-fältet-grupp”: ”anpassad fältet gruppering implementeras av SCP”.
2. ”: tx” eller ”: icke-tx” innebär att om det är transaktionell topologi. Vi behöver informationen eftersom startindexet skiljer sig åt i tx kontra-tx topologier.
3. [0,1] innebär en hash-uppsättning fältet ID, början på 0.

### <a name="hybrid-topology"></a>Hybrid-topologi
Den interna Storm är skriven i Java. Och SCP.Net har förbättrad den, så att C\# utvecklare skriva C\# kod för att hantera sina affärslogik. Men det stöder också hybridtopologier, som innehåller inte bara C\# kanaler/bultar men Java kanal/bultar.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Ange kanal/bult för Java i spec fil
”Scp-kanal” och ”scp-bult” kan också användas för att ange Java Spouts och bultar i spec fil, här är ett exempel:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Här `microsoft.scp.example.HybridTopology.Generator` är namnet på klassen Java-kanalen.

### <a name="specify-java-classpath-in-runspec-command"></a>Ange Java-klassökvägen i runSpec kommando
Om du vill skicka topologi som innehåller Java Spouts eller bultar måste du först kompilera Java Spouts eller bultar och hämta Jar-filerna. Du bör ange java-klassökvägen som innehåller Jar-filer när du skickar in topologin. Här är ett exempel:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Här **exempel\\HybridTopology\\java\\mål\\**  är den mapp som innehåller Java kanal/bult Jar-filen.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialisering och deserialisering mellan Java och C\#
SCP komponenten innehåller Java sida och C\# sida. För att kunna interagera med inbyggda Java kanaler/bultar serialisering/deserialisering utföras mellan Java sida och C\# sida, enligt beskrivningen i följande diagram.

![diagram över java-komponent som skickas till SCP-komponent som skickas till Java-komponent](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serialisering i Java-sida och deserialisering i C\# sida**
   
   Första vi tillhandahåller standardimplementering för serialisering i Java-sida och deserialisering i C\# sida. Serialiseringsmetod i Java-sida kan anges i SPEC filen:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   Metoden deserialisering i C\# sida måste anges i C\# användarkod:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Den här standardimplementering ska hantera de flesta fall förutsatt att datatypen inte är för komplex. För vissa fall, eftersom användaren datatyp är för komplex eller eftersom prestanda för våra standardimplementering inte uppfyller kraven för användarens, användare kan plugin-programmet sina egna implementering.
   
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
   
   Serialiseringsmetod i C\# sida måste anges i C\# användarkod:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   Metoden deserialisering i Java-sida måste anges i SPEC fil:
   
     (scp-kanal
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   ”Microsoft.scp.example.HybridTopology.Person” är målklassen data avserialiseras till ”microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer” är här namnet på funktionen för avserialisering.
   
   Användaren kan också ansluta sina egna implementering av C\# serialiseraren och Java funktionen för avserialisering. Den här koden är gränssnittet för C\# serialiseraren:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Den här koden är gränssnittet för Java funktionen för avserialisering:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Värden för SCP-läge
I det här läget kan användaren kompilera koderna till DLL-filen och använda SCPHost.exe som tillhandahålls av SCP för att skicka topologi. Klientfilsspecifik filen ser ut som den här koden:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Här, `plugin.name` har angetts som `SCPHost.exe` som tillhandahålls av SCP SDK. SCPHost.exe accepterar tre parametrar:

1. Den första är DLL-namn, som är `"HelloWorld.dll"` i det här exemplet.
2. Den andra är klassnamnet, som är `"Scp.App.HelloWorld.Generator"` i det här exemplet.
3. Det tredje är namnet på en offentlig statisk metod som kan anropas för att hämta en instans av ISCPPlugin.

Användarkod i värden läge är kompilerad som DLL-filen och anropas av SCP-plattformen. SCP-plattformen kan så få fullständig kontroll över hela standardbearbetningslogiken. Så rekommenderar vi våra kunder att skicka topologi i SCP värden läge eftersom den kan förenkla utvecklingen och sätta oss större flexibilitet och bättre bakåtkompatibilitet för samt senare version.

## <a name="scp-programming-examples"></a>Exempel för SCP-programmering
### <a name="helloworld"></a>HelloWorld
**HelloWorld** är ett enkelt exempel för att visa uppleva SCP.Net. Den använder en icke-transaktionell topologi med en kanal som kallas **generator**, och två bultar kallas **delningslisten** och **räknaren**. Kanal **generator** genererar meningar slumpmässigt och för att generera dessa meningar till **delningslisten**. Bulten **delningslisten** delar meningar ord och generera dessa ord att **räknaren** bulten. Bult ”räknaren” använder en ordlista för att registrera förekomsten av varje ord.

Det finns två spec filer **HelloWorld.spec** och **HelloWorld\_EnableAck.spec** för det här exemplet. I C\# kod, det kan ta reda på om ack aktiveras genom att hämta pluginConf från Java-sida.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

I kanal om ack aktiveras används en ordlista till att cachelagra tupplar som inte är ADE. Om Fail() anropas, spelas misslyckade tuppeln:

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
Den **HelloWorldTx** exemplet visar hur du implementerar transaktionella topologi. Den har en kanal som kallas **generator**, en batch bult kallas **partiell antal**, och en bult commit anropas **summan antal**. Det finns tre förskapade txt-filer: **DataSource0.txt**, **DataSource1.txt**, och **DataSource2.txt**.

I varje transaktion, kanal **generator** slumpmässigt väljer två filer i förväg skapade tre filer och generera två filnamnen till den **partiell antal** bulten. Bulten **partiell antal** hämtar filen namn från den mottagna tuppeln och sedan öppna filen räkna antalet ord i den här filen och slutligen genererar word numret till den **antal summan** bulten. Den **antal summan** bult sammanfattar det totala antalet.

Att uppnå **exakt en gång** semantik, commit bulten **summan antal** behöver för att bedöma om det är en upprepat transaktion. I det här exemplet har den en statisk medlemsvariabel:

    public static long lastCommittedTxId = -1; 

När en ISCPBatchBolt instans skapas, får den `txAttempt` från indataparametrar:

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

När `FinishBatch()` anropas, den `lastCommittedTxId` uppdateras om det inte är en upprepat transaktion.

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
Den här topologin innehåller en Java-kanalen och en C\# bulten. Den använder serialisering och deserialisering standardimplementering tillhandahålls av SCP-plattformen. Finns det **HybridTopology.spec** i **exempel\\HybridTopology** mapp för spec Filinformation och **SubmitTopology.bat** för hur du anger Java klassökvägen.

### <a name="scphostdemo"></a>SCPHostDemo
Det här exemplet är samma som HelloWorld i princip. Den enda skillnaden är att användarkod kompileras som DLL-filen och topologin skickas med hjälp av SCPHost.exe. Se avsnittet ”SCP värden läget” mer detaljerad förklaring.

## <a name="next-steps"></a>Nästa steg
Exempel på Storm-topologier som skapats med SCP finns i följande dokument:

* [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Bearbeta händelser från Azure Event Hubs med Storm på HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Bearbeta vehicle sensordata från Händelsehubbar med Storm på HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrahera, transformering och laddning (ETL) från Azure Event Hubs till HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)

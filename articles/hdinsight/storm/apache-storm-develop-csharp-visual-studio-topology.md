---
title: Apache Storm-topologier med Visual Studio och C# - Azure HDInsight
description: Läs om hur du skapar Storm-topologier i C#. Skapa en tonologiska ordräkning i Visual Studio med hjälp av Hadoop-verktygen för Visual Studio.
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/31/2019
ms.openlocfilehash: 1903c2faab865152d1f3666f3c9dadd745058b56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612299"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Utveckla C# topologier för Apache Storm med hjälp av Data Lake-verktygen för Visual Studio

Lär dig hur du skapar en C# Apache Storm-topologi med hjälp av Azure Data Lake (Apache Hadoop) verktyg för Visual Studio. Det här dokumentet går igenom processen att skapa ett Storm-projekt i Visual Studio, testa det lokalt och distribuera det till ett Apache Storm på Azure HDInsight-kluster.

Du lär dig också hur du skapar hybridtopologier som använder C# och Java-komponenter.

C# topologier använder .NET 4.5 och använder Mono för att köras på HDInsight-klustret. Information om potentiella inkompatibiliteter finns i [Mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/). Om du vill använda en C#-topologi måste du uppdatera `Microsoft.SCP.Net.SDK` NuGet-paketet som används av projektet till version 0.10.0.6 eller senare. Paketversionen måste även matcha huvudversionen av Storm som är installerad på HDInsight.

| HDInsight-version | Apache Storm version | SCP.NET version | Standardversion av mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>Krav

Ett Apache Storm-kluster på HDInsight. Se [Skapa Apache Hadoop-kluster med Azure-portalen](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **klustertyp**.

## <a name="install-visual-studio"></a>Installera Visual Studio

Du kan utveckla C# topologier med SCP.NET med hjälp av [Visual Studio](https://visualstudio.microsoft.com/downloads/). Instruktionerna här använder Visual Studio 2019, men du kan också använda tidigare versioner av Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Installera datasjöverktyg för Visual Studio

Om du vill installera DataSjöverktyg för Visual Studio följer du stegen i [Kom igång med datasjöverktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="install-java"></a>Installera Java

När du skickar en Storm-topologi från Visual Studio genererar SCP.NET en zip-fil som innehåller topologin och beroenden. Java används för att skapa dessa zip-filer, eftersom det använder ett format som är mer kompatibelt med Linux-baserade kluster.

1. Installera Java Developer Kit (JDK) 7 eller senare i din utvecklingsmiljö. Du kan få Oracle JDK från [Oracle](https://openjdk.java.net/). Du kan också använda [andra Java-distributioner](/java/azure/jdk/).

2. Ange `JAVA_HOME` miljövariabeln till katalogen som innehåller Java.

3. Ange `PATH` att miljövariabeln ska inkludera katalogen. `%JAVA_HOME%\bin`

Du kan skapa och köra följande C#-konsolprogram för att kontrollera att Java och JDK är korrekt installerade:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm-mallar

Datasjöverktygen för Visual Studio innehåller följande mallar:

| Projekttyp | Demonstrationer |
| --- | --- |
| Storm Ansökan |Ett tomt Storm-topologiprojekt. |
| Exempel på Storm Azure SQL Writer |Så här skriver du till Azure SQL Database. |
| Exempel på Azure Cosmos-läsare för Storm Azure Cosmos |Så här läser du från Azure Cosmos DB. |
| Exempel på Storm Azure Cosmos DB-brännare |Så här skriver du till Azure Cosmos DB. |
| Exempel på Storm EventHub-läsare |Så här läser du från Azure Event Hubs. |
| Exempel på Storm EventHub-brännare |Så här skriver du till Azure Event Hubs. |
| Exempel på Storm HBase-läsare |Så här läser du från HBase på HDInsight-kluster. |
| Exempel på Storm HBase Writer |Hur man skriver till HBase på HDInsight kluster. |
| Storm Hybrid Exempel |Så här använder du en Java-komponent. |
| Storm exempel |En grundläggande ordräkningstopologi. |

> [!WARNING]  
> Alla mallar fungerar inte med Linux-baserade HDInsight. NuGet-paket som används av mallarna kanske inte är kompatibla med Mono. Information om hur du identifierar potentiella problem finns i [Mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/) och använder [.NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

I stegen i det här dokumentet använder du den grundläggande projekttypen Storm Application för att skapa en topologi.

### <a name="apache-hbase-templates"></a>Apache HBase-mallar

HBase-läsare och skrivningsmallar använder HBase REST API, inte HBase Java API, för att kommunicera med ett HBase-kluster i HDInsight.The HBase reader and writer templates use the HBase REST API, not the HBase Java API, to communicate with an HBase on HDInsight cluster.

### <a name="eventhub-templates"></a>EventHub-mallar

> [!IMPORTANT]  
> Den Java-baserade EventHub-pipkomponenten som ingår i EventHub Reader-mallen kanske inte fungerar med Storm på HDInsight version 3.5 eller senare. En uppdaterad version av den här komponenten finns på [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

En exempeltopologi som använder den här komponenten och fungerar med Storm på HDInsight 3.5 finns i [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Skapa en C#-topologi

Så här skapar du ett C#-topologiprojekt i Visual Studio:

1. Öppna Visual Studio.

1. Välj Skapa ett **nytt projekt**i **startfönstret** .

1. Bläddra till i fönstret **Skapa ett nytt projekt** och välj **Stormprogram**och välj sedan **Nästa**.

1. I fönstret **Konfigurera det nya projektet** anger du ett **Projektnamn på** *WordCount,* går till eller skapar en **platskatalogsökväg** för projektet och väljer sedan **Skapa**.

    ![Storm-program, Konfigurera den nya projektdialogrutan, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

När du har skapat projektet bör du ha följande filer:

* *Program.cs*: Topologidefinitionen för ditt projekt. En standardtopologi som består av en pip och en bult skapas som standard.

* *Spout.cs*: Ett exempel pip som avger slumptal.

* *Bolt.cs*: En exempelbult som håller ett antal siffror som avges av pipen.

När du skapar projektet hämtar NuGet det senaste [SCP.NET-paketet](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

### <a name="implement-the-spout"></a>Implementera pipen

Lägg sedan till koden för pipen, som används för att läsa data i en topologi från en extern källa. Denna pip avger slumpmässigt en mening i topologin.

1. Öppna *Spout.cs*. De viktigaste komponenterna för en pip är:

   * `NextTuple`: Kallas av Storm när pipen tillåts avge nya tupplar.

   * `Ack`(endast transaktionstopologi): Hanterar bekräftelser som startats av andra komponenter i topologin för tupplar som skickas från pipen. Om du erkänner en tuple kan pipen veta att den har bearbetats av underordnade komponenter.

   * `Fail`(endast transaktionstopologi): Hanterar tupplar som inte bearbetar andra komponenter i topologin. Genom att `Fail` implementera en metod kan du åter avge tuppeln så att den kan bearbetas igen.

2. Ersätt innehållet i `Spout` klassen med följande text:

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Implementera bultarna

Nu skapa två Storm bultar i detta exempel:

1. Ta bort den befintliga *Bolt.cs* filen från projektet.

2. Högerklicka på projektet i **Solution Explorer**och välj **Lägg till** > **nytt objekt**. Välj **Storm Bolt**i listan och *ange Splitter.cs* som namn. Ändra namnområdesnamnet till i den nya `WordCount`filens kod . Upprepa sedan den här processen för att skapa en andra bult som heter *Counter.cs*.

   * *Splitter.cs*: Implementerar en bult som delar upp meningar i enskilda ord och avger en ny ström av ord.

   * *Counter.cs*: Implementerar en bult som räknar varje ord och avger en ny flöde av ord och antalet för varje ord.

     > [!NOTE]  
     > Dessa bultar läsa och skriva till strömmar, men du kan också använda en bult för att kommunicera med källor som en databas eller tjänst.

3. Öppna *Splitter.cs*. Den har bara en `Execute`metod som standard: . Metoden `Execute` anropas när bulten tar emot en tuppel för bearbetning. Här kan du läsa och bearbeta inkommande tupplar och avge utgående tupplar.

4. Ersätt innehållet i `Splitter` klassen med följande kod:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Öppna *Counter.cs*och ersätt klassinnehållet med följande kod:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Definiera topologin

Pipar och bultar är ordnade i ett diagram som definierar hur data flödar mellan komponenter. För denna topologi är diagrammet följande:

![Spout och bult komponent arrangemang diagram, Storm topologi](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Pipen avger meningar som distribueras till instanser av Splitterbulten. Splitterbulten bryter meningarna i ord, som distribueras till räknarbulten.

Eftersom räknarinstansen innehåller ordräkningen lokalt vill du se till att specifika ord flödar till samma counter-bultinstans. Varje instans håller reda på specifika ord. Eftersom Splitter bulten upprätthåller inget tillstånd, det spelar egentligen ingen roll vilken instans av splitter får vilken mening.

Öppna *Program.cs*. Den viktiga `GetTopologyBuilder`metoden är , som används för att definiera topologin som skickas till Storm. Ersätt innehållet `GetTopologyBuilder` i med följande kod för att implementera topologin som beskrivits tidigare:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Skicka topologin

Du är nu redo att skicka topologin till ditt HDInsight-kluster.

1. Navigera till **Visa** > **serverutforskaren**.

1. Högerklicka på **Azure,** välj **Anslut till Microsoft Azure-prenumeration...** och slutför inloggningsprocessen.

1. Högerklicka på projektet i **Solution Explorer**och välj Skicka till Storm **på HDInsight**.

1. Välj listan Storm på HDInsight under listrutan **Storm-kluster** i dialogrutan **Skicka topologi** och välj sedan **Skicka**. Du kan kontrollera om överföringen lyckas genom att visa **utdatafönstret.**

    När topologin har skickats ska fönstret **Storm Topologies View** för klustret visas. Välj **WordCount-topologin** i listan om du vill visa information om den löptopologin.

    ![Fönstret Stormtopologi, HDInsight-kluster, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > Du kan också visa **Storm Topologies** från **Server Explorer**. Expandera **Azure** > **HDInsight**, högerklicka på ett Storm-kluster på HDInsight och välj sedan Visa Storm **Topologies**.

    Om du vill visa information om komponenterna i topologin markerar du en komponent i diagrammet.

1. I avsnittet **Topologi sammanfattning** väljer du **Döda** för att stoppa topologin.

    > [!NOTE]  
    > Stormtopologier fortsätter att köras tills de inaktiveras eller klustret tas bort.

## <a name="transactional-topology"></a>Transaktionstopologi

Den tidigare topologin är icke-transaktionell. Komponenterna i topologin implementerar inte funktioner för att spela upp meddelanden. Om du vill ha en transaktionstopologi skapar du ett projekt och väljer **Stormex som** projekttyp.

Transaktionstopologier implementerar följande för att stödja uppspelning av data:

* **Cachelagring av metadata**: Pipen måste lagra metadata om de data som avges, så att data kan hämtas och avges igen om ett fel inträffar. Eftersom data som skickas av exemplet är små lagras rådata för varje tuppel i en ordlista för repris.

* **Ack**: Varje bult i `this.ctx.Ack(tuple)` topologin kan ringa för att erkänna att det har lyckats bearbeta en tuple. När alla bultar har erkänt `Ack` tuppeln anropas pipmetoden. Metoden `Ack` gör det möjligt för pipen att ta bort data som cachelagrades för repris.

* **Misslyckas**: Varje `this.ctx.Fail(tuple)` bult kan anropa för att indikera att bearbetningen har misslyckats för en tuppel. Felet sprids till `Fail` metoden för pipen, där tuppeln kan spelas upp med hjälp av cachelagrade metadata.

* **Sekvens-ID:** När du avger en tuppel kan ett unikt sekvens-ID anges. Det här värdet identifierar tuppeln `Fail`för uppspelning (och)`Ack` bearbetning. Pipen i projektet **Storm Sample** använder till exempel följande metodanrop när data avges:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Den här koden avger en tuppel som innehåller en mening till standardströmmen, med sekvens-ID-värdet i `lastSeqId`. I det `lastSeqId` här exemplet ökas för varje tuppel som avges.

Som visas i **stormexempelprojektet** kan om en komponent är transaktionell ställas in vid körning, baserat på konfiguration.

## <a name="hybrid-topology-with-c-and-java"></a>Hybridtopologi med C# och Java

Du kan också använda Data Lake-verktyg för Visual Studio för att skapa hybridtopologier, där vissa komponenter är C# och andra är Java.

Ett exempel på en hybridtopologi skapar du ett projekt och väljer **Storm Hybrid Sample**. Den här exempeltypen visar följande begrepp:

* **Java pip** och **C # bult:** Definieras i `HybridTopology_javaSpout_csharpBolt` klassen.

  En transaktionsversion definieras `HybridTopologyTx_javaSpout_csharpBolt` i klassen.

* **C# pip** och **Java bult:** Definieras i `HybridTopology_csharpSpout_javaBolt` klassen.

  En transaktionsversion definieras `HybridTopologyTx_csharpSpout_javaBolt` i klassen.

  > [!NOTE]  
  > Den här versionen visar också hur du använder Clojure-kod från en textfil som en Java-komponent.

Om du vill växla den topologi som används `[Active(true)]` när projektet skickas flyttar du satsen till den topologi som du vill använda innan du skickar den till klustret.

> [!NOTE]  
> Alla Java-filer som krävs tillhandahålls som en del av detta projekt i *JavaDependency-mappen.*

Tänk på följande när du skapar och skickar en hybridtopologi:

* Används `JavaComponentConstructor` för att skapa en instans av Java-klassen för en pip eller bult.

* Används `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` för att serialisera data till eller från Java-komponenter från Java-objekt till JSON.

* När du skickar topologin till servern måste du använda alternativet **Ytterligare konfigurationer** för att ange **Java-filsökvägarna**. Sökvägen som anges ska vara den katalog som innehåller JAR-filerna som innehåller javaklasserna.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET version 0.9.4.203 introducerar en ny klass och metod specifikt för att arbeta med Event Hub-pipen (en Java-pip som läser från Event Hubs). När du skapar en topologi som använder en Event Hub-pip (till exempel med hjälp av exempelmallen Exempel på **Storm EventHub Reader)** använder du följande API:er:

* `EventHubSpoutConfig`klass: Skapar ett objekt som innehåller konfigurationen för pipkomponenten.

* `TopologyBuilder.SetEventHubSpout`metod: Lägger till händelsehubbens pipkomponent i topologin.

> [!NOTE]  
> Du måste fortfarande `CustomizedInteropJSONSerializer` använda för att serialisera data som produceras av pipen.

## <a name="use-configurationmanager"></a>Använd ConfigurationManager

Använd inte **ConfigurationManager** för att hämta konfigurationsvärden från bult- och pipkomponenter. Om du gör det kan det orsaka ett undantag för null-pekaren. Skicka i stället konfigurationen för projektet till Storm-topologin som ett nyckel- och värdepar i topologikontexten. Varje komponent som är beroende av konfigurationsvärden måste hämta dem från kontexten under initieringen.

Följande kod visar hur du hämtar dessa värden:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Om du `Get` använder en metod för att returnera en instans av `Context` `Dictionary<string, Object>` komponenten måste du se till att den skickar både och parametrarna till konstruktorn. Följande exempel är `Get` en grundläggande metod som korrekt skickar dessa värden:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Så här uppdaterar du SCP.NET

De senaste versionerna av SCP.NET supportpaket uppgradering via NuGet. När en ny uppdatering är tillgänglig får du ett uppgraderingsmeddelande. Så här söker du manuellt efter en uppgradering:

1. Högerklicka på projektet i **Solution Explorer** och välj **Hantera NuGet-paket**.

2. Välj **Uppdateringar**i pakethanteraren . Om det finns en uppdatering för SCP.NET supportpaket visas. Välj **Uppdatera** för paketet och välj sedan **OK** för att installera det i dialogrutan **Förhandsgranska ändringar.**

> [!IMPORTANT]  
> Om projektet skapades med en tidigare version av SCP.NET som inte använde NuGet måste du utföra följande steg för att uppdatera till en nyare version:
>
> 1. Högerklicka på projektet i **Solution Explorer** och välj **Hantera NuGet-paket**.
> 2. Använda fältet **Sök,** sök efter och `Microsoft.SCP.Net.SDK` lägg sedan till i projektet.

## <a name="troubleshoot-common-issues-with-topologies"></a>Felsöka vanliga problem med topologier

### <a name="null-pointer-exceptions"></a>Undantag för null-pekare

När du använder en C#-topologi med ett Linux-baserat HDInsight-kluster kan bult- och pipkomponenter som använder **ConfigurationManager** för att läsa konfigurationsinställningar vid körning returnera null-pekarundantag.

Konfigurationen för projektet skickas till Storm-topologin som ett nyckel- och värdepar i topologikontexten. Den kan hämtas från ordlisteobjektet som skickas till dina komponenter när de initieras.

Mer information finns i avsnittet [Använd ConfigurationManager](#use-configurationmanager) i det här dokumentet.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

När du använder en C#-topologi med ett Linux-baserat HDInsight-kluster kan du stöta på följande fel:

`System.TypeLoadException: Failure has occurred while loading a type.`

Det här felet uppstår när du använder en binär som inte är kompatibel med den version av .NET som Mono stöder.

För Linux-baserade HDInsight-kluster kontrollerar du att binärfiler som har sammanställts för .NET 4.5 används i projektet.

### <a name="test-a-topology-locally"></a>Testa en topologi lokalt

Även om det är enkelt att distribuera en topologi till ett kluster, kan du i vissa fall behöva testa en topologi lokalt. Följ följande steg för att köra och testa exempeltopologin i den här artikeln lokalt i utvecklingsmiljön.

> [!WARNING]  
> Lokala tester fungerar bara för grundläggande, C #-only topologier. Du kan inte använda lokala tester för hybridtopologier eller topologier som använder flera strömmar.

1. Högerklicka på projektet i **Solution Explorer**och välj **Egenskaper**. I projektegenskaperna. Ändra sedan **utdatatypen** till **konsolprogram**.

   ![HDInsight Storm-program, projektegenskaper, utdatatyp](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Kom ihåg att ändra **tillbaka utdatatypen** till **klassbiblioteket** innan du distribuerar topologin till ett kluster.

1. Högerklicka på projektet i **Solution Explorer**och välj sedan **Lägg till** > **nytt objekt**. Välj **Klass**och ange *LocalTest.cs* som klassnamn. Slutligen väljer du **Lägg till**.

1. Öppna *LocalTest.cs*och lägg till `using` följande uttryck högst upp:

    ```csharp
    using Microsoft.SCP;
    ```

1. Använd följande kod som innehållet `LocalTest` i klassen:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Ta en stund att läsa igenom koden kommentarer. Den här `LocalContext` koden används för att köra komponenterna i utvecklingsmiljön. Dataströmmen mellan komponenterna till textfiler på den lokala enheten sparas.

1. Öppna *Program.cs*och lägg till följande kod `Main` i metoden:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

1. Spara ändringarna och välj sedan **F5** eller välj **Felsökning** > **Starta felsökning för** att starta projektet. Ett konsolfönster ska visas och loggstatus när testerna fortskrider. När `Tests finished` visas markerar du valfri tangent för att stänga fönstret.

1. Använd **Utforskaren** för att hitta katalogen som innehåller projektet. (Till exempel: *C:\\Användare\\\<your_user_name>\\källa\\reos\\\\WordCount WordCount*.) Öppna sedan *Bin*i den här katalogen och välj sedan *Felsökning*. Du bör se de textfiler som producerades när testerna kördes: *sentences.txt*, *counter.txt*och *splitter.txt*. Öppna varje textfil och kontrollera data.

   > [!NOTE]  
   > Strängdata kvarstår som en matris med decimalvärden i dessa filer. I filen `[[97,103,111]]` **splitter.txt** representerar till exempel ordet *sedan*.

> [!NOTE]  
> Var noga med att ställa in **projekttypen** tillbaka till **klassbibliotek i** projektegenskaperna innan du distribuerar till ett Storm on HDInsight-kluster.

### <a name="log-information"></a>Logga information

Du kan enkelt logga information från `Context.Logger`dina topologikomponenter med hjälp av . Följande kommando skapar till exempel en informationsloggpost:

`Context.Logger.Info("Component started");`

Loggad information kan visas från **Hadoop-serviceloggen**, som finns i **Server Explorer**. Expandera posten för storm i HDInsight-klustret och expandera sedan **Hadoop Service Log**. Slutligen markerar du loggfilen som ska visas.

> [!NOTE]  
> Loggarna lagras i Azure-lagringskontot som används av klustret. Om du vill visa loggarna i Visual Studio måste du logga in på Azure-prenumerationen som äger lagringskontot.

### <a name="view-error-information"></a>Visa felinformation

Så här visar du fel som har uppstått i en löptopologi:

1. Högerklicka på stormen på HDInsight-klustret från **Server Explorer**och välj **Visa stormtopologier**.

   För **pip** och **bultar**innehåller kolumnen **Senaste fel** information om det senaste felet.

2. Välj **pip-ID** eller **Bult-ID** för den komponent som har ett fel i listan. På **informationssidan** visas ytterligare felinformation i avsnittet Fel längst ned på sidan.

3. Om du vill ha mer information väljer du en **port** från avsnittet **Utförare** på sidan för att se stormarbetareloggen under de senaste minuterna.

### <a name="errors-submitting-topologies"></a>Fel som skickar topologier

Om du stöter på fel som skickar en topologi till HDInsight kan du hitta loggar för de serverkomponenter som hanterar topologiöverföring på ditt HDInsight-kluster. Om du vill hämta dessa loggar använder du följande kommando från en kommandorad:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Ersätt *sshuser* med SSH-användarkontot för klustret. Ersätt *klusternamn* med namnet på HDInsight-klustret. Mer information om `scp` `ssh` hur du använder och använder HDInsight finns i [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Inlämningar kan misslyckas av flera skäl:

* JDK är inte installerat eller finns inte i sökvägen.
* Obligatoriska Java-beroenden ingår inte i överföringen.
* Beroenden är inkompatibla.
* Topologinamn dupliceras.

Om loggfilen *hdinsight-scpwebapi.out* innehåller `FileNotFoundException`en kan undantaget orsakas av följande villkor:

* JDK är inte i vägen för utvecklingsmiljön. Kontrollera att JDK är installerat i utvecklingsmiljön och att det `%JAVA_HOME%/bin` finns i sökvägen.
* Du saknar ett Javaberoende. Se till att du inkluderar alla nödvändiga .jar-filer som en del av överföringen.

## <a name="next-steps"></a>Nästa steg

Ett exempel på bearbetning av data från eventhubbar finns i [Bearbeta händelser från Azure Event Hubs med Storm på HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Ett exempel på en C#-topologi som delar data i flera strömmar finns i [exempel på C# Storm](https://github.com/Blackmist/csharp-storm-example).

Mer information om hur du skapar C#-topologier finns i [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Fler sätt att arbeta med HDInsight och fler Storm on HDInsight-exempel finns i följande dokument:

**Microsoft SCP.NET**

* [SCP-programmeringsguide för Apache Storm i Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm på HDInsight**

* [Distribuera och hantera Apache Storm-topologier på Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md)
* [Exempel apachestormtopologier i Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop på HDInsight**

* [Vad är Apache Hive och HiveQL på Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [Använda MapReduce i Apache Hadoop på HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase på HDInsight**

* [Använda Apache HBase i Azure HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)

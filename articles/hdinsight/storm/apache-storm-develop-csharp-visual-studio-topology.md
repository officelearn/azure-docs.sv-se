---
title: Apache Storm topologier med Visual Studio C# och-Azure HDInsight
description: Lär dig hur du skapar Storm- C#topologier i. Skapa en enkel topologi för ord räkning i Visual Studio med hjälp av Hadoop-verktygen för Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 828ec2b925535df3f925093466556447e703cd76
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003815"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Utveckla C# topologier för Apache Storm med hjälp av data Lake verktyg för Visual Studio

Lär dig hur du skapar C# en Apache Storm topologi med hjälp av Azure Data Lake (Apache Hadoop) verktyg för Visual Studio. Det här dokumentet vägleder dig genom processen att skapa ett Storm-projekt i Visual Studio, testa det lokalt och distribuera det till en Apache Storm på Azure HDInsight-kluster.

Du lär dig också hur du skapar hybrid topologier C# som använder och Java-komponenter.

> [!NOTE]  
> Medan stegen i det här dokumentet är beroende av en Windows-utvecklings miljö med Visual Studio, kan det kompilerade projektet skickas till antingen ett Linux-eller Windows-baserat HDInsight-kluster. Endast Linux-baserade kluster skapade efter 28 oktober 2016, stöder SCP.NET-topologier.

Om du vill C# använda en topologi med ett Linux-baserat kluster måste du uppdatera Microsoft. SCP. net. SDK NuGet-paketet som används av projektet till version 0.10.0.6 eller senare. Paketversionen måste även matcha huvudversionen av Storm som är installerad på HDInsight.

| HDInsight-version | Apache Storm version | SCP.NET-version | Standard version av mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(endast på Windows-baserade HDInsight) | Ej tillämpligt |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2. x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]  
> C#-topologier på Linux-baserade kluster måste använda .NET 4.5 och använda Mono för att köra på HDInsight-klustret. Kontrol lera om det finns några möjliga inkompatibiliteter i [monoljud](https://www.mono-project.com/docs/about-mono/compatibility/) .

## <a name="install-visual-studio"></a>Installera Visual Studio

Du kan utveckla C# topologier med SCP.net genom att använda någon av följande versioner av Visual Studio:

* Visual Studio 2012 med uppdatering 4

* Visual Studio 2013 med uppdatering 4 eller [Visual studio 2013 community](https://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 eller [Visual studio 2015 community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (vilken utgåva som helst)

## <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake verktyg för Visual Studio

Om du vill installera Data Lake verktyg för Visual Studio följer du stegen i [Kom igång med data Lake verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Installera Java

När du skickar en Storm-topologi från Visual Studio genererar SCP.NET en zip-fil som innehåller topologin och beroenden. Java används för att skapa dessa zip-filer, eftersom den använder ett format som är mer kompatibelt med Linux-baserade kluster.

1. Installera Java Developer Kit (JDK) 7 eller senare i utvecklings miljön. Du kan hämta Oracle-JDK från [Oracle](https://aka.ms/azure-jdks). Du kan också använda [andra Java-distributioner](https://openjdk.java.net/).

2. `JAVA_HOME` Miljö variabeln måste peka på den katalog som innehåller Java.

3. Miljö variabeln måste `%JAVA_HOME%\bin` innehålla katalogen. `PATH`

Du kan använda följande C# konsol program för att kontrol lera att Java och JDK är korrekt installerade:

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

## <a name="apache-storm-templates"></a>Apache Storm mallar

Data Lake Tools för Visual Studio tillhandahåller följande mallar:

| Projekttyp | Visat |
| --- | --- |
| Storm-program |Ett tomt Storm Topology-projekt. |
| Exempel på Storm Azure SQL Writer |Så här skriver du till Azure SQL Database. |
| Exempel på Storm Azure Cosmos DB Reader |Läsa från Azure Cosmos DB. |
| Exempel på Storm Azure Cosmos DB Writer |Så här skriver du till Azure Cosmos DB. |
| Exempel på Storm EventHub-läsare |Läsa från Azure Event Hubs. |
| Exempel på Storm EventHub-skrivare |Så här skriver du till Azure Event Hubs. |
| Exempel på Storm HBase-läsare |Läsa från HBase i HDInsight-kluster. |
| Exempel på Storm HBase Writer |Så här skriver du till HBase i HDInsight-kluster. |
| Storm hybrid-exempel |Så här använder du en Java-komponent. |
| Storm-exempel |En grundläggande topologi för ord räkning. |

> [!WARNING]  
> Alla mallar fungerar inte med Linux-baserade HDInsight. NuGet-paket som används av mallarna kanske inte är kompatibla med mono. Kontrol lera dokumentet för [mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/) och identifiera eventuella problem genom att använda [.net-portabilitet Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) .

I stegen i det här dokumentet använder du projekt typen Basic Storm Application för att skapa en topologi.

### <a name="apache-hbase-templates-notes"></a>Anteckningar om Apache HBase-mallar

HBase Reader-och Writer-mallar använder HBase-REST API, inte HBase Java API, för att kommunicera med en HBase på HDInsight-kluster.

### <a name="eventhub-templates-notes"></a>Information om EventHub-mallar

> [!IMPORTANT]  
> Den Java-baserade EventHub kanalen-komponenten som ingår i EventHub-läsar mal len fungerar kanske inte med storm på HDInsight version 3,5 eller senare. En uppdaterad version av den här komponenten finns på [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Ett exempel på en topologi som använder den här komponenten och fungerar med storm på HDInsight 3,5 finns i [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Skapa en C# topologi

1. Öppna Visual Studio, Välj **Arkiv** > **nytt**och välj sedan **projekt**.

2. I fönstret **nytt projekt** expanderar du **installerade** > **mallar**och väljer **Azure Data Lake**. Välj **Storm-program**i listan med mallar. Längst ned på skärmen anger du **WORDCOUNT** som namnet på programmet.

    ![Skärm bild av nytt projekt fönster](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

3. När du har skapat projektet bör du ha följande filer:

   * **Program.cs**: Den här filen definierar topologin för projektet. En standardtopologi som består av en kanalen och en bult skapas som standard.

   * **Spout.cs**: Ett exempel på en kanalen som avger slumpmässiga tal.

   * **Bolt.cs**: En exempels bult som ger ett antal tal som har spridits av kanalen.

     När du skapar projektet laddar NuGet ned det senaste [SCP.net-paketet](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementera kanalen

1. Öppna **Spout.cs**. Kanaler används för att läsa data i en topologi från en extern källa. Huvud komponenterna för en kanalen är:

   * **NextTuple**: Anropas av storm när kanalen får generera nya tupler.

   * **Ack** (endast transaktionell topologi): Hanterar bekräftelser som initierats av andra komponenter i topologin för tupler som skickats från kanalen. Genom att bekräfta en tupel vet kanalen att det har bearbetats av underordnade komponenter.

   * **Fungerar inte** (endast transaktionell topologi): Hanterar tupler som inte kan bearbeta andra komponenter i topologin. Genom att implementera en misslyckad metod kan du skapa en ny tupel så att den kan bearbetas igen.

2. Ersätt innehållet i **kanalen** -klassen med följande text: Den här kanalen avger slumpmässigt en mening i topologin.

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

1. Ta bort den befintliga **Bolt.cs** -filen från projektet.

2. I **Solution Explorer**högerklickar du på projektet och väljer **Lägg till** > **nytt objekt**. I listan väljer du **Storm bult**och anger **splitter.cs** som namn. Upprepa processen för att skapa en andra bult med namnet **Counter.cs**.

   * **Splitter.cs**: Implementerar en bult som delar upp meningar i enskilda ord och avger en ny data ström.

   * **Counter.cs**: Implementerar en bult som räknar varje ord och utvärderar en ny ström med ord och antalet för varje ord.

     > [!NOTE]  
     > Dessa bultar läser och skriver till strömmar, men du kan också använda en bult för att kommunicera med källor som en databas eller tjänst.

3. Öppna **splitter.cs**. Den har bara en metod som standard: **Kör**. Metoden Execute anropas när bulten tar emot en tupel för bearbetning. Här kan du läsa och bearbeta inkommande tupler och generera utgående tupler.

4. Ersätt innehållet i **delnings** klassen med följande kod:

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

5. Öppna **Counter.cs**och ersätt klass innehållet med följande kod:

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

Kanaler och bultar är ordnade i ett diagram, vilket definierar hur data flödar mellan komponenter. I den här topologin är grafen följande:

![Diagram över hur komponenter ordnas](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Meningar genereras från kanalen och distribueras till instanser av delnings blixten. Delnings bulten delar upp meningarna i ord som distribueras till räknar bulten.

Eftersom antalet ord hålls lokalt i räknar instansen, vill du se till att vissa ord flödar till samma Counter bult-instans. Varje instans håller reda på vissa ord. Eftersom delnings blixten inte har något tillstånd spelar det ingen roll vilken instans av delnings listen som tar emot vilken mening.

Öppna **program.cs**. Den viktiga metoden är **GetTopologyBuilder**, som används för att definiera topologin som skickas till storm. Ersätt innehållet i **GetTopologyBuilder** med följande kod för att implementera topologin som beskrivs ovan:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

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

1. I **Solution Explorer**högerklickar du på projektet och väljer **Skicka till storm på HDInsight**.

   > [!NOTE]  
   > Ange autentiseringsuppgifterna för din Azure-prenumeration om du uppmanas att göra det. Om du har mer än en prenumeration loggar du in på den som innehåller din storm i HDInsight-klustret.

2. Välj ditt storm på HDInsight-kluster i list rutan **Storm-kluster** och välj sedan **Skicka**. Du kan övervaka om överföringen lyckas genom att använda fönstret **utdata** .

3. När topologin har skickats, ska Storm- **topologierna** för klustret visas. Välj **WORDCOUNT** -topologin i listan om du vill visa information om topologi som körs.

   > [!NOTE]  
   > Du kan också visa **Storm-topologier** från **Server Explorer**. Expandera **Azure** > **HDInsight**, högerklicka på en storm i HDInsight-kluster och välj sedan **Visa Storm-topologier**.

    Om du vill visa information om komponenterna i topologin dubbelklickar du på komponenten i diagrammet.

4. I vyn **topologi** klickar du på **Avsluta** för att stoppa topologin.

   > [!NOTE]  
   > Storm-topologier fortsätter att köras tills de har inaktiverats eller också tas klustret bort.

## <a name="transactional-topology"></a>Transaktionell topologi

Föregående topologi är inte transaktionell. Komponenterna i topologin implementerar inte funktioner för att spela upp meddelanden. Ett exempel på en transaktionell topologi är att skapa ett projekt och välja Storm- **exempel** som projekt typ.

Transaktionella topologier implementerar följande för att ge stöd för uppspelning av data:

* **Cachelagring av metadata**: Kanalen måste lagra metadata om de data som har spridits, så att data kan hämtas och genereras igen om ett fel uppstår. Eftersom de data som genereras av exemplet är små lagras rå data för varje tupel i en ord lista för uppspelning.

* **Ack**: Varje bult i topologin kan anropas `this.ctx.Ack(tuple)` för att bekräfta att den har bearbetat en tupel. När alla bultar har godkänt tuppeln `Ack` anropas metoden för kanalen. `Ack` Metoden tillåter att kanalen tar bort data som cachelagrats för uppspelning.

* **Misslyckad**: Varje bult kan anropa `this.ctx.Fail(tuple)` för att indikera att bearbetningen misslyckades för en tupel. Felen sprids till `Fail` metoden i kanalen, där tuppeln kan spelas upp med hjälp av cachelagrade metadata.

* **Sekvens-ID**: När du avger en tupel kan du ange ett unikt sekvens-ID. Det här värdet identifierar tuppeln för bearbetning av Replay (ack och misslyckande). Till exempel använder kanalen i storm- **exempelprojektet** följande vid sändning av data:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Den här koden ger en tupel som innehåller en mening till standard strömmen, med det sekvens-ID-värde som finns i **lastSeqId**. I det här exemplet ökar **lastSeqId** för varje tupel.

Som det visas i **Storm-exempelprojektet** , om en komponent är transaktionell, kan ställas in vid körning baserat på konfiguration.

## <a name="hybrid-topology-with-c-and-java"></a>Hybrid topologi med C# och Java

Du kan också använda Data Lake verktyg för Visual Studio för att skapa hybrid topologier, där vissa C# komponenter är och andra är Java.

Ett exempel på en hybrid topologi är att skapa ett projekt och välja **Storm hybrid-exempel**. Den här exempel typen visar följande begrepp:

* **Java-kanalen** och  **C# bult**: Definieras i **HybridTopology_javaSpout_csharpBolt**.

    * En transaktionell version definieras i **HybridTopologyTx_javaSpout_csharpBolt**.

* kanalen och **Java-bult**: **C#** Defined in **HybridTopology_csharpSpout_javaBolt**.

    * En transaktionell version definieras i **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]  
  > Den här versionen visar också hur du använder clojure-kod från en textfil som en Java-komponent.

Om du vill byta topologi som används när projektet skickas flyttar `[Active(true)]` du instruktionen till den topologi du vill använda innan du skickar den till klustret.

> [!NOTE]  
> Alla Java-filer som krävs ingår som en del av det här projektet i mappen **JavaDependency** .

Tänk på följande när du skapar och skickar en hybrid topologi:

* Använd **JavaComponentConstructor** för att skapa en instans av Java-klassen för en kanalen eller bult.

* Använd **Microsoft. SCP. Storm. Multilang. CustomizedInteropJSONSerializer** för att serialisera data till eller från Java-komponenter från Java-objekt till JSON.

* När du skickar topologin till servern måste du använda alternativet **ytterligare konfigurationer** för att ange **sökvägar till Java-filen**. Den angivna sökvägen ska vara den katalog som innehåller JAR-filerna som innehåller dina Java-klasser.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET version 0.9.4.203 introducerar en ny klass och metod för att arbeta med Event Hub-kanalen (ett Java-kanalen som läser från Event Hubs). När du skapar en topologi som använder en Event Hub-kanalen använder du följande metoder:

* **EventHubSpoutConfig** -klass: Skapar ett objekt som innehåller konfigurationen för kanalen-komponenten.

* **TopologyBuilder. SetEventHubSpout** -Metod: Lägger till Event Hub kanalen-komponenten i topologin.

> [!NOTE]  
> Du måste fortfarande använda **CustomizedInteropJSONSerializer** för att serialisera data som produceras av kanalen.

## <a id="configurationmanager"></a>Använd ConfigurationManager

Använd inte **ConfigurationManager** för att hämta konfigurations värden från bult-och kanalen-komponenter. Detta kan orsaka ett undantag för null-pekare. I stället överförs konfigurationen för ditt projekt till storm-topologin som nyckel-och värdepar i topologins kontext. Varje komponent som förlitar sig på konfigurations värden måste hämta dem från kontexten under initieringen.

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
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Om du använder en `Get` -metod för att returnera en instans av-komponenten måste du se till att den passerar `Context` både `Dictionary<string, Object>` parametrarna och för-konstruktorn. Följande exempel är en grundläggande `Get` metod som skickar dessa värden korrekt:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Så här uppdaterar du SCP.NET

De senaste versionerna av SCP.NET-uppgraderingen av support paketet via NuGet. När en ny uppdatering är tillgänglig får du ett uppgraderings meddelande. Följ dessa steg om du vill söka efter en uppgradering manuellt:

1. Högerklicka på projektet i **Solution Explorer** och välj **Hantera NuGet-paket**.

2. Välj **uppdateringar**från paket hanteraren. Om det finns en uppdatering visas den. Klicka på **Uppdatera** för att installera paketet.

> [!IMPORTANT]  
> Om projektet har skapats med en tidigare version av SCP.NET som inte använde NuGet, måste du utföra följande steg för att uppdatera till en nyare version:
>
> 1. Högerklicka på projektet i **Solution Explorer** och välj **Hantera NuGet-paket**.
> 2. Använd **Sök** fältet och Sök efter och Lägg sedan till **Microsoft. SCP. net. SDK** i projektet.

## <a name="troubleshoot-common-issues-with-topologies"></a>Felsöka vanliga problem med topologier

### <a name="null-pointer-exceptions"></a>Undantag för null-pekare

När du använder en C# topologi med ett Linux-baserat HDInsight-kluster kan bult-och kanalen-komponenter som använder **ConfigurationManager** för att läsa konfigurations inställningar vid körning returnera null-pekare.

Konfigurationen för ditt projekt överförs till storm-topologin som nyckel-och värdepar i topologins kontext. Den kan hämtas från Dictionary-objektet som skickas till dina komponenter när de initieras.

Mer information finns i avsnittet [ConfigurationManager](#configurationmanager) i det här dokumentet.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

När du använder en C# topologi med ett Linux-baserat HDInsight-kluster kan du stöta på följande fel:

    System.TypeLoadException: Failure has occurred while loading a type.

Det här felet uppstår när du använder en binärfil som inte är kompatibel med den version av .NET som mono stöder.

För Linux-baserade HDInsight-kluster ser du till att ditt projekt använder binärfiler som kompilerats för .NET 4,5.

### <a name="test-a-topology-locally"></a>Testa en topologi lokalt

Även om det är enkelt att distribuera en topologi till ett kluster kan du i vissa fall behöva testa en topologi lokalt. Använd följande steg för att köra och testa exempel sto pol Ogin i den här artikeln lokalt i utvecklings miljön.

> [!WARNING]  
> Lokal testning fungerar endast för Basic C#-topologier. Du kan inte använda lokal testning för Hybrid topologier eller topologier som använder flera strömmar.

1. I **Solution Explorer**högerklickar du på projektet och väljer **Egenskaper**. I projekt egenskaperna ändrar du **utdatatypen till** **konsol programmet**.

    ![Skärm bild av projekt egenskaper med Utdatatyp markerad](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Kom ihåg att ändra **utdatatypen tillbaka till** **klass biblioteket** innan du distribuerar topologin till ett kluster.

1. I **Solution Explorer**högerklickar du på projektet och väljer sedan **Lägg till** > **nytt objekt**. Välj **klass**och ange **LocalTest.cs** som klass namn. Klicka slutligen på **Lägg till**.

1. Öppna **LocalTest.cs**och Lägg till följande **using** -uttryck högst upp:

    ```csharp
    using Microsoft.SCP;
    ```

1. Använd följande kod som innehållet i klassen **LocalTest** :

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

    Ta en stund att läsa igenom kod kommentarerna. I den här koden används **LocalContext** för att köra komponenterna i utvecklings miljön och den behåller data strömmen mellan komponenter till textfiler på den lokala enheten.

1. Öppna **program.cs**och Lägg till följande i **huvud** metoden:

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

1. Spara ändringarna och klicka sedan på **F5** eller Välj **Felsök** > **Starta fel sökning** för att starta projektet. Ett konsol fönster bör visas och logg status visas som test förlopp. När **testerna är klara** trycker du på valfri tangent för att stänga fönstret.

1. Använd **Utforskaren** för att hitta den katalog som innehåller ditt projekt. Exempel: **C:\Users\<your_user_name>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**. I den här katalogen öppnar du **bin**och klickar sedan på **Felsök**. Du bör se de textfiler som skapades när testerna kördes: menings. txt, Counter. txt och delare. txt. Öppna varje textfil och granska data.

   > [!NOTE]  
   > Sträng data sparas som en matris med decimal värden i de här filerna. Till exempel \[är [97 103 111]] i **delare. txt** -filen ordet *och*.

> [!NOTE]  
> Se till att ange **projekt typen** tillbaka till **klass bibliotek** innan du distribuerar till en storm på HDInsight-kluster.

### <a name="log-information"></a>Logg information

Du kan enkelt logga information från dina Topology-komponenter med `Context.Logger`hjälp av. Följande kommando skapar till exempel en informations logg post:

```csharp
Context.Logger.Info("Component started");
```

Loggad information kan visas från **Hadoop-Tjänsteloggen**, som finns i **Server Explorer**. Expandera posten för din storm i HDInsight-klustret och expandera sedan **Hadoop-Tjänsteloggen**. Välj slutligen logg filen som du vill visa.

> [!NOTE]  
> Loggarna lagras på det Azure Storage-konto som används av klustret. Om du vill visa loggarna i Visual Studio måste du logga in på den Azure-prenumeration som äger lagrings kontot.

### <a name="view-error-information"></a>Visa fel information

Använd följande steg för att visa fel som har inträffat i en topologi som körs:

1. Från **Server Explorer**högerklickar du på HDInsight-klustret och väljer **Visa Storm-topologier**.

2. I **kanalen** och **bultarna**innehåller den **senaste fel** kolumnen information om det senaste felet.

3. Välj **kanalen-ID** eller **bult-ID** för komponenten som innehåller ett fel som visas. På informations sidan som visas visas ytterligare fel information i avsnittet **fel** längst ned på sidan.

4. Om du vill ha mer information väljer du en **port** från avsnittet **körningar** på sidan för att se Storm Worker-loggen under de senaste minuterna.

### <a name="errors-submitting-topologies"></a>Fel vid sändning av topologier

Om du stöter på fel när du skickar en topologi till HDInsight kan du hitta loggar för komponenter på Server sidan som hanterar topologin som skickas i HDInsight-klustret. Använd följande kommando från en kommando rad för att hämta loggarna:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Ersätt __sshuser__ med SSH-användarkontot för klustret. Ersätt __kluster__ namn med namnet på HDInsight-klustret. Mer information om hur du `scp` använder `ssh` och med HDInsight finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Det går inte att skicka bidrag av flera orsaker:

* JDK är inte installerat eller finns inte i sökvägen.
* Nödvändiga Java-beroenden ingår inte i överföringen.
* Inkompatibla beroenden.
* Duplicerade Topology-namn.

Om loggen innehåller en `FileNotFoundException`kan det bero på följande: `hdinsight-scpwebapi.out`

* JDK finns inte i sökvägen till utvecklings miljön. Kontrol lera att JDK har installerats i utvecklings miljön och att `%JAVA_HOME%/bin` det finns i sökvägen.
* Du saknar ett Java-beroende. Se till att du inkluderar alla obligatoriska. jar-filer som en del av överföringen.

## <a name="next-steps"></a>Nästa steg

Ett exempel på att bearbeta data från Event Hubs finns i [process händelser från Azure Event Hubs med storm på HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Ett exempel på en C# topologi som delar upp data strömmar i flera strömmar finns i [ C# Storm-exempel](https://github.com/Blackmist/csharp-storm-example).

Mer information om hur du skapar C# topologier finns i [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Fler sätt att arbeta med HDInsight och mer storm på HDInsight-exempel finns i följande dokument:

**Microsoft SCP.NET**

* [Guide för SCP-programmering](apache-storm-scp-programming-guide.md)

**Apache Storm på HDInsight**

* [Distribuera och övervaka topologier med Apache Storm på HDInsight](apache-storm-deploy-monitor-topology.md)
* [Exempeltopologier för Apache Storm på HDInsight](apache-storm-example-topology.md)

**Apache Hadoop på HDInsight**

* [Använda Apache Hive med Apache Hadoop på HDInsight](../hadoop/hdinsight-use-hive.md)
* [Använda Apache gris med Apache Hadoop på HDInsight](../hadoop/hdinsight-use-pig.md)
* [Använd Apache Hadoop MapReduce med Apache Hadoop på HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase på HDInsight**

* [Komma igång med Apache HBase på HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)

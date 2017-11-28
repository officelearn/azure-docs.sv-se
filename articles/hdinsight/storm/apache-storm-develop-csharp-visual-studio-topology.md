---
title: Apache Storm-topologier med Visual Studio och C# - Azure HDInsight | Microsoft Docs
description: "Lär dig mer om att skapa Storm-topologier i C#. Skapa en enkel word antal topologi i Visual Studio med hjälp av Hadoop-verktyg för Visual Studio."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: larryfr
ms.openlocfilehash: d777d467b3f0d4ef6101dffa551ec5c85feb209c
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Utveckla C#-topologier för Apache Storm med Data Lake-verktyg för Visual Studio

Lär dig hur du skapar en C# Storm-topologi med Azure Data Lake (Hadoop)-verktyg för Visual Studio. Det här dokumentet går igenom processen att skapa ett Storm-projekt i Visual Studio, lokal testning och distribuera den till en Apache Storm på Azure HDInsight-kluster.

Du också lära dig hur du skapar hybridtopologier som använder C# och Java-komponenter.

> [!NOTE]
> När stegen i det här dokumentet är beroende av en Windows-utvecklingsmiljö med Visual Studio, kan kompilerade projektet skickas till en Linux- eller Windows-baserade HDInsight-kluster. Linux-baserade kluster som skapas efter den 28 oktober 2016 stöder endast SCP.NET topologier.

Om du vill använda en C#-topologi med en Linux-baserade kluster, måste du uppdatera Microsoft.SCP.Net.SDK NuGet-paketet används av ditt projekt till version 0.10.0.6 eller senare. Paketversionen måste även matcha huvudversionen av Storm som är installerad på HDInsight.

| HDInsight-version | Storm-version | SCP.NET version | Monoljud standardversion |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(endast på Windows-baserade HDInsight) | Ej tillämpligt |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3.5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]
> C#-topologier på Linux-baserade kluster måste använda .NET 4.5 och använda Mono för att köra på HDInsight-klustret. Kontrollera [monoljud kompatibilitet](http://www.mono-project.com/docs/about-mono/compatibility/) för potentiella inkompatibiliteter.

> [!WARNING]
> Om du får problem i bygget projekt som använder SCP.NET version 1.0.0.x, kontakta Microsoft support om du behöver hjälp.

## <a name="install-visual-studio"></a>Installera Visual Studio

Du kan utveckla C#-topologier med SCP.NET med någon av följande versioner av Visual Studio:

* Visual Studio 2012 med [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

* Visual Studio 2013 med [uppdatering 4](http://www.microsoft.com/download/details.aspx?id=44921) eller [Visual Studio Community 2013](http://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 eller [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* 2017 för Visual Studio (någon utgåva)

## <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake-verktyg för Visual Studio

Om du vill installera Data Lake-verktyg för Visual Studio, följer du stegen i [komma igång med Data Lake-verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Installera Java

När du skickar en Storm-topologi från Visual Studio genererar SCP.NET en zip-fil som innehåller topologi och beroenden. Java används för att skapa dessa zip-filer eftersom den använder ett format som är mer kompatibel med Linux-baserade kluster.

1. Installera Java Developer Kit (JDK) 7 eller senare på din utvecklingsmiljö. Du kan hämta Oracle JDK från [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html). Du kan också använda [andra Java-distributioner](http://openjdk.java.net/).

2. Den `JAVA_HOME` miljövariabeln måste peka på den katalog som innehåller Java.

3. Den `PATH` miljövariabeln måste innehålla den `%JAVA_HOME%\bin` directory.

Du kan använda de följande C#-konsolprogrammet för att kontrollera att Java och JDK är korrekt installerat:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable(“JAVA_HOME”);
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @”\bin”, “jar.exe”);
               if (File.Exists(jarExe))
               {
                   Console.WriteLine(“JAVA Is Installed properly”);
                    return;
               }
               else
               {
                   Console.WriteLine(“A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.”);
               }
           }
           else
           {
             Console.WriteLine(“A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.”);
           }
       }  
   }
}
```

## <a name="storm-templates"></a>Storm-mallar

Data Lake-verktyg för Visual Studio innehåller följande mallar:

| Projekttyp | Visar |
| --- | --- |
| Storm-program |Ett tomt Storm-topologi projekt. |
| Storm Azure SQL Writer exempel |Hur du skriver till Azure SQL Database. |
| Storm Azure Cosmos DB Reader-exempel |Så att läsa från Azure Cosmos DB. |
| Storm Azure Cosmos DB Writer exempel |Hur du skriver till Azure Cosmos DB. |
| Storm EventHub Reader-exempel |Så här läsas från Händelsehubbar i Azure. |
| Storm EventHub Writer exempel |Så att skriva till Azure Event Hubs. |
| Storm HBase Reader-exempel |Hur ska läsas från HBase på HDInsight-kluster. |
| Storm HBase Writer exempel |Så att skriva till HBase på HDInsight-kluster. |
| Storm Hybrid-exempel |Hur du använder en Java-komponent. |
| Storm-exempel |En grundläggande word count-topologi. |

> [!WARNING]
> Inte alla mallar för att fungera med Linux-baserade HDInsight. Nuget-paket som används av mallarna är kanske inte kompatibelt med Mono. Kontrollera den [monoljud kompatibilitet](http://www.mono-project.com/docs/about-mono/compatibility/) dokumentera och använda den [.NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) identifiera potentiella problem.

I stegen i det här dokumentet använder du projekttypen grundläggande Storm-program för att skapa en topologi.

### <a name="hbase-templates-notes"></a>HBase mallar anteckningar

HBase läsare och skrivare mallar använder HBase REST-API, inte i HBase Java API: et för att kommunicera med en HBase på HDInsight-kluster.

### <a name="eventhub-templates-notes"></a>Anteckningar för EventHub-mallar

> [!IMPORTANT]
> Java-baserad EventHub kanal komponent som ingår i mallen EventHub läsare kanske inte fungerar med Storm på HDInsight version 3.5 eller senare. Det finns en uppdaterad version av den här komponenten på [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

En exempeltopologi som använder den här komponenten och fungerar med Storm på HDInsight 3.5 finns [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Skapa en C#-topologi

1. Öppna Visual Studio, markera **filen** > **ny**, och välj sedan **projekt**.

2. Från den **nytt projekt** fönstret expanderar **installerad** > **mallar**, och välj **Azure Data Lake**. Välj i listan över mallar **Storm-program**. Längst ned på skärmen Ange **WordCount** som namnet på programmet.

    ![Skärmbild av nytt projekt fönster](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. När du har skapat projektet, bör du ha följande filer:

   * **Program.CS**: den här filen definierar topologin för ditt projekt. En standard-topologi som består av en kanal och en bult skapas som standard.

   * **Spout.CS**: en exempel-kanal som genererar slumptal.

   * **Bolt.CS**: ett exempel bult som ett antal siffror som sänds av kanal.

     När du skapar projektet NuGet laddar ned senast [SCP.NET paketet](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementera kanal

1. Öppna **Spout.cs**. Kanaler används för att läsa data i en topologi från en extern källa. Huvudkomponenterna för en kanal är:

   * **NextTuple**: anropas av Storm när kanal som är tillåtet att generera nya tupplar.

   * **Ack** (endast transaktionella topologi): hanterar bekräftelser som initieras av andra komponenter i topologin för tupplar skickas från kanal. Bekräfta en tuppel kan kanal vet att den har bearbetats av underordnade komponenter.

   * **Misslyckas** (endast transaktionella topologi): hanterar tupplar som misslyckas bearbetning av andra komponenter i topologin. Implementera en metod som misslyckas kan du generera tuppeln igen så att den kan bearbetas igen.

2. Ersätt innehållet i den **prata** klassen med följande text. Den här kanal avger slumpmässigt en mening i topologin.

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

1. Ta bort det befintliga **Bolt.cs** filen från projektet.

2. I **Solution Explorer**, högerklicka på projektet och välj **Lägg till** > **nytt objekt**. I listan, väljer du **Storm bult**, och ange **Splitter.cs** som namn. Upprepa den här processen att skapa en andra bult med namnet **Counter.cs**.

   * **Splitter.CS**: implementerar en bult som delar upp meningar i enskilda ord och genererar en ny dataström ord.

   * **Counter.CS**: implementerar en bult som räknar varje ord och genererar en ny dataström ord och antalet för varje ord.

     > [!NOTE]
     > Dessa bultar läsa och skriva till dataströmmar, men du kan också använda en bult för att kommunicera med källor, till exempel en databas eller tjänst.

3. Öppna **Splitter.cs**. Det har endast en metod som standard: **kör**. Execute-metoden anropas när bulten tar emot en tuppel för bearbetning. Här kan du läsa och bearbeta inkommande tupplar och generera utgående tupplar.

4. Ersätt innehållet i den **delningslisten** klassen med följande kod:

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

5. Öppna **Counter.cs**, och Ersätt det klassen med följande:

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

Kanaler och bultar ordnas i ett diagram som definierar hur data flödar mellan komponenter. För den här topologin är diagrammet:

![Diagram över hur komponenter ordnas](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Meningar orsakat från kanal och distribueras till instanser av bulten delningslisten. Delningslisten bulten delar meningarna i ord, som har distribuerats till bulten räknaren.

Eftersom ordräkning lagras lokalt i räknarinstansen, som vi vill se till att specifika ord flöda till samma bult räknarinstansen. Varje instans håller reda på specifika ord. Eftersom delningslisten bulten upprätthåller inget tillstånd, det verkligen spelar ingen roll vilken instans på delningslisten tar emot vilka meningen.

Öppna **Program.cs**. Metoden viktigt är **GetTopologyBuilder**, som används för att definiera topologin som skickas till Storm. Ersätt innehållet i **GetTopologyBuilder** med följande kod för att implementera topologin som beskrivs ovan:

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

1. I **Solution Explorer**, högerklicka på projektet och välj **skicka till Storm på HDInsight**.

   > [!NOTE]
   > Om du uppmanas du ange autentiseringsuppgifterna för din Azure-prenumeration. Om du har mer än en prenumeration kan du logga in på den tabell som innehåller ditt Storm på HDInsight-kluster.

2. Välj ditt Storm på HDInsight-kluster från de **Storm-kluster** listrutan och välj sedan **skicka**. Du kan övervaka om överföring har genomförts med den **utdata** fönster.

3. När topologin har skickats, den **Storm-topologier** för klustret ska visas. Välj den **WordCount** topologi från listan för att visa information om topologin som körs.

   > [!NOTE]
   > Du kan också visa **Storm-topologier** från **Server Explorer**. Expandera **Azure** > **HDInsight**, högerklicka på ett Storm på HDInsight-kluster och välj sedan **visa Storm-topologier**.

    Dubbelklicka på komponenten i diagrammet om du vill visa information om komponenter i topologin.

4. Från den **Topology Summary** klickar du på **Kill** att stoppa topologin.

   > [!NOTE]
   > Storm-topologier fortsätter att köras tills de inaktiveras eller ta bort klustret.

## <a name="transactional-topology"></a>Transaktionell topologi

Den tidigare topologin är icke-transaktionell. Komponenter i topologin implementeras inte funktioner för att spela upp meddelanden. Ett exempel på en transaktionell topologi, skapa ett projekt och välj **Storm exempel** som projekttypen.

Transaktionell topologier implementera följande för att stödja omsändning av data:

* **Cachelagring av metadata**: kanal måste lagra metadata om data som sänds, så att data kan hämtas och orsakat igen om ett fel inträffar. Eftersom data som sänds av exemplet är liten lagras rådata för varje tuppel i en ordlista för att svarsidentifiering.

* **Ack**: varje bult i topologin kan anropa `this.ctx.Ack(tuple)` att bekräfta att den har bearbetat en tuppel. När alla bultar har ADE tuppel, den `Ack` kanal-metoden har anropats. Den `Ack` metoden kan kanal kan ta bort data som har lagrats i cacheminnet för att svarsidentifiering.

* **Misslyckas**: varje bult kan anropa `this.ctx.Fail(tuple)` att indikera att bearbetningen misslyckades för en tuppel. Felet sprids till den `Fail` metod för kanal, där tuppeln spelas upp med hjälp av cachelagrade metadata.

* **Aktivitetssekvensen ID**: när avger en tuppel, en unik sekvens-ID kan anges. Det här värdet identifierar tuppeln för bearbetning av replay (Ack och misslyckas). Till exempel kanal i den **Storm exempel** projektet använder följande när avger data:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Den här koden skickar en tuppel som innehåller en mening till standard-dataström med sekvens-ID värdet i **lastSeqId**. I det här exemplet **lastSeqId** ökas för varje tuppel orsakat.

Som visas i den **Storm exempel** projekt, om en komponent är transaktionell kan anges vid körning, baserat på konfigurationen.

## <a name="hybrid-topology-with-c-and-java"></a>Hybrid-topologi med C# och Java

Du kan också använda Data Lake-verktyg för Visual Studio för att skapa hybridtopologier där vissa komponenter är C# och andra är Java.

Ett exempel på en hybrid-topologi, skapa ett projekt och välj **Storm Hybrid exempel**. Den här typen av exemplet visar följande:

* **Java-kanal** och **C# bult**: definierade i **HybridTopology_javaSpout_csharpBolt**.

    * En transaktionell version har definierats i **HybridTopologyTx_javaSpout_csharpBolt**.

* **C#-kanal** och **Java bult**: definierade i **HybridTopology_csharpSpout_javaBolt**.

    * En transaktionell version har definierats i **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]
  > Den här versionen visas också hur du använder Clojure kod från en textfil som en Java-komponent.


Du kan växla topologin som används när projektet har skickats genom att flytta den `[Active(true)]` instruktionen till i topologin som du vill använda, innan den skickas till klustret.

> [!NOTE]
> Alla Java-filer som krävs har angetts som en del av det här projektet i den **JavaDependency** mapp.

Tänk på följande när du skapar och skickar en hybrid-topologi:

* Du måste använda **JavaComponentConstructor** att skapa en instans av Java-klass för en kanal eller bultar.

* Du bör använda **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** att serialisera data till eller från Java-komponenter från Java-objekt till JSON.

* När du skickar in topologin till servern, måste du använda den **ytterligare konfigurationer** alternativet för att ange den **Java sökvägar**. Den angivna sökvägen ska vara den katalog som innehåller de JAR-filer som innehåller Java-klasser.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET version 0.9.4.203 införs en ny klass och metod för att arbeta med Event Hub-kanal (en Java kanal som läser från Händelsehubbar). När du skapar en topologi som använder en Event Hub-kanal kan du använda följande metoder:

* **EventHubSpoutConfig** klass: skapar ett objekt som innehåller konfiguration för kanal-komponenten.

* **TopologyBuilder.SetEventHubSpout** metod: lägger till komponenten Event Hub-kanal till i topologin.

> [!NOTE]
> Du måste använda den **CustomizedInteropJSONSerializer** att serialisera data som produceras av kanal.

## <a id="configurationmanager"></a>Använd ConfigurationManager

Använd inte **ConfigurationManager** att hämta konfigurationsvärden från bult och prata komponenter. Detta kan orsaka ett undantag för null-pekare. Konfigurationen för ditt projekt skickas i stället till Storm-topologi som en nyckel och värde-par i kontexten topologi. Varje komponent som förlitar sig på konfigurationsvärden hämta dem från kontexten under initieringen.

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

Om du använder en `Get` metod för att returnera en instans av komponenten, måste du se till att passerar både den `Context` och `Dictionary<string, Object>` parametrar som konstruktorn. Följande exempel är en grundläggande `Get` metod som korrekt skickar dessa värden:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Så här uppdaterar du SCP.NET

Nya versioner av SCP.NET stöd för uppgradering av paketet via NuGet. När en ny uppdatering är tillgänglig kan få du ett meddelande om uppgraderingen. Om du vill söka manuellt efter en uppgradering, gör du följande:

1. I **Solution Explorer**, högerklicka på projektet och välj **hantera NuGet-paket**.

2. Package manager, Välj **uppdateringar**. Om en uppdatering är tillgänglig, visas. Klicka på **uppdatering** för paketet att installera den.

> [!IMPORTANT]
> Om projektet har skapats med en tidigare version av SCP.NET som inte har använt NuGet, måste du utföra följande steg för att uppdatera till en nyare version:
>
> 1. I **Solution Explorer**, högerklicka på projektet och välj **hantera NuGet-paket**.
> 2. Med hjälp av den **Sök** fältet, söka efter och Lägg sedan till **Microsoft.SCP.Net.SDK** i projektet.

## <a name="troubleshoot-common-issues-with-topologies"></a>Felsöka vanliga problem med topologier

### <a name="null-pointer-exceptions"></a>Undantag för null-pekare

När du använder en C#-topologi med en Linux-baserade HDInsight-kluster, bultar och prata komponenter som använder **ConfigurationManager** att läsa konfigurationsinställningar vid körning kan returnera null-pekare undantag.

Konfigurationen för ditt projekt skickas till Storm-topologi som en nyckel och värde-par i kontexten topologi. De kan hämtas från Ordlisteobjekt som skickas till komponenterna när de har initierats.

Mer information finns i [ConfigurationManager](#configurationmanager) i det här dokumentet.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Följande fel kan uppstå när du använder en C#-topologi med en Linux-baserade HDInsight-kluster:

    System.TypeLoadException: Failure has occurred while loading a type.

Det här felet uppstår när du använder en binärfil som inte är kompatibel med versionen av .NET som har stöd för Mono.

För Linux-baserade HDInsight-kluster, kontrollerar du att projektet använder binärfiler som kompilerats för .NET 4.5.

### <a name="test-a-topology-locally"></a>Testa en topologi lokalt

Även om det är lätt att distribuera en topologi till ett kluster, i vissa fall kan du behöva testa en topologi lokalt. Använd följande steg för att köra och testa en exempeltopologi i den här självstudiekursen lokalt i din utvecklingsmiljö.

> [!WARNING]
> Lokal testning fungerar bara för basic och C#-topologier endast. Du kan inte använda lokala testar hybridtopologier eller topologier med flera strömmar.

1. I **Solution Explorer**, högerklicka på projektet och välj **egenskaper**. I projektegenskaperna för kan ändra den **utdata typen** till **konsolprogram**.

    ![Skärmbild av projektegenskaperna med utdatatypen markerat](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Kom ihåg att ändra den **utdata typen** tillbaka till **klassbiblioteket** innan du distribuerar topologin till ett kluster.

2. I **Solution Explorer**, högerklicka på projektet och välj sedan **Lägg till** > **nytt objekt**. Välj **klassen**, och ange **LocalTest.cs** som namnet på klassen. Klicka slutligen på **Lägg till**.

3. Öppna **LocalTest.cs**, och Lägg till följande **med** uttryck överst:

    ```csharp
    using Microsoft.SCP;
    ```

4. Använd följande kod som innehållet i den **LocalTest** klass:

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

    Ta en stund att läsa igenom koden kommentarerna. Den här koden använder **LocalContext** köra komponenterna i utvecklingsmiljön och kvarstår dataströmmen mellan komponenter till textfiler på den lokala enheten.

1. Öppna **Program.cs**, och Lägg till följande för att den **Main** metoden:

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

2. Spara ändringarna och klicka sedan på **F5** eller välj **felsöka** > **Start Debugging** starta projektet. Ett konsolfönster ska visas och logga status som testerna pågår. När **testerna slutförda** visas, tryck på valfri tangent för att stänga fönstret.

3. Använd **Windows Explorer** att hitta katalogen som innehåller ditt projekt. Till exempel: **C:\Users\<användarnamn > \Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Öppna i den här katalogen, **Bin**, och klicka sedan på **felsöka**. Du bör se textfiler som skapas när testerna kördes: sentences.txt, counter.txt och splitter.txt. Öppna varje textfil och inspektera data.

   > [!NOTE]
   > Strängdata kvarstår som en matris med decimalvärden i dessa filer. Till exempel \[[97,103,111]] i den **splitter.txt** filen är ordet *och*.

> [!NOTE]
> Se till att ange den **projekttyp** tillbaka till **klassbiblioteket** innan du distribuerar till ett Storm på HDInsight-kluster.

### <a name="log-information"></a>Informationen i felloggen

Du kan enkelt logga information från din topologi-komponenter med hjälp av `Context.Logger`. Följande skapar ett informationsmeddelande loggpost:

```csharp
Context.Logger.Info("Component started");
```

Kan visa loggade informationen från den **loggen för Hadoop-tjänsten**, som finns i **Server Explorer**. Expandera posten för ditt Storm på HDInsight-klustret och expandera sedan **loggen för Hadoop-tjänsten**. Välj slutligen loggfilen för att visa.

> [!NOTE]
> Loggfilerna lagras i Azure storage-konto som används av klustret. Om du vill visa loggfilerna i Visual Studio kan logga du in på Azure-prenumerationen som äger lagringskontot.

### <a name="view-error-information"></a>Visa information om fel

Använd följande steg för att visa fel som uppstått i en topologi som körs:

1. Från **Server Explorer**, högerklicka på Storm på HDInsight-kluster och välj **visa Storm-topologier**.

2. För den **prata** och **Bolts**, **senaste fel** kolumnen innehåller information om det senaste felet.

3. Välj den **prata Id** eller **bulten Id** för komponenten som har ett fel visas. På sidan med information som visas, ytterligare fel information visas i den **fel** avsnittet längst ned på sidan.

4. Om du vill ha mer information, Välj en **Port** från den **Executors** avsnitt på sidan, för att se Storm worker logg under de senaste minuterna.

### <a name="errors-submitting-topologies"></a>Fel skickar topologier

Om du stöter på fel som skickar en topologi till HDInsight, hittar du loggar för serversidan komponenter som hanterar topologi skickas på ditt HDInsight-kluster. Om du vill hämta loggarna använder du följande kommando från en kommandorad:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Ersätt __sshuser__ med SSH-användarkonto för klustret. Ersätt __klusternamn__ med namnet på HDInsight-klustret. Mer information om hur du använder `scp` och `ssh` med HDInsight, se [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Bidrag kan misslyckas av flera skäl:

* JDK är inte installerat eller är inte i sökvägen.
* Nödvändiga Java beroendena ingår inte i programobjektet.
* Inkompatibel beroenden.
* Dubblettnamn topologi.

Om den `hdinsight-scpwebapi.out` loggen innehåller en `FileNotFoundException`, detta kan bero på följande villkor:

* JDK finns inte i sökvägen på utvecklingsmiljön. Kontrollera att JDK är installerat i utvecklingsmiljön och att `%JAVA_HOME%/bin` finns i sökvägen.
* Du saknar ett Java-beroende. Kontrollera att du inkluderar alla nödvändiga .jar-filer som en del av överföring.

## <a name="next-steps"></a>Nästa steg

Ett exempel på databearbetning från Händelsehubbar finns [bearbeta händelser från Azure Event Hubs med Storm på HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Ett exempel på en C#-topologi som delar upp dataströmmen data i flera strömmar finns [C# Storm exempel](https://github.com/Blackmist/csharp-storm-example).

Om du vill hitta mer information om hur du skapar C#-topologier finns [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Fler sätt att arbeta med HDInsight och mer Storm på HDInsight-exempel finns i följande dokument:

**Microsoft SCP.NET**

* [Programmeringsguide för SCP](apache-storm-scp-programming-guide.md)

**Apache Storm på HDInsight**

* [Distribuera och övervaka topologier med Apache Storm på HDInsight](apache-storm-deploy-monitor-topology.md)
* [Exempeltopologier för Storm på HDInsight](apache-storm-example-topology.md)

**Apache Hadoop i HDInsight**

* [Använda Hive med Hadoop i HDInsight](../hadoop/hdinsight-use-hive.md)
* [Använda Pig med Hadoop i HDInsight](../hadoop/hdinsight-use-pig.md)
* [Använda MapReduce med Hadoop i HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase på HDInsight**

* [Komma igång med HBase på HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)

---
title: Indexera mediefiler med Azure Media Indexer
description: Azure Media Indexer kan du göra innehållet i mediefiler sökbart och att generera en fulltextavskrift för dold textning och nyckelord. Det här avsnittet visar hur du använder Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: cfowler
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/20/2017
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: b6e0b2027dd4edbf1688a6c71efe2dde37fbbf96
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473375"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexera mediefiler med Azure Media Indexer
Azure Media Indexer kan du göra innehållet i mediefiler sökbart och att generera en fulltextavskrift för dold textning och nyckelord. Du kan bearbeta en mediefil eller flera mediefiler i en batch.  

> [!IMPORTANT]
> Vid indexering av innehåll, se till att använda mediefiler med tydliga tal (utan bakgrundsmusik, buller, effekter eller mikrofon hiss). Några exempel på rätt innehåll är: registreras möten, Föreläsningar och presentationer. Följande innehåll är kanske inte lämpligt för indexering: filmer, TV-program, allt med blandad ljud och visuella effekter, registreras dåligt innehåll med bakgrundsljud (hiss).
> 
> 

Ett indexeringsjobb kan generera följande utdata:

* Filer med dold textning i följande format: **SAMISKA**, **TTML**, och **WebVTT**.
  
    Filer med dold textning innehåller en tagg som kallas Recognizability, vilka resultat som ett indexeringsjobb baserat på hur känna igen tal i källan videon är.  Du kan använda värdet för Recognizability till skärmen utdatafilerna användbarhet. En låg poäng skulle innebära dåliga indexering resultat på grund av ljud kvalitet.
* Nyckelordsfil (XML).
* Ljud indexera blob-fil (AIB) för användning med SQLServer.
  
    Mer information finns i [med AIB-filer med Azure Media Indexer och SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

Den här artikeln visar hur du skapar indexerade jobb till **indexera en tillgång** och **Index flera filer**.

Läs de senaste uppdateringarna i Azure Media Indexer [Media Services-blogg](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Med hjälp av konfigurations-och manifest för indexering uppgifter
Du kan ange mer information för dina indexering aktiviteter med hjälp av en uppgiftskonfiguration. Du kan till exempel ange vilka metadata för din mediefil. Dessa metadata används av motorn språk för att expandera dess ordförråd och förbättrar avsevärt taligenkänningen.  Du kan också ange de önskade utdatafilerna.

Du kan också bearbeta flera mediefiler samtidigt med hjälp av en manifestfil.

Mer information finns i [uppgift förinställning för Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indexera en tillgång
Följande metod överför en mediefil som en tillgång och skapar ett jobb för att indexera tillgången.

Om ingen konfigurationsfil anges indexeras mediefilen med alla standardinställningar.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a id="output_files"></a>Utdatafiler
Som standard genererar ett indexeringsjobb följande utdatafiler. Filerna lagras i den första utdatatillgången.

Om det finns fler än en inkommande mediefil, skapar Indexer en manifestfil för utdata för jobbet, med namnet ”JobResult.txt”. För varje indatakort mediefil är resulterande AIB, SAMISKA, TTML, WebVTT och nyckelordet filer sekventiellt numrerade och namnges med ”Alias”.

| Filnamn | Beskrivning |
| --- | --- |
| **InputFileName.aib** |Indexera blob ljudfil. <br/><br/> Ljudfil indexera Blob (AIB) är en binär fil som kan sökas i Microsoft SQL server med fulltextsökning.  AIB-fil är mer kraftfullt än enkla undertextfiler eftersom den innehåller alternativ för varje ord, vilket gör att en mycket rikare upplevelse för sökning. <br/> <br/>Det kräver installation av indexeraren SQL-tillägg på en dator som kör Microsoft SQL server 2008 eller senare. Söka AIB med hjälp av Microsoft SQL ger server-fulltextsökning mer exakta sökresultat än filer med dold textning som genererats av WAMI. Det beror på att AIB innehåller word-alternativ som låter lika filer med dold textning innehåller högsta förtroende ordet för varje segment av ljudet. Om du söker efter talade ord är av upmost betydelse, rekommenderas det att använda AIB i-tillsammans med Microsoft SQL Server.<br/><br/> Ladda ned tillägget genom att klicka på <a href="https://aka.ms/indexersql">SQL-tillägg för Azure Media Indexer</a>. <br/><br/>Det är också möjligt att använda andra sökmotorer, till exempel Apache Lucene/Solr som helt enkelt indexerar videon baserat på dold textning och nyckelord XML-filer, men detta resulterar i mindre exakta sökresultat. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Stängda textning (kopia) filer i SAMI-, TTML- och WebVTT-format.<br/><br/>De kan användas för att göra det tillgängligt för personer med funktionshinder höra ljud- och bildfiler.<br/><br/>Filer med dold textning innehåller en tagg som kallas <b>Recognizability</b> som poängsätter en indexeringsjobb baserat på hur känna igen tal i källan videon finns.  Du kan använda värdet för <b>Recognizability</b> till skärmen utdatafilerna användbarhet. En låg poäng skulle innebära dåliga indexering resultat på grund av ljud kvalitet. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Information om nyckelord och filer. <br/><br/>Nyckelordsfil är en XML-fil med nyckelord extraheras från talinnehållet med frekvens och förskjutning information. <br/><br/>Information om filen är en oformaterad textfil som innehåller detaljerad information om varje term som känns igen. Den första raden är speciell och innehåller Recognizability poängen. Varje efterföljande rad är en tabbavgränsade över följande data: starta tid, sluttid, ord eller en viss fras, förtroende. Tiderna anges i sekunder och var säker ges som ett tal mellan 0-1. <br/><br/>Exempel rad: ”1,20 1,45 word 0.67” <br/><br/>Dessa filer kan används för flera olika skäl, t.ex, för att göra en talanalys, eller utsatta för sökmotorer t.ex Bing, Google eller Microsoft SharePoint för att göra mediefilerna mer synliga, men även som används för att leverera mer relevant annonser. |
| **JobResult.txt** |Utdata manifest, visas endast när indexering av flera filer, som innehåller följande information:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Fel</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Om inte alla inkommande mediefiler indexeras har, indexering jobbet misslyckas med felkoden 4000. Mer information finns i [felkoder](#error_codes).

## <a name="index-multiple-files"></a>Indexera flera filer
Följande metod laddar upp flera mediefiler som en tillgång och skapar ett jobb för att indexera de här filerna i en batch.

En manifestfil med tillägget ”.lst” är skapade och överför till tillgången. Manifestfilen innehåller listan över alla resursfiler. Mer information finns i [uppgift förinställning för Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>Lyckades delvis jobb
Om inte alla inkommande mediefiler indexeras har, indexering jobbet misslyckas med felkoden 4000. Mer information finns i [felkoder](#error_codes).

Samma utdata (som slutfört jobb) genereras. Du kan referera till utdata manifestfilen att ta reda på vilka indatafilerna växlas enligt kolumnvärdena fel. För inkommande filer som misslyckades och den resulterande AIB, SAMISKA, TTML, WebVTT och nyckelordet genereras filer inte.

### <a id="preset"></a> Uppgiften förinställd för Azure Media Indexer
Bearbetningen från Azure Media Indexer kan anpassas genom att tillhandahålla en valfri uppgift förinställda tillsammans med uppgiften.  Nedan beskrivs formatet för den här XML-format.

| Namn | Kräv | Beskrivning |
| --- | --- | --- |
| **Indata** |false |Tillgången fil(er) som du vill indexera.</p><p>Azure Media Indexer stöder följande media format: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Du kan ange namnet på filen (s) i den **namn** eller **lista** attributet för den **inkommande** element (som visas nedan). Om du inte anger vilken tillgångsfil till index plockas den primära filen. Om ingen primär tillgångsfil anges indexeras den första filen i indatatillgången.</p><p>För att uttryckligen ange filnamnet tillgången, gör du:<br/>`<input name="TestFile.wmv">`<br/><br/>Du kan också indexera flera tillgångsfiler på samma gång (upp till 10 filer). Gör så här:<br/><br/><ol class="ordered"><li><p>Skapa en textfil (manifestfilen) och ge den ett .lst-tillägg. </p></li><li><p>Lägg till en lista över alla tillgångar filnamn i din inkommande tillgång till den här manifestfilen. </p></li><li><p>Lägga till () manifestet uppladdningsfilen i tillgången.  </p></li><li><p>Ange namnet på manifestfilen i angivna indata-attribut.<br/>`<input list="input.lst">`</li></ol><br/><br/>Obs! Om du lägger till fler än 10 filer att manifestfilen misslyckas indexering jobbet med felkoden 2006. |
| **metadata** |false |Metadata för den angivna resursen/filerna som används för ordförråd.  Användbart att förbereda indexerare för att identifiera icke-standard ordförråd ord, till exempel egennamn.<br/>`<metadata key="..." value="..."/>` <br/><br/>Du kan ange **värden** för fördefinierade **nycklar**. För närvarande stöds följande nycklar:<br/><br/>”title” och ”beskrivning” - används för ordförråd för att justera språk modell för jobbet och förbättra taligenkänningen.  Värdena dirigera Internet-sökningar för att hitta sammanhangsmässigt relevanta textdokument, använda innehållet för att utöka den interna ordboken under indexering uppgiften.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funktioner** <br/><br/> Har lagts till i version 1.2. Den enda funktionen som stöds är för närvarande taligenkänning (”ASR”). |false |Funktionen har följande inställningar för nycklar:<table><tr><th><p>Nyckel</p></th>        <th><p>Beskrivning</p></th><th><p>Exempelvärde</p></th></tr><tr><td><p>Språk</p></td><td><p>Naturligt språk ska identifieras i multimediefilen.</p></td><td><p>Engelska, spanska</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>en semikolonavgränsad lista önskade textning utdataformat (i förekommande fall)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>En boolesk flagga som anger huruvida en AIB-fil krävs (för användning med SQL Server och kunden indexeraren IFilter).  Mer information finns i <a href="https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">med AIB-filer med Azure Media Indexer och SQL Server</a>.</p></td><td><p>True; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>En boolesk flagga som anger huruvida en XML-fil med nyckelord måste anges.</p></td><td><p>SANT. FALSE. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>En boolesk flagga som anger huruvida ska framtvinga fullständig undertexter (oavsett konfidensnivån) eller inte.  </p><p>Standardvärdet är FALSKT, i vilket fall ord och fraser som har mindre än 50% konfidensnivån utelämnas från sista textning utdata och ersättas med ellipserna (”...”).  Ellipserna är användbara för textning kvalitetskontroll och granskning.</p></td><td><p>SANT. FALSE. </p></td></tr></table> |

### <a id="error_codes"></a>Felkoder
Om det finns ett fel, Azure Media Indexer ska rapportera tillbaka en av följande felkoder:

| Kod | Namn | Möjliga orsaker |
| --- | --- | --- |
| 2000 |Ogiltig konfiguration |Ogiltig konfiguration |
| 2001 |Ogiltiga indata tillgångar |Saknar indata tillgångar eller tom tillgången. |
| 2002 |Ogiltigt manifest |Manifest är tom eller manifest innehåller ogiltigt objekt. |
| 2003 |Det gick inte att hämta mediefil |Ogiltig URL i manifestfilen. |
| 2004 |Protokoll som inte stöds |Protokollet för media-URL stöds inte. |
| 2005 |Filtypen stöds inte |Mediafiltypen stöds inte. |
| 2006 |För många indatafiler |Det finns fler än 10 filer i det inkommande manifestet. |
| 3000 |Det gick inte att avkoda mediefil |Codec för stöds inte <br/>eller<br/> Skadat mediefil <br/>eller<br/> Inga ljudström i mediet. |
| 4000 |Batch indexering delvis |Några av de inkommande mediefilerna växlas som ska indexeras. Mer information finns i <a href="#output_files">utdatafiler</a>. |
| övrigt |Internt fel |Kontakta supporten. indexer@microsoft.com |

## <a id="supported_languages"></a>Språk som stöds
Engelska och spanska språk stöds för närvarande. Mer information finns i [blogginlägget v1.2 versionen](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Azure Media Services Analytics Overview](media-services-analytics-overview.md)

[Använda AIB filer med Azure Media Indexer och SQLServer](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indexera mediefiler med Azure Media Indexer 2 förhandsgranskning](media-services-process-content-with-indexer2.md)


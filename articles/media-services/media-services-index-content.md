---
title: Indexering mediefiler med Azure Media indexeraren
description: "Azure Media Indexer gör det möjligt att göra innehåll för mediefiler sökbara och för att generera en fulltext betyg för stängd textning och nyckelord. Det här avsnittet visar hur du använder Media indexeraren."
services: media-services
documentationcenter: 
author: Asolanki
manager: cfowler
editor: 
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/20/2017
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: f75be3280ffd869339972859c028a178ec728480
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexering mediefiler med Azure Media indexeraren
Azure Media Indexer gör det möjligt att göra innehåll för mediefiler sökbara och för att generera en fulltext betyg för stängd textning och nyckelord. Du kan bearbeta en mediefil eller flera mediefiler i en batch.  

> [!IMPORTANT]
> När indexering av innehåll, se till att använda mediefiler med tydliga tal (utan bakgrundsmusik, ljud, effekter eller mikrofon hiss). Några exempel på rätt innehåll är: registreras möten, Föreläsningar och presentationer. Följande innehåll är inte lämpliga för indexering: filmer, TV-program, allt med blandade ljud och visuella effekter dåligt registreras innehåll med bakgrundsljud (hiss).
> 
> 

En indexering jobb kan generera följande utdata:

* Stängd bildtexten filer i följande format: **SAMI**, **TTML**, och **WebVTT**.
  
    Dold textning filer innehåller en tagg som kallas Recognizability, vilka resultat som en indexering jobb baserat på hur okänt tal i videon källa.  Du kan använda värdet för Recognizability skärmen användbarhet utgående filer. En låg poäng betyder dåliga indexering resultat på grund av ljud.
* Nyckelordsfil (XML).
* Ljud indexering blob-fil (AIB) för användning med SQLServer.
  
    Mer information finns i [med AIB filer med Azure Media Indexer och SQL Server](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/).

Det här avsnittet visar hur du skapar indexering jobb till **Index för en tillgång** och **flera indexfiler**.

De senaste uppdateringarna för Azure Media Indexer, se [Media Services bloggar](#preset).

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Med hjälp av konfigurations-och manifest för indexering uppgifter
Du kan ange mer information för dina indexering aktiviteter med hjälp av en konfiguration för aktiviteten. Du kan till exempel ange vilka metadata för media-fil. Dessa metadata används av motorn språk för att expandera dess ordförråd och förbättrar avsevärt taligenkänningen.  Du kan också ange de önskade utgående filerna.

Du kan också bearbeta flera filer samtidigt med hjälp av en manifestfil.

Mer information finns i [aktivitet förinställningen Azure Media indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Index för en tillgång
Följande metod överför en mediefil som en tillgång och skapar ett jobb för att indexera tillgången.

Observera att om ingen konfigurationsfil anges mediefilen indexeras med alla standardinställningar.

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
<!-- __ -->
### <a id="output_files"></a>Utgående filer
En indexering jobbet genererar följande utdatafiler som standard. Filerna lagras i första utdatatillgången.

När det finns fler än en inkommande mediefilen är genererar indexeraren en manifestfil jobbet-utdata med namnet 'JobResult.txt'. För varje indata mediefilen är resulterande AIB, SAMI, TTML, WebVTT och nyckelordet filer sekventiellt numrerade och namnges med ”Alias”.

| Filnamn | Beskrivning |
| --- | --- |
| **InputFileName.aib** |Ljud indexering blob-fil. <br/><br/> Ljudfilen indexering Blob (AIB) är en binär fil som kan sökas i Microsoft SQL server med fulltextsökning.  Filen AIB är mer kraftfulla än enkel etikett-filer eftersom den innehåller alternativ för varje ord, så att en mycket rikare upplevelse för sökning. <br/> <br/>Det kräver installation av indexerare SQL-tillägg på en dator kör Microsoft SQL server 2008 eller senare. Söker AIB med hjälp av Microsoft SQL ger server-fulltextsökning exaktare sökresultat än textning filer som skapas av WAMI. Det beror på att AIB innehåller alternativa ord. ljud som liknande textning filer innehåller högsta förtroende ordet för varje segment i ljuduppspelningen. Om söker efter tal upmost betydelse, rekommenderas det att använda AIB i-tillsammans med Microsoft SQL Server.<br/><br/> Hämta tillägget Klicka <a href="http://aka.ms/indexersql">Azure Media Indexer SQL tillägget</a>. <br/><br/>Det är också möjligt att använda andra sökmotorer, till exempel Apache Lucene/Solr att helt enkelt indexera videon baserat på textning och nyckelordet XML-filer, men detta resulterar i mindre exakt sökresultat. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |Stängd beskrivning (CC) filer i SAMI, TTML och WebVTT format.<br/><br/>De kan användas för att göra det tillgängligt för personer med funktionshinder hörsel ljud- och bildfiler.<br/><br/>Stängd bildtexten filer innehåller en tagg som kallas <b>Recognizability</b> som poäng en indexering jobb baserat på hur okänt tal i videon källa.  Du kan använda värdet för <b>Recognizability</b> skärmen användbarhet utgående filer. En låg poäng betyder dåliga indexering resultat på grund av ljud. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Information om nyckelord och filer. <br/><br/>Nyckelordet filen är en XML-fil som innehåller nyckelord som extraheras från tal-innehåll med frekvens och offset-information. <br/><br/>Information om filen är en oformaterad textfil som innehåller detaljerad information om varje term som identifieras. Den första raden är speciellt och innehåller Recognizability poäng. Varje efterföljande rad är en lista med tabbavgränsade följande data: starta tid, sluttid, /-fras, förtroende. Vilka tider som anges i sekunder och förtroende ges som ett tal mellan 0-1. <br/><br/>Exempel rad: ”1,20 1,45 word 0.67” <br/><br/>Dessa filer kan används för flera olika skäl, t.ex, att utföra taligenkänning analytics eller utsatta för sökmotorer t.ex. Bing, Google eller Microsoft SharePoint för att göra mediefiler mer synliga eller även används för att leverera mer relevant annonser. |
| **JobResult.txt** |Utdata manifestet, visas bara om indexering flera filer, som innehåller följande information:<br/><br/><table border="1"><tr><th>Indatafil</th><th>Alias</th><th>MediaLength</th><th>Fel</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Om inte alla inkommande mediefiler indexeras har, indexering jobbet misslyckas med felkoden 4000. Mer information finns i [felkoder](#error_codes).

## <a name="index-multiple-files"></a>Index flera filer
Följande metod överför flera mediefiler som en tillgång och skapar ett jobb för att indexera dessa filer i en batch.

Manifestfilen med filnamnstillägget .lst har skapats och ladda upp till tillgången. Manifestfilen innehåller listan över alla tillgångsfiler som. Mer information finns i [aktivitet förinställningen Azure Media indexer](https://msdn.microsoft.com/library/dn783454.aspx).

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

### <a name="partially-succeeded-job"></a>Delvis har slutförts jobb
Om inte alla inkommande mediefiler indexeras har, indexering jobbet misslyckas med felkoden 4000. Mer information finns i [felkoder](#error_codes).

Samma utdata (som har slutförts jobb) genereras. Du kan referera till utdata manifestfilen ta reda på vilka indatafiler misslyckades, enligt kolumnvärdena fel. För inkommande filer som har misslyckats, resulterande AIB, SAMI, TTML, WebVTT och nyckelordet genereras inte filer.

### <a id="preset"></a>Uppgiften förinställda Azure Media indexer
Bearbetningen från Azure Media Indexer kan anpassas genom att tillhandahålla en frivillig uppgift förinställningen tillsammans med uppgiften.  Följande beskriver formatet för den här xml-konfigurationsfilen.

| Namn | Kräv | Beskrivning |
| --- | --- | --- |
| **indata** |FALSKT |Tillgångsinformation fil(er) som du vill index.</p><p>Azure Media Indexer stöder följande media format: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Du kan ange filnamnet (s) i den **namn** eller **lista** attribut för den **inkommande** element (som visas nedan). Om du inte anger vilken tillgångsfil som till index hämtas den primära filen. Om inga primära resursfilen anges indexeras den första filen i den inkommande tillgången.</p><p>Gör att explicit ange filnamnet för tillgångsinformation:<br/>`<input name="TestFile.wmv">`<br/><br/>Du kan även indexera flera tillgångsfiler på en gång (upp till 10). Gör så här:<br/><br/><ol class="ordered"><li><p>Skapa en textfil (manifestfilen) och ge det ett .lst-tillägg. </p></li><li><p>Lägga till en lista över alla filnamn för tillgångsinformation i din inkommande tillgång till den här manifestfilen. </p></li><li><p>Lägg till (överföringen) thanifest filen till tillgången.  </p></li><li><p>Ange namnet på manifestfilen i angivna indata-attribut.<br/>`<input list="input.lst">`</li></ol><br/><br/>Obs: Om du lägger till fler än 10 filer manifestfilen indexering jobbet misslyckas med felkoden 2006. |
| **metadata** |FALSKT |Metadata för den angivna resursen/filerna som används för ordförråd anpassning.  Användbar för att förbereda indexerare för att identifiera standardmässiga ordförråd ord som namn.<br/>`<metadata key="..." value="..."/>` <br/><br/>Du kan ange **värden** för fördefinierade **nycklar**. Följande nycklar stöds för närvarande:<br/><br/>”title” och ”beskrivning” - används för ordförråd anpassning för att justera språket modellen för jobbet och förbättra taligenkänningen.  Värdena dirigera Internet-sökningar för att hitta sammanhang relevanta textdokument, med hjälp av innehållet för att utöka den interna ordlistan för varaktigheten för indexering-aktivitet.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **funktioner** <br/><br/> Lägga till i version 1.2. Den enda funktionen som stöds är för närvarande taligenkänning (”ASR”). |FALSKT |Taligenkänning har följande inställningar för:<table><tr><th><p>Nyckel</p></th>        <th><p>Beskrivning</p></th><th><p>Exempelvärde</p></th></tr><tr><td><p>Språk</p></td><td><p>Naturligt språk ska kunna identifieras i fil.</p></td><td><p>Engelska, spanska</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>en semikolonavgränsad lista önskade bildtexten utdataformat (eventuella)</p></td><td><p>ttml; sami; webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>En boolesk flagga som anger huruvida en AIB fil krävs (för användning med SQL Server och kunden indexeraren IFilter).  Mer information finns i <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">med AIB filer med Azure Media Indexer och SQL Server</a>.</p></td><td><p>SANT. FALSKT</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>En boolesk flagga som anger huruvida en nyckelordet XML-fil måste anges.</p></td><td><p>SANT. FALSKT. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>En boolesk flagga som anger om du tvingar fram fullständiga beskrivningar (oavsett konfidensnivån) eller inte.  </p><p>Standardvärdet är false, i vilket fall ord och fraser som har mindre än 50% konfidensnivå utelämnas från sista etiketten utdata och ersättas med ellipserna (”...”).  Ellipserna är användbara för rubriken kvalitetskontroll och granskning.</p></td><td><p>SANT. FALSKT. </p></td></tr></table> |

### <a id="error_codes"></a>Felkoder
Om ett fel Azure Media Indexer ska rapportera tillbaka något av följande felkoder:

| Kod | Namn | Möjliga orsaker |
| --- | --- | --- |
| 2000 |Ogiltig konfiguration |Ogiltig konfiguration |
| 2001 |Ogiltiga indata tillgångar |Saknar indata tillgångar eller tom tillgång. |
| 2002 |Ogiltigt manifest |Manifestet är tom eller manifest innehåller ogiltiga objekt. |
| 2003 |Det gick inte att hämta mediefil |Ogiltig URL i manifestfilen. |
| 2004 |Protokoll som inte stöds |Protokollet för media URL stöds inte. |
| 2005 |Typ som inte stöds |Mediafiltypen stöds inte. |
| 2006 |För många inkommande filer |Det finns fler än 10 filer i inkommande manifestet. |
| 3000 |Det gick inte att avkoda mediefilen |Codec för stöds inte <br/>eller<br/> Skadat mediefil <br/>eller<br/> Inga ljudström i mediet. |
| 4000 |Delvis lyckades batch indexering |Några av de inkommande mediefilerna är kunde inte indexeras. Mer information finns i <a href="#output_files">utdatafiler</a>. |
| övrigt |Internt fel |Kontakta supportteamet. indexer@microsoft.com |

## <a id="supported_languages"></a>Språk som stöds
De engelska och spanska språk stöds för närvarande. Mer information finns i [version 1.2-versionen blogginlägget](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Azure Media Services Analytics-översikt](media-services-analytics-overview.md)

[Med hjälp av AIB filer med Azure Media Indexer och SQLServer](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Indexering mediefiler med Azure Media Indexer 2 förhandsgranskning](media-services-process-content-with-indexer2.md)


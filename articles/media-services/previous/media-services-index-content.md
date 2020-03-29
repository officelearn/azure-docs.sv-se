---
title: Indexera mediefiler med Azure Media Indexer
description: Med Azure Media Indexer kan du göra innehållet i dina mediefiler sökbart och generera en fulltextavskrift för dold textning och nyckelord. Det här avsnittet visar hur du använder Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: johndeu
ms.openlocfilehash: 7ccc2d5956b44a8cd85f19e0905539c32f58bc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164006"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Indexera mediefiler med Azure Media Indexer

> [!NOTE]
> Azure Media Indexer-medieprocessorn dras tillbaka. **Azure Media Indexer** För pensioneringsdatumen finns i det här [äldre komponentavsnittet.](legacy-components.md) [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) ersätter den äldre medieprocessorn. Mer information finns i [Migrera från Azure Media Indexer och Azure Media Indexer 2 till Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).

Med Azure Media Indexer kan du göra innehållet i dina mediefiler sökbart och generera en fulltextavskrift för dold textning och nyckelord. Du kan bearbeta en mediefil eller flera mediefiler i en batch.  

När du indexerar innehåll ska du se till att använda mediefiler som har tydligt tal (utan bakgrundsmusik, brus, effekter eller mikrofonbrus). Några exempel på lämpligt innehåll är: inspelade möten, föreläsningar eller presentationer. Följande innehåll kanske inte är lämpligt för indexering: filmer, TV-program, allt med blandade ljud- och ljudeffekter, dåligt inspelat innehåll med bakgrundsljud (väs).

Ett indexeringsjobb kan generera följande utdata:

* Filer med dold textning i följande format: **TTML**och **WebVTT**.
  
    Filer med dold textning innehåller en tagg som heter Igenkännlighet, som betygsätter ett indexeringsjobb baserat på hur igenkännligt talet i källvideon är.  Du kan använda värdet av igenkännlighet för att granska utdatafiler för användbarhet. En låg poäng skulle innebära dåliga indexeringsresultat på grund av ljudkvalitet.
* Nyckelordsfil (XML).

Den här artikeln visar hur du skapar indexeringsjobb för att **indexera en tillgång** och **Indexera flera filer**.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>Använda konfigurations- och manifestfiler för indexering av uppgifter
Du kan ange mer information för dina indexeringsuppgifter med hjälp av en aktivitetskonfiguration. Du kan till exempel ange vilka metadata som ska användas för mediefilen. Dessa metadata används av språkmotorn för att utöka sitt ordförråd och förbättrar taligenkänningsnoggrannheten avsevärt.  Du kan också ange önskade utdatafiler.

Du kan också bearbeta flera mediefiler samtidigt med hjälp av en manifestfil.

Mer information finns i [Aktivitetsförinställning för Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

## <a name="index-an-asset"></a>Indexera en tillgång
Följande metod överför en mediefil som en tillgång och skapar ett jobb för att indexera tillgången.

Om ingen konfigurationsfil har angetts indexeras mediefilen med alla standardinställningar.

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
### <a name="output-files"></a><a id="output_files"></a>Utdatafiler
Som standard genererar ett indexeringsjobb följande utdatafiler. Filerna lagras i den första utdatatillgången.

När det finns mer än en indatamediefil genererar Indexer en manifestfil för jobbutdata, med namnet "JobResult.txt". För varje indatamediefil numreras de resulterande TTML-, WebVTT- och nyckelordsfilerna sekventiellt och namnges med "Alias".

| Filnamn | Beskrivning |
| --- | --- |
| **InputFileName.ttml**<br/>**InputFileName.vtt** |CC-filer (Closed Caption) i TTML- och WebVTT-format.<br/><br/>De kan användas för att göra ljud- och videofiler tillgängliga för personer med nedsatt hörsel.<br/><br/>Sluten textning filer innehåller en tagg som heter <b>Igenkännlighet</b> som betygsätter ett indexering jobb baserat på hur igenkännlig tal i källan videon är.  Du kan använda värdet <b>av igenkännlighet</b> för att granska utdatafiler för användbarhet. En låg poäng skulle innebära dåliga indexeringsresultat på grund av ljudkvalitet. |
| **InputFileName.kw.xml<br/>InputFileName.info** |Nyckelord och infofiler. <br/><br/>Nyckelordsfil är en XML-fil som innehåller nyckelord som extraherats från talinnehållet, med frekvens- och offsetinformation. <br/><br/>Info-filen är en oformaterad textfil som innehåller detaljerad information om varje uttryck som känns igen. Den första raden är speciell och innehåller igenkännlighetspoängen. Varje efterföljande rad är en flikseparerad lista med följande data: starttid, sluttid, ord/fras, konfidens. Tiderna ges i sekunder och självförtroendet ges som ett nummer från 0-1. <br/><br/>Exempelrad: "1,20 1,45 ord 0,67" <br/><br/>Dessa filer kan användas för ett antal ändamål, till exempel för att utföra talanalys eller exponeras för sökmotorer som Bing, Google eller Microsoft SharePoint för att göra mediefilerna mer upptäckbara, eller till och med användas för att leverera mer relevanta annonser. |
| **JobbResult.txt** |Utdatamanifest, som endast finns när flera filer indexeras, som innehåller följande information:<br/><br/><table border="1"><tr><th>Indatafil</th><th>Alias</th><th>MediaLängd</th><th>Fel</th></tr><tr><td>a.mp4 (på)</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4 (på andra)</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

Om inte alla indatamediefiler indexeras misslyckas indexeringsjobbet med felkod 4000. Mer information finns i [Felkoder](#error_codes).

## <a name="index-multiple-files"></a>Indexera flera filer
Följande metod överför flera mediefiler som en tillgång och skapar ett jobb för att indexera alla dessa filer i en batch.

En manifestfil med tillägget ".lst" skapas och laddas upp till tillgången. Manifestfilen innehåller en lista över alla tillgångsfiler. Mer information finns i [Aktivitetsförinställning för Azure Media Indexer](https://msdn.microsoft.com/library/dn783454.aspx).

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

### <a name="partially-succeeded-job"></a>Delvis efterföljande jobb
Om inte alla indatamediefiler indexeras misslyckas indexeringsjobbet med felkod 4000. Mer information finns i [Felkoder](#error_codes).

Samma utdata (som efterföljande jobb) genereras. Du kan referera till utdatamanifestfilen för att ta reda på vilka indatafiler som inte har det gick att använda, enligt kolumnvärdena Fel. För indatafiler som misslyckades genereras inte de resulterande TTML-, WebVTT- och nyckelordsfilerna.

### <a name="task-preset-for-azure-media-indexer"></a><a id="preset"></a>Aktivitetsförinställning för Azure Media Indexer
Bearbetningen från Azure Media Indexer kan anpassas genom att tillhandahålla en valfri uppgiftsförinställning bredvid uppgiften.  Nedan beskrivs formatet för den här konfigurations xml.The following describes the format of this configuration xml.

| Namn | Kräv | Beskrivning |
| --- | --- | --- |
| **Input** |false |Tillgångsfiler som du vill indexera.</p><p>Azure Media Indexer stöder följande mediefilformat: MP4, WMV, MP3, M4A, WMA, AAC, WAV.</p><p>Du kan ange filnamnet (s) i **namnet** eller **listattributet** för **indataelementet** (som visas nedan). Om du inte anger vilken tillgångsfil som ska indexeras plockas den primära filen. Om ingen primär tillgångsfil har angetts indexeras den första filen i indatatillgången.</p><p>Om du uttryckligen vill ange namnet på tillgångsfilen gör du:<br/>`<input name="TestFile.wmv">`<br/><br/>Du kan också indexera flera tillgångsfiler samtidigt (upp till 10 filer). Gör så här:<br/><br/><ol class="ordered"><li><p>Skapa en textfil (manifestfil) och ge den ett LST-tillägg. </p></li><li><p>Lägg till en lista över alla tillgångsfilnamn i indatatillgången i den här manifestfilen. </p></li><li><p>Lägg till (ladda upp) manifestfilen till tillgången.  </p></li><li><p>Ange namnet på manifestfilen i indatas listattribut.<br/>`<input list="input.lst">`</li></ol><br/><br/>Om du lägger till fler än 10 filer i manifestfilen misslyckas indexeringsjobbet med felkoden för 2006. |
| **Metadata** |false |Metadata för de angivna tillgångsfilerna som används för ordförrådsanpassning.  Användbart för att förbereda Indexer för att känna igen ordförrådsord som inte är standard, till exempel rätt substantiv.<br/>`<metadata key="..." value="..."/>` <br/><br/>Du kan ange **värden** för fördefinierade **nycklar**. För närvarande stöds följande nycklar:<br/><br/>"titel" och "beskrivning" - används för ordförråd anpassning för att justera språkmodellen för ditt jobb och förbättra taligenkänning noggrannhet.  Värdena dirigerar Internet söker efter kontextuellt relevanta textdokument, med hjälp av innehållet för att utöka den interna ordlistan under hela indexeringen.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **Funktioner** <br/><br/> Tillagd i version 1.2. För närvarande är den enda funktionen som stöds taligenkänning ("ASR"). |false |Funktionen Taligenkänning har följande inställningstangenter:<table><tr><th><p>Nyckel</p></th>        <th><p>Beskrivning</p></th><th><p>Exempelvärde</p></th></tr><tr><td><p>Språk</p></td><td><p>Det naturliga språk som ska kännas igen i multimediafilen.</p></td><td><p>Engelska, spanska</p></td></tr><tr><td><p>CaptionFormats (BildtextFormat)</p></td><td><p>en semikolonavseparerad lista över önskade utdatabildtextformat (om sådana finns)</p></td><td><p>ttml;webvtt</p></td></tr><tr><td><p></p></td><td><p> </p></td><td><p>Sant; Falska</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>En boolesk flagga som anger om en XML-nyckelordsfil krävs eller inte.</p></td><td><p>Sant; Falska. </p></td></tr><tr><td><p>ForceFullCaption (500)</p></td><td><p>En boolesk flagga som anger om du vill tvinga fram fullständiga bildtexter (oavsett konfidensnivå).  </p><p>Standard är falskt, i vilket fall ord och fraser som har en mindre än 50% konfidensnivå utelämnas från den slutliga bildtexten utgångar och ersättas med ellipser ("...").  Ellipserna är användbara för kvalitetskontroll och granskning av bildtexter.</p></td><td><p>Sant; Falska. </p></td></tr></table> |

### <a name="error-codes"></a><a id="error_codes"></a>Felkoder
Vid ett fel bör Azure Media Indexer rapportera tillbaka någon av följande felkoder:

| Kod | Namn | Möjliga orsaker |
| --- | --- | --- |
| 2000 |Ogiltig konfiguration |Ogiltig konfiguration |
| 2001 |Ogiltiga indatatillgångar |Indatatillgångar eller tom tillgång saknas. |
| 2002 |Ogiltigt manifest |Manifestet är tomt eller manifestet innehåller ogiltiga objekt. |
| 2003 |Det gick inte att hämta mediefilen |Ogiltig URL i manifestfilen. |
| 2004 |Protokoll som inte stöds |Protokoll för medie-URL stöds inte. |
| 2005 |Filtyper som inte stöds |Filtyp för indatamedia stöds inte. |
| 2006 |För många indatafiler |Det finns mer än 10 filer i indatamanifestet. |
| 3000 |Det gick inte att avkoda mediefilen |Media codec som inte stöds <br/>eller<br/> Skadad mediefil <br/>eller<br/> Ingen ljudström i indatamedia. |
| 4000 |Batchindexering delvis lyckats |Det gick inte att indexera vissa indatamediefiler. Mer information finns i <a href="#output_files">Utdatafiler</a>. |
| övrigt |Interna fel |Kontakta supportteamet. indexer@microsoft.com |

## <a name="supported-languages"></a><a id="supported_languages"></a>Språk som stöds
För närvarande stöds de engelska och spanska språken.  

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Relaterade länkar
[Översikt över Azure Media Services Analytics](media-services-analytics-overview.md)

[Indexera mediefiler med förhandsversionen av Azure Media Indexer 2](media-services-process-content-with-indexer2.md)


<properties
    pageTitle="Komma igång med att leverera innehåll på begäran med hjälp av .NET | Azure"
    description="Dessa självstudier vägleder dig genom stegen för implementera ett program med leverans av innehåll på begäran med Azure Media Services genom att använda .NET."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/11/2016"
    ms.author="juliako"/>



# <a name="get-started-with-delivering-content-on-demand-using-.net-sdk"></a>Kom igång med att leverera innehåll på begäran med hjälp av .NET SDK

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

>[AZURE.NOTE]
> Du behöver ett Azure-konto för att slutföra dessa självstudier. Mer information finns i [kostnadsfri utvärderingsversion av Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 
 
##<a name="overview"></a>Översikt 

Den här självstudiekursen vägleder dig genom stegen för att implementera ett program för leverans av Video-on-Demand (VoD) med Azure Media Services (AMS) SDK för .NET.


Självstudierna innehåller det grundläggande Media Services-arbetsflödet och de vanligaste programmeringsobjekt och -uppgifter som krävs för utveckling av Media Services. I slutet av självstudierna kommer du att kunna strömma eller progressivt hämta en exempelmediefil som du har överfört, kodat och hämtat.

## <a name="what-you'll-learn"></a>Detta får du får lära dig

Självstudien visar hur du utför följande uppgifter:

1.  Skapar ett Media Services-konto (med Azure-portalen).
2.  Konfigurerar direktuppspelande slutpunkter (med Azure-portalen).
3.  Skapar och konfigurerar ett Visual Studio-projekt.
5.  Ansluter till Media Services-kontot.
6.  Skapar en ny tillgång och överför en videofil.
7.  Kodar källfilen till en uppsättning MP4-filer med anpassningsbar bithastighet.
8.  Publicerar tillgången och får URL:er för strömmande och progressiv hämtning.
9.  Testar genom att spela upp ditt innehåll.

## <a name="prerequisites"></a>Krav

Följande krävs för att kunna genomföra självstudien.

- Du behöver ett Azure-konto för att slutföra den här självstudien. 
    
    Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](/pricing/free-trial/?WT.mc_id=A261C142F). Du får kredit som kan användas för att prova Azure-tjänster som normalt inte är kostnadsfria. Du kan behålla kontot även efter att krediten är slut och använda gratis Azure-tjänster och -funktioner som  Web Apps-funktionen i Azure App Service.
- Operativsystem: Windows 8 eller senare, Windows 2008 R2, Windows 7.
- .NET Framework 4.0 eller senare
- Visual Studio 2010 SP1 (Professional, Premium, Ultimate eller Express) eller senare versioner.


##<a name="download-sample"></a>Ladda ned exempel

Hämta och kör ett exempel [här](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Skapa ett Media Services-konto med Azure-portalen

Stegen i det här avsnittet visar hur du skapar ett AMS-konto.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **+New** > **Media + CDN** > **Media Services**.

    ![Skapa Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Ange de erfordrade värdena i **SKAPA MEDIA SERVICES-KONTO**.

    ![Skapa Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Ange namnet på det nya AMS-kontot vid **Kontonamn**. Namnet på ett Media Services-konto består av gemena bokstäver eller siffror utan blanksteg och 3 till 24 tecken.
    2. Vid Prenumeration väljer du mellan de olika Azure-prenumerationer som du har åtkomst till.
    
    2. I **Resursgrupp** väljer du ny eller befintlig resurs.  En resursgrupp är en samling resurser som delar livscykel, behörigheter och principer. Lär dig mer [här](resource-group-overview.md#resource-groups).
    3. För **Plats** väljer du den geografiska region som ska användas för att lagra media och metadataposter för ditt Media Services-konto. Den här regionen används för att bearbeta och strömma dina media. Endast de tillgängliga Media Services-regionerna visas i listrutan. 
    
    3. Vid **Storage-konto** väljer du ett lagringskonto för att tillhandahålla Blob Storage av medieinnehållet från ditt Media Services-konto. Du kan välja ett befintligt lagringskonto i samma geografiska region som ditt Media Services-konto eller skapa ett lagringskonto. Ett nytt lagringskonto skapas i samma region. Reglerna för namn på lagringskonton är desamma som för Media Services-konton.

        Mer information om lagring finns [här](storage-introduction.md).

    4. Välj **PIN-kod för instrumentpanelen** för att se förloppet för kontodistributionen.
    
7. Klicka på **Skapa** längst ned i formuläret.

    Statusen ändras till **Körs** när kontot har skapats. 

    ![Media Services-inställningar](./media/media-services-portal-vod-get-started/media-services-settings.png)

    För att hantera AMS-kontot (till exempel överföra videor, koda tillgångar och övervaka jobbförlopp) använder du fönstret **Inställningar**.

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Konfigurera direktuppspelande slutpunkter med Azure-portalen

När du arbetar med Azure Media Services är ett av de vanligaste scenarierna att leverera video via strömning med anpassad bithastighet till dina klienter. Media Services stöder följande strömningstekniker för anpassningsbar bithastighet: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH och HDS (endast för Adobe PrimeTime-/Access-licenstagare).

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera ditt MP4-kodade innehåll med anpassad bithastighet i strömningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) direkt när du så önskar, utan att du behöver lagra på förhand packade versioner av vart och ett av dessa strömningsformat.

Om du vill använda dynamisk paketering ska du göra följande:

- Koda din mezzaninfil (källa) till en uppsättning MP4-filer med anpassningsbar bithastighet (kodningsstegen visas längre fram i den här vägledningen).  
- Skapa minst en enhet för strömning för den *strömningsslutpunkt* från vilken du planerar att leverera ditt innehåll. Stegen nedan visar hur du kan ändra antalet strömningsenheter.

Med dynamisk paketering behöver du bara lagra och betala för filerna i ett enda lagringsformat, och Media Services skapar och ger lämplig respons baserat på begäranden från en klient.

Om du vill skapa och ändra antalet reserverade enheter för strömning gör du följande:


1. I fönstret **Inställningar** klickar du på **Strömningsslutpunkter**. 

2. Klicka på den strömningsslutpunkt som är standard. 

    Fönstret **INFORMATION OM DEN STRÖMNINGSSLUTPUNKT SOM ÄR STANDARD** visas.

3. Flytta på skjutreglaget **Strömningsenheter** för att ange antalet strömningsenheter.

    ![Strömningsenheter](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)

4. Klicka på knappen **Spara** för att spara ändringarna.

    >[AZURE.NOTE]Tilldelning av nya enheter kan ta cirka 20 minuter att slutföra.

##<a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

1. Skapa ett nytt C#-konsolprogram i Visual Studio 2013, Visual Studio 2012 eller Visual Studio 2010 SP1. Ange **namn**, **plats** och **lösningsnamn**. Klicka sedan på **OK**.

2. Använd NuGet-paketet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) för att installera **Azure Media Services .NET SDK-tilläggen**.  Media Services .NET SDK-tilläggen är en uppsättning tilläggsmetoder och hjälpfunktioner som förenklar koden och gör det enklare att utveckla med Media Services. När du installerar det här paketet installeras även **Media Services .NET SDK** och lägger till alla andra nödvändiga beroenden.

3. Lägg till en referens i sammansättningen System.Configuration. Den här sammansättningen innehåller klassen **System.Configuration.ConfigurationManager** som används för att komma åt konfigurationsfiler, till exempel App.config.

4. Öppna filen App.config (lägg till filen i projektet om den inte har lagts till som standard) och lägg till ett *appSettings*-avsnitt i den. Ange värden för kontonamnet och kontonyckeln i Azure Media Services, vilket visas i följande exempel. Hämta kontonamnet och viktig information genom att öppna den klassiska Azure-portalen. Välj Media Services-kontot och klicka på knappen **HANTERA NYCKLAR**.

        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
          
        </configuration>

5. Skriv över de befintliga **using**-satserna i början av filen Program.cs med följande kod.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
        

6. Skapa en ny mapp under projektkatalogen och kopiera en .mp4- eller .wmv-fil som du vill koda och strömma eller hämta progressivt. I det här exemplet används sökvägen ”C:\VideoFiles”.

##<a name="connect-to-the-media-services-account"></a>Anslut till Media Services-kontot

När du använder Media Services med .NET ska du använda klassen **CloudMediaContext** för de flesta Media Services-programmeringsuppgifter. Det gäller till exempel att ansluta till Media Services-kontot, skapa, uppdatera, komma åt och ta bort följande objekt: tillgångar, tillgångsfiler, jobb, åtkomstprinciper, positionerare o.s.v.

Skriv över den programklass som är standard med följande kod. Koden visar hur du läser anslutningsvärdena från filen App.config och hur du skapar objektet **CloudMediaContext** för att kunna ansluta till Media Services. Mer information om hur du ansluter till Media Services finns i [Ansluta till Media Services med Media Services SDK för .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

Funktionen **Main** anropar metoder som definieras ytterligare i det här avsnittet.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

##<a name="create-a-new-asset-and-upload-a-video-file"></a>Skapa en ny tillgång och ladda upp en videofil

I Media Services överför du (eller för in) dina digitala filer till en tillgång. Enheten **Tillgång** kan innehålla video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning (samt metadata om dessa filer.)  När filerna har överförts lagras innehållet på ett säkert sätt i molnet för ytterligare bearbetning och strömning. Filerna i tillgången kallas **Tillgångsfiler**.

Den **UploadFile**-metod som definieras nedan kallas **CreateFromFile** (definieras i .NET SDK-tillägg). **CreateFromFile** skapar en ny tillgång som den angivna källfilen överförs till.

**CreateFromFile**-metoden tar **AssetCreationOptions** , vilket gör att du kan ange något av följande alternativ för att skapa tillgångar:

- **Ingen** – Ingen kryptering används. Detta är standardvärdet. Observera att när du använder det här alternativet skyddas inte innehållet under överföring eller i vila i lagringsutrymmet.
Om du planerar att leverera en MP4 med progressivt nedladdning ska du använda det här alternativet.
- **StorageEncrypted** – Använd det här alternativet för att kryptera innehållet lokalt med hjälp av 256-bitars AES-kryptering (Advanced Encryption Standard), som sedan överför det till Azure Storage där den lagras krypterat i vila. Tillgångar som skyddas med Lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Lagringskryptering används i första hand när du vill skydda indatamediefiler av hög kvalitet med stark kryptering i vila på disk.
- **CommonEncryptionProtected** – Använd det här alternativet om du överför innehåll som redan har krypterats och som skyddas med vanlig kryptering eller PlayReady DRM (till exempel Smooth Streaming som skyddas med PlayReady DRM).
- **EnvelopeEncryptionProtected** – Använd det här alternativet om du överför HLS som krypterats med AES. Observera att filerna måste ha kodats och krypterats av Transform Manager.

Med metoden **CreateFromFile** kan du även ange ett återanrop för att rapportera filens överföringsförlopp.

I följande exempel anger vi **Inget** för tillgångsalternativen.

Lägg till följande metod i programklassen.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


##<a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Koda källfilen till en uppsättning MP4-filer med anpassningsbar bithastighet 

När du har fört in tillgångar i Media Services kan media kodas, användas med transmux, förses med vattenstämpel och så vidare innan de skickas till klienter. Dessa aktiviteter schemaläggs och körs mot flera bakgrundsrollinstanser för höga prestanda och tillgänglighet. De här aktiviteterna kallas jobb och varje jobb består av atomiska uppgifter som gör det faktiska arbetet i tillgångsfilen.

Som tidigare nämnts är ett mycket vanligt scenario när du arbetar med Azure Media Services att du levererar strömning med anpassningsbar bithastighet till klienterna. Media Services kan dynamiskt paketera en uppsättning MP4-filer med anpassningsbar bithastighet till något av följande format: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH och HDS (endast för Adobe PrimeTime/Acess-licenstagare).

Om du vill använda dynamisk paketering ska du göra följande:

- Koda eller omkoda din mezzaninfil (källa) till en uppsättning MP4- eller Smooth Streaming-filer med  anpassningsbar bithastighet.  
- Hämta minst en strömmande enhet för den strömmande slutpunkten från vilken du planerar att leverera ditt innehåll.

Följande kod visar hur du skickar ett kodningsjobb. Jobbet innehåller en uppgift som anger att omkodning av mezzaninfilen till en uppsättning MP4-filer med anpassningsbar bithastighet genom att använda den **media-kodaren som är standard**. Koden skickar jobbet och väntar tills det har slutförts.

När jobbet har slutförts ska du kunna strömma din tillgång eller progressivt hämta MP4-filer som skapades på grund av omkodningen.
Observera att du inte behöver ha fler än 0 strömmande enheter för att progressivt kunna hämta MP4-filer.

Lägg till följande metod i programklassen.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {
    
        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.
    
        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);
    
        Console.WriteLine("Submitting transcoding job...");
    
    
        // Submit the job and wait until it is completed.
        job.Submit();
    
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;
    
        Console.WriteLine("Transcoding job finished.");
    
        IAsset outputAsset = job.OutputMediaAssets[0];
    
        return outputAsset;
    }

##<a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publicera tillgången och få URL:er för strömning och progressiv hämtning

Om du vill strömma eller hämta en tillgång behöver du först ”publicera” den genom att skapa en positionerare. Positionerare ger åtkomst till filer som finns i tillgången. Media Services stöder två typer av positionerare: OnDemandOrigin-positionerare som används för att strömma media (till exempel MPEG DASH, HLS eller Smooth Streaming) och Access Signature (SAS)-positionerare som används för att hämta mediefiler.

När du har skapat positionerarna kan du skapa de URL:er som används för att strömma eller hämta dina filer.


En strömmande URL för Smooth Streaming har följande format:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

En strömmande URL för HLS har följande format:

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

En strömmande URL för MPEG DASH har följande format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

En SAS-URL som används för att hämta filer har följande format:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Media Services .NET SDK-tillägg ger praktiska hjälpmetoder som returnerar formaterade URL:er för den publicerade tillgången.

Följande kod använder .NET SDK-tillägg för att skapa positionerare och hämta URL:er för strömning och progressiv nedladdning. Koden visar även hur du laddar ned filer till en lokal mapp.

Lägg till följande metod i programklassen.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

##<a name="test-by-playing-your-content"></a>Testa genom att spela upp ditt innehåll  

När du kör programmet som definieras i föregående avsnitt visas URL:er som liknar dessa i konsolfönstret.

URL:er för anpassningsbar strömning:

Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URL:er för progressiv nedladdning (ljud och video).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Strömma videon med hjälp av [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Testa den progressiva nedladdningen genom att klistra in en URL i en webbläsare (till exempel Internet Explorer, Chrome eller Safari).


##<a name="next-steps:-media-services-learning-paths"></a>Nästa steg: sökvägar för Media Services-utbildning

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Ge feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


### <a name="looking-for-something-else?"></a>Letar du efter något annat?

Om det här ämnet inte innehöll vad du förväntades dig, om det saknar något, eller om det på annat sätt inte uppfyllde dina behov, kan du ge oss feedback i Disqus-tråden nedan.


<!-- Anchors. -->


<!-- URLs. -->
  [Installationsprogram för webbplattformen]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portal]: http://manage.windowsazure.com/



<!--HONumber=Oct16_HO3-->



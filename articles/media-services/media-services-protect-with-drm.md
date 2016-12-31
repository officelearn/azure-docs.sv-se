---
title: "Använda PlayReady och/eller Widevine Dynamic Common Encryption | Microsoft Docs"
description: "Med Microsoft Azure Media Services kan du leverera MPEG-DASH-, Smooth Streaming- och Http-Live-Streaming-dataströmmar som skyddas med Microsoft PlayReady DRM. Du kan också leverera DASH krypterat med Widevine DRM. I det här avsnittet beskrivs hur du krypterar dynamiskt med PlayReady och Widevine DRM."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/11/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 24d324a724792051eb6d86026da7b41ee9ff87b1
ms.openlocfilehash: 7c2709d472d7512eda927f4f70f82e7f74adca0c


---
# <a name="using-playready-andor-widevine-dynamic-common-encryption"></a>Använda PlayReady och/eller Widevine Dynamic Common Encryption

> [!div class="op_single_selector"]
> * [NET](media-services-protect-with-drm.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>
>

Med Microsoft Azure Media Services kan du leverera MPEG-DASH-, Smooth Streaming- och HTTP-Live-Streaming-dataströmmar som skyddas med [Microsoft PlayReady DRM](https://www.microsoft.com/playready/overview/). Du kan också leverera krypterade DASH-dataströmmar med Widevine DRM-licenser. Både PlayReady och Widevine krypteras enligt den gemensamma  krypteringsspecifikationen (ISO/IEC 23001 7 CENC). Du kan använda [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (från och med version 3.5.1) eller REST API för att konfigurera din AssetDeliveryConfiguration för att använda Widevine.

Media Services är en tjänst för att leverera PlayReady- och Widevine DRM-licenser. Media Services tillhandahåller också API:er med vilka du kan konfigurera de behörigheter och begränsningar som du vill ställa in för körtiden för PlayReady eller Widevine DRM när en användare spelar upp skyddat innehåll. När en användare begär ett DRM-skyddat innehåll, begär spelarappen en licens från AMS-licenstjänsten.  AMS-licenstjänsten utfärdar en licens till spelaren om den är auktoriserad. En PlayReady- eller Widevine-licens innehåller en krypteringsnyckel som kan användas av klientspelaren för att dekryptera och strömma innehållet.

Du kan också använda följande AMS-partner för hjälp med att leverera Widevine-licenser: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) och [castLabs](http://castlabs.com/company/partners/azure/). Mer information finns i Integrering med [Axinom](media-services-axinom-integration.md) och [castLabs](media-services-castlabs-integration.md).

Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: öppen eller tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) och [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Mer information finns i Konfigurera  innehållsnyckelns auktoriseringsprincip.

För att dra fördel av dynamisk kryptering behöver du en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Du måste också konfigurera leveransprinciperna för tillgången (beskrivs senare i det här avsnittet). Sedan, baserat på det format som anges i strömnings-URL:en, kommer  servern för strömning på begäran att säkerställa att dataströmmen levereras i det protokoll som du har valt. Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services skapar och ger lämplig HTTP-respons baserat på varje begärande från en klient.

Det här avsnittet kan vara användbart för utvecklare som arbetar på appar som levererar media som skyddas av flera DRM:er, som PlayReady och Widevine. Avsnittet visar hur du konfigurerar  PlayReadys licensleveranstjänst med auktoriseringsprinciper så att endast auktoriserade klienter kan få PlayReady- eller Widevine-licenser. Det visar även hur du använder dynamiska kryptering med PlayReady eller Widevine DRM över DASH.

> [!NOTE]
> Om du vill börja använda dynamisk kryptering, måste du först skaffa minst en skalningsenhet (även kallat strömningsenhet). Mer information finns i [Hur du skalar en medietjänst](media-services-portal-manage-streaming-endpoints.md).
>
>

## <a name="download-sample"></a>Hämta exempel
Du kan hämta det exempel som beskrivs i artikeln [här](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

## <a name="configuring-dynamic-common-encryption-and-drm-license-delivery-services"></a>Konfigurera Dynamic Common Encryption och DRM-licensleveranstjänster 

Nedan följer allmänna steg som du behöver utföra när du skyddar dina tillgångar med PlayReady med hjälp av Media Services licensleveranstjänst och även använder dynamisk kryptering.

1. Skapa en tillgång och överför filer till tillgången.
2. Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet.
3. Skapa en innehållsnyckel och associera den med den kodade tillgången. I Media Services innehåller innehållsnyckeln tillgångens krypteringsnyckel.
4. Konfigurera innehållsnyckelns auktoriseringsprincip. Innehållsnyckelns auktoriseringsprincip måste konfigureras av dig och uppfyllas av klienten för att innehållsnyckeln ska kunna levereras till klienten.

    När du skapar innehållsnyckelns auktoriseringsprincip behöver du specificera följande: leveransmetod (PlayReady eller Widevine), begränsningar (öppen eller token) och information som är specifik för nyckelleveranstypen och som definierar hur nyckeln levereras till klienten ([PlayReady](media-services-playready-license-template-overview.md)- eller [Widevine](media-services-widevine-license-template-overview.md)-licensmall).

5. Konfigurera leveransprincipen för en tillgång. Konfigurationen för leveransprincipen omfattar: leveransprotokoll (till exempel MPEG DASH, HLS, jämn direktuppspelning eller alla), typen av dynamisk kryptering (t.ex. Common Encryption) och URL för anskaffning av PlayReady- eller Widevine-licens.

    Du kan använda olika principer för varje protokoll för samma tillgång. Du kan till exempel tillämpa PlayReady-kryptering för Smooth/DASH och AES Envelope för HLS. Alla protokoll som inte har definierats i en leveransprincip (exempelvis kan du lägga till en enskild princip som endast anger HLS som protokoll) kommer att blockeras från strömning. Ett undantag till detta är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll fritt.

6. Skapa en OnDemand-lokaliserare för att få en strömnings-URL.

Du hittar ett komplett .NET-exempel i slutet av avsnittet.

Följande bild visar arbetsflödet som beskrivs ovan. Här används aktuellt token för autentisering.

![Skydda med PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

Resten av det här avsnittet innehåller detaljerade förklaringar, kodexempel och länkar till ämnen som visar hur du utför de uppgifter som beskrivs ovan.

## <a name="current-limitations"></a>Aktuella begränsningar
Om du lägger till eller uppdaterar en tillgångsleveransprincip måste du ta bort den associerade lokaliseraren (om sådan finns) och skapa en ny.

Begränsning vid kryptering med Widevine med Azure Media Services: för närvarande stöds inte nycklar för multiinnehåll.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>Skapa en tillgång och överföra filer till tillgången
För att hantera, koda och strömma videor måste du först överföra innehållet till Microsoft Azure Media Services. När du har överfört innehållet lagras det på ett säkert sätt i molnet för vidare bearbetning och strömning.

Utförlig information finns i [Överföra filer till ett Media Services-konto](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-containing-the-file-to-the-adaptive-bitrate-mp4-set"></a>Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet
Med dynamisk kryptering behöver du bara skapa en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Därefter, baserat på det format som anges i manifestet och i fragmentbegäran, kommer servern för strömning på begäran att säkerställa att du får den dataström i protokollet som du har valt. Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient. Mer information finns i ämnet [Översikt över dynamisk paketering](media-services-dynamic-packaging-overview.md).

Mer information om att koda finns i [Koda en tillgång med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="a-idcreatecontentkeyacreate-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Skapa en innehållsnyckel och associera den med den kodade tillgången
I Media Services innehåller innehållsnyckeln den nyckel som du vill kryptera en tillgång med.

Utförlig information finns i [Skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md).

## <a name="a-idconfigurekeyauthpolicyaconfigure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Konfigurera en auktoriseringsprincip för innehållsnyckeln
Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Innehållsnyckelns auktoriseringsprincip måste konfigureras av dig och uppfyllas av klienten (spelaren) för att nyckeln ska kunna levereras till klienten. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: öppen eller tokenbegränsning.

Mer information finns i [Konfigurera  innehållsnyckelns auktoriseringsprincip](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

## <a name="a-idconfigureassetdeliverypolicyaconfigure-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Konfigurera tillgångsleveransprincip
Konfigurera leveransprincipen för din tillgång. Tillgångsleveransprincipen innehåller bland annat följande:

* URL för anskaffning av DRM-licens.
* Protokollet för tillgångsleverans (t.ex. MPEG DASH, HLS, jämn direktuppspelning eller alla).
* Typen av dynamisk kryptering (i det här fallet Common Encryption).

Detaljerad information finns i [Konfigurera tillgångsleveransprincip ](media-services-rest-configure-asset-delivery-policy.md).

## <a name="a-idcreatelocatoracreate-an-ondemand-streaming-locator-in-order-to-get-a-streaming-url"></a><a id="create_locator"></a>Skapa en lokaliserare för OnDemand-strömning för att få en strömnings-URL
Du måste förse din användare med strömnings-URL:en för Smooth, DASH eller HLS.

> [!NOTE]
> Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort en befintlig lokaliserare (om sådan finns) och skapa en ny.
>
>

Anvisningar för hur du publicerar en tillgång och skapar en strömnings-URL finns i [Skapa en strömnings-URL](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Hämta en testtoken
Hämta en testtoken baserat på de tokenbegränsningar som användes för nyckelauktoriseringsprincipen.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string.
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);


Du kan använda [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) för att testa din dataström.

## <a name="a-idexampleaexample"></a><a id="example"></a>Exempel
I följande exempel visas de funktioner som introducerades i Azure Media Services SDK för .Net -Version 3.5.2 (särskilt möjligheten att definiera en Widevine-licensmall och begära en Widevine-licens från Azure Media Services). Följande Nuget-paketkommando användes för att installera paketet:

    PM> Install-Package windowsazure.mediaservices -Version 3.5.2


1. Skapa ett nytt Console-projekt.
2. Använd NuGet för att installera och lägga till Azure Media Services .NET SDK.
3. Lägg till ytterligare referenser: System.Configuration.
4. Lägg till den config-fil som innehåller kontonamn och nyckelinformation:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
            <startup>
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
              <appSettings>

                <add key="MediaServicesAccountName" value="AccountName"/>
                <add key="MediaServicesAccountKey" value="AccountKey"/>

                <add key="Issuer" value="http://testacs.com"/>
                <add key="Audience" value="urn:test"/>
              </appSettings>
        </configuration>
5. Du behöver minst en enhet för strömning för den strömningsslutpunkt från vilken du planerar att leverera ditt innehåll. Mer information finns i [Konfigurera strömningsslutpunkter](media-services-dotnet-get-started.md#configure-streaming-endpoints-using-the-azure-portal).
6. Skriv över koden i Program.cs-filen med koden som visas i det här avsnittet.

    Se till att uppdatera variablerna så att de pekar på mappar där dina indatafiler finns.

        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Threading;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
        using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
        using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
        using Newtonsoft.Json;

        namespace DynamicEncryptionWithDRM
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];

                private static readonly Uri _sampleIssuer =
                    new Uri(ConfigurationManager.AppSettings["Issuer"]);
                private static readonly Uri _sampleAudience =
                    new Uri(ConfigurationManager.AppSettings["Audience"]);

                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;

                private static readonly string _mediaFiles =
                    Path.GetFullPath(@"../..\Media");

                private static readonly string _singleMP4File =
                    Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

                static void Main(string[] args)
                {
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);

                    bool tokenRestriction = false;
                    string tokenTemplateString = null;

                    IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
                    Console.WriteLine("Uploaded asset: {0}", asset.Id);

                    IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
                    Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

                    IContentKey key = CreateCommonTypeContentKey(encodedAsset);
                    Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
                    Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
                    Console.WriteLine();

                    if (tokenRestriction)
                        tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
                    else
                        AddOpenAuthorizationPolicy(key);

                    Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
                    Console.WriteLine();

                    CreateAssetDeliveryPolicy(encodedAsset, key);
                    Console.WriteLine("Created asset delivery policy. \n");
                    Console.WriteLine();

                    if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
                    {
                        // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                        // back into a TokenRestrictionTemplate class instance.
                        TokenRestrictionTemplate tokenTemplate =
                            TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                        // Generate a test token based on the the data in the given TokenRestrictionTemplate.
                        // Note, you need to pass the key id Guid because we specified
                        // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                        Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                        string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                                                                DateTime.UtcNow.AddDays(365));
                        Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                        Console.WriteLine();
                    }

                    // You can use the http://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
                    // Note that DASH works on IE 11 (via PlayReady), Edge (via PlayReady), Chrome (via Widevine).

                    string url = GetStreamingOriginLocator(encodedAsset);
                    Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

                    Console.ReadLine();
                }

                static public IAsset UploadFileAndCreateAsset(string singleFilePath)
                {
                    if (!File.Exists(singleFilePath))
                    {
                        Console.WriteLine("File does not exist.");
                        return null;
                    }

                    var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
                    IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

                    var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

                    Console.WriteLine("Created assetFile {0}", assetFile.Name);

                    var policy = _context.AccessPolicies.Create(
                                            assetName,
                                            TimeSpan.FromDays(30),
                                            AccessPermissions.Write | AccessPermissions.List);

                    var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);

                    Console.WriteLine("Upload {0}", assetFile.Name);

                    assetFile.Upload(singleFilePath);
                    Console.WriteLine("Done uploading {0}", assetFile.Name);

                    locator.Delete();
                    policy.Delete();

                    return inputAsset;
                }

                static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
                {
                    var encodingPreset = "H264 Multiple Bitrate 720p";

                    IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                                            inputAsset.Name,
                                            encodingPreset));

                    var mediaProcessors =
                        _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

                    var latestMediaProcessor =
                        mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

                    ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
                    encodeTask.InputAssets.Add(inputAsset);
                    encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset),     AssetCreationOptions.StorageEncrypted);

                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
                    job.Submit();
                    job.GetExecutionProgressTask(CancellationToken.None).Wait();

                    return job.OutputMediaAssets[0];
                }


                static public IContentKey CreateCommonTypeContentKey(IAsset asset)
                {

                    Guid keyId = Guid.NewGuid();
                    byte[] contentKey = GetRandomBuffer(16);

                    IContentKey key = _context.ContentKeys.Create(
                                            keyId,
                                            contentKey,
                                            "ContentKey",
                                            ContentKeyType.CommonEncryption);

                    // Associate the key with the asset.
                    asset.ContentKeys.Add(key);

                    return key;
                }

                static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
                {

                    // Create ContentKeyAuthorizationPolicy with Open restrictions
                    // and create authorization policy          

                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Open",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                            Requirements = null
                        }
                    };

                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);

                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("",
                            ContentKeyDeliveryType.Widevine,
                            restrictions, WidevineLicenseTemplate);

                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with no restrictions").
                                Result;


                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
                    // Associate the content key authorization policy with the content key.
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;
                }

                public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
                {
                    string tokenTemplateString = GenerateTokenRequirements();

                    List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                        new ContentKeyAuthorizationPolicyRestriction
                        {
                            Name = "Token Authorization Policy",
                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                            Requirements = tokenTemplateString,
                        }
                    };

                    // Configure PlayReady and Widevine license templates.
                    string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

                    string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

                    IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.PlayReadyLicense,
                                restrictions, PlayReadyLicenseTemplate);

                    IContentKeyAuthorizationPolicyOption WidevinePolicy =
                        _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                            ContentKeyDeliveryType.Widevine,
                                restrictions, WidevineLicenseTemplate);

                    IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("Deliver Common Content Key with token restrictions").
                                Result;

                    contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
                    contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

                    // Associate the content key authorization policy with the content key
                    contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
                    contentKey = contentKey.UpdateAsync().Result;

                    return tokenTemplateString;
                }

                static private string GenerateTokenRequirements()
                {
                    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

                    template.PrimaryVerificationKey = new SymmetricVerificationKey();
                    template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
                    template.Audience = _sampleAudience.ToString();
                    template.Issuer = _sampleIssuer.ToString();
                    template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

                    return TokenRestrictionTemplateSerializer.Serialize(template);
                }

                static private string ConfigurePlayReadyLicenseTemplate()
                {
                    // The following code configures PlayReady License Template using .NET classes
                    // and returns the XML string.

                    //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user.
                    //It contains a field for a custom data string between the license server and the application
                    //(may be useful for custom app logic) as well as a list of one or more license templates.
                    PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

                    // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
                    // to be returned to the end users.
                    //It contains the data on the content key in the license and any rights or restrictions to be
                    //enforced by the PlayReady DRM runtime when using the content key.
                    PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
                    //Configure whether the license is persistent (saved in persistent storage on the client)
                    //or non-persistent (only held in memory while the player is using the license).  
                    licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

                    // AllowTestDevices controls whether test devices can use the license or not.  
                    // If true, the MinimumSecurityLevel property of the license
                    // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
                    licenseTemplate.AllowTestDevices = true;

                    // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class.
                    // It grants the user the ability to playback the content subject to the zero or more restrictions
                    // configured in the license and on the PlayRight itself (for playback specific policy).
                    // Much of the policy on the PlayRight has to do with output restrictions
                    // which control the types of outputs that the content can be played over and
                    // any restrictions that must be put in place when using a given output.
                    // For example, if the DigitalVideoOnlyContentRestriction is enabled,
                    //then the DRM runtime will only allow the video to be displayed over digital outputs
                    //(analog video outputs won’t be allowed to pass the content).

                    //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience.
                    // If the output protections are configured too restrictive,
                    // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

                    // For example:
                    //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

                    responseTemplate.LicenseTemplates.Add(licenseTemplate);

                    return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
                }


                private static string ConfigureWidevineLicenseTemplate()
                {
                    var template = new WidevineMessage
                    {
                        allowed_track_types = AllowedTrackTypes.SD_HD,
                        content_key_specs = new[]
                        {
                            new ContentKeySpecs
                            {
                                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                                security_level = 1,
                                track_type = "SD"
                            }
                        },
                        policy_overrides = new
                        {
                            can_play = true,
                            can_persist = true,
                            can_renew = false
                        }
                    };

                    string configuration = JsonConvert.SerializeObject(template);
                    return configuration;
                }

                static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
                {
                    // Get the PlayReady license service URL.
                    Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

                    // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
                    // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
                    // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption
                    // to append /? KID =< keyId > to the end of the url when creating the manifest.
                    // As a result Widevine license acquisition URL will have KID appended twice,
                    // so we need to remove the KID that in the URL when we call GetKeyDeliveryUrl.

                    Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
                    UriBuilder uriBuilder = new UriBuilder(widevineUrl);
                    uriBuilder.Query = String.Empty;
                    widevineUrl = uriBuilder.Uri;

                    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
                        {
                            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                            {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

                        };

                    // In this case we only specify Dash streaming protocol in the delivery policy,
                    // All other protocols will be blocked from streaming.
                    var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                            "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicCommonEncryption,
                        AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);


                    // Add AssetDelivery Policy to the asset
                    asset.DeliveryPolicies.Add(assetDeliveryPolicy);

                }

                /// <summary>
                /// Gets the streaming origin locator.
                /// </summary>
                /// <param name="assets"></param>
                /// <returns></returns>
                static public string GetStreamingOriginLocator(IAsset asset)
                {

                    // Get a reference to the streaming manifest file from the  
                    // collection of files in the asset.

                    var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                                 EndsWith(".ism")).
                                                 FirstOrDefault();

                    // Create a 30-day readonly access policy.
                    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
                        TimeSpan.FromDays(30),
                        AccessPermissions.Read);

                    // Create a locator to the streaming content on an origin.
                    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
                        policy,
                        DateTime.UtcNow.AddMinutes(-5));

                    // Create a URL to the manifest file.
                    return originLocator.Path + assetFile.Name;
                }

                static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
                        ((IJob)sender).Name,
                        e.CurrentState,
                        DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
                }

                static private byte[] GetRandomBuffer(int length)
                {
                    var returnValue = new byte[length];

                    using (var rng =
                        new System.Security.Cryptography.RNGCryptoServiceProvider())
                    {
                        rng.GetBytes(returnValue);
                    }

                    return returnValue;
                }
            }
        }


## <a name="next-step"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[CENC med Multi-DRM och Access Control](media-services-cenc-with-multidrm-access-control.md)

[Konfigurera Widevine-paketering med AMS](http://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

[Vi ber att få presentera Google Widevines licensleveranstjänster i Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)



<!--HONumber=Dec16_HO2-->



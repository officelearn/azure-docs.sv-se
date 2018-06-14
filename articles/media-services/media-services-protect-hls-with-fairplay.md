---
title: Skydda innehåll med Microsoft PlayReady- eller Apple FairPlay - Azure HLS | Microsoft Docs
description: Det här avsnittet ger en översikt och visar hur du använder Azure Media Services för att kryptera dynamiskt innehåll med Apple FairPlay HTTP Live Streaming (HLS). Den visar även hur du använder Media Services licensleveranstjänst för att leverera FairPlay-licenser till klienter.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 91f117c3b1b166a069b93c238380140f19e49280
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2018
ms.locfileid: "29738430"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Skydda ditt innehåll med Apple FairPlay eller Microsoft PlayReady HLS
Azure Media Services kan du kryptera dynamiskt innehåll HTTP Live Streaming (HLS) med hjälp av följande format:  

* **AES-128 kuvert klartextnyckeln**

    Det hela segmentet krypteras med hjälp av den **AES-128 CBC** läge. Dekryptering av dataströmmen stöds internt av iOS och OS X spelare. Mer information finns i [dynamisk med hjälp av AES-128-kryptering och viktiga leverans tjänsten](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Enskilda video och ljud prover krypteras med hjälp av den **AES-128 CBC** läge. **FairPlay strömning** (FPS) är integrerade i enhetens operativsystem, med inbyggt stöd på iOS- och Apple TV. Safari för OS X kan FPS genom att använda stöd för krypterade Media tillägg (EME)-gränssnittet.
* **Microsoft PlayReady**

Följande bild visar den **HLS + FairPlay eller PlayReady dynamisk kryptering** arbetsflöde.

![Diagram över arbetsflödet för dynamisk kryptering](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Den här artikeln visar hur du använder Media Services för att kryptera din HLS innehåll med Apple FairPlay dynamiskt. Den visar även hur du använder Media Services licensleveranstjänst för att leverera FairPlay-licenser till klienter.

> [!NOTE]
> Om du även vill kryptera dina HLS innehåll med PlayReady måste du skapa en gemensam innehållsnyckel och associera den med din tillgång. Du måste också konfigurera innehållsnyckelns auktoriseringsprincip, enligt beskrivningen i [med PlayReady-kryptering för dynamiska vanliga](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Krav och överväganden

Följande krävs när du använder Media Services att leverera HLS som krypterats med FairPlay och för att leverera FairPlay-licenser:

  * Ett Azure-konto. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Ett Media Services-konto. Om du vill skapa en finns [skapa ett Azure Media Services-konto med hjälp av Azure portal](media-services-portal-create-account.md).
  * Logga med [Apple Development Program](https://developer.apple.com/).
  * Apple kräver innehållets ägare att hämta den [distributionspaketet](https://developer.apple.com/contact/fps/). Tillstånd redan implementerats nyckeln Security Module (KSM) med Media Services och att du begär slutliga FPS paketet. Det finns instruktioner i sista FPS paketet för att generera certifiering och hämta program hemlig nyckel (be). Du kan använda be för att konfigurera FairPlay.
  * Azure Media Services .NET SDK-versionen **3.6.0** eller senare.

Följande måste anges på Media Services viktiga leverans sida:

  * **Appen Cert (nät)**: Detta är en .pfx-fil som innehåller den privata nyckeln. Du skapar den här filen och kryptera den med ett lösenord.

       När du konfigurerar en leveransprincip för viktiga, måste du ange lösenordet och PFX-filen i Base64-format.

      Följande steg beskriver hur du skapar en PFX-fil för FairPlay:

    1. Installera OpenSSL från https://slproweb.com/products/Win32OpenSSL.html.

        Gå till den mapp där FairPlay-certifikat och andra filer som levereras av Apple.
    2. Kör följande kommando från kommandoraden. Detta konverterar CER-filen till en PEM-filen.

        ”C:\OpenSSL-Win32\bin\openssl.exe” x509-informera der-i FairPlay.cer-ut FairPlay out.pem
    3. Kör följande kommando från kommandoraden. Detta konverterar PEM-filen till en .pfx-fil med den privata nyckeln. Lösenordet för PFX-filen blir sedan ombedd av OpenSSL.

        ”C:\OpenSSL-Win32\bin\openssl.exe” pkcs12-exportera - ut FairPlay out.pfx-inkey privatekey.pem-i FairPlay out.pem - passin file:privatekey-pem-pass.txt
  * **Appen Cert lösenord**: lösenordet för att skapa en .pfx-fil.
  * **ID för appen Cert lösenord**: du måste överföra lösenordet, som liknar hur de överför andra Media Services-nycklar. Använd den **ContentKeyType.FairPlayPfxPassword** enum-värde för att hämta ID för Media Services Det här är vad de behöver för att använda inuti alternativet viktiga leverans.
  * **IV**: Detta är ett slumpmässigt värde av 16 byte. Den måste matcha iv i principen för tillgångsleverans. Du genererar iv och placera den på båda platserna: tillgångsleveransprincip och alternativet viktiga leverans.
  * **Be**: den här nyckeln tas emot när du skapar ett certifikat med hjälp av Apple Developer-portalen. Varje Utvecklingsteamet tar emot en unik be. Spara en kopia av be och lagra den på en säker plats. Du måste konfigurera be som FairPlayAsk till Media Services senare.
  * **Be ID**: Detta ID hämtas när du överför be till Media Services. Du måste överföra be med hjälp av den **ContentKeyType.FairPlayAsk** enum-värde. Media Services ID returneras som ett resultat, och det här är vad ska användas när du ställer in alternativet viktiga leverans.

Följande måste anges av klientsidan FPS:

  * **Appen Cert (nät)**: Detta är en.cer/.der-fil som innehåller den offentliga nyckeln som operativsystemet använder för att kryptera vissa nyttolast. Media Services behöver veta om den eftersom det krävs av Windows Media player. Tjänsten key dekrypterar den med hjälp av motsvarande privata nyckel.

Skaffa en verklig be först om du vill spela upp en krypterad dataström FairPlay och generera ett verkliga certifikat. Den här processen skapar alla tre delar:

  * .der fil
  * .pfx-fil
  * lösenordet för PFX

Följande klienter stöder HLS med **AES-128 CBC** kryptering: Safari på OS X, Apple TV iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurera FairPlay dynamisk kryptering och licensen för leverans av tjänster
Följande är allmänna steg för att skydda dina tillgångar med FairPlay med hjälp av Media Services licensleveranstjänst och även med hjälp av dynamisk kryptering.

1. Skapa en tillgång och överföra filer till tillgången.
2. Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet.
3. Skapa en innehållsnyckel och associera den med den kodade tillgången.  
4. Konfigurera innehållsnyckelns auktoriseringsprincip. Ange följande:

   * Leveransmetod (i det här fallet FairPlay).
   * FairPlay alternativkonfiguration. Mer information om hur du konfigurerar FairPlay finns i **ConfigureFairPlayPolicyOptions()** metod i exemplet nedan.

     > [!NOTE]
     > Vanligtvis vill du konfigurera alternativ för FairPlay princip bara en gång, eftersom du bara har en uppsättning av en certifikatutfärdare och en fråga.
     >
     >
   * Begränsningar (öppen eller token).
   * Information som är specifik för nyckelleveranstypen och som definierar hur nyckeln levereras till klienten.
5. Konfigurera i principen för tillgångsleverans. Konfigurationen för leveransprincipen omfattar:

   * Leveransprotokoll (HLS).
   * Typen av dynamisk kryptering (vanliga CBC kryptering).
   * URL för anskaffning av licens.

     > [!NOTE]
     > Du måste konfigurera separata leveransprinciperna om du vill leverera en ström som har krypterats med FairPlay och en annan Digital Rights Management (DRM) system:
     >
     > * En IAssetDeliveryPolicy att konfigurera dynamisk anpassningsbar strömning via HTTP-(bindestreck) med vanliga kryptering (CENC) (PlayReady och Widevine) och Smooth med PlayReady
     > * En annan IAssetDeliveryPolicy att konfigurera FairPlay för HLS
     >
     >
6. Skapa en OnDemand-lokaliserare för att få en strömnings-URL.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Använd FairPlay viktiga leverans av player appar
Du kan utveckla player appar med hjälp av iOS SDK. Om du vill kunna spela upp FairPlay innehåll som du behöver implementera licens exchange-protokollet. Det här protokollet har inte angetts av Apple. Det är varje app så att skicka begäranden viktiga leverans. Media Services FairPlay viktiga tjänsten förväntar SPC komma som meddelandet www-form-url kodad post i följande format:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player stöder FairPlay uppspelning. Se [dokumentation för Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) för ytterligare information.
>
>

## <a name="streaming-urls"></a>Direktuppspelnings-URL:er
Om din tillgång har krypterats med flera DRM, bör du använda en kryptering-tagg i strömnings-URL: (format = 'm3u8 aapl', kryptering = ”xxx”).

Följande gäller:

* Endast noll eller en typ av enhetskryptering kan anges.
* Krypteringstypen behöver inte anges i URL: en om bara en kryptering har tillämpats på tillgången.
* Krypteringstypen är skiftlägeskänsligt.
* Följande krypteringstyper av kan anges:  
  * **cenc**: Common encryption (PlayReady eller Widevine)
  * **cbcs-aapl**: FairPlay
  * **CBC**: AES envelope kryptering

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

1. Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 
2. Lägg till följande element i **appSettings** som definieras i filen app.config:

    ```xml
            <add key="Issuer" value="http://testacs.com"/>
            <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Exempel

I följande exempel visar möjligheten att använda Media Services kan leverera ditt innehåll som krypterats med FairPlay. Den här funktionen introducerades i Azure Media Services SDK för .NET version 3.6.0. 

Skriv över koden i Program.cs-filen med koden som visas i det här avsnittet.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här artikeln](media-services-dotnet-manage-entities.md#limit-access-policies).

Se till att uppdatera variablerna så att de pekar på mappar där dina indatafiler finns.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

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

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

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


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

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

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
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
```

## <a name="next-steps-media-services-learning-paths"></a>Nästa steg: Utbildningsvägar för Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

---
title: Skydda HLS-innehåll med Microsoft PlayReady eller Apple FairPlay – Azure | Microsoft-dokument
description: Det här avsnittet innehåller en översikt och visar hur du använder Azure Media Services för att dynamiskt kryptera ditt HTTP Live Streaming (HLS) innehåll med Apple FairPlay. Den visar också hur du använder licensleveranstjänsten för Media Services för att leverera FairPlay-licenser till kunder.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 873bc4ab5e435b91ff4400a39c92db0d0bb9baa8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74968773"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Skydda ditt HLS-innehåll med Apple FairPlay eller Microsoft PlayReady

> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).   > Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)
>

Med Azure Media Services kan du dynamiskt kryptera ditt HTTP Live Streaming (HLS)-innehåll med hjälp av följande format:  

* **AES-128 kuvert klar nyckel**

    Hela segmentet krypteras med hjälp av **AES-128 CBC-läget.** Dekrypteringen av strömmen stöds av iOS- och OS X-spelare internt. Mer information finns i [Använda dynamisk kryptering och nyckelleveranstjänst för AES-128](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    De enskilda video- och ljudexemplen krypteras med hjälp av **AES-128 CBC-läget.** **FairPlay Streaming** (FPS) är integrerat i enhetens operativsystem, med inbyggt stöd på iOS och Apple TV. Safari på OS X aktiverar FPS med hjälp av EME-gränssnittsstödet (Encrypted Media Extensions).
* **Microsoft PlayReady**

Följande bild visar arbetsflödet **för dynamisk kryptering av HLS + FairPlay eller PlayReady.**

![Diagram över arbetsflöde för dynamisk kryptering](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Den här artikeln visar hur du använder Media Services för att dynamiskt kryptera ditt HLS-innehåll med Apple FairPlay. Den visar också hur du använder licensleveranstjänsten för Media Services för att leverera FairPlay-licenser till kunder.

> [!NOTE]
> Om du också vill kryptera ditt HLS-innehåll med PlayReady måste du skapa en gemensam innehållsnyckel och associera det med din tillgång. Du måste också konfigurera innehållsnyckelns auktoriseringsprincip enligt beskrivningen i [Använda PlayReady dynamisk gemensam kryptering](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Krav och överväganden

Följande krävs när du använder Media Services för att leverera HLS krypterad med FairPlay och för att leverera FairPlay-licenser:

  * Ett Azure-konto. Mer information finns i [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)av Azure .
  * Ett Media Services-konto. Information om hur du skapar ett finns i [Skapa ett Azure Media Services-konto med Azure-portalen](media-services-portal-create-account.md).
  * Registrera dig med [Apples utvecklingsprogram](https://developer.apple.com/).
  * Apple kräver att innehållsägaren hämtar [distributionspaketet](https://developer.apple.com/contact/fps/). Tillstånd som du redan har implementerat Key Security Module (KSM) med Media Services och att du begär det slutliga FPS-paketet. Det finns instruktioner i det slutliga FPS-paketet för att generera certifiering och hämta Application Secret Key (ASK). Du använder ASK för att konfigurera FairPlay.
  * Azure Media Services .NET SDK version **3.6.0** eller senare.

Följande saker måste anges på Media Services nyckelleveranssida:

  * **App Cert (AC)**: Det här är en PFX-fil som innehåller den privata nyckeln. Du skapar den här filen och krypterar den med ett lösenord.

       När du konfigurerar en nyckelleveransprincip måste du ange lösenordet och PFX-filen i Base64-format.

      I följande steg beskrivs hur du skapar en PFX-certifikatfil för FairPlay:

    1. Installera OpenSSL https://slproweb.com/products/Win32OpenSSL.htmlfrån .

        Gå till mappen där FairPlay-certifikatet och andra filer som levereras av Apple finns.
    2. Kör följande kommando från kommandoraden. Detta konverterar .cer-filen till en PEM-fil.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
    3. Kör följande kommando från kommandoraden. Detta konverterar PEM-filen till en PFX-fil med den privata nyckeln. Lösenordet för PFX-filen ställs sedan in av OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
  * **App Cert lösenord:** Lösenordet för att skapa .pfx-filen.
  * **Lösenords-ID för App Cert:** Du måste ladda upp lösenordet, ungefär som när de laddar upp andra Media Services-nycklar. Använd värdet **ContentKeyType.FairPlayPfxPassword för** att få Medietjänst-ID. Detta är vad de behöver använda inom det viktigaste leveransprincipalternativet.
  * **iv**: Detta är ett slumpmässigt värde på 16 byte. Den måste matcha iv i leveranspolicyn för tillgångar. Du genererar iv och placerar den på båda ställena: leveranspolicyn för tillgångar och alternativet för nyckelleveransprinciper.
  * **ASK:** Den här nyckeln tas emot när du genererar certifieringen med hjälp av Apples utvecklarportal. Varje utvecklingsteam får en unik ASK. Spara en kopia av ASK och förvara den på ett säkert ställe. Du måste konfigurera ASK som FairPlayAsk till Media Services senare.
  * **ASK ID:** Det här ID:t erhålls när du laddar upp ASK till Media Services. Du måste ladda upp ASK med hjälp av **contentkeytype.fairplayAsk-uppräkningsvärdet.** Därför returneras Media Services-ID:t och det är vad som ska användas när du anger alternativet för nyckelleveransprinciper.

Följande saker måste ställas in av FPS-klientsidan:

  * **App Cert (AC)**: Detta är en .cer/.der-fil som innehåller den offentliga nyckeln, som operativsystemet använder för att kryptera vissa nyttolaster. Media Services behöver veta om det eftersom det krävs av spelaren. Nyckelleveranstjänsten dekrypterar den med motsvarande privata nyckel.

Om du vill spela upp en FairPlay-krypterad ström får du en riktig ASK först och genererar sedan ett riktigt certifikat. Denna process skapar alla tre delarna:

  * .der-fil
  * .pfx-fil
  * lösenord för PFX

Följande klienter stöder HLS med **AES-128 CBC-kryptering:** Safari på OS X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Konfigurera FairPlays dynamiska krypterings- och licensleveranstjänster
Följande är allmänna steg för att skydda dina tillgångar med FairPlay med hjälp av Media Services licensleveranstjänst, och även genom att använda dynamisk kryptering.

1. Skapa en tillgång och överföra filer till tillgången.
2. Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet.
3. Skapa en innehållsnyckel och associera den med den kodade tillgången.  
4. Konfigurera innehållsnyckelns auktoriseringsprincip. Ange följande:

   * Leveransmetoden (i det här fallet FairPlay).
   * Konfiguration av fairplay-principalternativ. Mer information om hur du konfigurerar FairPlay finns i metoden **ConfigureFairPlayPolicyOptions()** i exemplet nedan.

     > [!NOTE]
     > Vanligtvis vill du bara konfigurera FairPlay-principalternativ en gång, eftersom du bara har en uppsättning en certifiering och en ASK.
     >
     >
   * Begränsningar (öppna eller token).
   * Information som är specifik för nyckelleveranstypen som definierar hur nyckeln levereras till klienten.
5. Konfigurera principen för tillgångsleverans. Konfigurationen av leveransprincipen omfattar:

   * Leveransprotokollet (HLS).
   * Typ av dynamisk kryptering (vanlig CBC-kryptering).
   * URL:en för licensförvärv.

     > [!NOTE]
     > Om du vill leverera en ström som är krypterad med FairPlay och ett annat DRM-system (Digital Rights Management) måste du konfigurera separata leveransprinciper:
     >
     > * En IAssetDeliveryPolicy för att konfigurera dynamisk adaptiv direktuppspelning via HTTP (DASH) med gemensam kryptering (CENC) (PlayReady + Widevine) och Smooth med PlayReady
     > * En annan IAssetDeliveryPolicy för att konfigurera FairPlay för HLS
     >
     >
6. Skapa en OnDemand-lokaliserare för att få en strömnings-URL.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Använd FairPlay-nyckelleverans av spelarappar
Du kan utveckla spelarappar med hjälp av iOS SDK. För att kunna spela FairPlay-innehåll måste du implementera licensutbytesprotokollet. Det här protokollet har inte angetts av Apple. Det är upp till varje app hur du skickar viktiga leveransförfrågningar. Media Services FairPlay nyckelleveranstjänsten förväntar sig att SPC kommer som ett www-form-url-kodat inläggsmeddelande i följande form:

    spc=<Base64 encoded SPC>

> [!NOTE]
> Azure Media Player stöder FairPlay-uppspelning. Mer information finns i Dokumentationen till [Azure Media Player.](https://amp.azure.net/libs/amp/latest/docs/index.html)
>
>

## <a name="streaming-urls"></a>Url:er för direktuppspelning
Om din tillgång krypterades med mer än en DRM bör du använda en krypteringstagg i direktuppspelnings-URL:en: (format='m3u8-aapl', encryption='xxx').

Följande gäller:

* Endast noll eller en krypteringstyp kan anges.
* Krypteringstypen behöver inte anges i URL:en om bara en kryptering har tillämpats på tillgången.
* Krypteringstypen är skiftlägesokänslig.
* Följande krypteringstyper kan anges:  
  * **cenc**: Gemensam kryptering (PlayReady eller Widevine)
  * **cbcs-aapl**: FairPlay
  * **cbc**: AES-kuvertkryptering

## <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

1. Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation enligt beskrivningen i [Media Services-utvecklingen med .NET](media-services-dotnet-how-to-use.md). 
2. Lägg till följande element i **appSettings** som definieras i filen app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Exempel

Följande exempel visar möjligheten att använda Media Services för att leverera ditt innehåll krypterat med FairPlay. Den här funktionen introducerades i Azure Media Services SDK för .NET version 3.6.0. 

Skriv över koden i Program.cs-filen med koden som visas i det här avsnittet.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

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

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
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

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
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

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="next-steps-media-services-learning-paths"></a>Nästa steg: Utbildningsvägar för Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

---
title: Offline FairPlay Streaming för iOS med Azure Media Services v3
description: Det här avsnittet ger en översikt och visar hur du använder Azure Media Services för att dynamiskt kryptera ditt HTTP Live Streaming (HLS) innehåll med Apple FairPlay i offlineläge.
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 70256046089a59df1de79b78124c5d60fde77080
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705946"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>Offline FairPlay Streaming för iOS med Media Services v3

 Azure Media Services tillhandahåller en uppsättning välutformade [innehållsskyddstjänster](https://azure.microsoft.com/services/media-services/content-protection/) som täcker:

- Microsoft PlayReady
- Google Widevine
    
    Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.
- Apple FairPlay
- AES-128-kryptering

DRM-kryptering (Digital Rights Management)/Advanced Encryption Standard (AES) av innehåll utförs dynamiskt på begäran av olika streamingprotokoll. DRM-licens/AES dekrypteringsnyckelleveranstjänster tillhandahålls också av Media Services.

Förutom att skydda innehåll för onlinestreaming via olika streamingprotokoll är offlineläge för skyddat innehåll också en ofta efterfrågad funktion. Stöd i offlineläge behövs för följande scenarier:

* Uppspelning när internetanslutning inte är tillgängligt, till exempel under resan.
* Vissa innehållsleverantörer kan inte tillåta leverans av DRM-licenser utanför ett lands/regions gräns. Om användarna vill titta på innehåll när de reser utanför landet/regionen behövs nedladdning offline.
* I vissa länder/regioner är internettillgängligheten och/eller bandbredden fortfarande begränsad. Användare kan välja att ladda ner först för att kunna titta på innehåll i en upplösning som är tillräckligt hög för en tillfredsställande tittarupplevelse. I det här fallet är problemet vanligtvis inte nätverkstillgänglighet utan begränsad nätverksbandbredd. Over-the-top (OTT) / online video plattform (OVP) leverantörer begära offline-läge stöd.

Den här artikeln innehåller stöd för FairPlay Streaming (FPS) offlineläge som riktar sig till enheter som kör iOS 10 eller senare. Den här funktionen stöds inte för andra Apple-plattformar, till exempel watchOS, tvOS eller Safari på macOS.

> [!NOTE]
> Offline DRM faktureras endast för att göra en enda begäran om en licens när du hämtar innehållet. Eventuella fel faktureras inte.

## <a name="prerequisites"></a>Krav

Innan du implementerar offline DRM för FairPlay på en iOS 10+-enhet:

* Granska innehållsskydd online för FairPlay: 

    - [Licenskrav för och konfiguration av Apple FairPlay](fairplay-license-overview.md)
    - [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
    - Ett .NET-exempel som innehåller konfiguration av fps-direktuppspelning online: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Skaffa FPS SDK från Apple Developer Network. FPS SDK innehåller två komponenter:

    - FPS Server SDK, som innehåller KSM (Key Security Module), klientexempel, en specifikation och en uppsättning testvektorer.
    - FPS Deployment Pack, som innehåller D-funktionsspecifikationen, tillsammans med instruktioner om hur du genererar FPS-certifikat, kundspecifik privat nyckel och Programhemlig nyckel. Apple utfärdar endast FPS Deployment Pack till licensierade innehållsleverantörer.
* Klona https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Du måste ändra koden i [Kryptera med DRM med .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) för att lägga till FairPlay-konfigurationer.  

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurera innehållsskydd i Azure Media Services

Gör följande i metoden [GetOrCreateContentKeyPolicyAsync:](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189)

Avkommentera koden som konfigurerar fairplay-principalternativet:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Ta också bort kommentera koden som lägger till CBCS ContentKeyPolicyOption i listan över ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Aktivera offlineläge

Om du vill aktivera offlineläge skapar du en anpassad StreamingPolicy och använder dess namn när du skapar en StreamingLocator i [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true  //this enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true //Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        //Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }

```

Nu är ditt Media Services-konto konfigurerat för att leverera offline FairPlay-licenser.

## <a name="sample-ios-player"></a>Exempel på iOS-spelare

Stöd för FPS-offlineläge är endast tillgängligt på iOS 10 och senare. FPS Server SDK (version 3.0 eller senare) innehåller dokumentet och exemplet för offline-läge för FPS. I synnerhet innehåller FPS Server SDK (version 3.0 eller senare) följande två objekt som är relaterade till offlineläge:

* Dokument: "Offlineuppspelning med FairPlay Streaming och HTTP Live Streaming." Apple, 14 september 2016. I FPS Server SDK version 4.0 slås det här dokumentet samman till det viktigaste FPS-dokumentet.
* Exempelkod: HLSCatalog-exempel (en del av Apples FPS Server SDK) för offline-FPS-läge i \FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. I exempelappen HLSCatalog används följande kodfiler för att implementera funktioner i offlineläge:

    - AssetPersistenceManager.swift-kodfil: AssetPersistenceManager är huvudklassen i det här exemplet som visar hur du:

        - Hantera hämtning av HLS-strömmar, till exempel API:er som används för att starta och avbryta hämtningar och ta bort befintliga tillgångar från enheter.
        - Övervaka hämtningsframsteget.
    - AssetListTableViewController.swift och AssetListTableViewCell.swift kodfiler: AssetListTableViewController är det viktigaste gränssnittet för detta exempel. Den innehåller en lista över tillgångar som exemplet kan använda för att spela upp, ladda ned, ta bort eller avbryta en hämtning. 

De här stegen visar hur du konfigurerar en iOS-spelare som körs. Om du utgår från HLSCatalog-exemplet i FPS Server SDK version 4.0.1 gör du följande kodändringar:

Implementera metoden `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` med hjälp av följande kod i HLSCatalog\Shared\Managers\ContentKeyDelegate.swift. Låt "drmUr" vara en variabel som tilldelats HLS-URL:en.

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

Implementera metoden `requestApplicationCertificate()`i HLSCatalog\Shared\Managers\ContentKeyDelegate.swift . Den här implementeringen beror på om du bäddar in certifikatet (endast offentlig nyckel) med enheten eller är värd för certifikatet på webben. I följande implementering används det värdbaserade programcertifikat som används i testexemplen. Låt "certUrl" vara en variabel som innehåller URL:en för programcertifikatet.

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

För det slutliga integrerade testet finns både video-URL:en och programcertifikat-URL:en i avsnittet "Integrerat test".

Lägg till webbadressen till testvideon i HLSCatalog\Shared\Resources\Streams.plist. För innehållsnyckel-ID använder du url:en för FairPlay-licensförvärv med skd-protokollet som unikt värde.

![Offline FairPlay iOS Appströmmar](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Använd din egen testvideo-URL, Url för FairPlay-licensförvärv och URL för programcertifikat, om du har konfigurerat dem. Du kan också fortsätta till nästa avsnitt, som innehåller testprover.

## <a name="integrated-test"></a>Integrerat test

Tre testexempel i Media Services täcker följande tre scenarier:

* FPS-skyddad, med video-, ljud- och alternativt ljudspår
* FPS skyddad, med video och ljud, men inget alternativt ljudspår
* FPS skyddad, med endast video och inget ljud

Du hittar dessa exempel på den [här demowebbplatsen](https://aka.ms/poc#22), med motsvarande programcertifikat som finns i en Azure-webbapp.
Med antingen version 3 eller version 4 exempel på FPS Server SDK, om en huvudspellista innehåller alternativt ljud, under offline-läge den spelar upp ljud bara. Därför måste du ta bort det alternativa ljudet. Med andra ord fungerar det andra och tredje exemplen som anges tidigare i online- och offlineläge. Exemplet som visas först spelar bara upp ljud i offlineläge, medan direktuppspelning online fungerar som det ska.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Följande vanliga frågor ger hjälp med felsökning:

- **Varför spelas bara ljud upp men inte video i offlineläge?** Det här beteendet verkar vara avsiktligt i exempelappen. När det finns ett alternativt ljudspår (vilket är fallet för HLS) i offlineläge, standard både iOS 10 och iOS 11 det alternativa ljudspåret. Om du vill kompensera för det här beteendet för FPS-offlineläge tar du bort det alternativa ljudspåret från strömmen. Om du vill göra detta på Media Services lägger du till det dynamiska manifestfiltret "endast ljud=false". Med andra ord slutar en HLS-URL med .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Varför spelar den fortfarande upp ljud endast utan video i offlineläge efter att jag har lagt till ljud-only=false?** Beroende på utformningen av cdn-cachenyckeln (Content Delivery Network) kan innehållet cachelagras. Rensa cacheminnet.
- **Stöds även FPS-offlineläge på iOS 11 utöver iOS 10?** Ja. FPS-offlineläge stöds för iOS 10 och iOS 11.
- **Varför hittar jag inte dokumentet "Offlineuppspelning med FairPlay Streaming och HTTP Live Streaming" i FPS Server SDK?** Sedan FPS Server SDK version 4 har det här dokumentet slagits samman till "FairPlay Streaming Programming Guide".
- **Vad är den nedladdade/offlinefilstrukturen på iOS-enheter?** Den nedladdade filstrukturen på en iOS-enhet ser ut som följande skärmbild. Mappen `_keys` lagrar nedladdade FPS-licenser, med en butiksfil för varje licenstjänstvärd. Mappen `.movpkg` lagrar ljud- och videoinnehåll. Den första mappen med ett namn som slutar med ett streck följt av ett numeriskt innehåller videoinnehåll. Det numeriska värdet är PeakBandwidth för videoåtergivningarna. Den andra mappen med ett namn som slutar med ett streck följt av 0 innehåller ljudinnehåll. Den tredje mappen med namnet "Data" innehåller huvudspellistan för FPS-innehållet. Slutligen ger boot.xml en fullständig `.movpkg` beskrivning av mappinnehållet. 

![Offline FairPlay iOS-exempelappfilstruktur](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Ett exempel boot.xml-fil:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="next-steps"></a>Nästa steg

Se hur du [skyddar med AES-128](protect-with-aes128.md)

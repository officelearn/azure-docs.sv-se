---
title: Skydda HLS-innehåll med offline Apple-FairPlay – Azure | Microsoft Docs
description: Det här avsnittet ger en översikt och visar hur du använder Azure Media Services för att dynamiskt Kryptera ditt HTTP Live Streaming (HLS)-innehåll med Apple FairPlay i offline-läge.
services: media-services
keywords: HLS, DRM, FairPlay streaming (FPS), offline, iOS 10
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
ms.openlocfilehash: 83fa8c9c6d98728d48ff4ed8993963cdbd522724
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974129"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline-FairPlay strömning för iOS 

 Azure Media Services tillhandahåller en uppsättning välkända [innehålls skydds tjänster](https://azure.microsoft.com/services/media-services/content-protection/) som omfattar:

- Microsoft PlayReady
- Google Widevine
    
    Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.
- Apple FairPlay
- AES-128-kryptering

Digital Rights Management (DRM)/Advanced Encryption Standard (AES) kryptering av innehåll utförs dynamiskt vid begäran om olika strömnings protokoll. DRM-Licens/AES-dekryptering av nyckel leverans tjänster tillhandahålls också av Media Services.

Förutom att skydda innehåll för online-direktuppspelning över olika strömnings protokoll, är offline-läge för skyddat innehåll också en ofta efterfrågad funktion. Stöd för offline-läge krävs i följande scenarier:

* Uppspelning när Internet anslutning inte är tillgängligt, till exempel under resan.
* Vissa innehålls leverantörer kan förhindra att DRM-licenser levereras bortom ett land/regions gräns. Om användarna vill titta på innehåll medan de reser utanför landet/regionen krävs offline-nedladdning.
* I vissa länder/regioner är Internet tillgänglighet och/eller bandbredd fortfarande begränsad. Användarna kan välja att ladda ned för att kunna se innehåll i en upplösning som är tillräckligt hög för en tillfredsställande visnings upplevelse. I det här fallet är problemet vanligt vis inte nätverks tillgänglighet men begränsad nätverks bandbredd. OVP-providers (OTT)/online-video plattform () stöder offline-läge.

Den här artikeln beskriver stöd för FairPlay streaming (FPS) offline-läge som är riktad mot enheter som kör iOS 10 eller senare. Den här funktionen stöds inte för andra Apple-plattformar, till exempel Watch, tvOS eller Safari på macOS.

> [!NOTE]
> Offline-DRM debiteras bara för att skapa en enskild begäran om en licens när du laddar ned innehållet. Eventuella fel faktureras inte.

## <a name="prerequisites"></a>Krav

Innan du implementerar offline DRM för FairPlay på en iOS 10 +-enhet:

* Granska innehålls skydd online för FairPlay: 

    - [Licenskrav för och konfiguration av Apple FairPlay](fairplay-license-overview.md)
    - [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
    - Ett .NET-exempel som innehåller konfiguration av strömmande online-FPS: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Hämta SDK: n för FPS från Apple Developer Network. SDK: n för FPS innehåller två komponenter:

    - Server-SDK för FPS, som innehåller nyckel säkerhetsmodulen (KSM), klient exempel, en specifikation och en uppsättning test vektorer.
    - Distributions paketet för FPS, som innehåller D-funktions specifikationen, tillsammans med anvisningar om hur du genererar ett FPS-certifikat, kundspecifik privat nyckel och en hemlig program nyckel. Apple utfärdar distributions paketet för FPS endast till licensierade innehålls leverantörer.
* Klona https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Du måste ändra koden i [kryptera med DRM med .net](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) för att lägga till Fairplay-konfigurationer.  

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurera innehålls skydd i Azure Media Services

Gör följande i [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) -metoden:

Ta bort kommentaren till den kod som konfigurerar FairPlay-princip alternativet:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Du kan också ta bort kommentaren till koden som lägger till CBCS ContentKeyPolicyOption i listan över ContentKeyPolicyOptions

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>Aktivera offline-läge

Om du vill aktivera offline-läge skapar du en anpassad StreamingPolicy och använder dess namn när du skapar en StreamingLocator i [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
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

Nu är ditt Media Services-konto konfigurerat för att leverera FairPlay-licenser offline.

## <a name="sample-ios-player"></a>Exempel på iOS-spelare

Stöd för offline-läge i FPS är bara tillgängligt på iOS 10 och senare. Server-SDK: n för FPS (version 3,0 eller senare) innehåller dokumentet och exemplet för offline-läge för FPS. Mer specifikt innehåller Server-SDK för FPS (version 3,0 eller senare) följande två objekt som är relaterade till offline-läge:

* Dokument: "offline-uppspelning med FairPlay streaming och HTTP Live Streaming". Apple, 14 september 2016. I FPS Server SDK version 4,0 är det här dokumentet sammanslaget i huvud-FPS-dokumentet.
* Exempel kod: HLSCatalog-exempel (en del av Apples Server-SDK för FPS) för offline-läge för FPS i \FairPlay Streaming Server SDK version 3.1 \ Development\Client\ HLSCatalog_With_FPS \HLSCatalog\. I HLSCatalog-exempel appen används följande kod för att implementera offline-läges funktioner:

    - AssetPersistenceManager. SWIFT-kod fil: AssetPersistenceManager är huvud klassen i det här exemplet som visar hur du:

        - Hantera nedladdning av HLS-strömmar, till exempel de API: er som används för att starta och avbryta hämtningar och ta bort befintliga till gångar från enheter.
        - Övervaka hämtnings förloppet.
    - AssetListTableViewController. SWIFT och AssetListTableViewCell. SWIFT-Koda filer: AssetListTableViewController är huvud gränssnittet i det här exemplet. Den innehåller en lista över till gångar som exemplet kan använda för att spela upp, Hämta, ta bort eller avbryta en nedladdning. 

De här stegen visar hur du konfigurerar en iOS-spelare som körs. Förutsatt att du börjar med HLSCatalog-exemplet i FPS Server SDK-version 4.0.1 gör du följande kod ändringar:

I HLSCatalog\Shared\Managers\ContentKeyDelegate.swift implementerar du metoden `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` med hjälp av följande kod. Låt "drmUr" vara en variabel som tilldelats HLS-URL: en.

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

Implementera metoden `requestApplicationCertificate()`i HLSCatalog\Shared\Managers\ContentKeyDelegate.swift. Den här implementeringen beror på om du bäddar in certifikatet (endast offentlig nyckel) med enheten eller är värd för certifikatet på webben. Följande implementering använder det värdbaserade program certifikatet som används i test exemplen. Låt "certUrl" vara en variabel som innehåller URL: en för applikations certifikatet.

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

För det slutliga integrerade testet finns både video-URL: en och URL: en för program certifikatet i avsnittet "integrerat test".

Lägg till din test video-URL i HLSCatalog\Shared\Resources\Streams.plist. För innehålls nyckel-ID: t använder du URL: en för FairPlay licens hämtning med SKD-protokollet som unikt värde.

![Offline-FairPlay iOS app-strömmar](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Använd din egen test video-URL, FairPlay License Acquisition URL och URL för program certifikat om du har konfigurerat dem. Eller också kan du fortsätta till nästa avsnitt, som innehåller test exempel.

## <a name="integrated-test"></a>Integrerat test

Tre test exempel i Media Services som tar upp följande tre scenarier:

* FPS-skyddad, med video, ljud och alternativt ljud spår
* FPS-skyddad, med video och ljud, men utan alternativ ljud spår
* FPS skyddas, med endast video och inget ljud

Du hittar dessa exempel på [den här demo webbplatsen](https://aka.ms/poc#22), med motsvarande program certifikat som finns i en Azure-webbapp.
Med version 3 eller version 4-exemplet på Server-SDK: n för FPS, om en Master-spelnings lista innehåller alternativ ljud, i offlineläge spelar den bara ljud. Därför måste du randig det alternativa ljudet. De andra och tredje exemplen som listas tidigare fungerar med andra ord i onlineläge och offline-läge. Exemplet som visas först spelar bara ljud i offlineläge, medan direkt uppspelningen fungerar som den ska.

## <a name="faq"></a>FAQ

Följande vanliga frågor ger hjälp med fel sökning:

- **Varför spelas bara ljud upp men inte video i offlineläge?** Det här beteendet verkar vara avsiktligt av exempel appen. När ett alternativt ljud spår finns (vilket är fallet för HLS) under offlineläge, är både iOS 10 och iOS 11 standardvärdet för det alternativa ljud spåret. För att kompensera det här beteendet för offline-läge för FPS tar du bort det alternativa ljud spåret från data strömmen. Om du vill göra detta på Media Services lägger du till det dynamiska manifest filtret "ljud-Only = false". Med andra ord slutar en HLS-URL med. ISM/manifest (format = M3U8-AAPL, endast ljud = falskt). 
- **Varför spelar det fortfarande bara ljud utan video i offlineläge efter att jag lagt till endast ljud = falskt?** Beroende på cache nyckel designen för Content Delivery Network (CDN) kan innehållet cachelagras. Rensa cacheminnet.
- **Stöds offline-läge för FPS även för iOS 11 förutom iOS 10?** Ja. Offline-läge för FPS stöds för iOS 10 och iOS 11.
- **Varför kan jag inte hitta dokumentet "offline-uppspelning med FairPlay streaming och HTTP Live Streaming" i serverns SDK för FPS?** Eftersom FPS Server SDK version 4 har det här dokumentet slagits samman i programmerings guiden "FairPlay streaming".
- **Vad är den nedladdade/offline-filstrukturen på iOS-enheter?** Den nedladdade fil strukturen på en iOS-enhet ser ut som på följande skärm bild. Mappen `_keys` lagrar hämtade FPS-licenser, med en lagrings fil för varje värd tjänst värd. Mappen `.movpkg` lagrar ljud-och video innehåll. Den första mappen med ett namn som slutar med ett streck följt av ett numeriskt objekt innehåller video innehåll. Det numeriska värdet är PeakBandwidth för video åter givningarna. Den andra mappen med ett namn som slutar med ett bindestreck följt av 0 innehåller ljud innehåll. Den tredje mappen med namnet "data" innehåller huvud spelnings listan för innehållet i FPS. Slutligen innehåller Boot. XML en fullständig beskrivning av innehållet i den `.movpkg` mappen. 

![FairPlay iOS-exempel fil struktur](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

En exempel-Boot. XML-fil:

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

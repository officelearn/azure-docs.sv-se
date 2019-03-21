---
title: Skydda HLS-innehåll med FairPlay offline Apple - Azure | Microsoft Docs
description: Det här avsnittet ger en översikt och visar hur du använder Azure Media Services för att kryptera dynamiskt HTTP Live Streaming (HLS) innehållet med Apple FairPlay i offline-läge.
services: media-services
keywords: HLS eller DRM, FairPlay Streaming (FPS), Offline, iOS 10
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
ms.openlocfilehash: 25bc7798853d350139a7802eaad68d52a1d7d99f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834820"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline FairPlay Streaming för iOS 

 Azure Media Services innehåller en uppsättning väl utformad [content protection services](https://azure.microsoft.com/services/media-services/content-protection/) som:

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128-kryptering

Digital rights management (DRM) / Standard AES (Advanced Encryption) kryptering av innehåll utförs dynamiskt på begäran för olika protokoll för direktuppspelning. DRM tillhandahålls-licens/AES dekryptering viktiga leverans också av Media Services.

Offline-läge för skyddat innehåll är också en funktion som begärs ofta utöver att skydda innehåll för online-direktuppspelning via olika protokoll för direktuppspelning. Stöd för offline-läge krävs för följande scenarier:

* Spela upp när internet-anslutning inte är tillgängliga, till exempel vid resa.
* Vissa innehållsleverantörer kan neka DRM-licensleverans utöver ett land kantlinje. Om användarna vill titta på innehållet under resor utanför landet krävs offline nedladdning.
* I vissa länder är internet tillgänglighet och/eller bandbredd fortfarande begränsad. Användare kan välja att hämta först om du vill kunna titta på innehållet i en lösning som är tillräckligt högt för en tillfredsställande tittarupplevelse. I det här fallet är problemet vanligtvis nätverkets tillgänglighet men begränsad nätverksbandbredd. Over-the-top (OTT) / plattform för onlinevideo (OVP)-providers begära stöd för offline-läge.

Den här artikeln innehåller stöd för FairPlay Streaming (FPS) offline-läge som riktar sig mot enheter som kör iOS 10 eller senare. Den här funktionen stöds inte för andra Apple-plattformar, till exempel watchOS, tvOS eller Safari på macOS.

## <a name="prerequisites"></a>Förutsättningar

Innan du implementerar offline DRM för FairPlay på en iOS 10 +-enhet:

* Granska online content protection för FairPlay: 

    - [Apple FairPlay licenskrav och konfiguration](fairplay-license-overview.md)
    - [Använda DRM dynamisk kryptering och licens video-on-demand](protect-with-drm.md)
    - Ett .NET-exempel som omfattar konfiguration av online FPS strömning: [ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* Hämta SDK: N FPS från Apple Developer Network. FPS SDK: N innehåller två komponenter:

    - SDK FPS-Server, som innehåller nyckeln Security-modul (KSM), klient-exempel, en specifikation och en uppsättning test vektorer.
    - Paketet FPS-distribution, som innehåller en D funktionen specifikation tillsammans med instruktioner om hur du skapar den FPS certifikat, kundspecifika privat nyckel och hemliga nyckeln. Apple utfärdar FPS Deployment Pack endast till licensierade innehållsleverantörer.
* Klona https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git. 

    Du behöver ändra koden i [kryptera med DRM med hjälp av .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) att lägga till FairPlay-konfigurationer.  

## <a name="configure-content-protection-in-azure-media-services"></a>Konfigurera innehållsskydd i Azure Media Services

I den [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metod, gör du följande:

Ta bort kommentarerna koden som konfigurerar alternativet FairPlay:

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

Dessutom ta bort kommentarerna koden som lägger till CBCS ContentKeyPolicyOption i listan över ContentKeyPolicyOptions

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

Att aktivera offline-läge, skapa en anpassad StreamingPolicy och använda dess namn när du skapar en StreamingLocator i [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563).
 
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

Media Services-kontot har nu konfigurerats för att leverera offline FairPlay-licenser.

## <a name="sample-ios-player"></a>Exempel på iOS Player

FPS stöd för offline-läge är bara tillgängliga på iOS 10 och senare. FPS Server SDK (version 3.0 eller senare) innehåller dokumentet och exempel för FPS offline-läge. Mer specifikt innehåller FPS Server SDK (version 3.0 eller senare) följande två objekt som är relaterade till offline-läge:

* Dokument: ”Offline uppspelning med FairPlay Streaming och HTTP-liveuppspelning”. Apple 14 September 2016. I FPS Server SDK-version 4.0, är det här dokumentet tillsammans huvudsakliga FPS dokumentet.
* Exempelkod: HLSCatalog exempel (del av Apple-FPS Server SDK) för FPS offlineläge i \FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. I exempelappen HLSCatalog för följande kodfiler att implementera funktioner för offline-läge:

    - AssetPersistenceManager.swift kodfilen: AssetPersistenceManager är huvudklass i det här exemplet visar hur du:

        - Hantera hämtar HLS strömmar, t.ex. API: er används för att starta och avbryta nedladdningar och ta bort befintliga resurser av enheter.
        - Övervaka hämtningsförloppet.
    - AssetListTableViewController.swift och AssetListTableViewCell.swift kodfiler: AssetListTableViewController är det viktigaste gränssnittet för det här exemplet. Den innehåller en lista över tillgångar exemplet kan använda för att spela upp, hämta, ta bort eller avbryta en hämtning. 

De här stegen visar hur du ställer in en som kör iOS-spelare. Anta att du startar från HLSCatalog exemplet i FPS Server SDK version 4.0.1 och gör följande ändringar i koden:

I HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementera metoden `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` med hjälp av följande kod. Låt ”drmUr” vara en variabel som tilldelats HLS-URL.

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

I HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementera metoden `requestApplicationCertificate()`. Den här implementeringen beror på om du bäddar in certifikatet (endast offentlig nyckel) med enheten eller vara värd för certifikatet på webben. Följande implementering använder värdbaserade programcertifikatet som används i test-exemplen. Låt ”certUrl” vara en variabel som innehåller URL: en för programcertifikatet.

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

För det avslutande integrera testet finns både video-URL och URL: en för programmet certifikat i avsnittet ”integrerad Test”.

I HLSCatalog\Shared\Resources\Streams.plist, lägger du till din video test-URL. För innehållet nyckeln ID, använda FairPlay licens-URL för anskaffning med skd-protokollet som det unika värdet.

![Offline FairPlay iOS App strömmar](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

Använd dina egna test video-URL, URL för anskaffning av FairPlay-licens och certifikat URL-adress, om du har de inte har några. Alternativt kan du fortsätta till nästa avsnitt som innehåller test-exempel.

## <a name="integrated-test"></a>Integrerad test

Tre test-exempel i Media Services omfattar följande tre scenarier:

* FPS som skyddas med video, ljud och alternativa ljudspår
* FPS som skyddas med video och ljud, men inga alternativa ljudspår
* FPS som skyddas med endast video och inga ljud

Du hittar de här exemplen på [demo platsen](https://aka.ms/poc#22), med motsvarande programcertifikatet finns i en Azure webbapp.
Med version 3 eller version 4 urval av FPS Server SDK om en master spelningslista innehåller alternativa ljud spelas under offlineläge ljud endast. Därför måste du ta bort alternativa ljudet. Med andra ord fungerar de andra och tredje exempel som visas tidigare i online och offline-läge. Exemplet som visas först spelar upp ljud under offline-läge, medan online för direktuppspelning fungerar korrekt.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Följande vanliga frågor och svar ge hjälp med felsökning:

- **Varför bara ljud spelas upp men inte video under offline-läge?** Det här beteendet verkar vara avsiktligt av exempelappen. När ett annat ljudspår finns (vilket är fallet för HLS) under offline-läge, både iOS 10 och alternativa ljudspåret iOS 11 som standard. Ta bort alternativa ljudspåret från dataströmmen för att kompensera FPS offlineläge detta beteende. Det gör du på Media Services genom att lägga till dynamiskt manifestfilter ”ljuddata = false”. Med andra ord slutar en HLS-URL med .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Varför den fortfarande spela upp ljudet utan video under offline-läge när jag har lagt till enbart ljud = false?** Beroende på content delivery network (CDN) cache viktiga utformning, kanske innehållet cachelagras. Rensa cacheminnet.
- **FPS offline-läge stöds även på iOS 11 förutom iOS 10?** Ja. FPS offline-läge stöds för iOS 10 och iOS 11.
- **Varför hittar inte dokumentet ”Offline uppspelning med FairPlay Streaming och HTTP Live Streaming” i FPS Server SDK?** Sedan FPS Server SDK version 4, skulle det här dokumentet samman i ”FairPlay Streaming Programming Guide”.
- **Vad är den hämtade/offline filstrukturen på iOS-enheter?** Den hämta filen strukturen på en iOS-enhet som ser ut som följande skärmbild. Den `_keys` mappen hämtade FPS licenser med en store-fil för varje licens tjänstevärd. Den `.movpkg` mappen lagras ljud-och videoinnehåll. Den första mappen med ett namn som slutar med ett bindestreck följt av ett numeriskt innehåller videoinnehåll. Det numeriska värdet är PeakBandwidth video renderingar. Den andra mappen med ett namn som slutar med ett bindestreck följt av 0 innehåller ljudinnehåll. Den tredje mapp med namnet ”Data” innehåller master spelningslistan FPS innehåll. Slutligen boot.xml innehåller en fullständig beskrivning av den `.movpkg` innehållet i mappen. 

![Exempel på offline FairPlay iOS app filstruktur](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

En exempelfil boot.xml:

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

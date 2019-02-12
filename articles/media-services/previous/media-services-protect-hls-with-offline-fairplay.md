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
ms.date: 02/10/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: 710ec72e9867ad180afcae8273a093f48933112a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991522"
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

## <a name="preliminary-steps"></a>Preliminära steg
Innan du implementerar offline DRM för FairPlay på en iOS 10 +-enhet:

* Bekanta dig med online content protection för FairPlay. Mer information finns i följande artiklar och -exempel:

    - [Apple FairPlay Streaming för Azure Media Services är allmänt tillgänglig](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Skydda ditt innehåll med Apple FairPlay eller Microsoft PlayReady för HLS](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Ett exempel för online FPS för direktuppspelning](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Hämta SDK: N FPS från Apple Developer Network. FPS SDK: N innehåller två komponenter:

    - SDK FPS-Server, som innehåller nyckeln Security-modul (KSM), klient-exempel, en specifikation och en uppsättning test vektorer.
    - Paketet FPS-distribution, som innehåller en D funktionen specifikation tillsammans med instruktioner om hur du skapar den FPS certifikat, kundspecifika privat nyckel och hemliga nyckeln. Apple utfärdar FPS Deployment Pack endast till licensierade innehållsleverantörer.

## <a name="configuration-in-media-services"></a>Konfigurationen i Media Services
För FPS offlineläge konfiguration via den [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices)använder Media Services .NET SDK version 4.0.0.4 eller senare, som tillhandahåller den nödvändiga API för att konfigurera FPS offline-läge.
Du måste också arbeta koden för att konfigurera innehållsskydd i onlineläge FPS. När du har fått koden för att konfigurera innehållsskydd i online-läge för FPS, behöver du bara följande två ändringar.

## <a name="code-change-in-the-fairplay-configuration"></a>Ändringar i koden i FairPlay-konfiguration
Den första ändringen är att definiera en ”aktivera offline-läge” Boolean, kallas objDRMSettings.EnableOfflineMode som är sant när den gör offline DRM-scenariot. Beroende på indikatorn, kan du göra följande ändring i FairPlay-konfigurationen:

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Koden ändras i konfigurationen för leveransprincipen tillgången
Den andra ändringen är att lägga till den tredje nyckeln i ordlistan < AssetDeliveryPolicyConfigurationKey sträng >.
Lägg till AssetDeliveryPolicyConfigurationKey som visas här:
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

Efter det här steget innehåller < Dictionary_AssetDeliveryPolicyConfigurationKey > strängen i FPS-tillgångsleveransprincip följande tre poster:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl eller AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, beroende på faktorer som FPS KSM/nyckel-server som används och om du återanvända samma tillgångsleverans princip över flera resurser
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Media Services-kontot har nu konfigurerats för att leverera offline FairPlay-licenser.

## <a name="sample-ios-player"></a>Exempel på iOS Player
FPS stöd för offline-läge är bara tillgängliga på iOS 10 och senare. FPS Server SDK (version 3.0 eller senare) innehåller dokumentet och exempel för FPS offline-läge. Mer specifikt innehåller FPS Server SDK (version 3.0 eller senare) följande två objekt som är relaterade till offline-läge:

* Dokument: ”Offline uppspelning med FairPlay Streaming och HTTP-liveuppspelning”. Apple 14 September 2016. I FPS Server SDK-version 4.0, är det här dokumentet tillsammans huvudsakliga FPS dokumentet.
* Exempelkod: HLSCatalog-exemplet för FPS offlineläge i \FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. I exempelappen HLSCatalog för följande kodfiler att implementera funktioner för offline-läge:

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

![Offline FairPlay iOS App strömmar](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

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
- **Vad den sista parametern står för i följande API: et för FPS offline-läge?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Dokumentation för detta API finns i [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration metoden](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Parametern representerar passets offline hyra, med timme som enheten.
- **Vad är den hämtade/offline filstrukturen på iOS-enheter?** Den hämta filen strukturen på en iOS-enhet som ser ut som följande skärmbild. Den `_keys` mappen hämtade FPS licenser med en store-fil för varje licens tjänstevärd. Den `.movpkg` mappen lagras ljud-och videoinnehåll. Den första mappen med ett namn som slutar med ett bindestreck följt av ett numeriskt innehåller videoinnehåll. Det numeriska värdet är PeakBandwidth video renderingar. Den andra mappen med ett namn som slutar med ett bindestreck följt av 0 innehåller ljudinnehåll. Den tredje mapp med namnet ”Data” innehåller master spelningslistan FPS innehåll. Slutligen boot.xml innehåller en fullständig beskrivning av den `.movpkg` innehållet i mappen. 

![Exempel på offline FairPlay iOS app filstruktur](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

En exempelfil boot.xml:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
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

## <a name="summary"></a>Sammanfattning
Det här dokumentet innehåller följande steg och information som du kan använda för att implementera FPS offline-läge:

* Media Services innehållsskydd konfiguration via Media Services .NET-API konfigurerar dynamisk FairPlay-kryptering och FairPlay-licensleverans i Media Services.
* En spelare för iOS som baseras på exemplet från FPS Server SDK ställer in en iOS-spelare som kan spela upp FPS innehåll i onlineläge för direktuppspelning eller en offline-läge.
* Exemplet FPS videor används för att testa offline-läge och online för direktuppspelning.
* En vanliga frågor och svar får du svar på frågor om FPS offline-läge.

---
title: Skydda innehåll med offline Apple FairPlay - Azure HLS | Microsoft Docs
description: Det här avsnittet ger en översikt och visar hur du använder Azure Media Services du krypterar dynamiskt med Apple FairPlay HTTP Live Streaming (HLS) innehållet i offline-läge.
services: media-services
keywords: HLS DRM, FairPlay Streaming (FPS), Offline, iOS 10
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
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: dc38772097dddb7c7135d55598373d7ab544f9ea
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790393"
---
# <a name="offline-fairplay-streaming-for-ios"></a>Offline FairPlay strömning för iOS 
 Azure Media Services innehåller en uppsättning väl utformad [innehåll skyddstjänsterna](https://azure.microsoft.com/services/media-services/content-protection/) som omfattar:

- Microsoft PlayReady
- Google Widevines
- Apple FairPlay
- AES-128-kryptering

Digital rights management (DRM) / Advanced Encryption Standard (AES) kryptering av innehåll utförs dynamiskt på begäran för olika protokoll för dataströmmar. DRM tillhandahålls-licens/AES dekryptering viktiga leverans också av Media Services.

Förutom att skydda innehåll för online strömning via olika protokoll för liveströmning är offline-läge för skyddat innehåll också en funktion som begärs ofta. Stöd för offline-läge krävs för följande scenarier:

* Uppspelning när internet-anslutning inte är tillgänglig, som under resor.
* Vissa innehållsleverantörer kan inte tillåta DRM-licensleverans utanför ett land kantlinje. Om användaren vill titta på innehållet resande utanför landet krävs offline hämtning.
* I vissa länder är internet tillgänglighet och/eller bandbredd fortfarande begränsad. Användare kan välja att ladda först om du vill titta på innehållet i en lösning som är tillräckligt högt för tillfredsställande visning. I det här fallet är problemet vanligtvis nätverkets tillgänglighet men begränsad nätverksbandbredd. Över-the-upp (OTT) / video online-plattformen (OVP) providers begär stöd för offline-läge.

Den här artikeln beskriver FairPlay Streaming (FPS) stöd för offline-läge som riktar sig till enheter som kör iOS 10 eller senare. Den här funktionen stöds inte för andra Apple plattformar, till exempel watchOS, tvOS eller Safari macOS.

## <a name="preliminary-steps"></a>Preliminära steg
Innan du implementerar offline DRM för FairPlay på en iOS 10 + enhet:

* Bekanta dig med innehåll onlineskydd för FairPlay. Mer information finns i följande artiklar och exempel:

    - [Apple FairPlay strömning för Azure Media Services är allmänt tillgängligt](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [Skydda ditt innehåll med Apple FairPlay eller Microsoft PlayReady HLS](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [Ett exempel för online FPS strömning](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* Hämta SDK FPS från Apple Developer Network. FPS SDK innehåller två komponenter:

    - SDK FPS-Server, som innehåller nyckeln Security Module (KSM), klient-exempel, en specifikation och en uppsättning test angreppsmetoderna.
    - Pack FPS-distribution, som innehåller specifikationen D funktionen tillsammans med instruktioner om hur du skapar FPS certifikat, kundspecifika privat nyckel och hemlig nyckel för programmet. Apple utfärdar FPS Deployment Pack endast för licensierade innehållsleverantörer.

## <a name="configuration-in-media-services"></a>Konfigurationen i Media Services
För FPS offlineläge konfiguration via den [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices)använder Media Services .NET SDK version 4.0.0.4 eller senare, som innehåller nödvändiga API för att konfigurera FPS offline-läge.
Du måste också fungerande-kod för att konfigurera online-läge FPS innehållsskydd. När du har fått koden för att konfigurera online-läge innehållsskydd för FPS måste bara följande två ändringarna.

## <a name="code-change-in-the-fairplay-configuration"></a>Koden ändring i FairPlay-konfiguration
Den första ändringen är att definiera ”aktivera offlineläge” Boolean, kallas objDRMSettings.EnableOfflineMode som är sant när den gör det offline DRM-scenariot. Beroende på denna indikator gör du följande ändring i FairPlay-konfigurationen:

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

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>Koden ändras i konfigurationen för leveransprincipen tillgångsinformation
Andra ändringen är att lägga till tredje nyckel i uppslagslistan < AssetDeliveryPolicyConfigurationKey sträng >.
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

Efter det här steget innehåller < Dictionary_AssetDeliveryPolicyConfigurationKey > strängen i FPS tillgångsleveransprincip följande tre poster:

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl eller AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, beroende på faktorer som FPS KSM/key-server som används och om du vill återanvända samma tillgång leverans webbplatsprincip flera tillgångar
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Media Services-konto har nu konfigurerats för att leverera offline FairPlay-licenser.

## <a name="sample-ios-player"></a>Exempel på iOS Player
Stöd för FPS offline-läge är bara tillgängliga på iOS 10 och senare. FPS Server SDK (version 3.0 eller senare) innehåller dokument och exempel för FPS offline-läge. Mer specifikt innehåller FPS Server SDK (version 3.0 eller senare) följande två objekt som är relaterade till offline-läge:

* Dokument: ”Offline uppspelning med FairPlay strömning och HTTP Live Streaming”. Apple 14 September 2016. Det här dokumentet slås samman med huvudsakliga FPS dokumentet i FPS Server SDK version 4.0.
* Exempelkod: HLSCatalog exempel för FPS offlineläge i \FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. I exempelapp HLSCatalog används följande kodfiler för att implementera funktioner i offline-läge:

    - AssetPersistenceManager.swift kodfilen: AssetPersistenceManager är den viktigaste klassen i det här exemplet visar hur du:

        - Hantera hämtar HLS dataströmmar som API: erna för att starta och avbryta hämtningar och ta bort befintliga tillgångar av enheter.
        - Övervaka hämtningsförloppet.
    - AssetListTableViewController.swift och AssetListTableViewCell.swift Platskod filer: AssetListTableViewController är det huvudsakliga gränssnittet för det här exemplet. Den innehåller en lista över tillgångar provet kan använda för att spela upp, hämta, ta bort eller avbryta hämtningen. 

Dessa steg visar hur du ställer in en iOS-spelare som körs. Under förutsättning att du startar från HLSCatalog provet i FPS Server SDK version 4.0.1, ändra enligt följande kod:

I HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementerar du metoden `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` med hjälp av följande kod. Låt ”drmUr” vara en variabel som tilldelats HLS-URL.

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

I HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementerar du metoden `requestApplicationCertificate()`. Den här implementeringen beror på om du bädda in certifikat (endast offentlig nyckel) med enheten eller värden certifikatet på webben. Följande implementeringen använder värdbaserade programcertifikatet som används i exemplen test. Låt ”certUrl” vara en variabel som innehåller URL: en för programmet certifikatet.

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

För det avslutande integrera testet både video-URL och URL: en för programmet certifikat tillhandahålls i avsnittet ”integrerad Test”.

HLSCatalog\Shared\Resources\Streams.plist, lägga till din video test-URL. För innehållet nyckeln ID, använda FairPlay licens förvärv URL med skd-protokollet som det unika värdet.

![Offline FairPlay iOS App strömmar](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

Använda egna test video-URL, FairPlay URL för anskaffning av licens och certifikat URL-adress, om du har dem konfigurera. Eller du kan fortsätta till nästa avsnitt som innehåller test prover.

## <a name="integrated-test"></a>Integrerad test
Tre test prover i Media Services omfattar följande tre fall:

* FPS skyddas med video, ljud och alternativa ljud spåra
* FPS skyddas med video och ljud, men inga alternativa ljud spåra
* FPS skyddas med endast video och inget ljud

Du hittar exemplen på [demo platsen](http://aka.ms/poc#22), med motsvarande program certifikat finns i en Azure-webbapp.
Med version 3 eller version 4 exempel på Server-SDK FPS om en master spelningslista innehåller alternativa ljud spelas under offlineläge ljud endast. Du måste därför remsans alternativa ljud. Med andra ord fungerar andra och tredje exemplen som visas tidigare i online och offline-läge. Exemplet som visas först spelas upp ljud endast under offline-läge, medan online strömning fungerar korrekt.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
Följande vanliga frågor och ge hjälp med felsökning:

- **Varför bara ljud spelar men inte video under offlineläge?** Det här problemet verkar vara avsiktligt av sample-appen. När är ett alternativ ljud spår finns (vilket är fallet för HLS) under offlineläge iOS 10- och iOS 11 standard till alternativa ljud spår. För att kompensera FPS offlineläge problemet tar du bort alternativa ljud spåra från dataströmmen. Gör detta på Media Services genom att lägga till dynamisk manifestet filtret ”ljuddata = false”. Med andra ord slutar en URL för HLS med .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Varför den fortfarande spela upp ljud utan att videon under offline-läge när du har lagt till ljuddata = false?** Beroende på content delivery network (CDN) cache key utformning, kanske innehållet cachelagras. Rensa cachen.
- **Stöds FPS offlineläge även på iOS 11 förutom iOS 10?** Ja. FPS offline-läge stöds för iOS 10 och iOS 11.
- **Varför kan jag inte hitta dokumentet ”Offline uppspelning med FairPlay strömning och HTTP Live Streaming” i FPS Server SDK?** Sedan FPS Server SDK version 4, skulle det här dokumentet samman i den ”FairPlay Streaming Programmeringsguide”.
- **Vad den sista parametern står för följande API för FPS offlineläge?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    Läs i dokumentationen för detta API [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration metoden](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Parametern representerar varaktighet offline uthyrning med timme som enhet.
- **Vad är hämtade eller är offline filstrukturen på iOS-enheter?** Det ser ut som följande skärmbild den hämta filen strukturen på en iOS-enhet. Den `_keys` mappen lagrar hämtas FPS licenser, med en fil för varje licens tjänstvärden. Den `.movpkg` mappen lagras ljud och video. Den första mappen med ett namn som slutar med ett streck följt av ett numeriskt innehåller videoinnehåll. Det numeriska värdet är PeakBandwidth video återgivningar. Den andra mappen med ett namn som slutar med ett streck följt av 0 innehåller ljudinnehåll. Den tredje mapp med namnet ”Data” innehåller master spelningslista FPS innehåll. Slutligen boot.xml innehåller en fullständig beskrivning av den `.movpkg` innehållet i mappen. 

![Exempel på offline FairPlay iOS app filstruktur](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

Boot.xml exempelfil:
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

* Media Services innehållsskydd konfigurationen via Media Services .NET API konfigurerar dynamisk FairPlay kryptering och FairPlay licensleverans i Media Services.
* En iOS-spelare utifrån urval av Server-SDK FPS ställer in en iOS-spelare som kan spela upp FPS innehåll i onlineläge strömmande eller offline-läge.
* Exempel FPS videor för att testa offline-läge och online strömning.
* Vanliga frågor svar på frågor om FPS offline-läge.

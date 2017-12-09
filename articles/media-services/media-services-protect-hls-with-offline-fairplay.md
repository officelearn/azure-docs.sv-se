---
title: "Skydda innehåll med offline Apple FairPlay - Azure HLS | Microsoft Docs"
description: "Det här avsnittet ger en översikt och visar hur du använder Azure Media Services du krypterar dynamiskt med Apple FairPlay HTTP Live Streaming (HLS) innehållet i offline-läge."
services: media-services
keywords: HLS DRM, FairPlay Streaming (FPS), Offline, iOS 10
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: bf5828ecd6b6bd2e862c4d7709014ecac47c6be0
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="offline-fairplay-streaming"></a>Offline FairPlay strömning
Microsoft Azure Media Services tillhandahåller en uppsättning väl utformad [innehåll skyddstjänsterna](https://azure.microsoft.com/services/media-services/content-protection/), ytbeläggning:
- Microsoft PlayReady
- Google Widevines
- Apple FairPlay
- AES-128-kryptering

DRM/AES-kryptering av innehåll utförs dynamiskt på begäran för olika protokoll för dataströmmar. DRM tillhandahålls-licens/AES dekryptering viktiga leverans också av Azure Media Services.

Förutom att skydda innehåll för online strömning via olika protokoll för liveströmning är offline-läge för skyddat innehåll också en funktion som begärs ofta. Support för frånkopplat läge krävs för följande scenarier:
1. Spela upp när Internet-anslutning inte är tillgänglig som under resa;
2. Vissa innehållsleverantörer får inte tillåta DRM-licensleverans utanför ett land kantlinje. Om en användare vill titta på innehållet resande utomlands behövs offline download.
3. I vissa länder är Internet tillgänglighet och/eller bandbredd fortfarande begränsad. Användare kan välja att ladda först om du vill titta på innehållet i tillräckligt hög upplösning för tillfredsställande bild. I det här fallet oftare, problemet är inte nätverkets tillgänglighet, i stället den nätverksbandbredden är begränsad. OTT/OVP providers ber för support för frånkopplat läge.

Den här artikeln beskriver FairPlay Streaming (FPS) offlineläge stöd målobjekt för enheter som kör iOS 10 eller senare. Den här funktionen stöds inte för andra Apple-plattformar, till exempel watchOS, tvOS eller Safari på macOS.

## <a name="preliminary-steps"></a>Preliminära steg
Innan du implementerar offline DRM för FairPlay på en iOS 10 +-enhet, bör du först:
1. Bekanta dig med innehåll onlineskydd för FairPlay. Detta beskrivs i detalj i följande artiklar/prov:
- [Apple FairPlay strömning för Azure Media Services allmänt tillgänglig](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
- [Skydda ditt innehåll med Apple FairPlay eller Microsoft PlayReady HLS](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
- [Ett exempel för online FPS strömning](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)
2. Hämta SDK FPS från Apple Developer Network. FPS SDK innehåller två komponenter:
- FPS Server SDK, som innehåller KSM (nyckeln Security Module), klient-exempel, en specifikation och en uppsättning test angreppsmetoderna;
- FPS distribution Pack, som innehåller funktionen D specifikation tillsammans med anvisningar om hur du skapar FPS certifikat, kundspecifika privat nyckel och programmet hemlig nyckel (be). Apple utfärdar FPS Deployment Pack endast för licensierade innehållsleverantörer.

## <a name="configuration-in-azure-media-services"></a>Konfigurationen i Azure Media Services
För FPS offlineläge konfiguration via [Azure Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices), måste du använda Azure Media Services .NET SDK-v. 4.0.0.4 eller senare, vilket angivna nödvändiga API för att konfigurera FPS offline-läge.
Som anges i antaganden ovan förutsätter vi att du har fungerande kod för att konfigurera onlineläge FPS innehållsskydd. När du har koden för att konfigurera onlineläge innehållsskydd för FPS behöver du bara följande två ändringar.

## <a name="code-change-in-fairplay-configuration"></a>Ändra koden i FairPlay konfiguration
Definiera en ”aktivera offlineläge” booleskt, kallat objDRMSettings.EnableOfflineMode som gäller när du aktiverar offline DRM-scenariot. Beroende på denna indikator göra vi följande ändring FairPlay-konfiguration:

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

## <a name="code-change-in-asset-delivery-policy-configuration"></a>Koden ändras i tillgångens konfigurationen för Leveransprincipen
Andra ändringen är att lägga till tredje nyckel i uppslagslistan ordlistan < AssetDeliveryPolicyConfigurationKey sträng >.
De tredje AssetDeliveryPolicyConfigurationKey måste läggas till är enligt nedan: 
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

Efter det här steget innehåller ordlista < AssetDeliveryPolicyConfigurationKey sträng > i FPS tillgångsleveransprincip följande tre poster:
1. AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl eller AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl beroende på faktorer som FPS KSM/key-server som används och om vi vill återanvända samma tillgång leverans Webbplatsprincip flera tillgångar
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

Media services-konto är nu konfigurerad för att leverera offline FairPlay-licenser.

## <a name="sample-ios-player"></a>Exempel på iOS Player
Först ska vi Observera att FPS offlineläge stöd är bara tillgängliga på iOS 10 och senare. Vi ska få FPS Server SDK (version 3.0 eller senare) som innehåller dokument och exempel för FPS offline-läge. Mer specifikt FPS Server SDK (version 3.0 eller senare) innehåller följande två objekt som är relaterade till offline-läge:
1. Dokument: Offline uppspelning med FairPlay strömning och HTTP Live Streaming. Apple 2016-14-9. Det här dokumentet har slagits samman med den huvudsakliga FPS strömmande doc i FPS Server SDK v 4.0.
2. Exempelkod: HLSCatalog exempel för FPS offlineläge i \FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\. Följande kodfiler är särskilt för att implementera funktioner för offline-läge i exempelapp HLSCatalog:
- AssetPersistenceManager.swift kodfilen: AssetPersistenceManager är den viktigaste klassen i det här exemplet visar
    - Så här hanterar du hämtar HLS strömmarna API: er för att starta och avbryter hämta, ta bort befintliga tillgångar av användarens enhet.
    - Så här övervakar du nedladdningen.
- AssetListTableViewController.swift och AssetListTableViewCell.swift Platskod filer: AssetListTableViewController är det huvudsakliga gränssnittet för det här exemplet. Den innehåller en lista över tillgångar provet kan spela upp, hämta, ta bort eller avbryta hämtningen. 

Nedan följer detaljerade anvisningar för att konfigurera en iOS-spelare som körs. Antar vi att du startar från HLSCatalog exemplet i FPS Server SDK v 4.0.1.  Vi behöver ändra följande kod:

I HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementerar du metoden `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)` med följande kod: låta drmUr vara en variabel som tilldelats HLS strömnings-URL.

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

I HLSCatalog\Shared\Managers\ContentKeyDelegate.swift, implementerar du metoden `requestApplicationCertificate()`. Den här implementeringen beror på om du bädda in certifikat (endast offentlig nyckel) med enheten eller värden certifikatet på webben. Nedan visas en implementering med värdbaserade programcertifikatet som används i våra exempel test. Kan vara en variabel som innehåller URL-Adressen till programcertifikatet certUrl.

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

För slutlig integrerad test anges både video Webbadressen och programmet certifikat i avsnittet ”integrerad Test”.

Lägg till din video test-URL i HLSCatalog\Shared\Resources\Streams.plist, och för innehållsnyckeln ID, vi kan bara använda FairPlay URL för anskaffning av licens med skd-protokollet som det unika värdet.

![Offline FairPlay iOS App strömmar](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

För testet video-URL, FairPlay URL för anskaffning av licens och URL för certifikat för programmet, du kan använda din egen, om du har dem konfigurera eller du kan fortsätta till nästa avsnitt som innehåller test prover.

## <a name="integrated-test"></a>Integrerad Test
Tre prover har ställts in i Azure Media Services som omfattar följande tre fall:
1.  FPS skyddas med video, ljud och alternativa ljud spåra;
2.  FPS skyddas med video, ljud, men inga alternativa ljud spåra;
3.  FPS skyddas med endast, video inget ljud.

De här exemplen finns på den här [demonstrationswebbplats](http://aka.ms/poc#22), med motsvarande program certifikat finns i en Azure-webbapp.
Vi har upptäckt att med v3 eller v4 prov av FPS Server SDK, om en master spelningslista innehåller alternativa ljud under offline-läge, spelas upp ljud endast. Vi behöver därför remsans alternativa ljud. Med andra ord bland tre exemplet ovan, (2) och (3) fungerar i både online och offline-läge. Men (1) att spela upp ljudet endast under offlineläge när online strömmande fungerar bra.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
Några och vanliga frågor för felsökning:
- **Vad har endast ljud play men ingen bild under offlineläge?** Det här problemet verkar vara avsiktligt av sample-appen. När alternativ ljud spåra är presentera (vilket är fallet för HLS), under offline-läge, både iOS 10 och iOS 11 som standard till alternativa ljud spår. Om du vill kompensera FPS offlineläge problemet som vi behöver ta bort alternativ ljud spår från dataströmmen. Om du vill göra detta på Azure Media Services-sida, vi bara lägga till dynamiska manifestet filtret ”ljuddata = false”. Med andra ord skulle en HLS-URL avslutas med .ism/manifest(format=m3u8-aapl,audio-only=false). 
- **Varför den fortfarande spela upp ljud utan att videon under offline-läge när jag lägger till ljuddata = false?** Beroende på CDN cache viktiga designbeslut kan innehållet cachelagras. Du måste rensa cachen.
- **Stöds FPS offlineläge även på iOS 11 förutom iOS 10?** Ja, FPS offline-läge stöds för både iOS 10 och iOS 11.
- **Varför hittar inte dokumentet Offline uppspelning med FairPlay strömning och HTTP Live Streaming i FPS Server SDK?** Det här dokumentet har slagits samman med FairPlay strömning Programmeringsguide dokument eftersom FPS Server SDK version 4.
- **Vad den sista parametern står för följande API för FPS offlineläge?**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

Dokumentation för detta API finns [här](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet). Parametern representerar varaktighet offline uthyrning med timme som enhet.
- **Vad är hämtade eller är offline filstrukturen på iOS-enheter?** Den hämta filen strukturen på en iOS-enhet ser ut som nedan (skärmbild). `_keys`mappen lagrar ned FPS licenser, en fil för varje licens tjänstvärden. `.movpkg`mappen lagras ljud och video. Den första mappen med namn som slutar med ett streck följt av ett numeriskt innehåller videoinnehåll. Det numeriska värdet är ”PeakBandwidth” video återgivningar. Den andra mappen med namn som slutar med ett streck följt av 0 innehåller ljudinnehåll. Den tredje mapp med namnet ”Data” innehåller master spelningslista FPS innehåll. Boot.XML ger en fullständig beskrivning av `.movpkg` innehållet i mappen (se nedan för en exempelfil boot.xml).

![Offline FairPlay iOS exempel App filstruktur](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

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
I det här dokumentet har vi tillhandahållit detaljerade anvisningar och information för att implementera FPS offline-läge, inklusive:
1. Azure Media Services innehållsskydd konfiguration via AMS .NET-API. Detta konfigurerar dynamisk FairPlay kryptering och FairPlay licensleverans i AMS.
2. iOS player utifrån urval av Apple FPS Server SDK. Detta skapar en iOS-spelare som kan spela upp FPS innehåll i onlineläge strömmande eller offline-läge.
3. Exempel FPS videor för att testa offline-läge och online strömning.
4. Vanliga frågor om FPS offline-läge.

---
title: Konfigurera tillgångsleveransprinciper med .NET SDK | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar olika tillgångsleveransprinciper med Azure Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b5e733c93fef8920c73c8cf460dac7a7051fddb5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465617"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurera tillgångsleveransprinciper med .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Översikt
Om du planerar att leverans krypterad tillgångar ett av stegen i arbetsflödet för Media Services-leverans av innehåll konfigurera leveransprinciper för tillgångar. Talar tillgångsleveransprincip om Media Services för hur du vill för tillgången som ska levereras: i vilket strömningsprotokoll bör din tillgång dynamiskt paketeras (till exempel, MPEG DASH, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera dynamiskt din tillgång och hur (kuvert eller gemensam kryptering).

Den här artikeln beskrivs varför och hur du skapar och konfigurerar principerna för tillgångsleverans.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
>
>Om du vill kunna använda dynamisk paketering och dynamisk kryptering måste dessutom din tillgång innehålla en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer.

Du kan tillämpa olika principer för samma tillgång. Du kan till exempel tillämpa PlayReady-kryptering för att kryptering för Smooth Streaming och AES Envelope för MPEG-DASH och HLS. Alla protokoll som inte har definierats i en leveransprincip (exempelvis kan du lägga till en enskild princip som endast anger HLS som protokoll) kommer att blockeras från strömning. Ett undantag är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll fritt.

Om du vill att leverera en krypterad tillgång för lagring, måste du konfigurera den tillgångsleveransprincip. Innan du kan strömma din tillgång, kommer servern för strömning tar bort lagringskryptering och strömmar ditt innehåll med den angivna principen. Till exempel för att leverera din tillgång som krypterats med Advanced Encryption Standard (AES) kuvert krypteringsnyckeln, anger du principtypen **DynamicEnvelopeEncryption**. Om du vill ta bort lagringskryptering och strömma tillgången i klartext, anger du principtypen **NoDynamicEncryption**. Exempel som visar hur du konfigurerar dessa principtyper följer.

Beroende på hur du konfigurerar tillgångsleveransprincip du dynamiskt paketera, kryptera och strömma följande strömningsprotokoll: Smooth Streaming, HLS och MPEG DASH.

I följande lista visas format som du använder för att strömma Smooth, HLS och DASH.

Smooth Streaming:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

HLS:

{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Överväganden
* Innan du tar bort AssetDeliveryPolicy, bör du ta bort alla positionerare för direktuppspelning som är associerade med tillgången. Du kan skapa nya strömningslokaliserare senare om du vill med en ny AssetDeliveryPolicy.
* Att går inte skapa en strömningslokaliserare på en krypterad lagring tillgång när någon tillgångsleveransprincip anges.  Om tillgången är inte krypterad lagring, kan systemet du skapa en positionerare och strömma tillgången i klartext utan en tillgångsleveransprincip.
* Du kan ha flera tillgångsleveransprinciper som är associerade med en enda resurs, men du kan bara ange ett sätt att hantera en viss AssetDeliveryProtocol.  Vilket innebär att om du försöker länka två leveransprinciper som anger det AssetDeliveryProtocol.SmoothStreaming protokoll som resulterar i ett fel eftersom systemet inte vet vilket du vill att det ska tillämpas när en klient gör en begäran om Smooth Streaming.
* Om du har en tillgång med en befintlig strömningslokaliserare, du kan inte länka en ny princip till tillgången (du kan antingen ta bort länk till en befintlig princip från tillgången eller uppdatera en leveransprincip som är associerade med tillgången).  Du måste först ta bort strömningslokaliseraren, justera principerna och sedan återskapa strömningslokaliseraren.  Du kan använda samma locatorId när du återskapa strömningslokaliseraren men bör du kontrollera den orsakar problem för klienter eftersom innehåll cachelagras av ursprunget eller en underordnad CDN.

## <a name="clear-asset-delivery-policy"></a>Rensa tillgångsleveransprincip

Följande **ConfigureClearAssetDeliveryPolicy** metoden anger ska inte använda dynamisk kryptering och leverera strömmen i någon av följande protokoll:  MPEG DASH, HLS och Smooth Streaming-protokoll. Du kanske vill tillämpa den här principen till din lagring krypteras tillgångar.

Information om vilka värden som du kan ange när du skapar en AssetDeliveryPolicy finns i den [används när du definierar AssetDeliveryPolicy](#types) avsnittet.

```csharp
    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
        _context.AssetDeliveryPolicies.Create("Clear Policy",
        AssetDeliveryPolicyType.NoDynamicEncryption,
        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);
        
        asset.DeliveryPolicies.Add(policy);
    }
```
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption tillgångsleveransprincip

Följande **CreateAssetDeliveryPolicy** metoden skapar den **AssetDeliveryPolicy** som är konfigurerad för att använda dynamiska gemensam kryptering (**DynamicCommonEncryption**) till en smidig strömningsprotokoll (andra protokoll kommer att blockeras från strömning). Metoden tar två parametrar: **Tillgången** (den tillgång som du vill tillämpa leveransprincipen) och **IContentKey** (innehållsnyckeln av den **CommonEncryption** typ, mer information finns i: [Skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md#common_contentkey)).

Information om vilka värden som du kan ange när du skapar en AssetDeliveryPolicy finns i den [används när du definierar AssetDeliveryPolicy](#types) avsnittet.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            };
    
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                    "AssetDeliveryPolicy",
                AssetDeliveryPolicyType.DynamicCommonEncryption,
                AssetDeliveryProtocol.SmoothStreaming,
                assetDeliveryPolicyConfiguration);
    
            // Add AssetDelivery Policy to the asset
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
    
            Console.WriteLine();
            Console.WriteLine("Adding Asset Delivery Policy: " +
                assetDeliveryPolicy.AssetDeliveryPolicyType);
     }
```

Azure Media Services kan du lägga till Widevine-kryptering. I följande exempel visar både PlayReady och Widevine som läggs till i principen för tillgångsleverans.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamic Encryption 
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
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineUrl.ToString()}

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }
```
> [!NOTE]
> När du krypterar med Widevine, skulle du bara att kunna leverera med bindestreck. Se till att ange DASH i protokollet tillgångsleverans.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption tillgångsleveransprincip
Följande **CreateAssetDeliveryPolicy** metoden skapar den **AssetDeliveryPolicy** som är konfigurerad för att använda dynamiska kryptering (**DynamicEnvelopeEncryption**) till Smooth Streaming, HLS och DASH-protokoll (om du väljer att inte ange vissa protokoll, de kommer att blockeras från strömning). Metoden tar två parametrar: **Tillgången** (den tillgång som du vill tillämpa leveransprincipen) och **IContentKey** (innehållsnyckeln av den **EnvelopeEncryption** typ, mer information finns i: [Skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Information om vilka värden som du kan ange när du skapar en AssetDeliveryPolicy finns i den [används när du definierar AssetDeliveryPolicy](#types) avsnittet.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamic Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }
```

## <a id="types"></a>Används när du definierar AssetDeliveryPolicy

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

Följande enum beskriver värden som du kan ange för protokollet tillgångsleverans.

```csharp
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        ProgressiveDownload = 0x10, 
 
        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }
```
### <a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

Följande enum beskriver värden som du kan ange för typen av tillgången leverans.  
```csharp
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
        }
```
### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

Följande enum beskriver värden som du kan använda för att konfigurera leveransmetod för innehållsnyckeln till klienten.
  ```csharp  
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquisition protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquisition protocol
        ///
        </summary>
        Widevine = 3

    }
```
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

Följande enum beskriver värden som du kan ange för att konfigurera nycklar som används för att få specifik konfiguration för en tillgångsleveransprincip.
```csharp
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,

        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }
```
## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


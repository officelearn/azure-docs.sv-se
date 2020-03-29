---
title: Konfigurera principer för tillgångsleverans med .NET SDK | Microsoft-dokument
description: Det här avsnittet visar hur du konfigurerar olika principer för tillgångsleverans med Azure Media Services .NET SDK.
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
ms.openlocfilehash: ab3c40ee408498453bb137c63c440d980b0b7255
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974520"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurera principer för tillgångsleverans med .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Översikt
Om du planerar att leverera krypterade tillgångar är ett av stegen i arbetsflödet för innehållsleverans i Media Services att konfigurera leveransprinciper för tillgångar. Principen för tillgångsleverans talar om för Media Services hur du vill att din tillgång ska levereras: till vilket direktuppspelningsprotokoll som din tillgång ska paketeras dynamiskt (till exempel MPEG DASH, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera dynamiskt eller inte tillgång och hur (kuvert eller gemensam kryptering).

I den här artikeln beskrivs varför och hur du skapar och konfigurerar principer för tillgångsleverans.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standardslutpunkt** för direktuppspelning till ditt konto i tillståndet **Stoppad.** Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
>
>För att kunna använda dynamisk förpackning och dynamisk kryptering måste din tillgång innehålla en uppsättning adaptiva bithastighetER MP4s eller adaptiva bithastighet Smooth Streaming-filer.

Du kan tillämpa olika principer på samma tillgång. Du kan till exempel använda PlayReady-kryptering för jämn strömning och AES-kuvertkryptering på MPEG DASH och HLS. Alla protokoll som inte har definierats i en leveransprincip (exempelvis kan du lägga till en enskild princip som endast anger HLS som protokoll) kommer att blockeras från strömning. Ett undantag är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll fritt.

Om du vill leverera en lagringskrypterad tillgång måste du konfigurera tillgångens leveransprincip. Innan tillgången kan strömmas tar strömningsservern bort lagringskrypteringen och strömmar ditt innehåll med den angivna leveransprincipen. Om du till exempel vill leverera din tillgång krypterad med AES-kuvertkrypteringsnyckel (Advanced Encryption Standard) anger du principtypen till **DynamicEnvelopeEncryption**. Om du vill ta bort lagringskryptering och strömma tillgången i clear anger du principtypen till **NoDynamicEncryption**. Exempel som visar hur du konfigurerar dessa principtyper följer.

Beroende på hur du konfigurerar principen för tillgångsleverans kan du dynamiskt paketera, kryptera och strömma följande direktuppspelningsprotokoll: Smooth Streaming, HLS och MPEG DASH.

I följande lista visas de format som du använder för att strömma Smooth, HLS och DASH.

Smidig streaming:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

Hls:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/{lokalisator-ID}/{filnamn}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Överväganden
* Innan du tar bort AssetDeliveryPolicy bör du ta bort alla direktuppspelade positionerare som är associerade med tillgången. Du kan senare skapa nya streamingpositionerare, om så önskas, med en ny AssetDeliveryPolicy.
* Det går inte att skapa en direktuppströmningspositionerare på en lagringskrypterad tillgång när ingen princip för tillgångsleverans har angetts.  Om tillgången inte är lagringskrypterad låter systemet dig skapa en positionerare och strömma tillgången i clear utan en princip för tillgångsleverans.
* Du kan ha flera principer för tillgångsleverans som är associerade med en enda tillgång, men du kan bara ange ett sätt att hantera en viss AssetDeliveryProtocol.  Betydelse om du försöker länka två leveransprinciper som anger AssetDeliveryProtocol.SmoothStreaming-protokollet som resulterar i ett fel eftersom systemet inte vet vilken du vill att det ska gälla när en klient gör en begäran om jämn direktuppspelning.
* Om du har en tillgång med en befintlig streaming locator kan du inte länka en ny princip till tillgången (du kan antingen ta bort länken till en befintlig princip från tillgången eller uppdatera en leveransprincip som är associerad med tillgången).  Du måste först ta bort strömningspositioneraren, justera principerna och sedan återskapa strömningspositioneraren.  Du kan använda samma locatorId när du återskapar streaming locator men du bör se till att inte orsakar problem för klienter eftersom innehåll kan cachelagras av ursprunget eller en nedströms CDN.

## <a name="clear-asset-delivery-policy"></a>Tydlig princip för tillgångsleverans

Följande **Metoden ConfigureClearAssetDeliveryPolicy** anger att dynamisk kryptering inte ska tillämpas och att dataströmmen ska levereras i något av följande protokoll: MPEG DASH, HLS och Smooth Streaming-protokoll. Du kanske vill tillämpa den här principen på dina lagringskrypterade tillgångar.

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [Typer som används när du definierar avsnittet AssetDeliveryPolicy.](#types)

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
## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption tillgång leveranspolitik

Följande **CreateAssetDeliveryPolicy-metod** skapar **AssetDeliveryPolicy** som är konfigurerad för att tillämpa dynamisk gemensam kryptering (**DynamicCommonEncryption**) på ett smidigt direktuppspelningsprotokoll (andra protokoll blockeras från direktuppspelning). Metoden tar två parametrar: **Tillgång** (den tillgång som du vill tillämpa leveransprincipen på) och **IContentKey** (innehållsnyckeln för **commonencryption-typen,** för mer information, se: [Skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md#common_contentkey)).

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [Typer som används när du definierar avsnittet AssetDeliveryPolicy.](#types)

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

Med Azure Media Services kan du också lägga till Widevine-kryptering. I följande exempel visas att både PlayReady och Widevine läggs till i leveranspolicyn för tillgångar.

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
> När du krypterar med Widevine, skulle du bara kunna leverera med DASH. Se till att ange DASH i tillgångsleveransprotokollet.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption tillgång leveranspolitik
Följande **CreateAssetDeliveryPolicy-metod** skapar **AssetDeliveryPolicy** som är konfigurerad för att tillämpa dynamisk kuvertkryptering **(DynamicEnvelopeEncryption**) för jämn direktuppspelning, HLS och DASH-protokoll (om du bestämmer dig för att inte ange vissa protokoll blockeras de från direktuppspelning). Metoden tar två parametrar: **Tillgång** (den tillgång som du vill tillämpa leveransprincipen på) och **IContentKey** (innehållsnyckeln för **typen Kuvertkrypning,** för mer information, se: [Skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [Typer som används när du definierar avsnittet AssetDeliveryPolicy.](#types)   

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

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typer som används vid definition av AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a><a id="AssetDeliveryProtocol"></a>TillgångDeliveryProtocol

I följande uppräkning beskrivs värden som du kan ange för tillgångsleveransprotokollet.

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
### <a name="assetdeliverypolicytype"></a><a id="AssetDeliveryPolicyType"></a>Tillgångsleveranspolicytyp

I följande uppräkning beskrivs värden som du kan ange för typen av tillgångsleveransprincip.  
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
### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

I följande uppräkning beskrivs värden som du kan använda för att konfigurera leveransmetoden för innehållsnyckeln till klienten.
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
### <a name="assetdeliverypolicyconfigurationkey"></a><a id="AssetDeliveryPolicyConfigurationKey"></a>TillgänglighetsleveransPolicyKonfigurationsnyckel

I följande uppräkning beskrivs värden som du kan ange för att konfigurera nycklar som används för att hämta specifik konfiguration för en princip för tillgångsleverans.
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

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


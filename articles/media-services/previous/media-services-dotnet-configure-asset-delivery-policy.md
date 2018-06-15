---
title: Konfigurera principerna för tillgångsleverans med .NET SDK | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar olika principerna för tillgångsleverans med Azure Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: cfowler
editor: ''
ms.assetid: 3ec46f58-6cbb-4d49-bac6-1fd01a5a456b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/05/2018
ms.author: juliako
ms.openlocfilehash: aee2477e0633974cba42ab26e102323cb9606810
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33789455"
---
# <a name="configure-asset-delivery-policies-with-net-sdk"></a>Konfigurera principerna för tillgångsleverans med .NET SDK
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

## <a name="overview"></a>Översikt
Om du planerar att leverera krypterade tillgångar ett av stegen i Media Services innehållsleverans arbetsflödet konfigurera leveransprinciperna för tillgångar. Anger tillgångsleveransprincip Media Services hur du vill använda för din tillgång som ska levereras: till vilka streaming-protokollet bör din tillgång dynamiskt paketeras (till exempel MPEG DASH, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera dynamiskt din tillgång och hur (envelope eller vanliga kryptering).

Den här artikeln beskriver varför och hur du skapar och konfigurerar principerna för tillgångsleverans.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
>
>Dessutom måste din tillgång för att kunna använda dynamisk paketering och dynamisk kryptering innehåller en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer.

Du kan tillämpa olika principer för samma tillgång. Du kan till exempel tillämpa PlayReady-kryptering till kryptering för Smooth Streaming- och AES Envelope MPEG DASH och HLS. Alla protokoll som inte har definierats i en leveransprincip (exempelvis kan du lägga till en enskild princip som endast anger HLS som protokoll) kommer att blockeras från strömning. Ett undantag är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll fritt.

Om du vill leverera en krypterad tillgång lagring måste du konfigurera den tillgångsleveransprincip. Innan din tillgång kan strömmas strömmande server tar du bort krypteringen lagring och strömmar ditt innehåll med hjälp av angivna leveransprincipen. Till exempel för att leverera din tillgång som krypterats med Advanced Encryption Standard (AES) kuvert krypteringsnyckeln anger du principtypen **DynamicEnvelopeEncryption**. Om du vill ta bort lagringskryptering och strömma tillgången i klartext, anger du principtypen **NoDynamicEncryption**. Exempel som visar hur du konfigurerar dessa principtyper följer.

Beroende på hur du konfigurerar tillgångsleveransprincip, du kan dynamiskt paketera, kryptera och strömma följande protokoll för dataströmmar: Smooth Streaming, HLS och MPEG DASH.

I följande lista visas format för att du använder för att strömma Smooth, HLS och STRECK.

Smooth Streaming:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

HLS:

{strömmande slutpunkten namn media services-konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{strömmande slutpunkten namn media services-konto name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

## <a name="considerations"></a>Överväganden
* Innan du tar bort AssetDeliveryPolicy, bör du ta bort alla strömmande lokaliserare som är kopplade till tillgången. Du kan skapa nya strömmande lokaliserare senare om du vill, med en ny AssetDeliveryPolicy.
* En strömningslokaliserare kan inte skapas på en krypterad tillgång lagring när någon tillgångsleveransprincip anges.  Om tillgången är lagringskrypterad, kan systemet du skapa en positionerare och strömma tillgången i klartext utan en tillgångsleveransprincip.
* Du kan ha flera principerna för tillgångsleverans som är associerade med en enda resurs, men du kan bara ange ett sätt att hantera en viss AssetDeliveryProtocol.  Vilket innebär att om du försöker länka två leveransprinciperna som anger protokollet AssetDeliveryProtocol.SmoothStreaming som leder till ett fel eftersom systemet inte vet vilken du vill att det ska tillämpas när en klient gör en begäran om Smooth Streaming.
* Om du har en tillgång med en befintlig strömmande lokaliserare, du kan inte länka en ny princip för tillgången (du kan antingen Avlänka en befintlig princip till tillgången eller uppdatera en leveransprincip som är kopplade till tillgången).  Du måste först ta bort strömningspositioneraren, justera principerna och sedan återskapa strömningspositioneraren.  Du kan använda samma locatorId när du återskapa strömningslokaliseraren men du bör se till att den orsakar problem för klienter eftersom innehållet kan cachelagras av ursprung eller en underordnad CDN.

## <a name="clear-asset-delivery-policy"></a>Rensa tillgångsleveransprincip

Följande **ConfigureClearAssetDeliveryPolicy** metoden anger inte att använda dynamisk kryptering och levererar dataströmmen i något av följande protokoll: MPEG DASH, HLS och Smooth Streaming-protokoll. Du kanske vill använda denna princip för dina lagringskrypterad tillgångar.

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i [typer som används när du definierar AssetDeliveryPolicy](#types) avsnitt.

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

Följande **CreateAssetDeliveryPolicy** metoden skapar den **AssetDeliveryPolicy** som är konfigurerad för att använda dynamisk vanliga kryptering (**DynamicCommonEncryption**) en smooth streaming-protokollet (andra protokoll kommer att blockeras från strömning). Metoden har två parametrar: **tillgången** (den tillgång som du vill tillämpa principen för tillgångsleverans) och **IContentKey** (innehållsnyckeln av den **CommonEncryption** typ för Mer information, se: [att skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md#common_contentkey)).

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i [typer som används när du definierar AssetDeliveryPolicy](#types) avsnitt.

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

Azure Media Services kan du lägga till Widevine-kryptering. Exemplet nedan visar både PlayReady och Widevine läggs till i principen för tillgångsleverans.

```csharp
    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        // Get the PlayReady license service URL.
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);


        // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
        // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
        // The WidevineBaseLicenseAcquisitionUrl (used below) also tells Dynamaic Encryption 
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
> Vid kryptering med Widevine, skulle du bara att kunna leverera med bindestreck. Se till att ange STRECK i protokollet för tillgångsleverans.
> 
> 

## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption tillgångsleveransprincip
Följande **CreateAssetDeliveryPolicy** metoden skapar den **AssetDeliveryPolicy** som är konfigurerad för att använda dynamiska kryptering (**DynamicEnvelopeEncryption**) Smooth Streaming, HLS och DASH-protokoll (om du väljer att inte ange vissa protokoll, de kommer att blockeras från strömning). Metoden har två parametrar: **tillgången** (den tillgång som du vill tillämpa principen för tillgångsleverans) och **IContentKey** (innehållsnyckeln av den **EnvelopeEncryption** typ Mer information finns: [att skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md#envelope_contentkey)).

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i [typer som används när du definierar AssetDeliveryPolicy](#types) avsnitt.   

```csharp
    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {

        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
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

## <a id="types"></a>Typer som används när du definierar AssetDeliveryPolicy

### <a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol

Följande enum beskrivs värden som du kan ange för protokollet för tillgångsleverans.

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

Följande enum beskriver värden som du kan ange för tillgångstyp leverans princip.  
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

Följande enum beskrivs värden som du kan använda för att konfigurera leveransmetod för innehållsnyckeln till klienten.
  ```csharp  
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        ///
        </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        ///
        </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        ///
        </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }
```
### <a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

Följande enum beskrivs värden som du kan ange för att konfigurera nycklar som används för att få specifik konfiguration för en tillgångsleveransprincip.
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


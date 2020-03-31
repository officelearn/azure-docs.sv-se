---
title: Konfigurera principer för tillgångsleverans med HJÄLP AV REST-API FÖR Media Services | Microsoft-dokument
description: Det här avsnittet visar hur du konfigurerar olika principer för tillgångsleverans med hjälp av REST-API för Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 5cb9d32a-e68b-4585-aa82-58dded0691d0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 09f0371bc189fcf7b25ec3261e2e1f5eaf1892ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194521"
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurera principer för tillgångsleverans
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Om du planerar att leverera dynamiskt krypterade tillgångar är ett av stegen i arbetsflödet för innehållsleverans i Media Services att konfigurera leveransprinciper för tillgångar. Principen för tillgångsleverans talar om för Media Services hur du vill att din tillgång ska levereras: till vilket direktuppspelningsprotokoll som din tillgång ska paketeras dynamiskt (till exempel MPEG DASH, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera dynamiskt eller inte tillgång och hur (kuvert eller gemensam kryptering).

I det här avsnittet beskrivs varför och hur du skapar och konfigurerar principer för tillgångsleverans.

> [!NOTE]
> När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
>
> För att kunna använda dynamisk förpackning och dynamisk kryptering måste din tillgång innehålla en uppsättning adaptiva bithastighetER MP4s eller adaptiva bithastighet Smooth Streaming-filer.

Du kan tillämpa olika principer på samma tillgång. Du kan till exempel använda PlayReady-kryptering för jämn strömning och AES-kuvertkryptering på MPEG DASH och HLS. Alla protokoll som inte har definierats i en leveransprincip (exempelvis kan du lägga till en enskild princip som endast anger HLS som protokoll) kommer att blockeras från strömning. Ett undantag till detta är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll fritt.

Om du vill leverera en lagringskrypterad tillgång måste du konfigurera tillgångens leveransprincip. Innan tillgången kan strömmas tar strömningsservern bort lagringskrypteringen och strömmar ditt innehåll med den angivna leveransprincipen. Om du till exempel vill leverera din tillgång krypterad med AES-kuvertkrypteringsnyckel (Advanced Encryption Standard) anger du principtypen till **DynamicEnvelopeEncryption**. Om du vill ta bort lagringskryptering och strömma tillgången i clear anger du principtypen till **NoDynamicEncryption**. Exempel som visar hur du konfigurerar dessa principtyper följer.

Beroende på hur du konfigurerar principen för tillgångsleverans kan du dynamiskt paketera, dynamiskt kryptera och strömma följande direktuppspelningsprotokoll: Smooth Streaming, HLS, MPEG DASH-strömmar.

I följande lista visas de format som du använder för att strömma Smooth, HLS, DASH.

Smidig streaming:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

Hls:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/{lokalisator-ID}/{filnamn}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Anvisningar för hur du publicerar en tillgång och skapar en strömnings-URL finns i [Skapa en strömnings-URL](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Överväganden
* Du kan inte ta bort en AssetDeliveryPolicy som är associerad med en tillgång medan en OnDemand-positionerare (streaming) finns för den tillgången. Rekommendationen är att ta bort principen från tillgången innan principen tas bort.
* Det går inte att skapa en direktuppströmningspositionerare på en lagringskrypterad tillgång när ingen princip för tillgångsleverans har angetts.  Om tillgången inte är lagringskrypterad låter systemet dig skapa en positionerare och strömma tillgången i clear utan en princip för tillgångsleverans.
* Du kan ha flera principer för tillgångsleverans som är associerade med en enda tillgång, men du kan bara ange ett sätt att hantera en viss AssetDeliveryProtocol.  Betydelse om du försöker länka två leveransprinciper som anger AssetDeliveryProtocol.SmoothStreaming-protokollet som resulterar i ett fel eftersom systemet inte vet vilken du vill att det ska gälla när en klient gör en begäran om jämn direktuppspelning.
* Om du har en tillgång med en befintlig streaming locator kan du inte länka en ny princip till tillgången, ta bort länken till en befintlig princip från tillgången eller uppdatera en leveransprincip som är associerad med tillgången.  Du måste först ta bort strömningspositioneraren, justera principerna och sedan återskapa strömningspositioneraren.  Du kan använda samma locatorId när du återskapar streaming locator men du bör se till att inte orsakar problem för klienter eftersom innehåll kan cachelagras av ursprunget eller en nedströms CDN.

> [!NOTE]
> 
> När du öppnar entiteter i Media Services måste du ange specifika rubrikfält och värden i HTTP-begäranden. Mer information finns i [Installationsprogrammet för REST API Development för Media Services](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API:et finns [i Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Tydlig princip för tillgångsleverans
### <a name="create-asset-delivery-policy"></a><a id="create_asset_delivery_policy"></a>Skapa princip för tillgångsleverans
Följande HTTP-begäran skapar en princip för tillgångsleverans som anger att inte tillämpa dynamisk kryptering och att leverera strömmen i något av följande protokoll: MPEG DASH, HLS och Smooth Streaming-protokoll. 

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [Typer som används när du definierar avsnittet AssetDeliveryPolicy.](#types)   

Begäran:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    Host: media.windows.net

    {"Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null}

Svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 363
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
    request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 06:21:27 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
    "Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
    "Name":"Clear Policy",
    "AssetDeliveryProtocol":7,
    "AssetDeliveryPolicyType":2,
    "AssetDeliveryConfiguration":null,
    "Created":"2015-02-08T06:21:27.6908329Z",
    "LastModified":"2015-02-08T06:21:27.6908329Z"}

### <a name="link-asset-with-asset-delivery-policy"></a><a id="link_asset_with_asset_delivery_policy"></a>Länka tillgång till tillgångsleveranspolicy
Följande HTTP-begäran länkar den angivna tillgången till tillgångsleveransprincipen till.

Begäran:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Svar:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption tillgång leveranspolitik
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Skapa innehållsnyckel för typen Kuvertkrypning och länka den till tillgången
När du anger leveransprincipen för DynamicEnvelopeEncryption måste du se till att länka din tillgång till en innehållsnyckel för kuvertkrypningstypen. Mer information finns i: [Skapa en innehållsnyckel](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a><a id="get_delivery_url"></a>Hämta leverans-URL
Hämta leverans-URL:en för den angivna leveransmetoden för innehållsnyckeln som skapades i föregående steg. En klient använder den returnerade URL:en för att begära en AES-nyckel eller en PlayReady-licens för att kunna spela upp det skyddade innehållet.

Ange vilken typ av URL som ska komma in i http-begäran. Om du skyddar ditt innehåll med PlayReady begär du en URL för att förvärva licens för Media Services PlayReady med 1 för keyDeliveryType: {"keyDeliveryType":1}. Om du skyddar innehållet med kuvertkrypteringen begär du en url för nyckelinsamling genom att ange 2 för keyDeliveryType: {"keyDeliveryType":2}.

Begäran:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    Host: media.windows.net
    Content-Length: 21

    {"keyDeliveryType":2}

Svar:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 198
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
    request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 08 Feb 2015 21:42:30 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


### <a name="create-asset-delivery-policy"></a>Skapa princip för tillgångsleverans
Följande HTTP-begäran skapar **AssetDeliveryPolicy** som är konfigurerad för att tillämpa dynamisk kuvertkryptering (**DynamicEnvelopeEncryption**) på **HLS-protokollet** (i det här exemplet blockeras andra protokoll från direktuppspelning). 

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [Typer som används när du definierar avsnittet AssetDeliveryPolicy.](#types)   

Begäran:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]"}


Svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 460
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 09 Feb 2015 05:24:38 GMT

    {"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\\/\"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


### <a name="link-asset-with-asset-delivery-policy"></a>Länka tillgång till tillgångsleveranspolicy
Se [Länka tillgång med tillgångsleveranspolicy](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption tillgång leveranspolitik
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Skapa innehållsnyckel för commonEncryption-typen och länka den till tillgången
När du anger Leveransprincipen för DynamicCommonEncryption måste du se till att länka din tillgång till en innehållsnyckel av commonencryption-typen. Mer information finns i: [Skapa en innehållsnyckel](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Hämta leverans-URL
Hämta leveransadressen för PlayReady-leveransmetoden för innehållsnyckeln som skapades i föregående steg. En klient använder den returnerade URL:en för att begära en PlayReady-licens för att kunna spela upp det skyddade innehållet. Mer information finns i [Hämta leverans-URL](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Skapa princip för tillgångsleverans
Följande HTTP-begäran skapar **AssetDeliveryPolicy** som är konfigurerad för att tillämpa dynamisk gemensam kryptering (**DynamicCommonEncryption**) på **Smooth Streaming-protokollet** (i det här exemplet blockeras andra protokoll från direktuppspelning). 

Information om vilka värden du kan ange när du skapar en AssetDeliveryPolicy finns i avsnittet [Typer som används när du definierar avsnittet AssetDeliveryPolicy.](#types)   

Begäran:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Om du vill skydda ditt innehåll med Widevine DRM uppdaterar du värdena AssetDeliveryConfiguration för att använda WidevineLicenseAcquisitionUrl (som har värdet 7) och anger URL:en för en licensleveranstjänst. Du kan använda följande AMS-partner för att hjälpa dig att leverera Widevine-licenser: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Ett exempel: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> När du krypterar med Widevine, skulle du bara kunna leverera med DASH. Se till att ange DASH (2) i tillgångsleveransprotokollet.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Länka tillgång till tillgångsleveranspolicy
Se [Länka tillgång med tillgångsleveranspolicy](#link_asset_with_asset_delivery_policy)

## <a name="types-used-when-defining-assetdeliverypolicy"></a><a id="types"></a>Typer som används vid definition av AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>TillgångDeliveryProtocol

I följande uppräkning beskrivs värden som du kan ange för tillgångsleveransprotokollet.

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

### <a name="assetdeliverypolicytype"></a>Tillgångsleveranspolicytyp

I följande uppräkning beskrivs värden som du kan ange för typen av tillgångsleveransprincip.  

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

### <a name="contentkeydeliverytype"></a>ContentKeyDeliveryType

I följande uppräkning beskrivs värden som du kan använda för att konfigurera leveransmetoden för innehållsnyckeln till klienten.
    
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


### <a name="assetdeliverypolicyconfigurationkey"></a>TillgänglighetsleveransPolicyKonfigurationsnyckel

I följande uppräkning beskrivs värden som du kan ange för att konfigurera nycklar som används för att hämta specifik konfiguration för en princip för tillgångsleverans.

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

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


---
title: Konfigurera tillgångsleveransprinciper med hjälp av Media Services REST API | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar olika tillgångsleveransprinciper med hjälp av Media Services REST API.
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
ms.openlocfilehash: 5e4e565b0b5272de19458617a9c4bd3509907cce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817408"
---
# <a name="configuring-asset-delivery-policies"></a>Konfigurera tillgångsleveransprinciper
[!INCLUDE [media-services-selector-asset-delivery-policy](../../../includes/media-services-selector-asset-delivery-policy.md)]

Om du planerar att leverera dynamiskt krypterade tillgångar ett av stegen i arbetsflödet för Media Services-leverans av innehåll konfigurera leveransprinciperna för tillgångar. Talar tillgångsleveransprincip om Media Services för hur du vill för tillgången som ska levereras: i vilket strömningsprotokoll bör din tillgång dynamiskt paketeras (till exempel, MPEG DASH, HLS, Smooth Streaming eller alla), oavsett om du vill kryptera dynamiskt din tillgång och hur (kuvert eller gemensam kryptering).

Det här avsnittet beskrivs varför och hur du skapar och konfigurerar principerna för tillgångsleverans.

> [!NOTE]
> När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
>
> Om du vill kunna använda dynamisk paketering och dynamisk kryptering måste dessutom din tillgång innehålla en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer.

Du kan tillämpa olika principer för samma tillgång. Du kan till exempel tillämpa PlayReady-kryptering för att kryptering för Smooth Streaming och AES Envelope för MPEG-DASH och HLS. Alla protokoll som inte har definierats i en leveransprincip (exempelvis kan du lägga till en enskild princip som endast anger HLS som protokoll) kommer att blockeras från strömning. Ett undantag till detta är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll fritt.

Om du vill att leverera en krypterad tillgång för lagring, måste du konfigurera den tillgångsleveransprincip. Innan du kan strömma din tillgång, kommer servern för strömning tar bort lagringskryptering och strömmar ditt innehåll med den angivna principen. Till exempel för att leverera din tillgång som krypterats med Advanced Encryption Standard (AES) kuvert krypteringsnyckeln, anger du principtypen **DynamicEnvelopeEncryption**. Om du vill ta bort lagringskryptering och strömma tillgången i klartext, anger du principtypen **NoDynamicEncryption**. Exempel som visar hur du konfigurerar dessa principtyper följer.

Beroende på hur du konfigurerar tillgångsleveransprincip skulle du kunna dynamiskt paketera dynamiskt kryptera och strömma följande strömningsprotokoll: Smooth Streaming, HLS, MPEG DASH streams.

I följande lista visas format som du använder till stream Smooth, HLS, DASH.

Smooth Streaming:

{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

HLS:

{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

{strömmande slutpunkt namn-name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Anvisningar för hur du publicerar en tillgång och skapar en strömnings-URL finns i [Skapa en strömnings-URL](media-services-deliver-streaming-content.md).

## <a name="considerations"></a>Överväganden
* Du kan inte ta bort en AssetDeliveryPolicy som är associerade med en tillgång, även om det finns en (streaming) OnDemand-positionerare för tillgången. Rekommendationen är att ta bort principen från tillgången innan du tar bort principen.
* Att går inte skapa en strömningslokaliserare på en krypterad lagring tillgång när någon tillgångsleveransprincip anges.  Om tillgången är inte krypterad lagring, kan systemet du skapa en positionerare och strömma tillgången i klartext utan en tillgångsleveransprincip.
* Du kan ha flera tillgångsleveransprinciper som är associerade med en enda resurs, men du kan bara ange ett sätt att hantera en viss AssetDeliveryProtocol.  Vilket innebär att om du försöker länka två leveransprinciper som anger det AssetDeliveryProtocol.SmoothStreaming protokoll som resulterar i ett fel eftersom systemet inte vet vilket du vill att det ska tillämpas när en klient gör en begäran om Smooth Streaming.
* Om du har en tillgång med en befintlig strömningslokaliserare det går inte att länka en ny princip till tillgången, ta bort länk till en befintlig princip från tillgången eller uppdaterar en leveransprincip som är associerade med tillgången.  Du måste först ta bort strömningslokaliseraren, justera principerna och sedan återskapa strömningslokaliseraren.  Du kan använda samma locatorId när du återskapa strömningslokaliseraren men bör du kontrollera den orsakar problem för klienter eftersom innehåll cachelagras av ursprunget eller en underordnad CDN.

> [!NOTE]
> 
> Vid åtkomst till entiteter i Media Services, måste du ange specifika namn på huvudfält och värden i HTTP-förfrågningar. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS API finns i [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="clear-asset-delivery-policy"></a>Rensa tillgångsleveransprincip
### <a id="create_asset_delivery_policy"></a>Skapa tillgångsleveransprincip
Följande HTTP-begäran skapar en tillgångsleveransprincip som anger att inte använda dynamisk kryptering och leverera strömmen i någon av följande protokoll:  MPEG DASH, HLS och Smooth Streaming-protokoll. 

Information om vilka värden som du kan ange när du skapar en AssetDeliveryPolicy finns i den [används när du definierar AssetDeliveryPolicy](#types) avsnittet.   

Begäran:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
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

### <a id="link_asset_with_asset_delivery_policy"></a>Länken tillgång med tillgångsleveransprincip
Följande HTTP-begäran länkar den angivna tillgången till tillgångsleveransprincip till.

Begäran:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
    Host: media.windows.net

    {"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Svar:

    HTTP/1.1 204 No Content


## <a name="dynamicenvelopeencryption-asset-delivery-policy"></a>DynamicEnvelopeEncryption tillgångsleveransprincip
### <a name="create-content-key-of-the-envelopeencryption-type-and-link-it-to-the-asset"></a>Skapa innehållsnyckeln av typen EnvelopeEncryption och länka det till tillgången
När du anger DynamicEnvelopeEncryption leveransprincip måste se till att länka din tillgång till en innehållsnyckel av typen EnvelopeEncryption. Mer information finns i: [Skapa en innehållsnyckel](media-services-rest-create-contentkey.md)).

### <a id="get_delivery_url"></a>Hämta URL för leverans
Hämta delivery-URL för den angivna leveransmetoden för innehållsnyckeln skapade i föregående steg. En klient använder returnerade URL: en för att begära en AES-nyckel eller en PlayReady-licens för att spela upp det skyddade innehållet.

Ange vilken typ av URL: en för att hämta i brödtexten i HTTP-begäran. Om du skyddar ditt innehåll med PlayReady, Media Services PlayReady-licens förvärv URL för begäran med 1 för keyDeliveryType: {”keyDeliveryType”: 1}. Om du skyddar ditt innehåll med kuvert-kryptering kan begära en URL för viktiga anskaffning genom att ange 2 för keyDeliveryType: {”keyDeliveryType”: 2}.

Begäran:

    POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
    Content-Type: application/json
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
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


### <a name="create-asset-delivery-policy"></a>Skapa tillgångsleveransprincip
Följande HTTP-begäran skapar den **AssetDeliveryPolicy** som är konfigurerad för att använda dynamiska kryptering (**DynamicEnvelopeEncryption**) till den **HLS** protokollet (i det här exemplet andra protokoll kommer att blockeras från strömning). 

Information om vilka värden som du kan ange när du skapar en AssetDeliveryPolicy finns i den [används när du definierar AssetDeliveryPolicy](#types) avsnittet.   

Begäran:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
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


### <a name="link-asset-with-asset-delivery-policy"></a>Länken tillgång med tillgångsleveransprincip
Se [länk tillgång med tillgångsleveransprincip](#link_asset_with_asset_delivery_policy)

## <a name="dynamiccommonencryption-asset-delivery-policy"></a>DynamicCommonEncryption tillgångsleveransprincip
### <a name="create-content-key-of-the-commonencryption-type-and-link-it-to-the-asset"></a>Skapa innehållsnyckeln av typen CommonEncryption och länka det till tillgången
När du anger DynamicCommonEncryption leveransprincip måste se till att länka din tillgång till en innehållsnyckel av typen CommonEncryption. Mer information finns i: [Skapa en innehållsnyckel](media-services-rest-create-contentkey.md)).

### <a name="get-delivery-url"></a>Hämta URL för leverans
Hämta URL: en för leverans för PlayReady leveransmetod för innehållsnyckeln skapade i föregående steg. En klient använder returnerade URL: en för att begära en PlayReady-licens för att spela upp det skyddade innehållet. Mer information finns i [hämta URL för leverans](#get_delivery_url).

### <a name="create-asset-delivery-policy"></a>Skapa tillgångsleveransprincip
Följande HTTP-begäran skapar den **AssetDeliveryPolicy** som är konfigurerad för att använda dynamiska gemensam kryptering (**DynamicCommonEncryption**) till den **Smooth Streaming**protocol (i det här exemplet andra protokoll kommer att blockeras från strömning). 

Information om vilka värden som du kan ange när du skapar en AssetDeliveryPolicy finns i den [används när du definierar AssetDeliveryPolicy](#types) avsnittet.   

Begäran:

    POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
    Host: media.windows.net

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":2,\"Value\":\"https:\\/\\/amsaccount1.keydelivery.mediaservices.windows.net\/PlayReady\/"}]"}


Om du vill skydda ditt innehåll med Widevine DRM uppdatera värdena för din AssetDeliveryConfiguration för att använda WidevineLicenseAcquisitionUrl (som har värdet av 7) och ange Webbadressen till en licensleveranstjänst. Du kan använda följande AMS-partner för att leverera Widevine-licenser: [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](https://ezdrm.com/), [castLabs](https://castlabs.com/company/partners/azure/).

Exempel: 

    {"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":2,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{\"Key\":7,\"Value\":\"https:\\/\\/example.net\/WidevineLicenseAcquisition\/"}]"}

> [!NOTE]
> När du krypterar med Widevine, skulle du bara att kunna leverera med bindestreck. Se till att ange DASH (2) i protokollet tillgångsleverans.
> 
> 

### <a name="link-asset-with-asset-delivery-policy"></a>Länken tillgång med tillgångsleveransprincip
Se [länk tillgång med tillgångsleveransprincip](#link_asset_with_asset_delivery_policy)

## <a id="types"></a>Används när du definierar AssetDeliveryPolicy

### <a name="assetdeliveryprotocol"></a>AssetDeliveryProtocol

Följande enum beskriver värden som du kan ange för protokollet tillgångsleverans.

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

### <a name="assetdeliverypolicytype"></a>AssetDeliveryPolicyType

Följande enum beskriver värden som du kan ange för typen av tillgången leverans.  

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

Följande enum beskriver värden som du kan använda för att konfigurera leveransmetod för innehållsnyckeln till klienten.
    
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


### <a name="assetdeliverypolicyconfigurationkey"></a>AssetDeliveryPolicyConfigurationKey

Följande enum beskriver värden som du kan ange för att konfigurera nycklar som används för att få specifik konfiguration för en tillgångsleveransprincip.

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

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


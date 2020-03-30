---
title: Publicera Azure Media Services-innehåll med REST
description: Lär dig hur du skapar en positionerare som används för att skapa en url för direktuppspelning. Koden använder REST API.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 787336f00a83d9403e3069754787743b9be6c5b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77050010"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publicera Azure Media Services-innehåll med REST 
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [Resten](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Du kan strömma en adaptiv bithastighet MP4-uppsättning genom att skapa en OnDemand-streaming locator och skapa en strömmande URL. Kodningen [av en tillgångsartikel](media-services-rest-encode-asset.md) visar hur du kodar till en adaptiv bithastighet MP4-uppsättning. Om innehållet är krypterat konfigurerar du principen för tillgångsleverans (enligt beskrivningen i [den här](media-services-rest-configure-asset-delivery-policy.md) artikeln) innan du skapar en positionerare. 

Du kan också använda en OnDemand-direktuppspelningspositionerare för att skapa webbadresser som pekar på MP4-filer som kan hämtas successivt.  

Den här artikeln visar hur du skapar en OnDemand-streaming locator för att publicera din tillgång och skapa en Smooth, MPEG DASH och HLS-strömningsadresser. Den visar också hur man bygger progressiva nedladdningsadresser.

I [följande](#types) avsnitt visas de uppräkningstyper vars värden används i REST-anropen.   

> [!NOTE]
> När du öppnar entiteter i Media Services måste du ange specifika rubrikfält och värden i HTTP-begäranden. Mer information finns i [Installationsprogrammet för REST API Development för Media Services](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API:et finns [i Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>När du har https://media.windows.netanslutit till får du en 301-omdirigering som anger en annan Media Services-URI. Du måste ringa efterföljande samtal till den nya URI:n.

## <a name="create-an-ondemand-streaming-locator"></a>Skapa en OnDemand-streamingpositionerare
Om du vill skapa ondemand-söksökaren och hämta webbadresser måste du göra följande:

1. Om innehållet är krypterat definierar du en åtkomstprincip.
2. Skapa en OnDemand-streamingpositionerare.
3. Om du planerar att strömma hämtar du manifestfilen för direktuppspelning (.ism) i tillgången. 
   
   Om du planerar att hämta successivt får du namnen på MP4-filer i tillgången. 
4. Skapa webbadresser till manifestfilen eller MP4-filerna. 
5. Du kan inte skapa en direktuppspelningspositionerare med hjälp av en AccessPolicy som innehåller skriv- eller borttagningsbehörigheter.

### <a name="create-an-access-policy"></a>Skapa en åtkomstprincip

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att finnas kvar under en längre tid (principer som inte är uppladdningsprinciper). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

Begäran:

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    Host: media.windows.net
    Content-Length: 68

    {"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

Svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 311
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
    Server: Microsoft-IIS/8.5
    request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
    x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:52:09 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}

### <a name="create-an-ondemand-streaming-locator"></a>Skapa en OnDemand-streamingpositionerare
Skapa positioneraren för den angivna tillgångs- och tillgångsprincipen.

Begäran:

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    Host: media.windows.net
    Content-Length: 181

    {"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}

Svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 637
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
    Server: Microsoft-IIS/8.5
    request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
    x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 18 Feb 2015 06:58:37 GMT

    {"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}

### <a name="build-streaming-urls"></a>Skapa url:er för direktuppspelning
Använd värdet **Sökväg** som returnerats när positioneraren har skapats för att skapa url:erna Smooth, HLS och MPEG DASH. 

Smooth Streaming: **Sökväg** + manifestfilnamn + "/manifest"

exempel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Sökväg** + manifestfilnamn + "/manifest(format=m3u8-aapl)"

exempel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Sökväg** + manifestfilnamn + "/manifest(format=mpd-time-csf)"

exempel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Skapa progressiva nedladdningsadresser
Använd värdet **Sökväg** som returnerats när positioneraren har skapats för att skapa den progressiva hämtnings-URL:en.   

URL: **Sökväg** + tillgångsfil mp4-namn

exempel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a name="enum-types"></a><a id="types"></a>Uppräkningstyper
    [Flags]
    public enum AccessPermissions
    {
        None = 0,
        Read = 1,
        Write = 2,
        Delete = 4,
        List = 8,
    }

    public enum LocatorType
    {
        None = 0,
        Sas = 1,
        OnDemandOrigin = 2,
    }

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Översikt över REST-API-API för Media Services-åtgärder](media-services-rest-how-to-use.md)

[Konfigurera princip för tillgångsleverans](media-services-rest-configure-asset-delivery-policy.md)


---
title: Publicera Azure Media Services-innehåll med hjälp av REST
description: Lär dig hur du skapar en positionerare som används för att skapa en strömnings-URL. Koden använder REST API.
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
ms.openlocfilehash: 974f0af461ecdc7de820191950b010035d02a601
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294968"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Publicera Azure Media Services-innehåll med hjälp av REST 
> [!div class="op_single_selector"]
> * [NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

Du kan strömma en anpassningsbar bithastighet MP4-uppsättningen genom att skapa en OnDemand-positionerare för direktuppspelning och att skapa en strömnings-URL. Den [koda en tillgång](media-services-rest-encode-asset.md) artikeln visar hur du koda till en med anpassningsbar bithastighet MP4-uppsättningen. Om ditt innehåll är krypterad, konfigurera principen för tillgångsleverans (enligt beskrivningen i [detta](media-services-rest-configure-asset-delivery-policy.md) artikel) innan du skapar en positionerare. 

Du kan också använda en OnDemand-strömning positionerare för att skapa webbadresser som pekar på MP4-filer som kan laddas ner progressivt.  

Den här artikeln visar hur du skapar en OnDemand-strömning positionerare för att kunna publicera tillgången och bygg ett Smooth, MPEG DASH och HLS-direktuppspelning URL: er. Den visar även hett för att skapa URL: er för progressiv nedladdning.

Den [följande](#types) delen visas uppräkningstyperna vars värden används i REST-anrop.   

> [!NOTE]
> Vid åtkomst till entiteter i Media Services, måste du ange specifika namn på huvudfält och värden i HTTP-förfrågningar. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS API finns i [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>När du har anslutit till https://media.windows.net, får du en 301 omdirigering att ange en annan URI för Media Services. Du måste göra efterföljande anrop till ny URI.

## <a name="create-an-ondemand-streaming-locator"></a>Skapa en OnDemand-positionerare för direktuppspelning
Om du vill skapa den OnDemand-positionerare för direktuppspelning och få URL: er, måste du göra följande:

1. Om innehållet krypteras, definiera en åtkomstprincip.
2. Skapa en OnDemand-positionerare för direktuppspelning.
3. Om du planerar att strömma få strömmande manifestfilen (.ism) i tillgången. 
   
   Om du planerar att progressivt hämta namnen på MP4-filer i tillgången. 
4. Skapa URL: er i manifestfilen eller MP4-filer. 
5. Du kan inte skapa en positionerare för direktuppspelning med hjälp av en AccessPolicy som innehåller skriva eller ta bort behörigheter.

### <a name="create-an-access-policy"></a>Skapa en åtkomstprincip

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar / åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här artikeln](media-services-dotnet-manage-entities.md#limit-access-policies).

Begäran:

    POST https://media.windows.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
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

### <a name="create-an-ondemand-streaming-locator"></a>Skapa en OnDemand-positionerare för direktuppspelning
Skapa lokaliserare för angiven tillgång och princip för tillgången.

Begäran:

    POST https://media.windows.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
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

### <a name="build-streaming-urls"></a>Skapa strömmande URL: er
Använd den **sökväg** värdet som returneras när du har skapat lokaliserare att skapa den Smooth, HLS och MPEG DASH URL: er. 

Smooth Streaming: **Sökvägen** + Manifestfilens filnamn + ”/ manifest”

exempel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Sökvägen** + Manifestfilens filnamn + ”/ manifest(format=m3u8-aapl)”

exempel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Sökvägen** + Manifestfilens filnamn + ”/ manifest(format=mpd-time-csf)”

exempel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Skapa URL: er för progressiv nedladdning
Använd den **sökväg** värdet som returneras när du har skapat lokaliserare skapa progressiv nedladdning-URL.   

URL: **Sökvägen** + tillgången mp4 filnamn

exempel:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a id="types"></a>Uppräkningstyperna
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

## <a name="see-also"></a>Se också
[Media Services operations REST API-översikt](media-services-rest-how-to-use.md)

[Konfigurera tillgångsleveransprincip](media-services-rest-configure-asset-delivery-policy.md)


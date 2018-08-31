---
title: Kom igång med att leverera innehåll på begäran med hjälp av REST | Microsoft Docs
description: Den här självstudien vägleder dig genom stegen för att implementera ett program för leverans av på begäran med Azure Media Services med hjälp av REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: juliako
ms.openlocfilehash: 757ba9e999bfbb46be96e653e7939d91bdf67679
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287133"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Kom igång med att leverera innehåll på begäran med hjälp av REST
[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Denna Snabbstart vägleder dig genom stegen för att implementera ett program för leverans av video på begäran (VoD) med hjälp av Azure Media Services (AMS) REST API: er.

Självstudierna innehåller det grundläggande Media Services-arbetsflödet och de vanligaste programmeringsobjekt och -uppgifter som krävs för utveckling av Media Services. I slutet av självstudien kan du strömma eller progressivt hämta en exempelmediefil som du överfört, kodat och ned.

Följande bild visar några av de vanligast använda objekten när du utvecklar VoD-program mot Media Services OData-modellen.

Klicka på bilden för att visa den i full storlek.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Förutsättningar
Följande krävs för att börja utveckla med Media Services med REST API: er.

* Ett Azure-konto. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).
* Ett Media Services-konto. Information om hur du skapar ett Media Services-konto finns i [Så här skapar du ett Media Services-konto](media-services-portal-create-account.md).
* Förståelse för hur du utvecklar med Media Services REST API. Mer information finns i [Media Services REST API-översikt](media-services-rest-how-to-use.md).
* Ett program som kan skicka HTTP-begäranden och svar. Den här självstudien används [Fiddler](http://www.telerik.com/download/fiddler).

Följande aktiviteter visas i den här snabbstarten.

1. Starta slutpunkt för direktuppspelning (med hjälp av Azure Portal).
2. Anslut till Media Services-konto med REST API.
3. Skapa en ny tillgång och överföra en videofil med REST API.
4. Koda källfilen till en uppsättning MP4-filer med REST API.
5. Publicera tillgången och hämta strömning och progressiv nedladdning URL: er med REST API.
6. Spela upp ditt innehåll.

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma dagar / åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här artikeln](media-services-dotnet-manage-entities.md#limit-access-policies).

Mer information om AMS REST-entiteter som används i den här artikeln finns [Azure Media Services REST API-referens](https://docs.microsoft.com/en-us/rest/api/media/services/azure-media-services-rest-api-reference). Se även [Azure Media Services-koncepten](media-services-concepts.md).

>[!NOTE]
>Vid åtkomst till entiteter i Media Services, måste du ange specifika namn på huvudfält och värden i HTTP-förfrågningar. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Starta slutpunkter för direktuppspelning med Azure Portal

När du arbetar med Azure Media Services, en av de vanligaste scenarierna att leverera video via direktuppspelning med anpassningsbar bithastighet. Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera ditt MP4-kodade innehåll med anpassningsbar bithastighet i direktuppspelningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming) direkt när du så önskar, utan att du behöver lagra på förhand paketerade versioner av vart och ett av dessa direktuppspelningsformat.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**.

Starta slutpunkten för direktuppspelning genom att göra följande:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. I fönstret Inställningar klickar du på Slutpunkter för direktuppspelning.
3. Klicka på den slutpunkt för direktuppspelning som är standard.

    Fönstret INFORMATION OM DEN SLUTPUNKT FÖR DIREKTUPPSPELNING SOM ÄR STANDARD visas.

4. Klicka på ikonen Start.
5. Klicka på knappen Spara för att spara ändringarna.

## <a id="connect"></a>Ansluta till Media Services-konto med REST API

Information om hur du ansluter till AMS API finns i [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a id="upload"></a>Skapa en ny tillgång och överföra en videofil med REST API

I Media Services överför du dina digitala filer till en tillgång. Den **tillgången** entiteten kan innehålla video, ljud, bilder, miniatyrsamlingar, textspår och dold textning filer (och metadata om dessa filer.)  När filerna har överförts till tillgången, lagras innehållet på ett säkert sätt i molnet för ytterligare bearbetning och strömning.

En av de värden som du måste ange när du skapar en tillgång är alternativ för skapande av tillgång. Den **alternativ** egenskapen är ett uppräkningsvärde som beskriver alternativ för kryptering som du kan skapa en tillgång med. Ett giltigt värde är ett av värdena i listan nedan, inte en kombination av värden från den här listan:

* **Ingen** = **0** – Ingen kryptering används. När du använder det här alternativet skyddas inte innehållet under överföring eller i vila i lagring.
    Om du planerar att leverera en MP4 med progressivt nedladdning ska du använda det här alternativet.
* **StorageEncrypted** = **1** – krypterar innehållet lokalt med hjälp av AES-256-bitarskryptering och överför dem till Azure Storage var den lagras krypterat i vila. Tillgångar som skyddas med Lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Lagringskryptering används i första hand när du vill skydda indatamediefiler av hög kvalitet med stark kryptering i vila på disk.
* **CommonEncryptionProtected** = **2** – Använd det här alternativet om du överför innehåll som redan har krypterats och skyddats med vanlig kryptering eller PlayReady DRM (till exempel Smooth Streaming skyddas med PlayReady DRM).
* **EnvelopeEncryptionProtected** = **4** – Använd det här alternativet om du överför HLS som krypterats med AES. Filerna måste ha kodats och krypterats av Transform Manager.

### <a name="create-an-asset"></a>Skapa en tillgång
En tillgång är en behållare för flera typer eller uppsättningar med objekt i Media Services, inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning. I REST-API kräver skapa en tillgång skicka POST-begäran till Media Services och placera all egenskapsinformation om din tillgång i begärandetexten.

I följande exempel visas hur du skapar en tillgång.

**HTTP-begäran**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**HTTP-svar**

Om detta lyckas, returneras följande:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Skapa en AssetFile
Den [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entitet representerar en video- eller ljudinnehåll fil som lagras i en blob-behållare. En resursfil är alltid kopplad till en tillgång och en tillgång kan innehålla en eller flera AssetFiles. Media Services Encoder aktiviteten misslyckas om ett objekt för tillgången-filen inte är associerad med en digital fil i en blobbehållare.

När du har överfört din digitala media-fil till en blobbehållare kan du använda den **sammanfoga** HTTP-förfrågan att uppdatera AssetFile med information om din mediefil (som visas längre fram i avsnittet).

**HTTP-begäran**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-svar**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Skapa AccessPolicy med behörighet att skriva
Innan du laddar upp filer till blob-lagring, ange principen rättigheter för att skriva till en tillgång. Gör detta genom att publicera en HTTP-begäran till entitetsuppsättning AccessPolicies. Definiera ett DurationInMinutes värde när de skapas eller meddelandet 500 Internt fel i svaret. Läs mer på AccessPolicies [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

I följande exempel visas hur du skapar en AccessPolicy:

**HTTP-begäran**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**HTTP-svar**

Om detta lyckas, returneras följande svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Hämta URL

Skapa en SAS-lokaliserare för att ta emot den faktiska URL. Lokaliserare definiera start- och typ av anslutningens slutpunkt för klienter som vill komma åt filer i en tillgång. Du kan skapa flera positionerare entiteter för en viss AccessPolicy och tillgången par att hantera olika klientbegäranden och behov. Var och en av dessa positionerare använder StartTime-värdet plus DurationInMinutes värdet för AccessPolicy för att avgöra hur lång tid som en URL som kan användas. Mer information finns i [positionerare](https://docs.microsoft.com/rest/api/media/operations/locator).

En SAS-URL har följande format:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Vissa förutsättningar gäller:

* Du kan inte ha fler än fem unik positionerare som är associerade med en given tillgång i taget. 
* Om du vill ladda upp dina filer omedelbart ska du ange StartTime-värdet till fem minuter före aktuell tid. Det beror på att det kan finnas klockan skeva mellan klientdatorn och Media Services. Dessutom StartTime-värdet måste vara i formatet DateTime: ÅÅÅÅ-MM-: ssZ (till exempel ”2014-05-23T17:53:50Z”).    
* Det kan finnas en 30 – 40 andra fördröjning när en positionerare skapas när den är tillgänglig för användning. Det här problemet gäller både [SAS-Webbadressen](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) och ursprung lokaliserare.

I följande exempel visar hur du skapar en SAS-URL: en lokaliserare som definieras av Type-egenskapen i begärandetexten (”1” för en SAS-lokaliserare) och ”2” för en lokaliserare för ursprung på begäran. Den **sökväg** -egenskapen som returneras innehåller den URL som du måste använda för att överföra din fil.

**HTTP-begäran**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP-svar**

Om detta lyckas, returneras följande svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Överföra en fil till ett blob storage-behållare
När du har AccessPolicy och positionerare som anger laddas den faktiska filen upp till en Azure blob storage-behållare med hjälp av Azure Storage REST-API: er. Du måste överföra filerna som blockblobar. Sidblobar stöds inte av Azure Media Services.  

> [!NOTE]
> Du måste lägga till filnamnet för den fil du vill ladda upp till lokaliseraren **sökväg** värdet som tas emot i föregående avsnitt. Till exempel, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .
>
>

Mer information om hur du arbetar med Azure storage-blobbar finns i [REST-API för Blob Service](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Uppdatera AssetFile
Nu när du har överfört filen kan du uppdatera informationen om FileAsset storlek (och andra). Exempel:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-svar**

Om detta lyckas, returneras följande:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Ta bort positionerare och AccessPolicy
**HTTP-begäran**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-svar**

Om detta lyckas, returneras följande:

    HTTP/1.1 204 No Content
    ...

**HTTP-begäran**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP-svar**

Om detta lyckas, returneras följande:

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>Koda källfilen till en uppsättning MP4-filer

När du matar in tillgångar i Media Services, media kan kodas, användas med transmux, förses med vattenstämpel och så vidare innan de skickas till klienter. Dessa aktiviteter schemaläggs och körs mot flera bakgrundsrollinstanser för höga prestanda och tillgänglighet. De här aktiviteterna kallas jobb och varje jobb består av atomiska uppgifter som gör det faktiska arbetet i tillgångsfilen (Mer information finns i [jobbet](https://docs.microsoft.com/en-us/rest/api/media/operations/job), [uppgift](https://docs.microsoft.com/en-us/rest/api/media/operations/task) beskrivningar).

Som tidigare nämnts, när du arbetar med Azure Media Services som ett av de vanligaste scenarierna att leverera strömning med anpassad bithastighet till dina klienter. Media Services kan dynamiskt Paketera en uppsättning MP4-filer till något av följande format: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

Följande avsnitt visar hur du skapar ett jobb som innehåller ett kodningsjobb. Uppgiften anger att omkodning av mezzaninfilen till en uppsättning med anpassningsbar bithastighet MP4s med **Media Encoder Standard**. Avsnittet visar också hur du övervakar jobbet bearbetningsförlopp. När jobbet har slutförts, skulle du kunna skapa lokaliserare som behövs för att få åtkomst till dina tillgångar.

### <a name="get-a-media-processor"></a>Hämta en medieprocessor
I Media Services är en komponent som hanterar en specifik bearbetning aktivitet, till exempel kodning, Formatkonvertering, kryptering eller dekryptering medieinnehåll i en medieprocessor. För kodningsjobbet som visas i den här självstudien, vi använder Media Encoder Standard.

Följande kod begär den kodaren id.

**HTTP-begäran**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-svar**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Skapa ett jobb
Varje jobb kan ha en eller flera aktiviteter beroende på vilken typ av bearbetning som du vill utföra. Via REST-API kan du skapa jobb och deras relaterade uppgifter i ett av två sätt: uppgifter kan vara definierats internt via navigeringsegenskapen uppgifter på jobbet entiteter eller OData-batch-bearbetning. Media Services SDK använder batch-bearbetning. För läsbarhet av kodexemplen i den här artikeln är dock uppgifter definierats internt. Information om batchbearbetning finns [Open Data Protocol (OData) gruppbearbetning](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

I följande exempel visas hur du skapar och publicerar ett jobb med en åtgärd ange för att koda en video med en viss upplösning och kvalitet. Avsnittet följande dokumentation innehåller listan över alla de [uppgift förinställningar](http://msdn.microsoft.com/library/mt269960) stöds av Media Encoder Standard processorn.  

**HTTP-begäran**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**HTTP-svar**

Om detta lyckas, returneras följande svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Det finns några viktiga saker att tänka på varje jobb begäran:

* TaskBody egenskaper måste använda literal XML för att ange hur många indata eller utdata tillgångar som används av aktiviteten. Uppgiften artikeln innehåller XML-schemadefinitionen för XML-filen.
* I definitionen av TaskBody varje inre värde för <inputAsset> och <outputAsset> måste anges som JobInputAsset(value) eller JobOutputAsset(value).
* En aktivitet kan ha flera utdataresultat. En JobOutputAsset(x) kan bara användas en gång som utdata för en aktivitet i ett jobb.
* Du kan ange JobInputAsset eller JobOutputAsset som en indatatillgången för en aktivitet.
* Uppgifter måste inte utgör en cykel.
* Värdeparametern som du skickar till JobInputAsset eller JobOutputAsset representerar indexvärdet för en tillgång. De faktiska resurser har definierats i navigeringsegenskaper InputMediaAssets och OutputMediaAssets på entiteten jobbdefinitionen.

> [!NOTE]
> Eftersom Media Services bygger på OData v3, enskilda tillgångar i InputMediaAssets och OutputMediaAssets navigering egenskapssamlingar refereras via en ”__metadata: uri” namn / värde-par.
>
>

* InputMediaAssets mappas till en eller flera resurser som du har skapat i Media Services. OutputMediaAssets skapas av systemet. De hänvisar inte till en befintlig tillgång.
* OutputMediaAssets kan namnges med attributet assetName. Om det här attributet finns inte, så är namnet på OutputMediaAsset är det inre textvärdet för den <outputAsset> elementet är med ett suffix för jobbets namn-värde eller jobb-Id-värdet (i de fall där egenskapen Name inte är definierat). Till exempel om du anger ett värde för assetName till ”exempel” kan skulle sedan OutputMediaAsset namnegenskapen anges till ”exempel”. Men om du inte har angett ett värde för assetName, men angetts Jobbnamnet för att ”NewJob”, OutputMediaAsset namnet skulle vara ”JobOutputAsset (värde) _NewJob”.

    I följande exempel visas hur du ställer in attributet assetName:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Aktivera aktiviteten länkning:

  * Ett jobb måste ha minst två aktiviteter
  * Det måste finnas minst en aktivitet vars indata är utdata från en annan aktivitet i jobbet.

Mer information finns i [skapar ett jobb för Encoding med Media Services REST API](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Övervaka Bearbetningsförlopp
Du kan hämta jobbets status med hjälp av egenskapen State som visas i följande exempel:

**HTTP-begäran**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**HTTP-svar**

Om detta lyckas, returneras följande svar:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Avbryta ett jobb
Media Services kan du avbryta jobb som körs via CancelJob-funktion. Det här anropet returnerar en 400 felkoden om du försöker avbryta ett jobb när dess tillstånd har avbrutits, avbryta, fel eller är klar.

I följande exempel visas hur du anropar CancelJob.

**HTTP-begäran**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Om detta lyckas, returneras en 204-svarskod med Ingen meddelandetext.

> [!NOTE]
> Du måste URL-koda jobb-id (normalt nb:jid:UUID: somevalue) vid sändning av den i som en parameter till CancelJob.
>
>

### <a name="get-the-output-asset"></a>Hämta utdatatillgången
Följande kod visar hur du begär utdatatillgången Id.

**HTTP-begäran**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-svar**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a id="publish_get_urls"></a>Publicera tillgången och hämta strömning och progressiv nedladdning URL: er med REST API

Om du vill strömma eller hämta en tillgång behöver du först ”publicera” den genom att skapa en positionerare. Positionerare ger åtkomst till filer som finns i tillgången. Media Services stöder två typer av positionerare: OnDemandOrigin-positionerare som används för att strömma media (till exempel MPEG DASH, HLS eller Smooth Streaming) och Access Signature (SAS)-positionerare som används för att hämta mediefiler. 

När du har skapat positionerna kan du skapa de webbadresser som används för att strömma eller hämta dina filer.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**.

En strömmande URL för Smooth Streaming har följande format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

En strömmande URL för HLS har följande format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

En strömmande URL för MPEG DASH har följande format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

En SAS-URL som används för att hämta filer har följande format:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Det här avsnittet visar hur du utför följande uppgifter krävs för att ”publicera” din tillgångar.  

* Skapa AccessPolicy med läsbehörighet
* Skapa en SAS-Webbadress för nedladdning av innehåll
* Skapa en ursprung-URL för strömning av innehåll

### <a name="creating-the-accesspolicy-with-read-permission"></a>Skapa AccessPolicy med läsbehörighet
Innan du ladda ned eller strömmande mediainnehåll, definiera en AccessPolicy med läsbehörighet och skapa lämpliga positionerare entiteten som anger vilken typ av leveransmekanismen som du vill aktivera för dina klienter. Mer information om egenskaper som är tillgängliga finns i [AccessPolicy Entitetsegenskaper](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

I följande exempel visas hur du anger AccessPolicy för Läs-och skrivrättigheter för en given tillgång.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Om detta lyckas, returneras 201 koden som beskriver entiteten AccessPolicy som du skapade. Du kan sedan använda AccessPolicy Id tillsammans med tillgångs-Id för den tillgång som innehåller filen som du vill leverera (till exempel en utdatatillgången) för att skapa lokaliserare entiteten.

> [!NOTE]
> Detta grundläggande arbetsflöde är detsamma som att ladda upp en fil när mata in en tillgång (som beskrevs tidigare i det här avsnittet). T.ex. ladda upp filer, om du (eller klienter) måste du komma åt dina filer omedelbart, ange dessutom StartTime-värdet till fem minuter före aktuell tid. Den här åtgärden är nödvändigt eftersom det kan finnas klockan skeva mellan klienten och Media Services. StartTime-värdet måste vara i formatet DateTime: ÅÅÅÅ-MM-: ssZ (till exempel ”2014-05-23T17:53:50Z”).
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Skapa en SAS-Webbadress för nedladdning av innehåll
Följande kod visar hur du får en URL som kan användas för att ladda ned en mediefil har skapat och överfört tidigare. AccessPolicy har läs behörigheter och positionerare sökvägen refererar till en SAS nedladdnings-URL.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Om detta lyckas, returneras följande svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

Den returnerade **sökväg** egenskapen innehåller SAS-Webbadressen.

> [!NOTE]
> Om du hämtar storage krypterat innehåll måste du manuellt dekryptera den innan du gör den eller använda Storage dekryptering MediaProcessor i en aktivitet för bearbetning av bearbetade utdatafiler i klartext till en OutputAsset och sedan ladda ned från tillgången. Mer information om bearbetningen finns i Skapa ett jobb för kodning med Media Services REST-API. SAS URL: en lokaliserare kan inte uppdateras när de väl har skapats. Du kan exempelvis återanvända samma lokaliserare med ett uppdaterat StartTime-värde. Detta beror på det sättet SAS URL: er skapas. Om du vill komma åt en tillgång för att ladda ned när en positionerare har upphört att gälla, måste du skapa en ny med en ny StartTime.
>
>

### <a name="download-files"></a>Hämta filer
När du har AccessPolicy och positionerare som angetts kan du ladda ned filer med hjälp av Azure Storage REST-API: er.  

> [!NOTE]
> Du måste lägga till filnamnet för den fil som du vill ladda ned till lokaliseraren **sökväg** värdet som tas emot i föregående avsnitt. Till exempel, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .
>
>

Mer information om hur du arbetar med Azure storage-blobbar finns i [REST-API för Blob Service](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Till följd av kodningsjobbet som du utförde tidigare (kodning till anpassningsbar MP4-uppsättningar), har du flera MP4-filer som du kan hämta progressivt. Exempel:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Skapa en strömnings-URL för strömning av innehåll
Följande kod visar hur du skapar en positionerare för direktuppspelning URL:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Om detta lyckas, returneras följande svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

För att strömma en Smooth Streaming ursprung URL i en strömmande media player, måste du lägga till sökvägen egenskap med namnet på Smooth Streaming-manifestfilen, följt av ”/ manifest”.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

För att strömma HLS, lägger du till (format = m3u8-aapl) när den ”/ manifest”.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

För att strömma MPEG DASH, lägger du till (format = mpd-time-csf) när den ”/ manifest”.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Spela upp ditt innehåll
Strömma videon med hjälp av [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Om du vill testa den progressiva nedladdningen, klistra du in en URL i en webbläsare (till exempel Internet Explorer, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Nästa steg: sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

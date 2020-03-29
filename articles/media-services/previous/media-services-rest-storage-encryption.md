---
title: Kryptera ditt innehåll med lagringskryptering med AMS REST API
description: Läs om hur du krypterar ditt innehåll med lagringskryptering med AMS REST API:er.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 2a5ef1837375cc395a871f9a9860fa8bde572a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773590"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Kryptera ditt innehåll med lagringskryptering 

> [!NOTE]
> Du behöver ett Azure-konto för att genomföra kursen. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).   > Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)
>   

Vi rekommenderar starkt att kryptera ditt innehåll lokalt med AES-256-bitars kryptering och sedan ladda upp det till Azure Storage där det lagras krypterat i vila.

Den här artikeln innehåller en översikt över AMS-lagringskryptering och visar hur du laddar upp det lagringskrypterade innehållet:

* Skapa en innehållsnyckel.
* Skapa en tillgång. Ange AssetCreationOption till StorageEncryption när du skapar tillgången.
  
     Krypterade tillgångar är associerade med innehållsnycklar.
* Länka innehållsnyckeln till tillgången.  
* Ange krypteringsrelaterade parametrar för AssetFile-entiteterna.

## <a name="considerations"></a>Överväganden 

Om du vill leverera en lagringskrypterad tillgång måste du konfigurera tillgångens leveransprincip. Innan tillgången kan strömmas tar strömningsservern bort lagringskrypteringen och strömmar ditt innehåll med den angivna leveransprincipen. Mer information finns i [Konfigurera principer för tillgångsleverans](media-services-rest-configure-asset-delivery-policy.md).

När du öppnar entiteter i Media Services måste du ange specifika rubrikfält och värden i HTTP-begäranden. Mer information finns i [Installationsprogrammet för REST API Development för Media Services](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Kryptering på lagringssidan

|Krypteringsalternativ|Beskrivning|Media Services v2|Media Services v3|
|---|---|---|---|
|Kryptering av lagring av Media Services|AES-256-kryptering, nyckel som hanteras av Media Services|Stöds<sup>(1)</sup>|Stöds inte<sup>(2)</sup>|
|[Kryptering av lagringstjänster för data i vila](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel som hanteras av Azure eller av kunden|Stöds|Stöds|
|[Kryptering på klientsidan för lagring](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Kryptering på klientsidan som erbjuds av Azure-lagring, nyckel som hanteras av kunden i Key Vault|Stöds inte|Stöds inte|

<sup>1</sup> Medan Media Services stöder hantering av innehåll i klar/utan någon form av kryptering, rekommenderas inte detta.

<sup>2</sup> I Media Services v3 stöds lagringskryptering (AES-256-kryptering) endast för bakåtkompatibilitet när dina tillgångar skapades med Media Services v2. Vilket innebär v3 fungerar med befintliga lagring krypterade tillgångar men kommer inte att tillåta skapandet av nya.

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API:et finns [i Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Översikt över lagringskryptering
AMS-lagringskrypteringen tillämpar kryptering i **AES-CTR-läge** för hela filen.  AES-CTR-läge är ett blockchiffer som kan kryptera godtyckliga längddata utan att behöva utfyllnad. Det fungerar genom att kryptera ett motblock med AES-algoritmen och sedan XOR-ing utdata av AES med data för att kryptera eller dekryptera.  Det motblock som används skapas genom att värdet för InitieringVector kopieras till byte 0 till 7 av räknarvärdet och byte 8 till 15 av räknarvärdet är noll. Av 16-byte counter block, bytes 8 till 15 (det villa, de minst betydande byte) används som en enkel 64-bitars osignerad heltal som ökas med en för varje efterföljande block av data som bearbetas och hålls i nätverksbyte ordning. Om detta heltal når det maximala värdet (0xFFFFFFFFFFFFFFFF), sedan öka det återställer blockräknaren till noll (byte 8 till 15) utan att påverka de andra 64 bitarna av räknaren (det villma, byte 0 till 7).   För att upprätthålla säkerheten för krypteringen i AES-CTR-läget ska InitieringVector-värdet för en viss nyckelidentifierare för varje innehållsnyckel vara unikt för varje fil och filerna ska vara kortare än 2^64 block.  Det här unika värdet är att säkerställa att ett räknarvärde aldrig återanvänds med en viss nyckel. Mer information om CTR-läget finns på den [här wiki-sidan](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (wiki-artikeln använder termen "Nonce" i stället för "InitializationVector").

Använd **lagringskryptering** för att kryptera ditt rensa innehåll lokalt med AES-256-bitars kryptering och sedan ladda upp det till Azure Storage där det lagras krypterat i vila. Tillgångar som skyddas med lagringskryptering okrypteras automatiskt och placeras i ett krypterat filsystem före kodning och krypteras eventuellt igen innan de laddas upp som en ny utdatatillgång. Det primära användningsfallet för lagringskryptering är när du vill skydda dina högkvalitativa indatamediefiler med stark kryptering i vila på disken.

För att kunna leverera en lagringskrypterad tillgång måste du konfigurera tillgångens leveransprincip så att Media Services vet hur du vill leverera ditt innehåll. Innan din tillgång kan strömmas tar strömningsservern bort lagringskrypteringen och strömmar ditt innehåll med den angivna leveransprincipen (till exempel AES, vanlig kryptering eller ingen kryptering).

## <a name="create-contentkeys-used-for-encryption"></a>Skapa ContentKeys som används för kryptering
Krypterade tillgångar är associerade med lagringskrypteringsnycklar. Skapa innehållsnyckeln som ska användas för kryptering innan du skapar tillgångsfilerna. I det här avsnittet beskrivs hur du skapar en innehållsnyckel.

Följande är allmänna steg för att generera innehållsnycklar som du associerar med tillgångar som du vill ska krypteras. 

1. För lagringskryptering genererar du slumpmässigt en AES-nyckel på 32 byte. 
   
    32-byte AES-nyckeln är innehållsnyckeln för din tillgång, vilket innebär att alla filer som är associerade med den tillgången måste använda samma innehållsnyckel under dekryptering. 
2. Anropa metoderna [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) och [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) för att få rätt X.509-certifikat som måste användas för att kryptera innehållsnyckeln.
3. Kryptera innehållsnyckeln med den offentliga nyckeln i X.509-certifikatet. 
   
   Media Services .NET SDK använder RSA med OAEP när krypteringen.  Du kan se ett .NET-exempel i [funktionen EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Skapa ett kontrollsummavärde som beräknas med hjälp av nyckelidentifieraren och innehållsnyckeln. I följande .NET-exempel beräknas kontrollsumman med hjälp av GUID-delen av nyckelidentifieraren och nyckeln för rensa innehåll.

    ```csharp
            public static string CalculateChecksum(byte[] contentKey, Guid keyId)
            {
                const int ChecksumLength = 8;
                const int KeyIdLength = 16;

                byte[] encryptedKeyId = null;

                // Checksum is computed by AES-ECB encrypting the KID
                // with the content key.
                using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
                {
                    rijndael.Mode = CipherMode.ECB;
                    rijndael.Key = contentKey;
                    rijndael.Padding = PaddingMode.None;

                    ICryptoTransform encryptor = rijndael.CreateEncryptor();
                    encryptedKeyId = new byte[KeyIdLength];
                    encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
                }

                byte[] retVal = new byte[ChecksumLength];
                Array.Copy(encryptedKeyId, retVal, ChecksumLength);

                return Convert.ToBase64String(retVal);
            }
    ```

5. Skapa innehållsnyckeln med **värdena EncryptedContentKey** (konverterad till bas64-kodad sträng), **ProtectionKeyId,** **ProtectionKeyType,** **ContentKeyType**och **Checksum-värden** som du har tagit emot i tidigare steg.

    För lagringskryptering bör följande egenskaper inkluderas i begärandetexten.

    Begär brödtextegenskap    | Beskrivning
    ---|---
    Id | ContentKey-ID:n genereras med följande format, "nb:kid:UUID:\<NEW GUID>".
    ContentKeyType | Innehållsnyckeltypen är ett heltal som definierar nyckeln. För lagringskrypteringsformat är värdet 1.
    EncryptedContentKey | Vi skapar ett nytt innehållsnyckelvärde som är ett värde på 256 bitar (32 byte). Nyckeln krypteras med hjälp av det X.509-certifikat för lagringskryptering som vi hämtar från Microsoft Azure Media Services genom att köra en HTTP GET-begäran för GetProtectionKeyId- och GetProtectionKey-metoderna. Som ett exempel, se följande .NET-kod: **metoden EncryptSymmetricKeyData** som definieras [här](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Detta är skyddsnyckel-ID:n för X.509-certifikatet för lagringskryptering som användes för att kryptera vår innehållsnyckel.
    ProtectionKeyType | Det här är krypteringstypen för den skyddsnyckel som användes för att kryptera innehållsnyckeln. Det här värdet är StorageEncryption(1) för vårt exempel.
    Kontrollsumma |MD5 beräknade kontrollsumman för innehållsnyckeln. Det beräknas genom att kryptera innehålls-ID med innehållsnyckeln. Exempelkoden visar hur du beräknar kontrollsumman.


### <a name="retrieve-the-protectionkeyid"></a>Hämta ProtectionKeyId
I följande exempel visas hur du hämtar ProtectionKeyId, ett certifikat tumavtryck, för certifikatet som du måste använda när du krypterar innehållsnyckeln. Gör det här steget för att se till att du redan har rätt certifikat på datorn.

Begäran:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

Svar:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Hämta ProtectionKey för ProtectionKeyId
I följande exempel visas hur du hämtar X.509-certifikatet med hjälp av ProtectionKeyId som du fick i föregående steg.

Begäran:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net

Svar:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>Skapa innehållsnyckeln
När du har hämtat X.509-certifikatet och använt dess offentliga nyckel för att kryptera innehållsnyckeln skapar du en **ContentKey-entitet** och anger dess egenskapsvärden därefter.

Ett av de värden som du måste ange när du skapar innehållsnyckeln är typen. När du använder lagringskryptering ska värdet anges till "1". 

I följande exempel visas hur du skapar en **ContentKey** med en **ContentKeyType-uppsättning** för lagringskryptering ("1") och **ProtectionKeyType-uppsättningen** till "0" för att ange att skyddsnyckel-ID:t är tumavtrycket x.509-certifikatet.  

Förfrågan

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }

Svar:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Skapa en tillgång
I följande exempel visas hur du skapar en tillgång.

**HTTP-begäran**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**HTTP-svar**

Om det lyckas returneras följande svar:

    HTP/1.1 201 Created
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
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

## <a name="associate-the-contentkey-with-an-asset"></a>Associera ContentKey med en tillgång
När du har skapat ContentKey associerar du den med din tillgång med hjälp av $links-åtgärden, som visas i följande exempel:

Begäran:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Svar:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Skapa en tillgångsfil
[AssetFile-entiteten](https://docs.microsoft.com/rest/api/media/operations/assetfile) representerar en video- eller ljudfil som lagras i en blob-behållare. En tillgångsfil är alltid associerad med en tillgång och en tillgång kan innehålla en eller flera tillgångsfiler. Aktiviteten Media Services Encoder misslyckas om ett tillgångsfilobjekt inte är associerat med en digital fil i en blob-behållare.

**AssetFile-förekomsten** och den faktiska mediefilen är två olika objekt. AssetFile-instansen innehåller metadata om mediefilen, medan mediefilen innehåller det faktiska medieinnehållet.

När du har laddat upp din digitala mediefil till en blob-behållare använder du **APPENS HTTP-begäran** om att uppdatera AssetFile med information om mediefilen (visas inte i den här artikeln). 

**HTTP-begäran**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
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
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }

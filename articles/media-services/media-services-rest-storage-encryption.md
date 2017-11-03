---
title: "Kryptera ditt innehåll med lagringskryptering med AMS REST API"
description: "Lär dig mer om att kryptera innehållet med lagringskryptering med AMS REST API: er."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: 1979f5bf5e8cab88dab5fba49018afacf24504b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Kryptera din innehåll med lagringskryptering

Vi rekommenderar starkt att kryptera innehållet lokalt med hjälp av AES 256 bitarskryptering och överföra den till Azure Storage där den lagras krypterat i vila.

Den här artikeln ger en översikt över AMS lagringskryptering och visar hur du överför innehållet lagringskrypterad:

* Skapa en innehållsnyckel.
* Skapa en tillgång. Ange AssetCreationOption till StorageEncryption när du skapar tillgången.
  
     Krypterade tillgångar måste associeras med innehåll nycklar.
* Länka innehållsnyckeln till tillgången.  
* Ange krypteringen relaterade parametrar på AssetFile entiteter.

## <a name="considerations"></a>Överväganden 

Om du vill leverera en krypterad tillgång lagring måste du konfigurera den tillgångsleveransprincip. Innan din tillgång kan strömmas strömmande server tar du bort krypteringen lagring och strömmar ditt innehåll med hjälp av angivna leveransprincipen. Mer information finns i [konfigurerar principerna för Tillgångsleverans](media-services-rest-configure-asset-delivery-policy.md).

Vid åtkomst till entiteter i Media Services måste du ange specifika namn på huvudfält och värden i HTTP-begäranden. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md). 

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS API: et finns [åtkomst till Azure Media Services-API med Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>När du har anslutit till https://media.windows.net, får du en 301 omdirigering att ange en annan Media Services-URI. Du måste göra följande anrop till en ny URI.

## <a name="storage-encryption-overview"></a>Översikt över kryptering
Lagringskryptering AMS gäller **AES CTR** läge kryptering hela filen.  AES-CTR läge är ett blockchiffer som kan kryptera godtycklig längddata utan behov av utfyllnad. Den fungerar genom att kryptera en räknare block med AES-algoritmen och XOR-ing utdata från AES med data för att kryptera eller dekryptera.  Räknaren-block som används har skapats genom att kopiera värdet för InitializationVector till byte 0 till 7 för räknarvärdet och byte 8 och 15 räknarens värde är angivet till noll. Räknaren-blockets 16 byte används byte 8 och 15 (d.v.s. minst viktiga byte) som en enkel 64-bitars osignerat heltal som ökar med ett för varje efterföljande datablock bearbetas och sparas i nätverket byte-ordning. Observera att om heltalet når det högsta värdet (0xFFFFFFFF) öka det återställer räknaren block med noll (byte 8 och 15) utan att påverka andra 64 bitar för räknaren (d.v.s. byte 0 till 7).   För att upprätthålla säkerheten på AES CTR läge kryptering InitializationVector värdet för en viss nyckelidentifierare för varje innehållsnyckeln vara unikt för varje fil och filer bör vara mindre än 2 ^ 64 block i längd.  Detta är att säkerställa att ett värde för prestandaräknaren återanvänds aldrig med en viss nyckel. Mer information om CTR läge finns [wiki-sida](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (wiki-artikeln använder termen ”temporärt ID” i stället för ”InitializationVector”).

Använd **Lagringskryptering** för att kryptera innehållet lokalt med hjälp av AES 256-bitars kryptering och överföra den till Azure Storage där den lagras krypterat i vila. Tillgångar som skyddas med lagringskryptering avkrypteras automatiskt och placeras i ett krypterat filsystem före kodning och kan krypteras igen innan de överförs tillbaka som en ny utdatatillgång. Det primära användningsfallet för lagringskryptering är när du vill skydda dina hög kvalitet inkommande mediefiler med stark kryptering i vila på disk.

För att kunna leverera en krypterad tillgång för lagring, måste du konfigurera den tillgångsleveransprincip så Media Services vet hur du vill leverera ditt innehåll. Innan din tillgång kan strömmas strömmande server tar du bort krypteringen lagring och strömmar ditt innehåll med hjälp av angivna leveransprincipen (till exempel AES, vanliga kryptering eller Ingen kryptering).

## <a name="create-contentkeys-used-for-encryption"></a>Skapa ContentKeys som används för kryptering
Krypterade tillgångar måste associeras med krypteringsnyckeln för lagring. Du måste skapa innehållsnyckeln som ska användas för kryptering innan du skapar tillgångsfiler. Det här avsnittet beskriver hur du skapar en innehållsnyckel.

Följande är allmänna steg för att generera innehåll nycklar som ska associeras med resurser som du vill ska vara krypterad. 

1. Generera slumpmässigt en 32-byte AES-nyckel för kryptering. 
   
    Det här är innehållsnyckeln för tillgången, vilket innebär att alla filer som hör till tillgången måste använda samma innehållsnyckeln under dekrypteringen. 
2. Anropa den [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) och [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) metoder för att få rätt X.509-certifikat som ska användas för att kryptera din innehållsnyckeln.
3. Kryptera din innehållsnyckeln med den offentliga nyckeln för X.509-certifikatet. 
   
   Media Services .NET SDK använder RSA med OAEP vid krypteringen.  Du kan se en .NET-exempel i den [EncryptSymmetricKeyData funktionen](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Skapa ett kontrollsummevärde beräknas med hjälp av nyckelidentifierare och innehållsnyckeln. I följande exempel .NET beräknar kontrollsumma använder GUID-del av Nyckelidentifieraren och avmarkera innehållsnyckeln.

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

1. Skapa innehållsnyckel med den **EncryptedContentKey** (konverteras till base64-kodad sträng) **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType**, och **kontrollsumma** värden som du har fått i föregående steg.

    Följande egenskaper ska tas med i begärandetexten för storage kryptering.

    Egenskapen för brödtext i begäran    | Beskrivning
    ---|---
    Id | ContentKey-Id som vi generera oss själva i följande format ”nb:kid:UUID:<NEW GUID>”.
    ContentKeyType | Detta är viktiga innehållstypen som ett heltal för den här nyckeln. Vi skickar värdet 1 för kryptering.
    EncryptedContentKey | Vi skapa ett nytt innehåll nyckelvärde som är en 256-bitars (32 byte)-värde. Nyckeln är krypterad med storage kryptering X.509-certifikat som vi hämta från Microsoft Azure Media Services genom att köra en HTTP GET-begäran för GetProtectionKeyId och GetProtectionKey metoder. Ett exempel finns i följande .NET-kod: den **EncryptSymmetricKeyData** metod som definieras [här](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Detta är skydd nyckel-id för storage kryptering X.509-certifikatet som användes för att kryptera våra innehållsnyckeln.
    ProtectionKeyType | Detta är krypteringstyp för skydd nyckeln som används för att kryptera innehållsnyckeln. Det här värdet är StorageEncryption(1) i vårt exempel.
    Kontrollsumma |Den beräknade kontrollsumman MD5 för innehållsnyckeln. Det beräknas genom att kryptera innehållet Id med innehållsnyckeln. Koden visar hur du beräkna kontrollsumman.


### <a name="retrieve-the-protectionkeyid"></a>Hämta ProtectionKeyId
I följande exempel visas hur du hämtar ProtectionKeyId, en certifikat-tumavtrycket för certifikatet måste du använda när du krypterar din innehållsnyckeln. Gör detta steg för att se till att du redan har rätt certifikat på din dator.

Begäran:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
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
I följande exempel visas hur du hämtar X.509-certifikatet med ProtectionKeyId du fick i föregående steg.

Begäran:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
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
När du har hämtats X.509-certifikat och används den offentliga nyckeln för att kryptera din innehållsnyckeln, skapar du en **ContentKey** entiteten och ange egenskapen värden i enlighet med detta.

Ett av de värden som du måste ange när skapa innehållet nyckeln är typen. Vid lagringskryptering är värdet '1'. 

I följande exempel visas hur du skapar en **ContentKey** med en **ContentKeyType** för lagringskryptering (”1”) och **ProtectionKeyType** värdet ”0” anger att skydd nyckeln Id är tumavtrycket för X.509-certifikatet.  

Förfrågan

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423034908&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=7eSLe1GHnxgilr3F2FPCGxdL2%2bwy%2f39XhMPGY9IizfU%3d
    x-ms-version: 2.11
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
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**HTTP-svar**

Om det lyckas, returneras följande:

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

## <a name="associate-the-contentkey-with-an-asset"></a>Koppla ContentKey till en tillgång
När du har skapat ContentKey, associera den med din tillgång med hjälp av $links-åtgärd som visas i följande exempel:

Begäran:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423141026&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=lDBz5YXKiWe5L7eXOHsLHc9kKEUcUiFJvrNFFSksgkM%3d
    x-ms-version: 2.11
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Svar:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Skapa en AssetFile
Den [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entiteten representerar en video eller ljud-fil som lagras i en blob-behållare. En resursfil är alltid associerat med en tillgång och en tillgång kan innehålla en eller många tillgångsfiler. Media Services Encoder uppgiften misslyckas om objekttypen tillgången filen inte är associerad med en digital fil i en blob-behållaren.

Observera att den **AssetFile** instansen och den faktiska mediefilen är två distinkta objekt. AssetFile-instans innehåller metadata om filen media när mediefilen innehåller faktiskt medieinnehåll.

När du har överfört din digitala media-fil till en blobbbehållare, ska du använda den **sammanfoga** HTTP-begäran om uppdatering av AssetFile med information om din mediefilen (visas inte i det här avsnittet). 

**HTTP-begäran**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
    x-ms-version: 2.11
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

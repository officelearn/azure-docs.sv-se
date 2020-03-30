---
title: Använd dynamisk AES-128-kryptering och nyckelleveranstjänsten | Microsoft-dokument
description: Det här avsnittet visar hur du krypterar dynamiskt med AES-128 och använder nyckelleveranstjänsten.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 01153317b49e4543f10faa517bce7bcc01ce22d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269736"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Använda dynamisk AES-128-kryptering och tjänsten för nyckelleverans
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Du kan använda Media Services för att leverera HTTP Live Streaming (HLS) och Smooth Streaming krypterat med AES med hjälp av 128-bitars krypteringsnycklar. Media Services tillhandahåller också den viktiga leveranstjänsten som levererar krypteringsnycklar till behöriga användare. Om du vill att Media Services ska kryptera en tillgång associerar du en krypteringsnyckel med tillgången och konfigurerar även auktoriseringsprinciper för nyckeln. När en stream begärs av en spelare använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med hjälp av AES-kryptering. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren har behörighet att hämta nyckeln utvärderar tjänsten de auktoriseringsprinciper som du har angett för nyckeln.

Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: antingen öppen eller tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten [simple web token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) och [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Mer information finns i [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-protect-with-aes128.md#configure_key_auth_policy).

För att dra fördel av dynamisk kryptering behöver du en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Du måste också konfigurera leveransprincipen för tillgången (beskrivs senare i den här artikeln). Sedan, baserat på det format som anges i strömnings-URL:en, kommer servern för strömning på begäran att säkerställa att dataströmmen levereras i det protokoll som du har valt. Därför behöver du bara lagra och betala för filerna i ett enda lagringsformat. Media Services skapar och hanterar lämpligt svar baserat på begäran från en klient.

Den här artikeln är användbar för utvecklare som arbetar med program som levererar skyddade medier. Artikeln visar hur du konfigurerar nyckelleveranstjänsten med auktoriseringsprinciper så att endast behöriga klienter kan ta emot krypteringsnycklar. Den visar också hur du använder dynamisk kryptering.

Information om hur du krypterar innehåll med Advanced Encryption Standard (AES) för leverans till Safari på macOS finns i [det här blogginlägget](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
En översikt över hur du skyddar medieinnehållet med AES-kryptering finns i [den här videon](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 dynamisk kryptering och nyckelleveranstjänstarbetsflöde

Utför följande allmänna steg när du krypterar dina tillgångar med AES med hjälp av mediatjänsternas nyckelleveranstjänst och även med hjälp av dynamisk kryptering:

1. [Skapa en tillgång och ladda upp filer till tillgången](media-services-protect-with-aes128.md#create_asset).

2. [Koda tillgången som innehåller filen till den adaptiva bitrate MP4-uppsättningen](media-services-protect-with-aes128.md#encode_asset).

3. [Skapa en innehållsnyckel och associera den med den kodade tillgången](media-services-protect-with-aes128.md#create_contentkey). I Media Services innehåller innehållsnyckeln tillgångens krypteringsnyckel.

4. [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-protect-with-aes128.md#configure_key_auth_policy). Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Klienten måste uppfylla principen innan innehållsnyckeln kan levereras till klienten.

5. [Konfigurera leveransprincipen för en tillgång](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Konfigurationen av leveransprincipen innehåller url:en för nyckelanskaffning och en initieringsvektor (IV). (AES-128 kräver samma IV för kryptering och dekryptering.) Konfigurationen innehåller även leveransprotokollet (till exempel MPEG-DASH, HLS, Smooth Streaming eller alla) och typen av dynamisk kryptering (till exempel kuvert eller ingen dynamisk kryptering).

    Du kan använda olika principer för varje protokoll för samma tillgång. Du kan till exempel tillämpa PlayReady-kryptering för Smooth/DASH och AES envelope för HLS. Alla protokoll som inte har definierats i en leveransprincip blockeras från direktuppspelning. (Ett exempel är om du lägger till en enskild princip som bara anger HLS som protokoll.) Undantaget är om du inte har någon princip för tillgångsleverans definierad alls. Därefter tillåts alla protokoll fritt.

6. [Skapa en OnDemand locator](media-services-protect-with-aes128.md#create_locator) för att få en strömmande URL.

Artikeln visar också [hur ett klientprogram kan begära en nyckel från nyckelleveranstjänsten](media-services-protect-with-aes128.md#client_request).

Du hittar ett fullständigt [.NET-exempel](media-services-protect-with-aes128.md#example) i slutet av artikeln.

Följande bild visar arbetsflödet som beskrevs tidigare. Här används aktuellt token för autentisering.

![Skydda med AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Resten av den här artikeln innehåller förklaringar, kodexempel och länkar till ämnen som visar hur du uppnår de uppgifter som tidigare beskrivits.

## <a name="current-limitations"></a>Aktuella begränsningar
Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort eventuella befintliga lokaliserare och skapa en ny.

## <a name="create-an-asset-and-upload-files-into-the-asset"></a><a id="create_asset"></a>Skapa en tillgång och ladda upp filer till tillgången
För att hantera, koda och strömma videor måste du först överföra innehållet till Media Services. När du har överfört innehållet lagras det på ett säkert sätt i molnet för vidare bearbetning och strömning. 

Utförlig information finns i [Överföra filer till ett Media Services-konto](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a><a id="encode_asset"></a>Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet
Med dynamisk kryptering skapar du en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Baserat på det angivna formatet i manifest- eller fragmentbegäran säkerställer den direktuppspelningsservern på begäran att du tar emot strömmen i det protokoll du valde. Då behöver du bara lagra och betala för filerna i ett enda lagringsformat. Media Services skapar och hanterar lämpligt svar baserat på begäran från en klient. Mer information finns i [Översikt över dynamisk paketering](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av standardtyp till i kontot med tillståndet ”Stoppad”. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet ”Körs”. 
>
>Om du vill använda dynamisk förpackning och dynamisk kryptering måste din tillgång innehålla en uppsättning adaptiva mp4-filer med adaptiv bithastighet eller adaptiv bithastighet.

Mer information om att koda finns i [Koda en tillgång med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Skapa en innehållsnyckel och associera den med den kodade tillgången
I Media Services innehåller innehållsnyckeln den nyckel som du vill kryptera en tillgång med.

Mer information finns i [Skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md).

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Konfigurera innehållsnyckelns auktoriseringsprincip
Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Klienten (spelaren) måste uppfylla principen innan nyckeln kan levereras till klienten. Behörighetsprincipen för innehållsnyckel kan ha en eller flera auktoriseringsbegränsningar, antingen öppna, tokenbegränsningar eller IP-begränsning.

Mer information finns i [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-dotnet-configure-content-key-auth-policy.md).

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Konfigurera en tillgångsleveransprincip
Konfigurera leveransprincipen för din tillgång. Tillgångsleveransprincipen innehåller bland annat följande:

* Url:en för nyckelförvärv. 
* Initieringsvektorn (IV) som ska användas för kuvertkrypteringen. AES-128 kräver samma IV för kryptering och dekryptering. 
* Protokollet för tillgångsleverans (t.ex. MPEG DASH, HLS, jämn direktuppspelning eller alla).
* Typ av dynamisk kryptering (till exempel AES-kuvert) eller ingen dynamisk kryptering. 

Mer information finns i [Konfigurera tillgångsleveransprincip](media-services-dotnet-configure-asset-delivery-policy.md).

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>Skapa en lokaliserare för OnDemand-strömning för att få en strömnings-URL
Du måste förse din användare med strömnings-URL:en för Smooth Streaming, DASH eller HLS.

> [!NOTE]
> Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort eventuella befintliga lokaliserare och skapa en ny.
> 
> 

Anvisningar för hur du publicerar en tillgång och skapar en strömnings-URL finns i [Skapa en strömnings-URL](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Hämta en testtoken
Hämta en testtoken baserat på de tokenbegränsningar som användes för nyckelauktoriseringsprincipen.

```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word "Bearer" in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Du kan använda [Azure Media Services Player](https://aka.ms/azuremediaplayer) för att testa din dataström.

## <a name="how-can-your-client-request-a-key-from-the-key-delivery-service"></a><a id="client_request"></a>Hur kan din klient begära en nyckel från nyckelleveranstjänsten?
I föregående steg har du skapat webbadressen som pekar på en manifestfil. Din klient måste extrahera nödvändig information från strömmande manifestfiler för att göra en begäran till nyckelleveranstjänsten.

### <a name="manifest-files"></a>Manifestfiler
Klienten måste extrahera URL-värdet (som också innehåller innehållsnyckel-ID [kid]) från manifestfilen. Klienten försöker sedan hämta krypteringsnyckeln från nyckelleveranstjänsten. Klienten måste också extrahera IV-värdet och använda det för att dekryptera strömmen. Följande utdrag visar elementet i `<Protection>` manifestet För jämn direktuppspelning:

```xml
    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>
```

När det gäller HLS är rotmanifestet indelat i segmentfiler. 

Rotmanifestet är till exempel: http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Den innehåller en lista med filnamn för segment.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Om du öppnar en av segmentfilerna i en\/textredigerare (t.ex. http: /test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), den innehåller #EXT-X-KEY. vilket indikerar att filen är krypterad.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Om du planerar att spela en AES-krypterad HLS i Safari, se [den här bloggen](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Begär nyckeln från nyckelleveranstjänsten

Följande kod visar hur du skickar en begäran till mediatjänster nyckelleveranstjänsten med hjälp av en nyckelleverans Uri (som extraherades från manifestet) och en token. (Den här artikeln förklarar inte hur man får SWTs från en STS.)

```csharp
    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }
```

## <a name="protect-your-content-with-aes-128-by-using-net"></a>Skydda ditt innehåll med AES-128 med hjälp av .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

1. Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md).

2. Lägg till följande element i appSettings, enligt definitionen i filen app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a name="example"></a><a id="example"></a>Exempel

Skriv över koden i Program.cs-filen med koden som visas i det här avsnittet.
 
>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika Media Services-principer (till exempel för Locator-princip eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använder samma behörigheter för dagar/åtkomst. Ett exempel är principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i avsnittet "Begränsa åtkomstprinciper" i [Hantera tillgångar och relaterade entiteter med Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Se till att uppdatera variablerna så att de pekar på mappar där dina indatafiler finns.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

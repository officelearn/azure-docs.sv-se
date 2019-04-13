---
title: Använda dynamisk kryptering för AES-128 och nyckelleveranstjänst | Microsoft Docs
description: Leverera ditt innehåll som krypteras med AES 128-bitars krypteringsnycklar med hjälp av Microsoft Azure Media Services. Media Services tillhandahåller också nyckelleveranstjänst som levererar krypteringsnycklar till behöriga användare. Det här avsnittet visar hur du dynamiskt kryptera med AES-128 och använda nyckelleveranstjänst.
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
ms.openlocfilehash: 6f76d6aed8dc5eed3dbf673b265c404f27b0536d
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526834"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Använda dynamisk kryptering för AES-128 och nyckelleveranstjänst
> [!div class="op_single_selector"]
> * [NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>  

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledningen från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Du kan använda Media Services för att leverera HTTP Live Streaming (HLS) och Smooth Streaming som krypterats med AES med hjälp av 128-bitars krypteringsnycklar. Media Services tillhandahåller också nyckelleveranstjänst som levererar krypteringsnycklar till behöriga användare. Om du vill kryptera en tillgång med medietjänster kan du associera en krypteringsnyckel med tillgången och även ange auktoriseringsprinciper för nyckeln. När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med hjälp av AES-kryptering. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. Tjänsten utvärderar auktoriseringsprinciper som du angav för nyckeln för att avgöra om användaren har behörighet att hämta nyckel.

Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: antingen öppen eller tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten [simple web token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) och [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Mer information finns i [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-protect-with-aes128.md#configure_key_auth_policy).

För att dra fördel av dynamisk kryptering behöver du en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Du måste också konfigurera leveransprincipen för tillgången (beskrivs senare i den här artikeln). Sedan, baserat på det format som anges i strömnings-URL:en, kommer servern för strömning på begäran att säkerställa att dataströmmen levereras i det protokoll som du har valt. Därför kan behöva du lagra och betala endast för filerna i ett enda lagringsformat. Media Services skapar och hanterar lämpligt svar baserat på begäran från en klient.

Den här artikeln är användbar för utvecklare som arbetar med appar som levererar media som är skyddade. Artikeln visar hur du konfigurerar nyckelleveranstjänst med auktoriseringsprinciper så att endast auktoriserade klienter kan få krypteringsnycklar. Den visar även hur du använder dynamisk kryptering.

Information om hur du krypterar innehåll med den Standard AES (Advanced Encryption) för leverans till Safari på macOS, finns i [det här blogginlägget](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
En översikt över hur du skyddar ditt medieinnehåll med AES-kryptering, se [videon](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 dynamisk kryptering och viktiga leverans-tjänstens arbetsflöde

Utför dessa allmänna steg när du krypterar dina tillgångar med AES med hjälp av Media Services-nyckelleveranstjänst och även använder dynamisk kryptering:

1. [Skapa en tillgång och överföra filer till tillgången](media-services-protect-with-aes128.md#create_asset).

2. [Koda den tillgång som innehåller filen för med anpassad bithastighet MP4-uppsättningen](media-services-protect-with-aes128.md#encode_asset).

3. [Skapa en innehållsnyckel och associera den med den kodade tillgången](media-services-protect-with-aes128.md#create_contentkey). I Media Services innehåller innehållsnyckeln tillgångens krypteringsnyckel.

4. [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-protect-with-aes128.md#configure_key_auth_policy). Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Klienten måste uppfylla principen innan innehållsnyckeln kan levereras till klienten.

5. [Konfigurera leveransprincipen för en tillgång](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Konfigurationen för leveransprincipen omfattar URL för viktiga anskaffning och en initieringsvektor (IV). (AES-128 kräver samma IV för kryptering och dekryptering.) Konfigurationen omfattar leveransprotokoll (till exempel MPEG-DASH, HLS, Smooth Streaming eller alla) och typen av dynamisk kryptering (till exempel kuvert eller ingen dynamisk kryptering).

    Du kan använda olika principer för varje protokoll för samma tillgång. Du kan till exempel tillämpa PlayReady-kryptering för Smooth/DASH och AES envelope för HLS. Alla protokoll som inte har definierats i en leveransprincip blockeras från strömning. (Ett exempel är om du lägger till en enda princip som endast anger HLS som protokoll). Ett undantag är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll fritt.

6. [Skapa en OnDemand-positionerare](media-services-protect-with-aes128.md#create_locator) att få en strömnings-URL.

Artikeln beskriver också [hur ett klientprogram kan begära en nyckel från nyckelleveranstjänst](media-services-protect-with-aes128.md#client_request).

Du hittar ett komplett [.NET-exempel](media-services-protect-with-aes128.md#example) i slutet av artikeln.

Följande bild visar arbetsflödet som beskrevs tidigare. Här används aktuellt token för autentisering.

![Skydda med AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Resten av den här artikeln innehåller förklaringar, kodexempel och länkar till ämnen som visar hur du utför de uppgifter som beskrivs ovan.

## <a name="current-limitations"></a>Aktuella begränsningar
Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort eventuella befintliga lokaliserare och skapa en ny.

## <a id="create_asset"></a>Skapa en tillgång och överföra filer till tillgången
För att hantera, koda och strömma videor måste du först överföra innehållet till Media Services. När du har överfört innehållet lagras det på ett säkert sätt i molnet för vidare bearbetning och strömning. 

Utförlig information finns i [Överföra filer till ett Media Services-konto](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Koda den tillgång som innehåller filen för anpassningsbar bithastighet MP4-uppsättningen
Med dynamisk kryptering skapar du en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Sedan, baserat på formatet som anges i manifestet- eller fragmentdel förfrågan, kommer servern för strömning på begäran till säkerställer att du får dataströmmen i protokollet som du har valt. Sedan behöver du bara lagra och betala för filerna i ett enda lagringsformat. Media Services skapar och hanterar lämpligt svar baserat på begäran från en klient. Mer information finns i [Översikt över dynamisk paketering](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av standardtyp till i kontot med tillståndet ”Stoppad”. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet ”Körs”. 
>
>Om du vill använda dynamisk paketering och dynamisk kryptering, måste dessutom din tillgång innehålla en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer.

Mer information om att koda finns i [Koda en tillgång med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Skapa en innehållsnyckel och associera den med den kodade tillgången
I Media Services innehåller innehållsnyckeln den nyckel som du vill kryptera en tillgång med.

Mer information finns i [Skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Konfigurera en auktoriseringsprincip för innehållsnyckeln
Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Klienten (spelaren) måste uppfylla principen innan nyckeln kan levereras till klienten. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar, antingen öppna, token begränsning eller IP-begränsning.

Mer information finns i [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Konfigurera en tillgångsleveransprincip
Konfigurera leveransprincipen för din tillgång. Tillgångsleveransprincipen innehåller bland annat följande:

* URL för viktiga anskaffning. 
* Initieringsvektorn (IV) ska användas för kuvert-kryptering. AES-128 kräver samma IV för kryptering och dekryptering. 
* Protokollet för tillgångsleverans (t.ex. MPEG DASH, HLS, jämn direktuppspelning eller alla).
* Typen av dynamisk kryptering (till exempel AES envelope) eller ingen dynamisk kryptering. 

Mer information finns i [Konfigurera tillgångsleveransprincip](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Skapa en lokaliserare för OnDemand-strömning för att få en strömnings-URL
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

Du kan använda [Azure Media Services Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html) för att testa din dataström.

## <a id="client_request"></a>Hur kan klienten begära en nyckel från nyckelleveranstjänst?
I föregående steg skapas den URL som pekar på en manifestfil. Klienten måste extrahera den nödvändiga informationen från strömmande manifestfiler och gör en begäran till nyckelleveranstjänst.

### <a name="manifest-files"></a>Manifestfiler
Klienten måste extrahera URL: en (som också innehåller innehåll nyckeln ID [barn]) värdet från manifestfilen. Klienten försöker hämta krypteringsnyckeln från nyckelleveranstjänst. Klienten måste också extrahera IV värdet och använda den för att dekryptera dataströmmen. I följande fragment visas den `<Protection>` element av Smooth Streaming-manifest:

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

När det gäller HLS eller bryts rot-manifestet i segment filer. 

Rot-manifestet är till exempel: http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Den innehåller en lista över segment filnamn.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Om du öppnar en av filerna segment i en textredigerare (till exempel http:\//test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), den innehåller #EXT-X-nyckel, vilket anger att filen är krypterad.

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
>Om du planerar att spela upp en AES-krypterad HLS i Safari Se [den här bloggen](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Begära nyckeln från nyckelleveranstjänst

Följande kod visar hur du skickar en begäran till nyckelleveranstjänst för Media Services med hjälp av en nyckel leverans Uri (som har extraherats från manifestet) och en token. (Den här artikeln inte beskriver hur du hämtar SWTs från en STS.)

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

2. Lägg till följande element i appSettings, som definieras i filen app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

### <a id="example"></a>Exempel

Skriv över koden i Program.cs-filen med koden som visas i det här avsnittet.
 
>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika Media Services-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter. Ett exempel är principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i avsnittet ”gränsen åtkomstprinciper” i [hantera tillgångar och relaterade entiteter med Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Se till att uppdatera variablerna så att de pekar på mappar där dina indatafiler finns.

[!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

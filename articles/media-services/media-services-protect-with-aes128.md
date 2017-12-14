---
title: "Med hjälp av AES-128 dynamisk kryptering och viktiga leveranser | Microsoft Docs"
description: "Microsoft Azure Media Services kan du leverera ditt innehåll som krypterats med AES 128-bitars krypteringsnycklar. Media Services tillhandahåller också nyckeln tjänsten som levererar krypteringsnycklar till behöriga användare. Det här avsnittet visar hur du dynamiskt kryptera med AES-128 och använda tjänsten nyckel."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 04c015a6fb6f9398e83b8717e869ba1d8e32a702
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>Med hjälp av AES-128 dynamisk kryptering och viktiga leveranser
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

## <a name="overview"></a>Översikt
> [!NOTE]
> Se den här [blogginlägget](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) för att kryptera innehållet med AES för leverans till **Safari på macOS**.
> Se [detta](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) video för en översikt över hur du skyddar dina Media-innehåll med AES-kryptering.
> 
> 

Microsoft Azure Media Services kan du leverera Http-Live-Streaming (HLS) och Smooth dataströmmar som krypterats med Standard AES (Advanced Encryption) (med 128-bitars krypteringsnycklar). Media Services tillhandahåller också nyckeln tjänsten som levererar krypteringsnycklar till behöriga användare. Om du vill använda för Media Services att kryptera en tillgång, måste du associera en krypteringsnyckel med tillgången och även konfigurera auktoriseringsprinciper för nyckeln. När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt innehåll med AES-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från tjänsten nyckel. Om du vill avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: öppen eller tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten [Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) och [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Mer information finns i [konfigurera innehållsnyckelns auktoriseringsprincip](media-services-protect-with-aes128.md#configure_key_auth_policy).

För att dra fördel av dynamisk kryptering behöver du en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Du måste också konfigurera leveransprincipen för tillgången (beskrivs senare i den här artikeln). Sedan, baserat på det format som anges i strömnings-URL, strömning på begäran-server ser till att dataströmmen levereras i det protokoll som du har valt. Därför behöver du bara lagra och betala för filerna i ett enda lagringsformat och Media Services-tjänsten skapar och hanterar lämplig respons baserat på begäranden från en klient.

Den här artikeln kan vara användbart för utvecklare som arbetar på appar som levererar media som är skyddade. Artikeln visar hur du konfigurerar tjänsten nyckel med auktoriseringsprinciper så att endast auktoriserade klienter kan få krypteringsnycklarna. Den visar även hur du använder dynamisk kryptering.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 dynamisk kryptering och viktiga Delivery Service arbetsflöde

Följande är allmänna steg som du behöver utföra när du krypterar dina tillgångar med AES, använder Media Services viktiga tjänsten och även använder dynamisk kryptering.

1. [Skapa en tillgång och överföra filer till tillgången](media-services-protect-with-aes128.md#create_asset).
2. [Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet](media-services-protect-with-aes128.md#encode_asset).
3. [Skapa en innehållsnyckel och associera den med den kodade tillgången](media-services-protect-with-aes128.md#create_contentkey). I Media Services innehåller innehållsnyckeln tillgångens krypteringsnyckel.
4. [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-protect-with-aes128.md#configure_key_auth_policy). Innehållsnyckelns auktoriseringsprincip måste konfigureras av dig och uppfyllas av klienten för att innehållsnyckeln ska kunna levereras till klienten.
5. [Konfigurera leveransprincipen för en tillgång](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Konfigurationen för leveransprincipen omfattar: URL för anskaffning och initiering Vector (IV) (AES 128 kräver samma IV ska anges när kryptera och dekryptera), leveransprotokoll (till exempel MPEG DASH, HLS, Smooth Streaming eller alla), typen av dynamisk kryptering (till exempel kuvert eller ingen dynamisk kryptering).

    Du kan använda olika principer för varje protokoll för samma tillgång. Du kan till exempel tillämpa PlayReady-kryptering för Smooth/DASH och AES Envelope för HLS. Alla protokoll som inte har definierats i en leveransprincip (exempelvis kan du lägga till en enskild princip som endast anger HLS som protokoll) kommer att blockeras från strömning. Ett undantag till detta är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll i klartext.

6. [Skapa en OnDemand-positionerare](media-services-protect-with-aes128.md#create_locator) för att få en strömnings-URL.

Artikeln beskriver också [hur ett klientprogram kan begära en nyckel från tjänsten key](media-services-protect-with-aes128.md#client_request).

Du hittar ett komplett .NET [exempel](media-services-protect-with-aes128.md#example) i slutet av artikeln.

Följande bild visar arbetsflödet som beskrivs ovan. Här används aktuellt token för autentisering.

![Skydda med AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Resten av den här artikeln innehåller detaljerade förklaringar, kodexempel och länkar till avsnitt som visar hur du utför de uppgifter som beskrivs ovan.

## <a name="current-limitations"></a>Aktuella begränsningar
Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort en befintlig lokaliserare (om sådan finns) och skapa en ny.

## <a id="create_asset"></a>Skapa en tillgång och överföra filer till tillgången
För att hantera, koda och strömma videor måste du först överföra innehållet till Microsoft Azure Media Services. När du har överfört innehållet lagras det på ett säkert sätt i molnet för vidare bearbetning och strömning. 

Utförlig information finns i [Överföra filer till ett Media Services-konto](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet
Med dynamisk kryptering alla måste är att skapa en tillgång som innehåller en uppsättning med flera bithastigheter MP4-filer eller Smooth Streaming källfiler i multibithastighet. Baserat på formatet som anges i begäran manifest eller fragment strömning på begäran server ser till att du får dataströmmen i protokollet som du har valt. Detta innebär att du bara behöver lagra och betala för filerna i ett enda lagringsformat, och Media Services-tjänsten skapar och ger lämplig respons baserat på begäranden från en klient. Mer information finns i [översikt över dynamisk paketering](media-services-dynamic-packaging-overview.md) artikel.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
>
>Dessutom måste din tillgång för att kunna använda dynamisk paketering och dynamisk kryptering innehåller en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer.

Mer information om att koda finns i [Koda en tillgång med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Skapa en innehållsnyckel och associera den med den kodade tillgången
I Media Services innehåller innehållsnyckeln den nyckel som du vill kryptera en tillgång med.

Utförlig information finns i [Skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Konfigurera en auktoriseringsprincip för innehållsnyckeln
Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Innehållsnyckelns auktoriseringsprincip måste konfigureras av dig och uppfyllas av klienten (spelaren) för att nyckeln ska kunna levereras till klienten. Principen för auktorisering av innehållsnyckel kan ha en eller flera auktoriseringsbegränsningar: öppen och token begränsning eller en IP-begränsning.

Mer information finns i [Konfigurera  innehållsnyckelns auktoriseringsprincip](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Konfigurera tillgångsleveransprincip
Konfigurera leveransprincipen för din tillgång. Tillgångsleveransprincipen innehåller bland annat följande:

* URL för anskaffning av nyckeln. 
* Den initieringen Vector (IV) för kuvert-kryptering. AES-128 kräver samma IV ska anges när kryptera och dekryptera. 
* Protokollet för tillgångsleverans (t.ex. MPEG DASH, HLS, jämn direktuppspelning eller alla).
* Typen av dynamisk kryptering (till exempel AES envelope) eller ingen dynamisk kryptering. 

Detaljerad information finns i [konfigurera tillgångsleveransprincip](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Skapa en lokaliserare för OnDemand-strömning för att få en strömnings-URL
Du måste ge din användare med strömnings-URL för Smooth, DASH eller HLS.

> [!NOTE]
> Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort en befintlig lokaliserare (om sådan finns) och skapa en ny.
> 
> 

Anvisningar för hur du publicerar en tillgång och skapar en strömnings-URL finns i [Skapa en strömnings-URL](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Hämta en testtoken
Hämta en testtoken baserat på de tokenbegränsningar som användes för nyckelauktoriseringsprincipen.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Du kan använda [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) för att testa din dataström.

## <a id="client_request"></a>Hur kan klienten begär en nyckel från tjänsten key?
I föregående steg skapas den URL som pekar på en manifestfil. Klienten måste extrahera den nödvändiga informationen från strömmande manifest-filer för att göra en begäran till tjänsten nyckel.

### <a name="manifest-files"></a>Manifest-filer
Klienten måste extrahera URL (som också innehåller innehåll nyckeln ID (kid)) värdet från manifestfilen. Klienten att försöka hämta krypteringsnyckeln från tjänsten nyckel. Klienten måste också extrahera IV värdet och Använd den dekryptera dataströmmen. I följande fragment visas den <Protection> element för Smooth Streaming-manifestet.

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

När det gäller HLS bryts rot-manifestet i segmentet filer. 

Rot-manifestet är till exempel: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) den innehåller en lista över segment filnamn.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Om du öppnar en fil segment i textredigerare (till exempel http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), it should contain #EXT-X-nyckel som anger att filen är krypterad.

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
>Om du planerar att spela upp en AES krypteras HLS i Safari, se [bloggen](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Begära nyckeln från tjänsten nyckel

Följande kod visar hur du skickar en begäran till Media Services viktiga tjänsten med hjälp av en nyckel leverans Uri (som har extraherats från manifestet) och en token (den här artikeln inte dig om hur du hämtar Simple Web Tokens från en Secure Token Service).

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

## <a name="protect-your-content-with-aes-128-using-net"></a>Skydda ditt innehåll med AES-128 med hjälp av .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Skapa och konfigurera ett Visual Studio-projekt

1. Konfigurera utvecklingsmiljön och fyll i filen app.config med anslutningsinformation, enligt beskrivningen i [Media Services-utveckling med .NET](media-services-dotnet-how-to-use.md). 
2. Lägg till följande element i **appSettings** som definieras i filen app.config:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>Exempel

Skriv över koden i Program.cs-filen med koden som visas i det här avsnittet.
 
>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [detta](media-services-dotnet-manage-entities.md#limit-access-policies) artikel.

Se till att uppdatera variablerna så att de pekar på mappar där dina indatafiler finns.

[!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


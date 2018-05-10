---
title: Använd dynamiska AES-128-kryptering och tjänsten key | Microsoft Docs
description: Leverera ditt innehåll som krypterats med AES 128-bitars krypteringsnycklar med hjälp av Microsoft Azure Media Services. Media Services tillhandahåller också viktiga tjänsten som levererar krypteringsnycklar till behöriga användare. Det här avsnittet visar hur du dynamiskt kryptera med AES-128 och använda tjänsten nyckel.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 335c080519df48709ebc5c1c3c44d9386d16b790
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>Använd dynamiska AES-128-kryptering och tjänsten nyckel
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> Information om hur du hämtar den senaste versionen av Java SDK och börjar utveckla med Java finns i [Kom igång med Java-klientens SDK för Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Om du vill hämta den senaste PHP SDK för Media Services, leta efter version 0.5.7 av Microsoft/WindowsAzure-paketet i [Packagist-databasen](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="overview"></a>Översikt
> [!NOTE]
> Mer information om hur du krypterar innehåll med den Standard AES (Advanced Encryption) för leverans till Safari på macOS finns [i det här blogginlägget](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).
> En översikt över hur du skyddar dina medieinnehåll med AES-kryptering, se [den här videon](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption).
> 
> 

 Du kan använda Media Services för att leverera HTTP Live Streaming (HLS) och Smooth Streaming krypteras med AES med 128-bitars krypteringsnycklar. Media Services tillhandahåller också viktiga tjänsten som levererar krypteringsnycklar till behöriga användare. Media Services för att kryptera en tillgång kan du associera en krypteringsnyckel med tillgången och även konfigurera auktoriseringsprinciper för nyckeln. När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att kryptera dynamiskt innehåll med hjälp av AES-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från tjänsten nyckel. För att avgöra om användaren har behörighet att hämta nyckel för utvärderar tjänsten auktoriseringsprinciper som du angav för nyckeln.

Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Auktoriseringsprincipen för innehållsnyckeln kan ha en eller flera auktoriseringsbegränsningar: antingen öppen eller tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten [simple web token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) och [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). Mer information finns i [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-protect-with-aes128.md#configure_key_auth_policy).

För att dra fördel av dynamisk kryptering behöver du en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Du måste också konfigurera leveransprincipen för tillgången (beskrivs senare i den här artikeln). Sedan, baserat på det format som anges i strömnings-URL:en, kommer servern för strömning på begäran att säkerställa att dataströmmen levereras i det protokoll som du har valt. Därför kan behöva du lagra och betala endast för filerna i ett enda lagringsformat. Media Services skapar och hanterar lämpligt svar baserat på begäran från en klient.

Den här artikeln är användbart för utvecklare som arbetar på appar som levererar media som är skyddade. Artikeln visar hur du konfigurerar tjänsten nyckel med auktoriseringsprinciper så att endast auktoriserade klienter kan få krypteringsnycklar. Den visar även hur du använder dynamisk kryptering.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>AES-128 dynamisk kryptering och viktiga delivery service arbetsflöde

Utför följande allmänna steg när du krypterar dina tillgångar med AES med hjälp av Media Services viktiga tjänsten och genom att använda dynamisk kryptering:

1. [Skapa en tillgång och överföra filer till tillgången](media-services-protect-with-aes128.md#create_asset).

2. [Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet](media-services-protect-with-aes128.md#encode_asset).

3. [Skapa en innehållsnyckel och associera den med den kodade tillgången](media-services-protect-with-aes128.md#create_contentkey). I Media Services innehåller innehållsnyckeln tillgångens krypteringsnyckel.

4. [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-protect-with-aes128.md#configure_key_auth_policy). Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Klienten måste uppfylla principen innan innehållsnyckeln kan levereras till klienten.

5. [Konfigurera leveransprincipen för en tillgång](media-services-protect-with-aes128.md#configure_asset_delivery_policy). Konfigurationen för leveransprincipen omfattar viktiga förvärv URL: en och en initieringsvektor (IV). (AES-128 kräver samma IV för kryptering och dekryptering.) Konfigurationen innehåller också leveransprotokoll (till exempel MPEG DASH, HLS, Smooth Streaming eller alla) och typen av dynamisk kryptering (till exempel kuvert eller ingen dynamisk kryptering).

    Du kan använda olika principer för varje protokoll för samma tillgång. Du kan till exempel tillämpa PlayReady-kryptering för Smooth/DASH och AES envelope för HLS. Alla protokoll som inte är definierad i en leveransprincip blockeras från strömning. (Ett exempel är om du lägger till en enda princip som anger endast HLS som protokoll). Ett undantag är om du inte har definierat någon tillgångsleveransprincip alls. Därefter tillåts alla protokoll fritt.

6. [Skapa en OnDemand-positionerare](media-services-protect-with-aes128.md#create_locator) få en strömnings-URL.

Artikeln beskriver också [hur ett klientprogram kan begära en nyckel från tjänsten key](media-services-protect-with-aes128.md#client_request).

Du hittar ett komplett [.NET-exempel](media-services-protect-with-aes128.md#example) i slutet av artikeln.

Följande bild visar arbetsflödet som beskrevs tidigare. Här används aktuellt token för autentisering.

![Skydda med AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

Resten av den här artikeln innehåller förklaringar, kodexempel och länkar till avsnitt som visar hur du utför de uppgifter som beskrevs tidigare.

## <a name="current-limitations"></a>Aktuella begränsningar
Om du lägger till eller uppdaterar din tillgångs leveransprincip måste du ta bort eventuella befintliga lokaliserare och skapa en ny.

## <a id="create_asset"></a>Skapa en tillgång och överföra filer till tillgången
För att hantera, koda och strömma videor måste du först överföra innehållet till Media Services. När du har överfört innehållet lagras det på ett säkert sätt i molnet för vidare bearbetning och strömning. 

Utförlig information finns i [Överföra filer till ett Media Services-konto](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Koda den tillgång som innehåller filen för MP4-uppsättningen med anpassad bithastighet
Med dynamisk kryptering skapar du en tillgång som innehåller en uppsättning MP4-filer eller Smooth Streaming-källfiler i multibithastighet. Sedan, baserat på det angivna formatet i manifestet eller fragment begäran på begäran strömmande ser servern till att du får dataströmmen i protokollet som du har valt. Sedan behöver du bara lagra och betala för filerna i ett enda lagringsformat. Media Services skapar och hanterar lämpligt svar baserat på begäran från en klient. Mer information finns i [Översikt över dynamisk paketering](media-services-dynamic-packaging-overview.md).

>[!NOTE]
>När ditt Media Services-konto skapas läggs en slutpunkt för direktuppspelning av standardtyp till i kontot med tillståndet ”Stoppad”. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet ”Körs”. 
>
>Om du vill använda dynamisk paketering och dynamisk kryptering, måste även din tillgång innehåller en uppsättning MP4s med anpassningsbar bithastighet eller Smooth Streaming-filer.

Mer information om att koda finns i [Koda en tillgång med Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Skapa en innehållsnyckel och associera den med den kodade tillgången
I Media Services innehåller innehållsnyckeln den nyckel som du vill kryptera en tillgång med.

Mer information finns i [Skapa en innehållsnyckel](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Konfigurera en auktoriseringsprincip för innehållsnyckeln
Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Du måste konfigurera en auktoriseringsprincip för innehållsnyckeln. Klienten (spelaren) måste uppfylla principen innan nyckeln levereras till klienten. Principen för auktorisering av innehållsnyckel kan ha en eller flera auktoriseringsbegränsningar, antingen öppna, token-begränsning eller en IP-begränsning.

Mer information finns i [Konfigurera innehållsnyckelns auktoriseringsprincip](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Konfigurera en tillgångsleveransprincip
Konfigurera leveransprincipen för din tillgång. Tillgångsleveransprincipen innehåller bland annat följande:

* Viktiga förvärv URL. 
* Initieringsvektorn (IV) för kuvert-kryptering. AES-128 kräver samma IV för kryptering och dekryptering. 
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

Du kan använda [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) för att testa din dataström.

## <a id="client_request"></a>Hur kan klienten begär en nyckel från tjänsten key?
I föregående steg skapas den URL som pekar på en manifestfil. Klienten måste extrahera den nödvändiga informationen från strömmande manifestfiler och gör en begäran till tjänsten nyckel.

### <a name="manifest-files"></a>Manifest-filer
Klienten måste extrahera URL (som också innehåller innehåll nyckeln ID [kid]) värdet från manifestfilen. Klienten försöker hämta krypteringsnyckeln från tjänsten nyckel. Klienten måste också extrahera IV värdet och använda den för att dekryptera dataströmmen. I följande fragment visas den <Protection> element för Smooth Streaming-manifestet:

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

När det gäller HLS bryts rot-manifestet i segmentet filer. 

Rot-manifestet är till exempel: http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl). Den innehåller en lista över segment filnamn.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Om du öppnar en fil segment i en textredigerare (till exempel http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video, format = m3u8 aapl), den innehåller #EXT X-nyckel, vilket betyder att filen är krypterad.

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
>Om du planerar att spela upp ett HLS som krypterats med AES i Safari Se [bloggen](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Begära nyckeln från tjänsten nyckel

Följande kod visar hur du skickar en begäran till Media Services viktiga tjänsten med hjälp av en nyckel leverans Uri (som har extraherats från manifestet) och en token. (Den här artikeln inte beskriver hur du hämta SWTs från en Säkerhetstokentjänst.)

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
            <add key="Issuer" value="http://testacs.com"/>
            <add key="Audience" value="urn:test"/>
    ```

### <a id="example"></a>Exempel

Skriv över koden i Program.cs-filen med koden som visas i det här avsnittet.
 
>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika Media Services-principer (till exempel för lokaliserare principen eller ContentKeyAuthorizationPolicy). Använd samma princip-ID om du alltid använda samma dagar/behörigheter. Ett exempel är principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i avsnittet ”gränsen åtkomstprinciper” i [hantera tillgångar och relaterade entiteter med Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies).

Se till att uppdatera variablerna så att de pekar på mappar där dina indatafiler finns.

```csharp
    [!code-csharp[Main](../../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]
```

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


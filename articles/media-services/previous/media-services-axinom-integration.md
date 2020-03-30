---
title: Använda Axinom för att leverera Widevine-licenser till Azure Media Services | Microsoft-dokument
description: I den här artikeln beskrivs hur du kan använda Azure Media Services (AMS) för att leverera en ström som är dynamiskt krypterad av AMS med både PlayReady- och Widevine DRMs. PlayReady-licensen kommer från Licensservern Media Services PlayReady och Widevine-licensen levereras av Axinom-licensservern.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 2ec3276b9b02c29b80d46e5fd31298c909857182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197172"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Använd Axinom för att leverera Widevine-licenser till Azure Media Services 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Översikt
Azure Media Services (AMS) har lagt till dynamiskt skydd för Google Widevine (se [Mingfeis blogg](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) för mer information). Dessutom har Azure Media Player (AMP) också lagt till Widevine-stöd (se [AMP-dokument](https://amp.azure.net/libs/amp/latest/docs/) för mer information). Detta är en stor prestation i streaming DASH innehåll som skyddas av CENC med multi-native-DRM (PlayReady och Widevine) på moderna webbläsare utrustade med MSE och EME.

Från och med Media Services .NET SDK version 3.5.2 kan du med Media Services konfigurera Widevine-licensmallen och få Widevine-licenser. Du kan också använda följande AMS-partner för hjälp med att leverera Widevine-licenser: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/) och [castLabs](https://castlabs.com/company/partners/azure/).

I den här artikeln beskrivs hur du integrerar och testar Widevine-licensserver som hanteras av Axinom. Närmare bestämt omfattar det följande:  

* Konfigurera dynamisk gemensam kryptering med flera DRM (PlayReady och Widevine) med motsvarande url:er för licensförvärv.
* Generera en JWT-token för att uppfylla licensserverkraven.
* Utveckla Azure Media Player-appen, som hanterar licensförvärv med JWT-tokenautentisering.

Hela systemet och flödet av innehållsnyckel, nyckel-ID, nyckelutsäde, JTW-token och dess anspråk kan bäst beskrivas av följande diagram:

![DASH och CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Information om hur du konfigurerar dynamiskt skydd och nyckelleveransprincip finns i Mingfeis blogg: [Så här konfigurerar du Widevine-paketering med Azure Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

Du kan konfigurera dynamiskt CENC-skydd med multi-DRM för DASH-direktuppspelning med båda följande:

1. PlayReady skydd för Microsoft Edge och IE11, som kan ha en token auktorisering begränsning. Principen för tokenbegränsad måste åtföljas av en token som utfärdats av en säker tokentjänst (STS), till exempel Azure Active Directory;
2. Widevine skydd för Chrome, kan det kräva tokenautentisering med token utfärdas av en annan STS. 

Se [JWT Token Generation](media-services-axinom-integration.md#jwt-token-generation) avsnitt för varför Azure Active Directory inte kan användas som en STS för Axinom widevine licensserver.

### <a name="considerations"></a>Överväganden
1. Du måste använda det angivna nyckelfröet i Axinom (88880000000000000000000000000000000000000000000) och ditt genererade eller valda nyckel-ID för att generera innehållsnyckeln för att konfigurera nyckelleveranstjänsten. Axinom-licensservern utfärdar alla licenser som innehåller innehållsnycklar baserat på samma nyckelutsäde, vilket gäller för både testning och produktion.
2. Den Widevine licens förvärv [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense)URL för testning: . Både HTTP och HTTS är tillåtna.

## <a name="azure-media-player-preparation"></a>Förbered Azure Media Player
AMP v1.4.0 stöder uppspelning av AMS-innehåll som är dynamiskt förpackat med både PlayReady och Widevine DRM.
Om Widevine-licensservern inte kräver tokenautentisering behöver du inte göra något ytterligare för att testa ett DASH-innehåll som skyddas av Widevine. Ett exempel ger AMP-teamet ett enkelt [exempel](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), där du kan se det fungera i Microsoft Edge och IE11 med PlayReady och Chrome med Widevine.
Widevine-licensservern som tillhandahålls av Axinom kräver JWT-tokenautentisering. JWT-token måste skickas med licensbegäran via ett HTTP-huvud "X-AxDRM-Message". För detta ändamål måste du lägga till följande javascript på webbsidan som är värd för AMP innan du ställer in källan:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

Resten av AMP-koden är standard AMP API som i AMP-dokument [här](https://amp.azure.net/libs/amp/latest/docs/).

Ovanstående javascript för att ställa in anpassade auktoriseringshuvud är fortfarande en kortsiktig metod innan den officiella långsiktiga metoden i AMP släpps.

## <a name="jwt-token-generation"></a>JVV Token Generation
Axinom Widevine licensserver för testning kräver JWT token autentisering. Dessutom är en av anspråken i JWT-token av en komplex objekttyp i stället för primitiv datatyp.

Tyvärr kan Azure AD endast utfärda JWT-token med primitiva typer. På samma sätt kan .NET Framework API (System.IdentityModel.Tokens.SecurityTokenHandler och JwtPayload) bara mata in komplex objekttyp som anspråk. Anspråken serialiseras dock fortfarande som sträng. Därför kan vi inte använda någon av de två för att generera JWT-token för Widevine-licensbegäran.

John Sheehan's [JWT NuGet paket](https://www.nuget.org/packages/JWT) uppfyller behoven så vi kommer att använda denna NuGet paketet.

Nedan är koden för att generera JWT token med nödvändiga anspråk som krävs av Axinom Widevine licensserver för testning:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Licensserver för Axinom Widevine

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Överväganden
1. Även om AMS PlayReady-licensleveranstjänsten kräver "Bearer=" före en autentiseringstoken, använder inte Axinom Widevine-licensservern den.
2. Kommunikationsnyckeln Axinom används som signeringsnyckel. Nyckeln är en hex sträng, men det måste behandlas som en serie byte inte en sträng vid kodning. Detta uppnås med metoden ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Hämta nyckel-ID
Du kanske har märkt att i koden för att generera en JWT-token krävs nyckel-ID. Eftersom JWT-token måste vara klar innan AMP-spelaren läses in måste nyckel-ID hämtas för att generera JWT-token.

Naturligtvis finns det flera sätt att få tag på nyckel-ID. Man kan till exempel lagra nyckel-ID tillsammans med innehållsmetadata i en databas. Du kan också hämta nyckel-ID från DASH MPD-filen (Media Presentation Description). Koden nedan är för den senare.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Sammanfattning

Med det senaste tillägget av Widevine-stöd i både Azure Media Services Content Protection och Azure Media Player kan vi implementera streaming av DASH + Multi-native-DRM (PlayReady + Widevine) med både PlayReady-licenstjänst i AMS- och Widevine-licens från Axinom för följande moderna webbläsare:

* Chrome
* Microsoft Edge på Windows 10
* IE 11 på Windows 8.1 och Windows 10
* Både Firefox (Desktop) och Safari på Mac (inte iOS) stöds också via Silverlight och samma WEBBADRESS med Azure Media Player

Följande parametrar krävs i mini-lösning som utnyttjar Axinom Widevine-licensservern. Med undantag för nyckel-ID tillhandahålls resten av parametrarna av Axinom baserat på deras Widevine-serverinställning.

| Parameter | Hur det används |
| --- | --- |
| Id för kommunikationsnyckel |Måste inkluderas som värdet för anspråket "com_key_id" i JWT-token (se [det här](media-services-axinom-integration.md#jwt-token-generation) avsnittet). |
| Kommunikationsnyckel |Måste användas som signeringsnyckel för JWT-token (se [det här](media-services-axinom-integration.md#jwt-token-generation) avsnittet). |
| Viktiga frö |Måste användas för att generera innehållsnyckel med ett givet innehållsnyckel-ID (se [det här](media-services-axinom-integration.md#content-protection) avsnittet). |
| Widevine Licens förvärv URL |Måste användas för att konfigurera leveransprincip för DASH-direktuppspelning (se [det här](media-services-axinom-integration.md#content-protection) avsnittet). |
| Nyckel-ID för innehåll |Måste inkluderas som en del av värdet för anspråk på rättighetsmeddelande för JWT-token (se [det här](media-services-axinom-integration.md#jwt-token-generation) avsnittet). |

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av användarvillkoren och sekretesspolicyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Tack
Vi vill uppmärksamma följande personer som bidragit till att skapa detta dokument: Kristjan Jõgi från Axinom, Mingfei Yan och Amit Rajput.

